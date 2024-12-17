# Employer_management
Step 1: Set Up SQL Database
1.1 Install MySQL
Download and install MySQL Community Server from the official MySQL website.
1.2 Create the Database and Table
Open MySQL Workbench or terminal.

Log in to MySQL:

Go to command prompt:
mysql -u root -p
Enter the password you set for MySQL.
Run the following SQL script to create the database and the employees table:

sql
CREATE DATABASE employee_management;

USE employee_management;

CREATE TABLE employees (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    employee_id VARCHAR(10) NOT NULL UNIQUE,
    email VARCHAR(100) NOT NULL UNIQUE,
    phone_number VARCHAR(10) NOT NULL,
    department ENUM('HR', 'Engineering', 'Marketing') NOT NULL,
    date_of_joining DATE NOT NULL,
    role VARCHAR(50) NOT NULL
);
Verify the table was created:

sql
SHOW TABLES;
DESCRIBE employees;

Step 2: Set Up Backend with Node.js and Express
2.1 Install Node.js
Download and install Node.js from the official website.

Verify installation:
bash
node -v
npm -v

2.2 Create the Backend Directory
Open VS Code.
Create a folder named employee-management-backend.
Open this folder in VS Code.
2.3 Initialize the Backend Project
Open the terminal in VS Code.

Initialize the Node.js project:
bash
npm init -y

Install necessary dependencies:
bash
npm install express mysql2 body-parser cors

2.4 Create the Backend Server
Create a file named server.js in the backend folder.
Add the following code:

javascript
const express = require("express");
const mysql = require("mysql2");
const bodyParser = require("body-parser");
const cors = require("cors");

const app = express();
app.use(bodyParser.json());
app.use(cors());

// Database connection
const db = mysql.createConnection({
    host: "localhost",
    user: "root",
    password: "yourpassword", // Replace with your MySQL password
    database: "employee_management",
});

db.connect((err) => {
    if (err) throw err;
    console.log("Connected to MySQL database.");
});

// API to add employee
app.post("/addEmployee", (req, res) => {
    const { name, employee_id, email, phone_number, department, date_of_joining, role } = req.body;

    const query = `
        INSERT INTO employees (name, employee_id, email, phone_number, department, date_of_joining, role)
        VALUES (?, ?, ?, ?, ?, ?, ?);
    `;

    db.query(query, [name, employee_id, email, phone_number, department, date_of_joining, role], (err) => {
        if (err) {
            if (err.code === "ER_DUP_ENTRY") {
                return res.status(400).json({ error: "Duplicate Employee ID or Email" });
            }
            return res.status(500).json({ error: "Server error." });
        }
        res.status(200).json({ message: "Employee added successfully!" });
    });
});

// API to fetch employees
app.get("/employees", (req, res) => {
    const query = "SELECT * FROM employees";

    db.query(query, (err, results) => {
        if (err) {
            return res.status(500).json({ error: "Failed to fetch employees." });
        }
        res.status(200).json(results);
    });
});

// Start the server
app.listen(5000, () => {
    console.log("Server running on http://localhost:5000");
});

2.5 Test the Backend Server
Run the server:

bash
node server.js
You should see:

bash
Server running on http://localhost:5000
Test API endpoints using Postman or a browser:

POST: http://localhost:5000/addEmployee
Body (JSON):
json
Copy code
{
    "name": "John Doe",
    "employee_id": "EMP001",
    "email": "john.doe@example.com",
    "phone_number": "1234567890",
    "department": "HR",
    "date_of_joining": "2024-02-01",
    "role": "Manager"
}
GET: http://localhost:5000/employees

Step 3: Set Up React Frontend
3.1 Create React App
Go back to VS Code.
Create a folder named employee-management-frontend.
Open this folder in VS Code.

Initialize React:
bash
npx create-react-app .

Install Axios for HTTP requests and Material-UI for styling:
bash
npm install axios @mui/material @emotion/react @emotion/styled

3.2 Create the Form Component
Inside the src folder, create AddEmployeeForm.js:

javascript
import React, { useState } from "react";
import axios from "axios";
import { TextField, Button, Box, Typography } from "@mui/material";

const AddEmployeeForm = () => {
    const [formData, setFormData] = useState({
        name: "",
        employee_id: "",
        email: "",
        phone_number: "",
        department: "HR",
        date_of_joining: "",
        role: "",
    });

    const [message, setMessage] = useState("");

    const handleSubmit = (e) => {
        e.preventDefault();
        axios.post("http://localhost:5000/addEmployee", formData)
            .then((res) => setMessage(res.data.message))
            .catch((err) => setMessage(err.response?.data.error || "Error"));
    };

    return (
        <Box sx={{ maxWidth: "500px", margin: "auto", mt: 5 }}>
            <Typography variant="h4">Add Employee</Typography>
            <form onSubmit={handleSubmit}>
                <TextField label="Name" fullWidth margin="normal" 
                    onChange={(e) => setFormData({ ...formData, name: e.target.value })}
                />
                <TextField label="Employee ID" fullWidth margin="normal" 
                    onChange={(e) => setFormData({ ...formData, employee_id: e.target.value })}
                />
                <Button type="submit" variant="contained" sx={{ mt: 2 }}>Submit</Button>
            </form>
            {message && <Typography color="green">{message}</Typography>}
        </Box>
    );
};

export default AddEmployeeForm;

3.3 Update App.js
Replace App.js with:

javascript
import React from "react";
import AddEmployeeForm from "./AddEmployeeForm";

function App() {
    return (
        <div>
            <AddEmployeeForm />
        </div>
    );
}

export default App;

3.4 Run the React App

Start the React app:
bash
npm start

Open your browser and visit:
bash
http://localhost:3000

Step 4: Verify Data Using MySQL Workbench
Open MySQL Workbench:

Connect to your MySQL server using the root username and password.
Select the Database:
Run the following command in the query window to use the correct database:
sql
USE employee_management;

Check the Data in the Table:

Run the following query to view all the data in the employees table:
sql
SELECT * FROM employee;
View the Results:

The query results will display all the records in the employees table, including the ones submitted from your React form.

Step 5: How to Test This Form
Backend:

Ensure server.js is running:
bash
node server.js
Start the server:
bash
npm start

You will see the following:
Server running on http://localhost:5000
Server Connected

Frontend:

Start your React app:
bash
npm start

You will see the following:
Starting the development server
React App

After that enter the details and submit the form
You will see Submitted in green
To check in Database, use Select * from employee
Finish
