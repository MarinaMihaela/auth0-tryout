const dotenv = require('dotenv');
const express = require('express');
const http = require('http');
const logger = require('morgan');
const path = require('path');
const router = require('./routes/index');
const { auth } = require('express-openid-connect');
const passwordRoutes = require('./routes/password')

dotenv.load();

const app = express();

app.set('views', path.join(__dirname, 'views'));
app.set('view engine', 'ejs');

app.use(logger('dev'));
app.use(express.static(path.join(__dirname, 'public')));
app.use(express.json());

const config = {
  authRequired: false,
  auth0Logout: true
};

const port = process.env.PORT || 3000;
if (!config.baseURL && !process.env.BASE_URL && process.env.PORT && process.env.NODE_ENV !== 'production') {
  config.baseURL = `http://localhost:${port}`;
}

app.use(auth(config));

// Middleware to make the `user` object available for all views
app.use(function (req, res, next) {
  res.locals.user = req.oidc.user;
  next();
});

app.use('/', router);

// Catch 404 and forward to error handler
app.use(function (req, res, next) {
  const err = new Error('Not Found');
  err.status = 404;
  next(err);
});

// Error handlers
app.use(function (err, req, res, next) {
  res.status(err.status || 500);
  res.render('error', {
    message: err.message,
    error: process.env.NODE_ENV !== 'production' ? err : {}
  });
});

http.createServer(app)
  .listen(port, () => {
    console.log(`Listening on ${config.baseURL}`);
  });




<%- include('partials/header') -%>

<h1 class="text-4xl mb-4">Welcome <%= user.nickname %></h1>

<% if (user.picture) { %>
  <img class="block py-3" src="<%= user.picture %>" width="300">
<% } %>

<p class="py-3">
  This is the content of <code class="bg-gray-200">req.user</code>.<br>
  <strong>Note:</strong> <code class="bg-gray-200">_raw</code> and <code class="bg-gray-200">_json</code> properties have been omitted.
</p>

<pre class="block bg-gray-300 p-4 text-sm overflow-scroll"><%= userProfile %></pre>

<div class="py-4">
  <button id="changePasswordBtn" class="px-4 py-2 bg-blue-500 text-white rounded hover:bg-blue-700">
    Change Password
  </button>
</div>

<script>
  document.getElementById('changePasswordBtn').addEventListener('click', async () => {
    try {
      const response = await fetch('/api/change-password', { method: 'POST' });
      const data = await response.json();
      if (response.ok) {
        alert(data.message);
      } else {
        alert('Error: ' + data.error);
      }
    } catch (err) {
      console.error('Error:', err);
      alert('An unexpected error occurred.');
    }
  });
</script>

<%- include('partials/footer') -%>

const express = require('express');
const { createPasswordChangeTicketById } = require('./utils/auth0Utils');

const router = express.Router();

/**
 * POST /change-password
 * Generates a password change ticket for the logged-in user using their Auth0 user ID.
 */
router.post('/change-password', async (req, res) => {
    const { sub: userId } = req.oidc.user; // Retrieve the user ID from the authenticated user
   // const redirectUrl = 'https://your-app.com/password-changed'; // Optional redirect URL

    try {
        const ticketUrl = await createPasswordChangeTicketById(userId, redirectUrl);
        res.json({ message: 'Password change link generated successfully.', ticketUrl });
    } catch (error) {
        console.error('Error generating password change link:', error.message);
        res.status(500).json({ error: 'Failed to generate password change link.' });
    }
});

module.exports = router;



const axios = require('axios');
const { URLSearchParams } = require('url');
require('dotenv').config();

/**
 * Fetches a Management API access token for production using form-urlencoded data.
 * @returns {Promise<string>} The Management API access token.
 */
async function getManagementApiToken() {
    const options = {
        method: 'POST',
        url: `https://${process.env.AUTH0_DOMAIN}/oauth/token`,
        headers: { 'content-type': 'application/x-www-form-urlencoded' },
        data: new URLSearchParams({
            grant_type: 'client_credentials',
            client_id: 'j1gCTgaHT5v0cKPSFC4f2TZshA9z0pQR',
            client_secret:'{cJP3ydy4IbikZ0pzH6TNRM0H6hF8TJoPE0Wqk3K5pKfEbsL3XWbBiY25I2pCw7rw}',
            audience:'https://test-api/',
        }),
    };

    try {
        const response = await axios.request(options);
        return response.data.access_token; // Return the access token
    } catch (error) {
        console.error('Error fetching Management API token:', error.response?.data || error.message);
        throw new Error('Failed to fetch Management API token.');
    }
}

/**
 * Creates a password change ticket for a user.
 * @param {string} email - The email of the user requesting the password change.
 * @param {string} [redirectUrl] - Optional: A URL to redirect the user after password reset.
 * @returns {Promise<string>} The password change ticket URL.
 */
async function createPasswordChangeTicket(email, redirectUrl) {
    const token = await getManagementApiToken();

    const options = {
        method: 'POST',
        url: `https://${process.env.AUTH0_DOMAIN}/api/v2/tickets/password-change`,
        headers: {
            Authorization: `Bearer ${token}`,
            'Content-Type': 'application/json',
        },
        data: {
            user_id: userId,
            result_url: redirectUrl || null,
        },
    };

    try {
        const response = await axios.request(options);
        return response.data.ticket; // Return the password change ticket URL
    } catch (error) {
        console.error('Error creating password change ticket:', error.response?.data || error.message);
        throw new Error('Failed to create password change ticket.');
    }
}

module.exports = {
    getManagementApiToken,
    createPasswordChangeTicket,
};
