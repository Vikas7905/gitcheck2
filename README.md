<?php
// Database connection
$servername = "localhost";
$username = "root";  // Your database username
$password = "";      // Your database password
$dbname = "your_database";  // Your database name

// Create connection
$conn = new mysqli($servername, $username, $password, $dbname);
if ($conn->connect_error) {
    die("Connection failed: " . $conn->connect_error);
}

// Check if form is submitted and file is uploaded
if ($_SERVER['REQUEST_METHOD'] == 'POST' && isset($_FILES['image'])) {
    // Get the image info
    $image = $_FILES['image'];
    $imageName = $image['name'];
    $imageTmpName = $image['tmp_name'];
    $imageSize = $image['size'];
    $imageError = $image['error'];

    // Generate dynamic folder name (example: based on timestamp or user ID)
    $folderId = rand(1000, 9999); // Dynamic folder ID (you can use a user ID or any other dynamic value)
    $imageFolderPath = "images/" . $folderId; // Fixed folder + dynamic subfolder

    // Create folder if it doesn't exist
    if (!is_dir($imageFolderPath)) {
        mkdir($imageFolderPath, 0777, true);
    }

    // Generate a unique name for the image (optional)
    $imageNewName = uniqid() . ".png"; // You can also use image file extension dynamically

    // Set the final image path
    $imageFinalPath = $imageFolderPath . "/" . $imageNewName;

    // Check for any errors during upload
    if ($imageError === 0) {
        // Move the uploaded image to the desired location
        if (move_uploaded_file($imageTmpName, $imageFinalPath)) {
            // Store image path in database
            $sql = "INSERT INTO images (image_path) VALUES ('$imageFinalPath')";
            if ($conn->query($sql) === TRUE) {
                echo "Image uploaded and path saved successfully!";
            } else {
                echo "Error: " . $sql . "<br>" . $conn->error;
            }
        } else {
            echo "Failed to upload image.";
        }
    } else {
        echo "Error uploading image.";
    }
}
?>

<!-- HTML Form for Image Upload -->
<form action="" method="POST" enctype="multipart/form-data">
    <input type="file" name="image" required>
    <button type="submit">Upload Image</button>
</form>
