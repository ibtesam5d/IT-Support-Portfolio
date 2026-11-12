# Lab 01: DNS Troubleshooting and SSH Remote Administration

## Environment

- Host OS: Windows
- Virtualization: VMware Workstation Player
- Guest OS: Linux Mint
- Guest IP: VM IP
- Network mode: VmwareNAT

## Objectives

- Verify basic IP connectivity and DNS resolution.
- Capture and inspect DNS traffic using tcpdump.
- Identify listening network services and ports using ss.
- Install and verify OpenSSH server.
- Establish an SSH connection from Windows to Linux Mint.

## Exercises and results

### 1. Network Connectivity and DNS Troubleshooting

- Tested IP connectivity using `ping`.
- Verified hostname resolution for `google.com`.
- Captured DNS traffic using `tcpdump`.
- Identified DNS record types, including A, AAAA, and PTR.
- Simulated a DNS configuration failure by temporarily assigning an invalid DNS server address (`192.0.2.1`).
- Observed that IP connectivity could remain available while hostname resolution failed.
- Restored the original DNS configuration and verified that hostname resolution worked again.

Key finding: IP connectivity and DNS resolution are separate components of network communication. Successful connectivity to an IP address does not guarantee that DNS is functioning.

### 2. Listening Services and Port Inspection

- Used sudo ss -tulnp to inspect listening TCP and UDP sockets.
- Identified services and processes associated with listening ports, including systemd-resolved, CUPS, and Avahi.
- Checked for an SSH listener on TCP port 22.

Key finding: A listening socket indicates that a process is accepting connections on a particular address and port. It does not independently confirm end-to-end network reachability or successful application operation.

### 3. SSH Remote Administration

- Installed OpenSSH Server on Linux Mint.
- Verified that the SSH service was listening on TCP port 22.
- Tested local SSH connectivity.
- Connected remotely from Windows PowerShell to the Linux VM using SSH.
- Confirmed successful remote access over the VMware virtual network.

Key finding: Successful SSH access requires a functioning SSH server, network connectivity, an accessible listening port, and valid authentication.

## Key Takeways

- DNS resolution and IP connectivity are separate troubleshooting concerns.
- DNS A, AAAA, and PTR records serve different purposes.
- Linux socket inspection helps identify listening services and ports.
- A listening port alone does not guarantee remote accessibility.
- SSH provides secure remote administration over an encrypted connection.
- VMware NAT provides network connectivity to the VM while influencing how other devices can access it.

## Skills Practiced

- Linux command-line administration.
- Basic network connectivity troubleshooting.
- DNS configuration and packet inspection.
- TCP/UDP socket inspection.
- OpenSSH installation and verification.
- Windows-to-Linux remote administration.
- VMware virtual networking fundamentals.

## Next Steps

- Investigate Linux firewall rules and SSH security configuration.
- Practice TCP and UDP troubleshooting.
- Expand Windows networking diagnostics using PowerShell.
