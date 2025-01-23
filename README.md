const express = require('express');
const { createPasswordChangeTicketById } = require('../utils/auth0Utils');

const router = express.Router();

/**
 * POST /change-password
 * Generates a password change ticket for the logged-in user using their Auth0 user ID.
 */
router.post('/change-password', async (req, res) => {
    const { sub: userId } = req.oidc.user; // Retrieve the user ID from the authenticated user
    const redirectUrl = 'https://your-app.com/password-changed'; // Optional redirect URL

    try {
        const ticketUrl = await createPasswordChangeTicketById(userId, redirectUrl);
        res.json({ message: 'Password change link generated successfully.', ticketUrl });
    } catch (error) {
        console.error('Error generating password change link:', error.message);
        res.status(500).json({ error: 'Failed to generate password change link.' });
    }
});

module.exports = router;
