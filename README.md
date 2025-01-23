const express = require('express');
const { createPasswordChangeTicketById } = require('../utils/auth0Utils');

const router = express.Router();

/**
 * POST /change-password
 * Generates a password change ticket for the logged-in user using their Auth0 user ID.
 */
router.post('/change-password', async (req, res) => {
    const { sub: userId } = req.oidc.user; // Extract the user ID (sub) from the authenticated user

    try {
        const redirectUrl = `${req.protocol}://${req.get('host')}/password-changed`; // Optional redirect URL
        const ticketUrl = await createPasswordChangeTicketById(userId, redirectUrl);
        res.json({ message: 'Password change link generated successfully.', ticketUrl });
    } catch (error) {
        console.error('Error generating password change link:', error.message);
        res.status(500).json({ error: 'Failed to generate password change link.' });
    }
});

module.exports = router;
