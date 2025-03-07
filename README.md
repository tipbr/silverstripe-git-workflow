# silverstripe-git-workflow

1. Generate an SSH Key Pair
First, let's create an SSH key specifically for GitHub Actions to use for deployment:
bashCopy# Generate a new SSH key (on your local machine)
ssh-keygen -t ed25519 -C "github-actions-deploy" -f ~/.ssh/github_actions_deploy
When prompted for a passphrase, you can leave it empty for automation purposes, but be aware this makes the key more vulnerable if compromised.
2. Add the Public Key to Your Server
You need to add the public key to your server's authorized_keys file:
bashCopy# Copy the public key to your server
ssh-copy-id -i ~/.ssh/github_actions_deploy.pub your-username@your-server.com
Alternatively, manually add it:
bashCopycat ~/.ssh/github_actions_deploy.pub | ssh your-username@your-server.com "mkdir -p ~/.ssh && chmod 700 ~/.ssh && cat >> ~/.ssh/authorized_keys && chmod 600 ~/.ssh/authorized_keys"
3. Test the SSH Connection
Verify the key works:
bashCopyssh -i ~/.ssh/github_actions_deploy your-username@your-server.com
4. Get Your Server's SSH Fingerprint
You need this for the KNOWN_HOSTS secret:
bashCopyssh-keyscan -H your-server.com > known_hosts.txt
5. Set Up GitHub Repository Secrets
Now, let's add the required secrets to your GitHub repository:

Go to your GitHub repository
Navigate to "Settings" → "Secrets and variables" → "Actions"
Click "New repository secret" and add each of these:

SSH_PRIVATE_KEY
bashCopy# Get the private key content
cat ~/.ssh/github_actions_deploy
Copy the entire output (including BEGIN and END lines) into this secret
KNOWN_HOSTS
bashCopy# Get the known hosts content
cat known_hosts.txt
Copy the entire output into this secret
SERVER_USER
Your SSH username (e.g., ubuntu, root, etc.)
SERVER_HOST
Your server's hostname or IP address (e.g., example.com or 123.45.67.89)
PROJECT_PATH
The full path to your SilverStripe project on the server (e.g., /var/www/container/application)



6. Verify Server-Side Git Permissions
Make sure your server can pull from your repository:

If your repository is public, this should work automatically
If your repository is private, you need to set up SSH access from your server to GitHub:
bashCopy# On your server, generate an SSH key if you don't have one
ssh-keygen -t ed25519 -C "server-github-access"

# Display the public key
cat ~/.ssh/id_ed25519.pub
Then add this public key to your GitHub account or as a deploy key in your repository.

7. Test the Git Pull on Server
SSH into your server and test if git pull works:
bashCopycd /path/to/your/project
git pull
If this works without prompting for a password, you're good to go!
8. Verify Your Workflow File
Make sure your .github/workflows/deploy.yml file is correctly set up and committed to your repository.
After completing these steps, your GitHub Actions workflow should be ready to deploy your SilverStripe project automatically whenever you push to the master branch.
