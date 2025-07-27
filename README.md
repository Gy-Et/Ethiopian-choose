<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>MatchFinder Dating App</title>
    <link rel="stylesheet" href="css/style.css">
</head>
<body>
    <header>
        <h1>MatchFinder</h1>
        <nav>
            <button id="loginBtn">Login</button>
            <button id="signupBtn">Sign Up</button>
        </nav>
    </header>

    <main>
        <section id="auth-forms" class="hidden">
            <form id="loginForm">
                <h2>Login</h2>
                <input type="email" placeholder="Email" required>
                <input type="password" placeholder="Password" required>
                <button type="submit">Login</button>
            </form>

            <form id="signupForm" class="hidden">
                <h2>Sign Up</h2>
                <input type="text" placeholder="Name" required>
                <input type="email" placeholder="Email" required>
                <input type="password" placeholder="Password" required>
                <input type="number" placeholder="Age" min="18" required>
                <select id="gender">
                    <option value="male">Male</option>
                    <option value="female">Female</option>
                    <option value="other">Other</option>
                </select>
                <button type="submit">Create Account</button>
            </form>
        </section>

        <section id="dating-profiles">
            <!-- Profiles will be loaded here -->
        </section>
    </main>

    <script src="js/app.js"></script>
</body>
</html>
git add .
git commit -m "Initial commit for dating app"
git branch -M main
git remote add origin https://github.com/yourusername/dating-app.git
git push -u origin main
