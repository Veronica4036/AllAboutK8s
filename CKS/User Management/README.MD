# Linux User Management and SSH Configuration Guide

## Types of Users

In Linux, there are three main types of users:

1. **Root User**: 
   - Also known as the superuser
   - Has UID 0
   - Has unlimited privileges on the system

2. **System Users**: 
   - Created by the system for specific services or applications
   - Usually have UIDs between 1-999
   - Often have `/usr/sbin/nologin` or `/sbin/nologin` as their shell to prevent interactive login

3. **Regular Users**: 
   - Created for individual human users
   - Usually have UIDs 1000 or greater
   - Typically have `/bin/bash` or another interactive shell

## Identifying User Types

You can identify the type of user by examining the `/etc/passwd` file:

```
cat /etc/passwd
```

Each line in this file represents a user account and has seven fields separated by colons:

```
username:password:UID:GID:comment:home_directory:shell
```

- System users often have low UID numbers and `/usr/sbin/nologin` as their shell
- Regular users typically have higher UID numbers and an interactive shell like `/bin/bash`

Example:
```
root:x:0:0:root:/root:/bin/bash
nobody:x:65534:65534:nobody:/nonexistent:/usr/sbin/nologin
ubuntu:x:1000:1000:Ubuntu:/home/ubuntu:/bin/bash
```

## Basic Command Pattern

In Linux, there's a consistent pattern for basic user and group management commands:

```
[user/group] + [add/mod/del]
```

### User Management Commands

| Operation | Command  | Description                |
|-----------|----------|----------------------------|
| Add       | useradd  | Create a new user account  |
| Modify    | usermod  | Modify a user account      |
| Delete    | userdel  | Delete a user account      |

### Group Management Commands

| Operation | Command   | Description               |
|-----------|-----------|---------------------------|
| Add       | groupadd  | Create a new group        |
| Modify    | groupmod  | Modify a group            |
| Delete    | groupdel  | Delete a group            |

## Important Files

1. `/etc/passwd`: Contains user account information
2. `/etc/shadow`: Stores encrypted user passwords
3. `/etc/group`: Contains group information
4. `/etc/sudoers`: Configures sudo access (use `visudo` to edit)
5. `/etc/ssh/sshd_config`: SSH server configuration file
6. `~/.ssh/authorized_keys`: Contains public keys for SSH key-based authentication

### Sudo Configuration
```bash
visudo                        # Safely edit /etc/sudoers file
```

### SSH Management
```bash
ssh-keygen -t rsa             # Generate SSH key pair
ssh-copy-id user@hostname     # Copy SSH public key to remote host
service sshd restart          # Restart SSH service
```

## Key Pointers

1. Always use `visudo` to edit the sudoers file to prevent syntax errors.
2. When changing SSH configuration, keep an active session open while testing the new settings.
3. Disable root login and password authentication in SSH for better security.
4. Use SSH key-based authentication instead of passwords.
5. Regularly audit user accounts and their permissions.
6. Be cautious when granting sudo access, especially without password.

## Common User Management Commands and Their Usage

### 1. User Management:

#### a) Create a user:
```
useradd [options] username
```
Example: `useradd -m -s /bin/bash john`

#### b) Delete a user:
```
userdel [options] username
```
Example: `userdel -r john` (removes home directory too)

#### c) Modify user properties:
```
usermod [options] username
```
Example: `usermod -aG sudo john` (adds john to sudo group)

#### d) Set/change password:
```
passwd username
```

### 2. Group Management:

#### a) Create a group:
```
groupadd groupname
```

#### b) Delete a group:
```
groupdel groupname
```

#### c) Modify group properties:
```
groupmod [options] groupname
```

#### d) Add user to a group:
```
usermod -aG groupname username
```

### 3. User Information:

#### a) View user info:
```
id username
```

#### b) List all users:
```
cat /etc/passwd
```

#### c) List all groups:
```
cat /etc/group
```

#### d) Show groups a user belongs to:
```
groups username
```

### 4. Switching Users:

#### a) Switch to another user:
```
su - username
```

#### b) Execute a command as another user:
```
sudo -u username command
```

### 5. File Permissions:

#### a) Change file owner:
```
chown user:group filename
```

#### b) Change file permissions:
```
chmod permissions filename
```

