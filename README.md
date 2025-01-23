app.post('/change-password', async (req, res) => {
  if (!req.oidc || !req.oidc.user) {
    return res.status(401).json({ error: 'User is not authenticated.' });
  }

  const { sub: userId } = req.oidc.user; // Obține ID-ul utilizatorului logat
  const redirectUrl = `${req.protocol}://${req.get('host')}/password-changed`; // Opțional: URL pentru redirecționare

  try {
    // Obține access token de la Auth0
    const tokenResponse = await axios.post(
      `https://${process.env.AUTH0_DOMAIN}/oauth/token`,
      new URLSearchParams({
        grant_type: 'client_credentials',
        client_id: process.env.AUTH0_CLIENT_ID,
        client_secret: process.env.AUTH0_CLIENT_SECRET,
        audience: `https://${process.env.AUTH0_DOMAIN}/api/v2/`,
      }),
      {
        headers: { 'Content-Type': 'application/x-www-form-urlencoded' },
      }
    );
    const accessToken = tokenResponse.data.access_token;

    // Creează ticket-ul pentru resetarea parolei
    const ticketResponse = await axios.post(
      `https://${process.env.AUTH0_DOMAIN}/api/v2/tickets/password-change`,
      {
        user_id: userId,
        result_url: redirectUrl,
      },
      {
        headers: {
          Authorization: `Bearer ${accessToken}`,
          'Content-Type': 'application/json',
        },
      }
    );

    res.json({ message: 'Password change link generated successfully.', ticketUrl: ticketResponse.data.ticket });
  } catch (error) {
    console.error('Error generating password change link:', error.response?.data || error.message);
    res.status(500).json({ error: 'Failed to generate password change link.' });
  }
});
