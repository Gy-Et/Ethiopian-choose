# Ethiopian-choose
// Node.js + Express backend for Dating App
const express = require('express');
const mongoose = require('mongoose');
const bcrypt = require('bcrypt');
const jwt = require('jsonwebtoken');
const cors = require('cors');

const app = express();
app.use(express.json());
app.use(cors());

// MongoDB connection
mongoose.connect('mongodb://localhost:27017/datingapp', { useNewUrlParser: true, useUnifiedTopology: true });

// User schema
const userSchema = new mongoose.Schema({
  username: String,
  password: String,
  name: String,
  age: Number,
  gender: String,
  interests: [String],
  bio: String,
  matches: [{ type: mongoose.Schema.Types.ObjectId, ref: 'User' }]
});
const User = mongoose.model('User', userSchema);

// Registration route
app.post('/api/register', async (req, res) => {
  const { username, password, name, age, gender, interests, bio } = req.body;
  const hash = await bcrypt.hash(password, 10);
  const user = new User({ username, password: hash, name, age, gender, interests, bio });
  await user.save();
  res.json({ message: "User registered!" });
});

// Login route
app.post('/api/login', async (req, res) => {
  const { username, password } = req.body;
  const user = await User.findOne({ username });
  if (!user) return res.status(404).json({ error: "User not found" });
  const valid = await bcrypt.compare(password, user.password);
  if (!valid) return res.status(401).json({ error: "Invalid credentials" });
  const token = jwt.sign({ userId: user._id }, 'secretkey');
  res.json({ token });
});

// Get user profile
app.get('/api/profile', async (req, res) => {
  const token = req.headers.authorization?.split(' ')[1];
  if (!token) return res.status(401).json({ error: "No token provided" });
  try {
    const decoded = jwt.verify(token, 'secretkey');
    const user = await User.findById(decoded.userId).select('-password');
    res.json(user);
  } catch {
    res.status(401).json({ error: "Invalid token" });
  }
});

// Search matches (simple: age and gender filtering)
app.get('/api/matches', async (req, res) => {
  const token = req.headers.authorization?.split(' ')[1];
  if (!token) return res.status(401).json({ error: "No token provided" });
  try {
    const decoded = jwt.verify(token, 'secretkey');
    const user = await User.findById(decoded.userId);
    const potentialMatches = await User.find({
      _id: { $ne: user._id },
      gender: { $ne: user.gender },
      age: { $gte: user.age - 5, $lte: user.age + 5 }
    }).select('-password');
    res.json(potentialMatches);
  } catch {
    res.status(401).json({ error: "Invalid token" });
  }
});

// Like a user (match)
app.post('/api/like/:id', async (req, res) => {
  const token = req.headers.authorization?.split(' ')[1];
  if (!token) return res.status(401).json({ error: "No token provided" });
  try {
    const decoded = jwt.verify(token, 'secretkey');
    const user = await User.findById(decoded.userId);
    user.matches.push(req.params.id);
    await user.save();
    res.json({ message: "User liked!" });
  } catch {
    res.status(401).json({ error: "Invalid token" });
  }
});

app.listen(5000, () => {
  console.log('Dating app backend running on http://localhost:5000');
});
