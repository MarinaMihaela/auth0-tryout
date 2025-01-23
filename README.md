const dotenv = require('dotenv');
const express = require('express');
const http = require('http');
const logger = require('morgan');
const path = require('path');
const router = require('./routes/index');
const passwordRoutes = require('./routes/password');
const { auth } = require('express-openid-connect');

dotenv.config();

const app = express();

app.set('views', path.join(__dirname, 'views'));
app.set('view engine', 'ejs');

app.use(logger('dev'));
app.use(express.static(path.join(__dirname, 'public')));
app.use(express.json());

const config = {
    authRequired: false,
    auth0Logout: true,
    secret: process.env.AUTH0_SECRET,
    baseURL: process.env.BASE_URL || `http://localhost:${process.env.PORT || 3000}`,
    clientID: process.env.AUTH0_CLIENT_ID,
    issuerBaseURL: `https://${process.env.AUTH0_DOMAIN}`,
};

app.use(auth(config));

// Middleware to make the `user` object available in views
app.use((req, res, next) => {
    res.locals.user = req.oidc.user;
    next();
});

app.use('/', router);
app.use('/api', passwordRoutes); // Register the password routes with `/api` prefix

// Catch 404 errors
app.use((req, res, next) => {
    const err = new Error('Not Found');
    err.status = 404;
    next(err);
});

// Error handler
app.use((err, req, res, next) => {
    res.status(err.status || 500);
    res.render('error', {
        message: err.message,
        error: process.env.NODE_ENV !== 'production' ? err : {},
    });
});

const port = process.env.PORT || 3000;
http.createServer(app).listen(port, () => {
    console.log(`Listening on ${config.baseURL}`);
});
