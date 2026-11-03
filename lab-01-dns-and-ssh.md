#Lab 01: DNS Troubleshooting and SSH Remote Administration

##Environment

- Host: Windows laptop
- Virtualization: VMware Workstation 17 Player
- Guest OS: Linux Mint 22.3
- VM IP: 192.168.96.133
- VM network mode: NAT

##Objectives

- Verify basic IP connectivity and DNS resolution.
- Inspect DNS traffic with tcpdump.
- Identify listening services and ports with ss.
- Install and verify OpenSSH server.
- Connect remotely from Windows to Linux Mint.

##Exercises and results

###1. Connectivity and DNS

-Tested connectivity to `8.8.8.8` and hostname resolution for `google.com`.

- Captured DNS queries and identified `A`, `AAAA`, and `PTR` record types.
- Temporarily changed the DNS server to `192.0.2.1`.
- Observed that IP connectivity could still work while hostname resolution failed.
- Restored DNS configuration and verified connectivity.

###2. Listening Services

- Used `sudo ss -tulnp` to inspect TCP and UDP sockets.
- Identified `systemd-resolved`, `CUPS`, and `Avahi`.
- Checked for an SSH listener on TCP port 22.

###3. SSH Remote Administration

- Installed OpenSSH server.
- Verified the SSH listener on port `22`.
- Successfully connected locally using SSH.
- Successfully connected from Windows PowerShell to the VM at `192.168.x.x`.
- Confirmed that the VM was reachable over the virtual network.

##Key Takeways

- IP connectivity and DNS resolution are separate troubleshooting concerns.
- A listening port does not automatically prove a service is reachable.
- SSH requires a client, a server, network connectivity, and successful authentication.
- VMware NAT affects how the VM is reachable from other devices.

##Next Steps

- Investigate Linux firewall rules and SSH service configuration.
- Learn basic TCP/UDP troubleshooting.
- Practice Windows networking commands.
