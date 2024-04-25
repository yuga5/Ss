<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Form</title>
</head>
<body>
    <h1>Form</h1>
    <form action="/submit" method="POST">
        <label for="name">Name:</label>
        <input type="text" id="name" name="name" required><br><br>
        
        <label for="email">Email:</label>
        <input type="email" id="email" name="email" required><br><br>
        
        <label for="message">Message:</label><br>
        <textarea id="message" name="message" rows="4" cols="50" required></textarea><br><br>
        
        <button type="submit">Submit</button>
    </form>
</body>
</html>

const express = require('express');
const fs = require('fs');
const path = require('path');

const app = express();
const port = 3000;

app.use(express.urlencoded({ extended: true }));
app.use(express.static(path.join(__dirname, 'public')));

// Route to serve the HTML form
app.get('/', (req, res) => {
    res.sendFile(path.join(__dirname, 'public', 'index.html'));
});

// Route to handle form submission
app.post('/submit', (req, res) => {
    const formData = req.body;
    const jsonData = JSON.stringify(formData);

    fs.writeFile('data.json', jsonData, (err) => {
        if (err) throw err;
        console.log('Data saved successfully');
    });

    res.redirect('/display');
});

// Route to display the stored data
app.get('/display', (req, res) => {
    fs.readFile('data.json', (err, data) => {
        if (err) throw err;
        const formData = JSON.parse(data);
        res.send(`<h1>Stored Data:</h1><pre>${JSON.stringify(formData, null, 2)}</pre>`);
    });
});

app.listen(port, () => {
    console.log(`Server is running on http://localhost:${port}`);
});
