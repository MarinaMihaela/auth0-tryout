Dear Team,

I hope this email finds you well. Thank you for attending our second build session on Friday, 24th January. Below is a summary of the key points we discussed during the session.

We began by working with a Node.js Auth0 sample application, where I demonstrated how to make a call to the POST /dbconnections/change_password endpoint in the Authentication API. This example showcased how users can independently update their passwords through a secure process.

Next, we reviewed how to securely make calls to the Management API from your own applications. To do this, you will need to create a Machine-to-Machine (M2M) Application, authorize it for the Auth0 Management API, and specify the required scopes/permissions (e.g., read:users, update:users) based on the endpoints you plan to call. You will need an access token for these API calls to be authorized, which involves making a POST request to https://YOUR_DOMAIN/oauth/token. You can find more details on this process in the Auth0 documentation here: Get Management API Access Tokens for Production.

We also explored Multi-Factor Authentication (MFA) and how it appears to end users during login. I demonstrated how you can customize MFA using Auth0 Actions. Specifically, I created a post-login action to enforce MFA only for users with a specific email domain, showing how you can tailor MFA policies to meet your security requirements.

We discussed how custom databases work with Auth0. I explained how you can integrate your own database (e.g., SQL Server) using custom database scripts, which can handle tasks such as:

Authenticating users (Login Script)
Creating users (Create Script)
Retrieving user information (Get User Script)
Updating passwords (Change Password Script)
Verifying user accounts (Verify Script)
Removing user accounts (Delete Script)
Before starting the implementation, I recommend reviewing the Custom Database Connection Environment Checklist.

Additionally, we discussed security concerns related to custom database integration. It’s crucial to follow best practices to ensure safe interaction with your database. I recommend reviewing this section of the Auth0 documentation for detailed guidance: Custom Database Connection Security.

Lastly, we covered the use of the Auth0 Deploy CLI tool to facilitate deployments from a development tenant to staging or production tenants. For further details, I’ve attached a document to this email that provides step-by-step instructions on using the Deploy CLI.

I look forward to our next build session and continuing to support your implementation process. Please don’t hesitate to reach out if you have any questions or need clarification on anything we discussed. I’ll do my best to provide answers as quickly as possible.

Best regards,
[Your Name]
