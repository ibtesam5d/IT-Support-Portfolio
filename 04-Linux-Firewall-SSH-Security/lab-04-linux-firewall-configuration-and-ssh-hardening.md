# Lab 4: Linux Firewall Configuration and SSH Hardening

## 1. Objective

The objective of this lab was to learn how to configure a Linux firewall, restrict inbound network access, understand TCP connectivity, and harden an SSH server using key-based authentication.

The lab focused on applying practical security controls and verifying their effectiveness through testing from a Windows client.

## 2. Lab Environment

- Host Operating System: Windows 10 22H2 (Legacy LAB Environment with ESU)
- Virtualization software: VMware Workstation Player
- Virtual Machine: Linux Mint
- Network Mode: VMware NAT
- Firewall: UFW
- Remote Access Service: OpenSSH
- SSH Port: TCP 22
- SSH Client: Windows OpenSSH Client
- Authentication method: Ed25519 public-key-authentication

## 3. Initial Firewall and SSH Assessment

First, I inspected the existing firewall configuration:

```
sudo ufw status verbose
```

The firewall was initially inactive.

I then inspected listening TCP ports:

```
sudo ss -tlnp
```

This confirmed that the SSH service was listening on TCP port 22.

I also checked the available UFW application profiles:

```
sudo ufw app list
```

The OpenSSH profile was available.

## 4. Firewall Configuration Using UFW

I configured UFW to allow SSH and enabled the firewall:

```
sudo ufw allow OpenSSH
sudo ufw enable
```

The initial firewall configuration allowed SSH from anywhere.

After recognizing that this was broader than necessary and posed security threat, I restricted SSH access to the Windows laptop's VMware network adapter IP.

I added a source-specific rule:

```
sudo ufw allow from WINDOWS_IP to any port 22 proto tcp
```

I inspected the numbered firewall rules:

```
sudo ufw status numbered
```

I then removed the broad IPv4 and IPv6 OpenSSH permissions while retaining the restricted IPv4 rule.

The final firewall configuration was verified using:

```
sudo ufw status verbose
```

The resulting configuration was:

- Firewall active.
- Default incoming policy: deny.
- Default outgoing policy: allow.
- SSH permitted only from the specified Windows VMnet8 IP.
- No broad SSH permissions for IPv4 or IPv6.

## 5. Network Connectivity and Firewall Testing

I used Windows PowerShell's Test-NetConnection utility to test connectivity to the Linux VM.

### SSH connectivity test

```
Test-NetConnection Linux-VM-IP -Port 22

TcpTestSucceeded: True
```

This confirmed that SSH remained accessible from the authorized Windows client after restricting the firewall rule.

### HTTP port 80 test

```
Test-NetConnection Linux-VM-IP -Port 80

TcpTestSucceeded: False
```

Ping succeeded, but the TCP connection failed.

I inspected listening TCP ports and confirmed that no service was listening on port 80.

This demonstrated that network reachability does not necessarily mean an application is accepting connections on a particular port.

### Temporary Python HTTP server

To investigate the relationship between listening services and firewall permissions, I created a temporary HTTP server:

```
python3 -m http.server 8000 --bind 0.0.0.0
```

I verified that the service was listening:

```
sudo ss -tlnp
```

The output showed:

```
LISTEN  0  5  0.0.0.0:8000  0.0.0.0:*
```

I tested TCP port 8000 from Windows before adding a firewall rule. The connection failed even though the Python server was listening.

I then added a restricted firewall permission:

```
sudo ufw allow from WINDOWS-IP to any port 8000 proto tcp
```

Repeating the Windows connectivity test produced:

`TcpTestSucceeded: True`

After testing, I stopped the temporary Python server using `Ctrl+C` and removed the temporary firewall rule.

This exercise demonstrated the difference between a running service, firewall permissions, and network reachability.

## 6. SSH Key-Based Authentication

I investigated the SSH server's effective configuration:

```
sudo sshd -T | grep -E 'permitrootlogin|passwordauthentication|pubkeyauthentication'
```

The initial configuration showed:

```
permitrootlogin without-password
passwordauthentication yes
pubkeyauthentication yes
```

