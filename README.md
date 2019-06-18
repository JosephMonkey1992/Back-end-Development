# Back-end-Development
html, PHP, SQL. 
## Top View
- [1.User log in to database](#1User-log-in-to-database)
- [2.Connect to DBMS and Select DB](#2Connect-to-DBMS-and-Select-DB)
- [3.Query](#3Query)
- [4.use result](#4use-result)
- [5.video upload to database and retrieve to server site](#5video-upload-to-database-and-retrieve-to-server-site)
- [6.Read video uploaded](#6Read-video-uploaded)
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
$query= "SELECT * FROM user_data WHERE username = '$username' AND password = '$password'";
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
### 4.use result
```PHP
//5.use results
echo '<span style="color: white;" /><b>We provide you with other users information, feel free to reach out to them and have fun!<b><hr/>';
while($row = $result->fetch_assoc()){
	echo '<span style="color: white;" /><b>User Name</b>: ' .$row['username'] . '<br />';
	echo '<span style="color: white;" /><b>JHU Email</b>: ' .$row['jhuemail'] . '<hr />';
}
//disconnect DBMS
$conn->close();
```
### 5.video upload to database and retrieve to server site.
connect to another database for videos.
```PHP
<?php
##this file called config.php
$host = "localhost"; /* Host name */
$user = "root"; /* User */
$password = ""; /* Password */
$dbname = "asian"; /* Database name */

$con = mysqli_connect($host, $user, $password,$dbname);
// Check connection
if (!$con) {
  die("Connection failed: " . mysqli_connect_error());
}
```
upload video to database
```PHP
<!doctype html>
<html>
  <head>
    <?php
    include("config.php");

    if(isset($_POST['but_upload'])){
       $maxsize = 5242880; // 5MB

       $name = $_FILES['file']['name'];

       $target_file = $_FILES['file']['name'];

       // Select file type
       $videoFileType = strtolower(pathinfo($target_file,PATHINFO_EXTENSION));

       // Valid file extensions
       $extensions_arr = array("mp4","avi","3gp","mov","mpeg");

       // Check extension
       if( in_array($videoFileType,$extensions_arr) ){

          // Check file size
          if(($_FILES['file']['size'] >= $maxsize) || ($_FILES['file']['size'] == 0)) {
            echo "File too large. File must be less than 5MB.";
          }else{
            // Upload
            if(move_uploaded_file($_FILES['file']['tmp_name'],$target_file)){
              // Insert record
              $query = "INSERT INTO videos(name,location) VALUES('$name','$target_file')";

              mysqli_query($con,$query);
              echo "Upload successfully.";
            }
          }

       }else{
          echo "Invalid file extension.";
       }

     }
     ?>
  </head>
  <body bgcolor="skyblue">
  <body>
    <form method="post" action="" enctype='multipart/form-data'>
      <input type='file' name='file' />
      <input type='submit' value='Upload' name='but_upload'>
    </form>

  </body>
</html>
```
### 6.Read video uploaded
post those videos uploaded to server site.
```PHP
<?php
include('config.php');
?>
<!doctype html>
<html>
  <head>
    <style>
    video{
     float: left;
    }
    </style>
  </head>
  <body>
    <div>

     <?php
     $fetchVideos = mysqli_query($con, "SELECT location FROM videos ORDER BY id DESC");
     while($row = mysqli_fetch_assoc($fetchVideos)){
       $location = $row['location'];

       echo "<div >";
       echo "<video src='".$location."' controls width='320px' height='200px' >";
       echo "</div>";
     }
     ?>

    </div>

  </body>
  <body bgcolor="#000000">
  <body bgcolor="rgb(0,0,0)">
  <body bgcolor="black">
</html>
```