Certainly! Here's the section in Markdown format:

## Setting or Changing Passwords

### Set or change a user's password:
```
passwd username
```
Example: `passwd david`

When you run this command:
- If you're logged in as root, it will prompt you to enter and confirm a new password for the specified user.
- If you're a regular user, it will first ask for your current password, then prompt you to enter and confirm your new password.

### Additional passwd options:

| Option | Description                                      |
|--------|--------------------------------------------------|
| -l     | Lock a user's password                           |
| -u     | Unlock a user's password                         |
| -d     | Delete a user's password (make it empty)         |
| -e     | Expire a user's password (force change on next login) |
| -S     | Display account status information               |

Examples:
```
sudo passwd -l david     # Lock david's account
sudo passwd -u david     # Unlock david's account
sudo passwd -e david     # Force david to change password on next login
sudo passwd -S david     # Display password status for david
```

Note: Changing passwords for other users typically requires root or sudo privileges.

## Common Arguments for User Management Commands

### useradd

| Argument | Description                                      | Example                            |
|----------|--------------------------------------------------|------------------------------------|
| -d       | Specify home directory                           | `-d /opt/username`                 |
| -s       | Specify login shell                              | `-s /bin/bash`                     |
| -G       | Add to supplementary groups                      | `-G admin,developers`              |
| -u       | Specify user ID                                  | `-u 2328`                          |
| -m       | Create home directory if it doesn't exist        | `-m`                               |
| -c       | Add a comment (usually full name)                | `-c "John Doe"`                    |
| -e       | Set account expiration date                      | `-e 2023-12-31`                    |

Example:
```
useradd -d /opt/sam -s /bin/bash -G admin -u 2328 -m -c "Sam Smith" sam
```

### usermod

| Argument | Description                                      | Example                            |
|----------|--------------------------------------------------|------------------------------------|
| -s       | Change login shell                               | `-s /usr/sbin/nologin`             |
| -G       | Replace supplementary groups                     | `-G admin,developers`              |
| -aG      | Add to supplementary groups without replacing    | `-aG docker`                       |
| -L       | Lock user account                                | `-L`                               |
| -U       | Unlock user account                              | `-U`                               |
| -e       | Set account expiration date                      | `-e 2023-12-31`                    |
| -d       | Change home directory                            | `-d /new/home/dir`                 |

Examples:
```
usermod -s /usr/sbin/nologin himanshi
usermod -aG docker username
```

Note: Many of these arguments are common between `useradd` and `usermod`. The main difference is that `useradd` is for creating new users, while `usermod` is for modifying existing users.

## Common Use Cases:

1. Creating a new user account for a new employee.
2. Adding a user to the sudo group to grant administrative privileges.
3. Changing a user's shell to restrict access (e.g., to /usr/sbin/nologin).
4. Creating a system user for running a service.
5. Modifying file permissions to allow or restrict access.
6. Changing ownership of files after user changes.
7. Viewing which groups a user belongs to for troubleshooting access issues.
8. Deleting a user account when an employee leaves.
9. Creating a new group for collaborative work and adding users to it.
10. Using sudo to perform administrative tasks without logging in as root.

### Adding a User to Sudoers
Add this line to `/etc/sudoers` using `visudo`:
```
username ALL=(ALL:ALL) ALL
```
For passwordless sudo:
```
username ALL=(ALL:ALL) NOPASSWD: ALL
```

### Securing SSH
Edit `/etc/ssh/sshd_config`:
```
PermitRootLogin no
PasswordAuthentication no
```
Then restart SSH: `service sshd restart`

### Copying SSH Key
```bash
ssh-copy-id -i ~/.ssh/id_rsa.pub user@remote_host
```

### Checking User's Groups
```bash
groups username
```

### Viewing Sudo Permissions
```bash
sudo -l -U username
```

Remember to always test changes in a safe environment before applying them to production systems.
```

Remember, many of these commands require root or sudo privileges to execute. Always be cautious when modifying user accounts and permissions, as incorrect changes can affect system security and functionality.

This comprehensive guide covers the most important aspects of user management and SSH configuration in Linux. It includes the crucial files to be aware of, commonly used commands, key security pointers, and step-by-step instructions for common tasks. This information should provide a solid foundation for managing users and SSH on Linux systems.
