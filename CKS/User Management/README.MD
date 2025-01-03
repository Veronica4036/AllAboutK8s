```markdown
# Linux User Management Guide

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

Remember, many of these commands require root or sudo privileges to execute. Always be cautious when modifying user accounts and permissions, as incorrect changes can affect system security and functionality.
```
