# UFW (Uncomplicated Firewall) Command Guide

## Introduction

UFW (Uncomplicated Firewall) is a user-friendly front-end for managing iptables firewall rules on Ubuntu and other Debian-based systems. This guide covers essential UFW commands, their usage, and best practices for effective firewall management.

## Table of Contents

1. [Basic UFW Commands](#basic-ufw-commands)
2. [Managing Rules](#managing-rules)
3. [Advanced Usage](#advanced-usage)
4. [Best Practices](#best-practices)
5. [Common Pitfalls](#common-pitfalls)
6. [Further Reading](#further-reading)

## Basic UFW Commands

### Checking Firewall Status

To view the current firewall status with a numbered list of rules:

```bash
ufw status numbered
```

### Enabling and Disabling UFW

Enable UFW:
```bash
ufw enable
```

Disable UFW (preserves rules):
```bash
ufw disable
```

### Resetting UFW

Reset UFW to default configuration:
```bash
ufw reset
```

## Managing Rules

### Allowing Connections

Allow a specific port:
```bash
ufw allow 22
```

Allow a port range:
```bash
ufw allow 1000:2000/tcp
```

Allow from specific IP or subnet:
```bash
ufw allow from 192.168.1.0/24 to any port 80
```

### Denying Connections

Deny a specific port:
```bash
ufw deny 80
```

### Deleting Rules

Delete a rule by number:
```bash
ufw delete 2
```

## Advanced Usage

### Allowing Connections from Specific IP Ranges

Allow connections to multiple ports from an IP range:
```bash
ufw allow from 135.22.65.0/24 to any port 9090 proto tcp
ufw allow from 135.22.65.0/24 to any port 9091 proto tcp
```

### Managing Rules on Remote Hosts

To manage UFW on a remote host (e.g., node01), prefix the command with `ssh user@node01`:

```bash
ssh user@node01 'ufw allow 22'
```

## Best Practices

1. Always start with a default deny policy.
2. Allow only necessary services and ports.
3. Use specific IP addresses or ranges when possible.
4. Regularly review and audit your firewall rules.
5. Test rules thoroughly before applying them in production.
6. Document all changes to firewall rules.

## Common Pitfalls

1. Locking yourself out by denying SSH access.
2. Forgetting to enable the firewall after adding rules.
3. Using overly permissive rules that compromise security.
4. Not considering the order of rules (first matching rule applies).
5. Neglecting to update firewall rules when services change.

## Further Reading

1. [Ubuntu UFW Documentation](https://help.ubuntu.com/community/UFW)
2. [DigitalOcean UFW Essentials Guide](https://www.digitalocean.com/community/tutorials/ufw-essentials-common-firewall-rules-and-commands)
3. [Linux.com UFW Tutorial](https://www.linux.com/training-tutorials/introduction-uncomplicated-firewall-ufw/)
4. [UFW Manual Page](http://manpages.ubuntu.com/manpages/bionic/man8/ufw.8.html)
5. [Arch Linux UFW Wiki](https://wiki.archlinux.org/index.php/Uncomplicated_Firewall)

This guide provides a comprehensive overview of UFW commands and best practices. Always ensure you understand the implications of firewall changes before applying them to your systems.
