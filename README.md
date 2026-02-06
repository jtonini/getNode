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
ssh-keygen -t rsa -b 4096
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

### Script Options

To see all available options and parameters:
```bash
./getNode.sh --help
```

**Available options:**
- `-u, --user`: Cluster username (default: your local username)
- `-p, --partition`: SLURM partition (default: basic)
- `-t, --time`: Time limit in HH:MM:SS format (default: 4:00:00)
- `-c, --cpus`: Number of CPU cores (default: 4)
- `-m, --memory`: Memory amount, e.g., 16G, 32G (default: 16G)
- `-g, --gpus`: Number of GPUs (default: 1 for GPU partitions)
- `-i, --interactive`: Start interactive terminal session instead of remote development allocation
- `--cluster`: Cluster hostname (default: spydur)

**Common usage examples:**
```bash
# Basic usage - all defaults (spydur cluster, basic partition, 4 hours, 4 CPUs, 16G RAM)
./getNode.sh

# Specify user (if different from local username)
./getNode.sh -u jtonini

# Request ML partition with GPU for machine learning
./getNode.sh -p ML

# Request ML partition for specific user
./getNode.sh -u jtonini -p ML

# Request more resources and longer time on large partition
./getNode.sh -p large -t 8:00:00 -c 16 -m 64G

# Request multiple GPUs for deep learning with extended time
./getNode.sh -p ML -t 12:00:00 -g 2 -c 8 -m 128G

# Start an interactive terminal session for quick testing (NOT for VSCode)
./getNode.sh -i -t 1:00:00

# Interactive session on GPU partition
./getNode.sh -i -p ML -t 2:00:00

# Use faculty condo resources with specific user
./getNode.sh -u faculty_user -p yang1 -t 24:00:00 -c 32 -m 256G

# Connect to a different cluster
./getNode.sh --cluster spiderweb -p basic

# Complete example: different cluster, user, partition, and resources
./getNode.sh --cluster arachne -u researcher -p sci -t 16:00:00 -g 4 -c 16 -m 128G
```

**Available Partitions:**
- **CPU Partitions**: `basic` (default), `medium`, `large`
- **GPU Partitions**: `ML` (A100 GPUs), `sci` (A40 GPUs)
- **Faculty Condos**: `yang1`, `yang2`, `yangnolin`

**Available Clusters at University of Richmond:**
- `spydur`: General research cluster (default)
- `spiderweb`: Teaching-focused cluster
- `arachne`: GPU-intensive research cluster

**Note**: The default `basic` partition is designed for teaching and lightweight computational tasks. Research users should select appropriate partitions based on their computational needs. The script can be adapted for other SLURM-based HPC systems by specifying the `--cluster` parameter.
### Connecting with VS Code

**Important**: Do NOT use the `-i` (interactive) flag for VS Code. Use the default allocation mode.

1. **Run getNode to allocate resources:**
```bash
# Basic allocation for VSCode work
./getNode.sh

# Or specify your requirements (examples)
./getNode.sh -p ML -t 8:00:00 -c 8 -m 32G
./getNode.sh --cluster spiderweb -p medium -t 4:00:00
```

2. **Open VS Code**

3. **Connect to the compute node:**
   - Click the remote connection button (`><`) in the bottom-left corner
   - Select "Connect to Host"
   - From the dropdown menu, choose `{cluster}-compute`:
     - `spydur-compute` if you used default or `--cluster spydur`
     - `spiderweb-compute` if you used `--cluster spiderweb`
     - `arachne-compute` if you used `--cluster arachne`
   - VS Code will open a new window connected to your allocated compute node

4. **Verify your connection:**
   - Open a terminal in VS Code (Ctrl+Shift+\` or Terminal → New Terminal)
   - Run `hostname` to confirm you're on the compute node
   - You should see something like `spdr08` or similar (not the login node name)

5. **Work on your code:**
   - Browse and edit files directly on the compute node
   - Run code and scripts in the integrated terminal
   - Install VS Code extensions on the remote server as needed

6. **When done working:**
   - Press Ctrl+C in the terminal where you ran `getNode.sh` to release the allocation
   - Or let it expire automatically when the time limit is reached

**Important Notes:**
- **Do NOT use the `-i` flag for VS Code** - that's for interactive terminal sessions only
- The allocation runs in the background while you work in VS Code
- You can close and reopen VS Code and reconnect to `{cluster}-compute` as long as the allocation is still active
- The SSH config entry is automatically updated each time you run `getNode.sh`
- Each time you run `getNode.sh`, you get a fresh compute node allocation

**Session Types:**
- **Default (no `-i` flag)**: Creates persistent allocation for remote development (VS Code, PyCharm, etc.)
- **Interactive (`-i` flag)**: Creates interactive terminal session on compute node (NOT for VS Code)
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

**Note**: This script is designed for the University of Richmond cluster. Modify the hostnames and paths as needed for other systems.
