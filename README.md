# Back-end-Development
html, PHP, SQL. 
## Top View
- [1.User log in to database](#1User-log-in-to-database)
- [2.Connect to DBMS and Select DB](#2Connect-to-DBMS-and-Select-DB)
- [3.Query](#3Query)
### 1.User log in to database
Required to log in PHPadmin
```PHP
<?php

$db_hostname='localhost';

$db_database='user_data';

$db_username='root';

$db_password='';

?>
```
### 2.Connect to DBMS and Select DB
require once login, and then connect to DB
```PHP
<?php
//receive client form data via post method
$username=$_POST['username'];
$password=$_POST['password'];
$jhuemail=$_POST['email'];
 require_once 'login.php';  
//1&2.connect to DBMS, select DB 
$conn = new mysqli($db_hostname, $db_username, $db_password, $db_database);

if ($conn->connect_error)
{
  echo "Failed to connect to MySQL: " . $conn->connect_error;
}
?>
```
### 3.Query 
build query, check whether taken? close database.
```PHP
//3.build query
$query= "SELECT * FROM user_data WHERE username = '$username'";
//4.execute query
$result = $conn->query($query);
//5.use results
if($result->num_rows > 0) {
	echo "<script type='text/javascript'>alert('username already taken');
		window.location.href = 'Homepage.html'; </script>";
	exit();
}
##add user into database
//3.build query
$query= "INSERT INTO user_data (username, password, jhuemail) VALUES ('$username', '$password', '$jhuemail')";
//4.execute query
$result = $conn->query($query);
//echo $query;

//3&4.build query, execute query
$result = $conn->query("SELECT * FROM user_data");
//disconnect DBMS
$conn->close();
```
