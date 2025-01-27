

Step 1: Introduction to Auth0 Deploy CLI
Auth0 Deploy CLI is a command-line tool designed to manage Auth0 tenant configurations through export and import operations. With Deploy CLI, OneValley can:
Export an existing tenant configuration as a reusable template.
Modify configurations to suit new tenants.
Automate deployments of configurations to streamline onboarding.
Resources:
Auth0 Deploy CLI Documentation
Auth0 Management API Scopes



Step 2: Setting Up Auth0 Deploy CLI
1. Install Node.js
Ensure that Node.js is installed on your system (version 12 or higher is recommended). Download it from the official Node.js website.
2. Install Auth0 Deploy CLI
Run the following command to install the CLI tool globally on your system:

npm install -g auth0-deploy-cli
3. Create an M2M Application for API Access
In your Auth0 dashboard:
Go to Applications > Applications and click Create Application.
Choose the Machine to Machine application type.
In the application settings, grant the following Management API scopes:
read:tenant_settings
read:clients
create:clients
update:clients
delete:clients
Note the Client ID, Client Secret, and Auth0 domain for use in the configuration.
4. Create a CLI Configuration File
Create a config.json file in your working directory to store the API credentials securely:

{
  "AUTH0_DOMAIN": "<your-domain>.auth0.com",
  "AUTH0_CLIENT_ID": "<your-client-id>",
  "AUTH0_CLIENT_SECRET": "<your-client-secret>"
}

Step 3: Exporting a Tenant Configuration
1. Select the Source Tenant
Choose an existing tenant that serves as a template for others. This tenant should have the base configuration, including:
Applications
APIs
Rules
Branding
2. Run the Export Command
Execute the following command to export the tenant’s configuration:

auth0 deploy export --output_folder ./tenant-config
3. Verify Exported Files
The command generates a directory (tenant-config) containing JSON files for the tenant’s settings. Common files include:
clients.json (Applications)
resource-servers.json (APIs)
branding.json (Branding and customizations)
rules.json (Rules and scripts)
Resources:
Export Tenant Configurations
Step 4: Customizing Configurations for New Tenants
1. Edit Configuration Files
Open the exported JSON files in a text editor or IDE to make tenant-specific changes. Common updates include:
Branding (e.g., logos, friendly names):
{
  "friendly_name": "OneValley - Customer ABC",
  "logo_url": "https://example.com/customer-abc-logo.png"
}
Application Callback URLs:

{
  "callbacks": [
    "https://customer-abc.example.com/callback"
  ]
}
2. Save Customized Files
Save the updated configuration files in a directory specific to the new tenant (e.g., tenant-config-customer-abc).
Resources:
Configuration File Formats

Step 5: Importing Configuration into a New Tenant
1. Prepare the Target Tenant
Ensure the new tenant has been created manually in Auth0.
2. Run the Import Command
Use the Deploy CLI to import the customized configuration files into the target tenant:
auth0 deploy import --input_file ./tenant-config-customer-abc



3. Verify the Imported Configuration
Log in to the Auth0 Dashboard for the new tenant and confirm that:
Applications, APIs, rules, and branding have been correctly imported.
Any customer-specific settings (e.g., callback URLs) are accurate.
Resources:
Import Tenant Configurations

Step 6: Centralizing and Automating Configuration Management
1. Use Version Control for Configurations
Store all tenant configuration files in a version-controlled repository (e.g., GitHub or GitLab). This ensures:
Consistency across deployments.
The ability to track changes and revert to previous versions if needed.
2. Automate with CI/CD
Integrate the deployment process into CI/CD pipelines using tools like:
GitHub Actions: Automate configuration imports with triggers for updates.
Jenkins: Schedule deployments and validate configurations.
Example GitHub Action Workflow:

name: Deploy Configurations
on:
  push:
    branches:
      - main
jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout Code
        uses: actions/checkout@v3
      - name: Install Auth0 Deploy CLI
        run: npm install -g auth0-deploy-cli
      - name: Deploy to Tenant
        run: auth0 deploy import --input_file ./tenant-config-customer-abc

Resources:
Using CI/CD with Auth0 Deploy CLI

