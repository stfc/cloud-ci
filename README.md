# cloud-ci

STFC Cloud Github App README
This Repo Details info on our GitHub App for cloud-specific CI/CD Jobs 

# Overview

The STFC Cloud make use of a Github App for authenticating our CI/CD pipelines instead of Personal Access Tokens (PATs).

# Why a GitHub App?

Using a GitHub App provides several advantages over PATs:

- Tokens generated from GitHub Apps are scoped to specific repositories and permissions
  - This one is only allowed to work with cloud repos
- Not tied to an individual user account (acts as a service account - though only organization owners can edit them)
- Tokens expire after 1 hour, reducing security risks of leaked tokens. 
- We can set fine-grained permissions: Only grant the exact permissions needed

# How To Use It

1. Create a GitHub App for the stfc organization (this has been done already for STFC Cloud)
  - set appropriate permissions. For an authentication app, we don't need callbacks or webhooks
  - https://docs.github.com/en/apps/creating-github-apps/registering-a-github-app/registering-a-github-app
  
2. Install the Github App on the repos you want

3. Store the App's ID (or Client ID) in your repository environment variables (example: APP_ID).
  - https://docs.github.com/actions/learn-github-actions/variables#defining-configuration-variables-for-multiple-workflows
    
4. Store the App's private key in your repository secrets (example: PRIVATE_KEY).
  - https://docs.github.com/actions/security-guides/encrypted-secrets?tool=webui#creating-encrypted-secrets-for-a-repository

5. For Your CI/CD Job use this action to generate "installation tokens" on the fly:
 - https://github.com/marketplace/actions/create-github-app-token

# How It Works:

During CI job execution, the app generates a short-lived installation token, this token is used to authenticate API requests and git operations. 
The token automatically expires after 1 hour

# Use cases

Our CI jobs use the GitHub App to:

- Create PRs, due to this limitation: https://github.com/orgs/community/discussions/65321. We require using Tokens to make sure our CI/CD flows work properly 

But we can also use this to:

- Push commits and tags
- Create and update pull requests
- Modify repository contents
- Perform other automated repository operations

# Security Notes

- The App generates a private key which must be shared outside of select repos. Also in Keeper as well.
- Generating an App requires stfc organization permissions.
- Make sure to select specific repos to prevent unintended access to other repos (i.e. those made by teams outside cloud).
- Generate the token just before usage - so the token stays valid.
