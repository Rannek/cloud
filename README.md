
# Encrypted Cloud Storage on Arch Linux with SSHFS and EncFS

This guide covers setting up a secure, encrypted cloud storage system on Arch Linux, using SSHFS for remote file system mounting and EncFS for encryption, ensuring your files are encrypted on your VPS.

## Prerequisites

- A VPS with SSH access.
- Arch Linux on your local machine.
- Basic command-line knowledge.

## Installation

### Install SSHFS and EncFS

1. Update your system packages:

   ```sh
   sudo pacman -Syu
   ```

2. Install SSHFS and EncFS:

   ```sh
   sudo pacman -S sshfs encfs
   ```

## Setup

### Setting Up SSHFS

1. Create a local mount point for your VPS:

   ```sh
   mkdir /mnt/Cloud
   ```

2. Mount the VPS directory using SSHFS:

   ```sh
   sshfs user@vps_ip:/remote_directory /mnt/Cloud
   ```
   
   Replace `user` with your VPS username, `vps_ip` with your VPS IP address, and `/remote_directory` with the path to the directory on your VPS.

### Setting Up EncFS

1. Create a directory for the EncFS decrypted view:

   ```sh
   mkdir /home/$USER/EncryptedCloud
   ```

2. Initialize EncFS on the mounted directory:

   ```sh
   encfs /mnt/Cloud /home/$USER/EncryptedCloud
   ```

   Follow the prompts to configure EncFS. Choose a password when prompted.

## Usage

After setting up, you'll have two key directories:

- **Encrypted Directory (`/mnt/Cloud`)**: This is where your files are stored in encrypted form, synced to your VPS.
- **Decrypted View (`/home/$USER/EncryptedCloud`)**: This is where you interact with your files in their decrypted state.

To work with your files, simply access them through the decrypted view. EncFS automatically encrypts these files and stores them in the encrypted directory, which is synced to your VPS.

## Automating the Mount Process

Create a script to automate the mounting process. Here's a basic example:

```sh
#!/bin/bash

# Mount the remote filesystem via SSHFS
sshfs -o allow_other user@vps_ip:/remote_directory /mnt/Cloud

# Check if SSHFS mount was successful
if mountpoint -q /mnt/Cloud; then
    echo "SSHFS mount successful. Proceeding to mount EncFS."

    # Prompt for EncFS password
    encfs /mnt/Cloud /home/$USER/EncryptedCloud
else
    echo "SSHFS mount failed. Please check your settings."
fi
```

Replace placeholders with your actual data. Make the script executable:

```sh
chmod +x mount_encrypted.sh
```

## Security Considerations

- **Passwords**: Avoid hardcoding passwords in scripts. Consider secure password management practices.
- **SSH Keys**: Use SSH keys instead of passwords for SSHFS for added security.
- **Permissions**: Ensure correct permissions for your `.encfs6.xml` configuration file and mounted directories.

## Conclusion

You now have a secure, encrypted filesystem synced with your VPS, ensuring data privacy and security. Remember, the security of your encrypted filesystem is only as strong as your password and SSH key management practices.