I then generated an Ed25519 SSH key pair on Windows:

```
ssh-keygen -t ed25519
```

The key pair consisted of:

- xx_xxxxx: Private key, retained securely on Windows.

- xx_xxxxx.pub: Public key, installed on Linux Mint.

I transferred the public key to the Linux VM using:

```
Get-Content xxx\xxxx.pub | ssh mint@Linux-VM-IP "mkdir -p ~/.ssh && chmod 700 ~/.ssh && cat >> ~/.ssh/authorized_keys && chmod 600 ~/.ssh/authorized_keys"
```

I verified that the authorized_keys file existed and contained one entry.

I then tested the SSH connection from Windows:

```
ssh mint@Linux-VM-IP
```

Authentication succeeded using the SSH key and its passphrase.

I also used verbose mode:

```
ssh -v mint@Linux-VM-IP
```

The output confirmed successful public-key authentication.

## 7. SSH Server Hardening

Before modifying the configuration, I inspected the main SSH configuration file:

```
sudo grep -nE '^(Include|PermitRootLogin|PasswordAuthentication|PubkeyAuthentication)' /etc/ssh/sshd_config
```

I identified the included configuration directory:

```
/etc/ssh/sshd_config.d/
```

I inspected the directory and confirmed that it contained no additional configuration files defining the settings being changed.

I edited the main configuration file:

```
sudo nano /etc/ssh/sshd_config
```

I configured the following settings:

```
PermitRootLogin no
PasswordAuthentication no
PubkeyAuthentication yes
```

These settings:

- Prevent direct SSH login as root.
- Disable password-based SSH authentication.
- Retain public-key authentication.

Before applying the changes, I validated the configuration:

```
sudo sshd -t
```

No syntax errors were reported.

I verified the effective configuration:

```
sudo sshd -T | grep -E '^(permitrootlogin|passwordauthentication|pubkeyauthentication)'
```

The output confirmed:

```
permitrootlogin no
passwordauthentication no
pubkeyauthentication yes
```

I then reloaded the SSH service:

```
sudo systemctl reload ssh
```

## 8. SSH Authentication Verification

I opened a new Windows PowerShell session and tested SSH authentication:

```
ssh -v mint@Linux-VM-IP
```

The connection succeeded using the Ed25519 key.

I then deliberately disabled public-key authentication on the client to test whether password authentication was blocked:

```
ssh -o PreferredAuthentications=password -o PubkeyAuthentication=no mint@Linux-VM-IP
```

The server rejected the authentication attempt, confirming that password-based SSH authentication was disabled.

Finally, I verified that the SSH service remained operational:

```
sudo systemctl status ssh
```

The service reported:
`Active: active (running)`

## 9. Key Troubleshooting Lessons

Through this lab, I learned several important troubleshooting concepts:

- A firewall permission does not automatically mean a service is listening on that port.
- A listening service does not guarantee successful connectivity if firewall rules block the traffic.
- Successful ping tests do not guarantee successful TCP connections.
- SSH network access restrictions and SSH authentication controls are separate security layers.
- Public-key authentication can provide stronger protection than password-only authentication.
- SSH configuration should be validated before reloading the service.
- Existing remote sessions should be retained until a new connection has been tested successfully.
- Temporary services and firewall permissions should be removed after completing testing.

## 10. Skills Demonstrated

- Linux command-line administration.
- UFW firewall configuration and troubleshooting.
- TCP/IP connectivity testing.
- Network access control and source-IP restrictions.
- OpenSSH server configuration.
- SSH key generation and public-key authentication.
- Linux file and directory permissions.
- Windows PowerShell networking utilities.
- Service inspection and management using systemd.
- Security hardening and configuration validation.
- Technical troubleshooting and verification.

## 11. Conclusion

This lab provided hands-on experience configuring a Linux firewall and securing remote SSH access.

I successfully restricted inbound SSH connections to an authorized Windows client, configured Ed25519 key-based authentication, disabled password authentication and direct root login, and verified the resulting configuration through connectivity and authentication tests.

The exercise reinforced the importance of troubleshooting network connectivity systematically by examining network reachability, firewall permissions, listening services, and authentication independently.
