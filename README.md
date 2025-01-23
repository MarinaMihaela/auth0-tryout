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
            client_id: process.env.AUTH0_CLIENT_ID,
            client_secret: process.env.AUTH0_CLIENT_SECRET,
            audience: process.env.AUTH0_AUDIENCE,
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
 * Creates a password change ticket for a user by user ID.
 * @param {string} userId - The Auth0 user ID (e.g., "auth0|123456789").
 * @param {string} [redirectUrl] - Optional: A URL to redirect the user after password reset.
 * @returns {Promise<string>} The password change ticket URL.
 */
async function createPasswordChangeTicketById(userId, redirectUrl) {
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
    createPasswordChangeTicketById,
};
