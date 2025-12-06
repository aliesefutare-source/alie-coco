const sqlite3 = require('sqlite3').verbose();
const db = new sqlite3.Database('data.db');

db.serialize(() => {
  db.run(`CREATE TABLE IF NOT EXISTS users(
           id INTEGER PRIMARY KEY AUTOINCREMENT,const express = require('express');
const db = require('../db');
const auth = require('../middleware/auth'); // verifică token
const router = express.Router();const express = require('express');
const stripe = require('stripe')(process.env.STRIPE_SECRET_KEY);
const auth = require('../middleware/auth');
const router = express.Router();

router.post('/create-session', auth, async (req, res) => {
  const {amount, currency = 'eur'} = req.body; // amount în cenţi (ex. 5000 = 50€)require('dotenv').config();
const express = require('express');
const cors = require('cors');
const app = express();
app.use(cors());
app.use(express.json());

app.use('/api/auth', require('./routes/auth'));
app.use('/api/contact', require('./routes/contact'));
app.use('/api/pay', require('./routes/pay'));

app.listen(process.env.PORT, () => console.log(`Server http://localhost:${process.env.PORT}`));
  const session = await stripe.checkout.sessions.create({
    payment_method_types: ['card'],
    mode: 'payment',
    line_items: [{
      price_data: {
        currency,
        product_data: {name: 'Serviciu site'},
        unit_amount: amount,
      },
      quantity: 1,
    }],
    success_url: `${req.headers.origin}/succes.html`,
    cancel_url: `${req.headers.origin}/pay.html`,
    customer_email: req.user.email,
  });
  res.json({id: session.id});
});

module.exports = router;
const jwt = require('jsonwebtoken');
module.exports = function (req, res, next) {
  const token = req.headers['authorization']?.split(' ')[1];
  if (!token) return res.status(401).json({error: 'Fără token'});
  try {
    req.user = jwt.verify(token, process.env.JWT_SECRET);
    next();
  } catch { res.status(401).json({error: 'Token invalid'}); }
};
router.post('/', auth, (req, res) => {
  const {subject, body} = req.body;const express = require('express');
const stripe = require('stripe')(process.env.STRIPE_SECRET_KEY);
const auth = require('../middleware/auth');
const router = express.Router();

router.post('/create-session', auth, async (req, res) => {
  const {amount, currency = 'eur'} = req.body; // amount în cenţi (ex. 5000 = 50€)
  const session = await stripe.checkout.sessions.create({
    payment_method_types: ['card'],
    mode: 'payment',
    line_items: [{
      price_data: {
        currency,
        product_data: {name: 'Serviciu site'},
        unit_amount: amount,
      },
      quantity: 1,
    }],
    success_url: `${req.headers.origin}/succes.html`,
    cancel_url: `${req.headers.origin}/pay.html`,
    customer_email: req.user.email,
  });
  res.json({id: session.id});
});

module.exports = router;
  db.run('INSERT INTO messages(userId,subject,body) VALUES (?,?,?)',
         [req.user.id, subject, body], function(err) {
    if (err) return res.status(500).json({error: 'Eroare server'});
    res.json({msg: 'Mesaj trimis'});
  });
});

module.exports = router;
           email TEXT UNIQUE,
           pass TEXT,
           name TEXT,
           created_at DATETIME DEFAULT CURRENT_TIMESTAMP)`);

  db.run(`CREATE TABLE IF NOT EXISTS messages(
           id INTEGER PRIMARY KEY AUTOINCREMENT,
           userId INTEGER,
           subject TEXT,
           body TEXT,
           sent_at DATETIME DEFAULT CURRENT_TIMESTAMP,
           FOREIGN KEY(userId) REFERENCES users(id))`);
});
module.exports = db;const sqlite3 = require('sqlite3').verbose();
const db = new sqlite3.Database('data.db');

db.serialize(() => {
  db.run(`CREATE TABLE IF NOT EXISTS users(
           id INTEGER PRIMARY KEY AUTOINCREMENT,
           email TEXT UNIQUE,
           pass TEXT,
           name TEXT,
           created_at DATETIME DEFAULT CURRENT_TIMESTAMP)`);

  db.run(`CREATE TABLE IF NOT EXISTS messages(
           id INTEGER PRIMARY KEY AUTOINCREMENT,
           userId INTEGER,const express = require('express');
const bcrypt = require('bcrypt');
const jwt = require('jsonwebtoken');
const db = require('../db');
const router = express.Router();

function generateToken(user) {
  return jwt.sign({id: user.id, email: user.email}, process.env.JWT_SECRET, {expiresIn: '7d'});
}

// REGISTER
router.post('/register', async (req, res) => {
  const {name, email, pass} = req.body;
  const hash = await bcrypt.hash(pass, 10);
  db.run('INSERT INTO users (name,email,pass) VALUES (?,?,?)', [name,email,hash], function(err) {
    if (err) return res.status(400).json({error: 'Email existent'});
    const user = {id: this.lastID, email, name};
    res.json({token: generateToken(user), user});
  });
});

// LOGIN
router.post('/login', (req, res) => {
  const {email, pass} = req.body;
  db.get('SELECT * FROM users WHERE email=?', [email], async (err, user) => {
    if (!user || !await bcrypt.compare(pass, user.pass))
      return res.status(401).json({error: 'Date incorecte'});
    res.json({token: generateToken(user), user: {id: user.id, email: user.email, name: user.name}});
  });
});

module.exports = router;
           subject TEXT,
           body TEXT,
           sent_at DATETIME DEFAULT CURRENT_TIMESTAMP,
           FOREIGN KEY(userId) REFERENCES users(id))`);
});
module.exports = db;
