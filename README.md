# Express-CRUD
node js express read write delete update


To read, write, and delete data in MySQL using Express, you'll need to interact with the MySQL database using a Node.js MySQL library

Step 1:
```bash
Install mysql
```

```bash
npm install mysql
```

Step 2: Use mysql in Your Express Application
2.1. Create a Connection Pool


```javascript

const mysql = require('mysql');

const pool = mysql.createPool({
  host: 'your-mysql-host',
  user: 'your-mysql-username',
  password: 'your-mysql-password',
  database: 'your-database-name',
  connectionLimit: 10,
});

module.exports = pool;
```

2.2. Read Data

```javascript

const express = require('express');
const pool = require('./your-pool-module');

const app = express();
const port = 5000;

app.get('/users', (req, res) => {
  pool.getConnection((err, connection) => {
    if (err) {
      console.error('Error getting connection:', err);
      res.status(500).json({ error: 'Internal Server Error' });
      return;
    }

    connection.query('SELECT * FROM users', (queryError, results) => {
      connection.release(); // Release the connection when done with it

      if (queryError) {
        console.error('Error fetching users:', queryError);
        res.status(500).json({ error: 'Internal Server Error' });
        return;
      }

      res.json({ users: results });
    });
  });
});
// Similar CRUD routes for create, update, and delete can be added.
```
2.3. Write Data (Create)

```javascript
app.post('/users', (req, res) => {
  const { username, email } = req.body;

  pool.getConnection((err, connection) => {
    if (err) {
      console.error('Error getting connection:', err);
      res.status(500).json({ success: false, error: 'Internal Server Error' });
      return;
    }

    connection.query('INSERT INTO users (username, email) VALUES (?, ?)', [username, email], (queryError, result) => {
      connection.release(); // Release the connection when done with it

      if (queryError) {
        console.error('Error creating user:', queryError);
        res.status(500).json({ success: false, error: 'Internal Server Error' });
        return;
      }

      res.json({ success: true, message: 'User created successfully', insertedId: result.insertId });
    });
  });
});

// Update and delete routes can be added similarly.
```
2.4. Update and Delete Data

```javascript

app.put('/users/:id', (req, res) => {
  const userId = req.params.id;
  const { username, email } = req.body;

  pool.getConnection((err, connection) => {
    if (err) {
      console.error('Error getting connection:', err);
      res.status(500).json({ success: false, error: 'Internal Server Error' });
      return;
    }

    connection.query('UPDATE users SET username = ?, email = ? WHERE id = ?', [username, email, userId], (queryError) => {
      connection.release(); // Release the connection when done with it

      if (queryError) {
        console.error('Error updating user:', queryError);
        res.status(500).json({ success: false, error: 'Internal Server Error' });
        return;
      }

      res.json({ success: true, message: 'User updated successfully' });
    });
  });
});

app.delete('/users/:id', (req, res) => {
  const userId = req.params.id;

  pool.getConnection((err, connection) => {
    if (err) {
      console.error('Error getting connection:', err);
      res.status(500).json({ success: false, error: 'Internal Server Error' });
      return;
    }

    connection.query('DELETE FROM users WHERE id = ?', [userId], (queryError) => {
      connection.release(); // Release the connection when done with it

      if (queryError) {
        console.error('Error deleting user:', queryError);
        res.status(500).json({ success: false, error: 'Internal Server Error' });
        return;
      }

      res.json({ success: true, message: 'User deleted successfully' });
    });
  });
});
```

This adaptation uses the mysql library for handling MySQL database connections and queries. Remember to handle connections properly by releasing them when you're done, especially in asynchronous operations.






