# Uploading Files with React.js and PHP to a Shared Host cPanel

**Author:** Jessy Bandya

This guide will walk you through the process of uploading files using React.js for the frontend and PHP for the backend, hosted on a shared hosting environment with cPanel.

## Prerequisites

- Basic understanding of React.js and PHP.
- Access to a cPanel hosting account with PHP support.
- Node.js and npm installed on your development machine.

## Setup

1. **Create a React.js Project**: If you haven't already, create a new React.js project using Create React App or your preferred method.

2. **Install Axios**: Axios is a promise-based HTTP client for the browser and Node.js, which we'll use to make HTTP requests. Install it using npm:

   ```bash
   npm install axios
   ```

3. **Create a PHP Backend**: In your cPanel File Manager, create a folder named `backend` in your web root directory. Inside this folder, create a PHP file named `upload.php` with the following content:

   ```php
   <?php
   header("Access-Control-Allow-Origin: *");
   header("Access-Control-Allow-Headers: *");
   header("Access-Control-Allow-Methods: *");

   // Establish a database connection (replace with your database configuration)
   // Database configuration
   $host = 'localhost';
   $dbName = 'your_database_name';
   $username = 'your_database_username';
   $password = 'your_database_password';

   try {
     $pdo = new PDO("mysql:host=$host;dbname=$dbName", $username, $password);
     $pdo->setAttribute(PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION);
   } catch (PDOException $e) {
     echo "Connection failed: " . $e->getMessage();
     exit();
   }

   $uploadDirectory = '/home/your_username/public_html/backend/media/files/';

   if ($_FILES['file']['error'] === UPLOAD_ERR_OK) {
       $tempFile = $_FILES['file']['tmp_name'];
       $targetFile = $uploadDirectory . basename($_FILES['file']['name']);

       if (move_uploaded_file($tempFile, $targetFile)) {
           $linkToStoreInDB = 'http://your_domain.com/backend/media/files/' .basename($_FILES['file']['name']);

               try {
                   $stmt = $pdo->prepare("INSERT INTO your_table_name (file) 
                            VALUES (:file)");
                   $stmt->bindParam(':file', $linkToStoreInDB);
                   $stmt->execute();

                   // Return a success message after successful insertion and file upload
                   echo json_encode(['message' => 'File added successfully!']);
               } catch (PDOException $e) {
                   // Return an error message if insertion fails
                   echo json_encode(['error' => $e->getMessage()]);
               }
       } else {
           echo "Failed to move the file.";
       }
   } else {
       echo "Error occurred during file upload.";
   }
   ?>
   ```

   Replace placeholders (`your_database_name`, `your_database_username`, `your_database_password`, `your_table_name`, `your_username`, and `your_domain.com`) with your actual database and cPanel credentials.

4. **Frontend Implementation**: In your React.js project, implement the file upload functionality. Here's an example using Axios:

   ```javascript
   import React, { useState } from 'react';
   import axios from 'axios';

   function FileUpload() {
     const [file, setFile] = useState(null);

     const handleFileChange = (e) => {
       setFile(e.target.files[0]);
     };

     const handleUpload = () => {
       const formData = new FormData();
       formData.append('file', file);

       axios.post('http://your_domain.com/backend/upload.php', formData, {
         headers: {
           'Content-Type': 'multipart/form-data',
         },
       })
       .then((response) => {
         console.log('Response:', response.data);
         // Handle success
       })
       .catch((error) => {
         console.error('Error:', error);
         // Handle error
       });
     };

     return (
       <div>
         <input type="file" onChange={handleFileChange} />
         <button onClick={handleUpload}>Upload</button>
       </div>
     );
   }

   export default FileUpload;
   ```

   Replace `http://your_domain.com/backend/upload.php` with the actual URL to your PHP backend file.

5. **Testing**: Run your React.js application and test the file upload functionality.

## Conclusion



## HAPPY CODING
