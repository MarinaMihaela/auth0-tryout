const axios = require('axios');

async function getManagementApiToken() {
    const domain = 'YOUR_AUTH0_DOMAIN'; // e.g., mydomain.us.auth0.com
    const clientId = 'YOUR_CLIENT_ID';
    const clientSecret = 'YOUR_CLIENT_SECRET';
    const audience = `https://${domain}/api/v2/`;

    const response = await axios.post(`https://${domain}/oauth/token`, {
        client_id: clientId,
        client_secret: clientSecret,
        audience,
        grant_type: 'client_credentials',
    });

    return response.data.access_token;
}

module.exports = { getManagementApiToken };
