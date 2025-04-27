# sitehost-git-workflows

These are simple example deployment workflows for projects using GitHub Actions. They

Examples included:

- Silverstripe

## Installation

Copy the relevant deploy.yml file to your .github/workflows folder in your SilverStripe project.

## Configuration

This guide explains how to configure two SSH keys for your deployment process:

1. An SSH key for your GitHub Action to log in to your remote server.
2. A deploy key for the remote server to authenticate with GitHub and pull the code.

### 1. Setting Up SSH Access from GitHub Action to the Remote Server

A. **Generate an SSH Key Pair**

**On your local machine**, open a terminal and run:

```bash
ssh-keygen -t ed25519 -C "github-action@yourdomain.com" -f github_action_key
```

When prompted for a passphrase, you can leave it empty for automation purposes.

B. **Install the Public Key on Your Remote Server**

1.  Copy the public key from the generated file (github_action_key.pub).
2.  SSH into your remote server as your target user.
3.  Ensure the .ssh dir exists:

```bash
mkdir -p ~/.ssh
```

5.  Append the public key to the authorized_keys file:

```bash
echo "paste-your-public-key-here" >> ~/.ssh/authorized_keys
```

4.  Ensure proper permissions:

```bash
chmod 700 ~/.ssh
chmod 600 ~/.ssh/authorized_keys
```

C. **Configure Your GitHub Repository to Use the Private Key**

1.  In your GitHub repository, navigate to Settings > Secrets and variables > Actions.
2.  Add a new repository secret named SSH_PRIVATE_KEY and paste the contents of your private key file (github_action_key).

### 2. Setting Up a Deploy Key on the Remote Server for GitHub Pulls

A. **Generate a Deploy Key on the Remote Server**

1.  SSH into your remote server if not already connected.
2.  Generate a new SSH key pair for the deploy key:

```bash
ssh-keygen -t rsa -b 4096 -C "deploy-key-for-your-repo" -f ~/.ssh/deploy_key
```

B. **Add the Deploy Key to Your GitHub Repository**

1. Copy the public key from the remote server:

```bash
cat ~/.ssh/deploy_key.pub
```

2. In your GitHub repository, go to Settings > Deploy keys.
3. Click “Add deploy key”, provide a title (e.g., "Remote Server Deploy Key"), and paste the public key.
4. For pulling code, read-only access is sufficient. Only grant write access if necessary.

C. **Configure SSH on the Remote Server to Use the Deploy Key for GitHub**

1. Edit (or create) the SSH config file on the remote server (~/.ssh/config):

```bash
Host github.com
    HostName github.com
    User git
    IdentityFile ~/.ssh/deploy_key
    IdentitiesOnly yes
```

2. Ensure the file permissions are correct:

```bash
chmod 600 ~/.ssh/deploy_key
chmod 600 ~/.ssh/config
chmod 700 ~/.ssh
```

3. Test the SSH connection to GitHub:

```bash
ssh -T git@github.com
```

4. When prompted, add github to the list of known hosts.
