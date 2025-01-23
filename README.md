app.post('/api/change-password', async (req, res) => {
    const { email } = req.body;

    if (!email) {
        return res.status(400).json({ error: 'Email is required' });
    }

    try {
        // Trigger the password reset email
        const response = await axios.post(`https://${AUTH0_DOMAIN}/dbconnections/change_password`, {
            client_id: AUTH0_CLIENT_ID,
            email: email,
            connection: 'Username-Password-Authentication', // Replace with your database connection name if different
        });

        res.status(200).json({ message: 'Password reset email sent successfully' });
    } catch (error) {
        console.error('Error sending password reset email:', error.response?.data || error.message);
        res.status(500).json({ error: 'Failed to send password reset email' });
    }
});
