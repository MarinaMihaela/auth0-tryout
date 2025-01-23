async function createPasswordChangeTicket(email, redirectUrl) {
    const domain = 'YOUR_AUTH0_DOMAIN'; // e.g., mydomain.us.auth0.com
    const { getManagementApiToken } = require('./auth0Utils');
    const token = await getManagementApiToken();

    const response = await axios.post(
        `https://${domain}/api/v2/tickets/password-change`,
        {
            user_email: email,
            result_url: redirectUrl, // Optional: Where the user will be redirected after password reset
        },
        {
            headers: {
                Authorization: `Bearer ${token}`,
            },
        }
    );

    return response.data.ticket;
}

module.exports = { getManagementApiToken, createPasswordChangeTicket };
