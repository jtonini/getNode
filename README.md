# Compute Node Allocation Script

A script to automatically request compute nodes and configure SSH access for remote development with VS Code.

## What This Script Does

This script helps you:

- Request a compute node from the cluster
- Automatically configure SSH settings to connect to both the head node (`spydur`) and compute node (`spydur-compute`)
- Set up your environment for remote development with VS Code

## Prerequisites

Before using this script, make sure you have:

### 1. Git Installation

**Check if Git is installed:**

```bash
git --version
```

**If Git is not installed:**

- **macOS**: Install Xcode Command Line Tools: `xcode-select --install`
- **Linux**: `sudo apt install git` (Ubuntu/Debian) or `sudo yum install git` (RHEL/CentOS)
- **Windows**: Download from [git-scm.com](https://git-scm.com/)

### 2. SSH Key Setup

You need SSH keys to connect to the cluster.

**Check if you have SSH keys:**

```bash
ls -la ~/.ssh/
```

**If you don't see `id_rsa` and `id_rsa.pub`, create them:**

```bash
ssh-keygen 
```

- Press Enter to accept the default file location

**Add your public key to the cluster:**

```bash
ssh-copy-id your_username@spydur.richmond.edu
```

### 3. Cluster Account Access

- You must have an active account on the cluster
- You should be able to SSH to `spydur.richmond.edu`
- Test this with: `ssh your_username@spydur.richmond.edu`

### 4. VS Code with Remote Development

**Install VS Code:**

- Download from [code.visualstudio.com](https://code.visualstudio.com/)

**Install the Remote Development extension pack:**

1. Open VS Code
2. Go to Extensions (Ctrl+Shift+X)
3. Search for "Remote Development"
4. Install the extension pack by Microsoft

## Installation

1. **Clone this repository:**

```bash
git clone https://github.com/jtonini/getNode.git
cd getNode
```

2. **Make the script executable:**

```bash
chmod +x getNode.sh
```

## Usage

### Running the Script

```bash
./getNode.sh
```

The script will:

1. Request a compute node from the cluster
2. Update your `~/.ssh/config` file with the new node information
3. Create entries for both `spydur` (head node) and `spydur-compute` (allocated compute node)

### Connecting with VS Code

1. **Open VS Code**

2. **Connect to the compute node:**
   - Click the remote connection button in the bottom-left corner (looks like `><`)
   - Select "Connect to Host"
   - Choose `spydur-compute` from the list

3. **Verify your connection:**
   - Open a terminal in VS Code (Ctrl+Shift+`)
   - Run `hostname` to confirm you're on the compute node
   - You should see something like `spdr08` or similar

### Working on the Remote Server

Once connected:

- Browse files using the Explorer panel
- Edit code directly on the compute node
- Run scripts and programs in the integrated terminal
- Install VS Code extensions that will run on the remote server

## Troubleshooting

### Permission Errors

If you see "UNPROTECTED PRIVATE KEY FILE" warnings:

```bash
chmod 600 ~/.ssh/id_rsa
chmod 644 ~/.ssh/id_rsa.pub
```

### Connection Issues

**1. Can't connect to spydur:**
- Verify your cluster account is active
- Check if you can SSH manually: `ssh your_username@spydur.richmond.edu`

**2. Asked for root password:**
- Check your `~/.ssh/config` file
- Make sure the `User` field is set to your username, not `root`

**3. VS Code can't find the host:**
- Make sure you selected `spydur-compute`, not just `spydur`
- Try restarting VS Code and reconnecting

### Script Issues

**1. Script fails to run:**
- Make sure it's executable: `chmod +x getNode.sh`
- Check if you're on the right network/VPN

**2. No compute node allocated:**
- Check cluster availability
- Try running the script again after a few minutes

## Understanding the SSH Configuration

The script creates two entries in your `~/.ssh/config`:

- **`spydur`**: Direct connection to the head node
- **`spydur-compute`**: Connection to your allocated compute node (goes through the head node)

You can manually check your SSH config:

```bash
cat ~/.ssh/config
```

## Best Practices

1. **Always use the compute node** (`spydur-compute`) for development work
2. **Don't run heavy computations on the head node** (`spydur`)
3. **Remember to log out** when you're done to free up resources
4. **Keep your code in version control** (Git) for backup

## Getting Help

1. **Check the troubleshooting section** above
2. **Verify all prerequisites** are met
3. **Test SSH connection manually** before using VS Code
4. **Contact your system administrator** if cluster issues persist

## Additional Resources

- [VS Code Remote Development Documentation](https://code.visualstudio.com/docs/remote/remote-overview)
- [SSH Configuration Guide](https://www.ssh.com/academy/ssh/config)
- [Git Tutorial](https://git-scm.com/docs/gittutorial)

---

**Note**: This script is designed for the Richmond University cluster. Modify the hostnames and paths as needed for other systems.
