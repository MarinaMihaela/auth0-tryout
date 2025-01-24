// Auth0 Action: Trigger MFA Based on Email Domain
// To use this Action:
// 1. Go to the Actions section of the Auth0 Dashboard.
// 2. Create a new Action in the Login flow.
// 3. Paste this code and configure it as needed.

/**
 * Trigger MFA for users with specific email domains.
 * @param {Event} event - Details about the user and the context of the login.
 * @param {API} api - Interface for modifying the login transaction.
 */
exports.onExecutePostLogin = async (event, api) => {
  // List of domains requiring MFA
  const domainsRequiringMFA = ['example.com', 'secure.com'];

  // Extract the domain from the user's email address
  const emailDomain = event.user.email.split('@')[1];

  // Check if the domain requires MFA
  if (domainsRequiringMFA.includes(emailDomain)) {
    // Check if the user is already enrolled in MFA
    const isMfaEnrolled = event.user.multifactor && event.user.multifactor.length > 0;

    if (isMfaEnrolled) {
      // Trigger MFA challenge using OTP
      api.authentication.challengeWithAny([{ type: 'otp' }]);
    } else {
      // Enroll the user in MFA using OTP
      api.authentication.enrollWith({ type: 'otp' });
    }
  }
};
