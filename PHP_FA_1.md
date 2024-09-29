### PHP Fundamentals Activity

```index.php```

```php
<!DOCTYPE html>
<html lang="en">
<head>
	<meta charset="UTF-8">
	<meta name="viewport" content="width=device-width, initial-scale=1.0">
	<title>Document</title>
</head>
<body>
	<?php 
	session_start(); 
	?>

	<!-- login form -->
	<form action="handleForm.php" method="POST">
		<p>Username <input type="text" name="firstName"></p>
		<p>Password <input type="password" name="password"></p>
		<p><input type="submit" value="Login" name="submitBtn"></p>
	</form>

	<!-- logout button -->
	<form action="logout.php" method="POST">
		<p><input type="submit" value="Logout" name="logoutBtn"></p>
	</form>

	<!-- display user information when logged in -->
	<?php 
	if(isset($_SESSION['isLoggedIn']) && $_SESSION['isLoggedIn'] === true && !isset($_SESSION['errorMessage'])): 
	?>
		<h2 style="color:black;">User logged in: <?php echo $_SESSION['firstName']; ?></h2>
		<h2 style="color:black;">User password: <?php echo $_SESSION['password']; ?></h2>
	<?php endif; ?>

	<!-- display error message if another login attempt is made -->
	<p>
		<?php
		if (isset($_SESSION['errorMessage'])) {
			echo $_SESSION['errorMessage'];
			unset($_SESSION['errorMessage']);
		}
		?>
	</p>

</body>
</html>
```

```handleForm.php```

```php
<?php 
session_start();

// check if the login form is submitted
if(isset($_POST['submitBtn'])) {
	// check if a user is already logged in
	if (isset($_SESSION['isLoggedIn']) && $_SESSION['isLoggedIn'] === true) {
		$_SESSION['errorMessage'] = "User " . $_SESSION['firstName'] . " is already logged in. Wait for them to logout first.";
		header('Location: index.php');
		exit();
	}

	// get the first name and password from the form
	$firstName = $_POST['firstName'];
	$password = md5($_POST['password']);

	// set the session variables
	$_SESSION['firstName'] = $firstName;
	$_SESSION['password'] = $password;
	$_SESSION['isLoggedIn'] = true;

	header('Location: index.php');
	exit();
}
?>
```

```unset.php```

```php
<?php  
session_start(); // establish connection to the current session

// delete all session variables
unset($_SESSION['firstName']);
unset($_SESSION['password']);
unset($_SESSION['userInfo']); 

// destroy session and redirect to homepage
session_destroy();
header('Location: index.php');
exit();
?>
```

```functions.php```

```php
<?php  
function addUser($conn, $username, $password) {
	$sql = "SELECT * FROM users WHERE username=?";
	$stmt = $conn->prepare($sql);
	$stmt->execute([$username]);

	if($stmt->rowCount()==0) {
		$sql = "INSERT INTO users (username,password) VALUES (?,?)";
		$stmt = $conn->prepare($sql);
		return $stmt->execute([$username, $password]);
	}
}

function login($conn, $username, $password) {
	$query = "SELECT * FROM users WHERE username=?";
	$stmt = $conn->prepare($query);
	$stmt->execute([$username]);

	if($stmt->rowCount() == 1) {
		// returns associative array
		$row = $stmt->fetch();

		// store user info as a session variable
		$_SESSION['userInfo'] = $row;

		// get values from the session variable
		$uid = $row['user_id'];
		$uname = $row['username'];
		$passHash = $row['password'];

		// validate password 
		if(password_verify($password, $passHash)) {
			$_SESSION['user_id'] = $uid;
			$_SESSION['username'] = $uname;
			$_SESSION['email'] = $email;
			$_SESSION['userLoginStatus'] = 1;
			return true;
		}
		else {
			return false;
		}
	}
}

?>
```

```register.php```

```php
<!DOCTYPE html>
<html lang="en">
<head>
	<meta charset="UTF-8">
	<meta name="viewport" content="width=device-width, initial-scale=1.0">
	<title>Document</title>
</head>
<body>
	<p>Register here</p>
	<form action="handleForm.php" method="POST">
		<div class="fields">
			<p><input type="text" placeholder="username here" class="fields" name="username"></p>
			<p><input type="password" placeholder="password here" class="fields" name="password"></p>
			<p><input type="submit" value="Register" id="submitBtn" name="regBtn"></p>
		</div>
	</form>
</body>
</html>
```

```login.php```

```php
<?php 
session_start();

if(isset($_SESSION['welcomeMessage']) && !isset($_SESSION['username'])) {
	echo $_SESSION['welcomeMessage'];
}

?>
<!DOCTYPE html>
<html lang="en">
<head>
	<meta charset="UTF-8">
	<meta name="viewport" content="width=device-width, initial-scale=1.0">
	<title>Document</title>
</head>
<body>
	<p>Login here</p>
	<form action="handleForm.php" method="POST">
		<div class="fields">
			<p><input type="text" placeholder="username here" class="fields" name="username"></p>
			<p><input type="password" placeholder="password here" class="fields" name="password"></p>
			<p><input type="submit" value="login" id="loginBtn" name="loginBtn"></p>
		</div>
	</form>
	<a href="register.php">Register</a>
</body>
</html>
```

```logout.php```

```php
<?php  
session_start(); // establish connection to the current session
session_unset(); // delete all session variables
session_destroy(); // destroy the session itself
header('Location: index.php'); // go back to homepage
exit();
?>
```
