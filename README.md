
/**
 * Trigger MFA for users with specific email domains.
 * @param {Event} event - Details about the user and the context of the login.
 * @param {API} api - Interface for modifying the login transaction.
 */
exports.onExecutePostLogin = async (event, api) => {
  // List of domains requiring MFA
  const domainsRequiringMFA = ['atko.email', 'atko.com'];

  // Extract the domain from the user's email address
  const emailDomain = event.user.email.split('@')[1];

  // Check if the domain requires MFA
  if (domainsRequiringMFA.includes(emailDomain)) {
    // Enforce MFA with OTP (One-Time Password)
    api.multifactor.enable("any");

    // Require specific factor: OTP (Time-based One-Time Password)
    api.authentication.challengeWithAny([{type: 'otp'}]);

    // Ensure the user is enrolled in MFA
api.authentication.enrollWith({type: 'otp'})
  }
};
