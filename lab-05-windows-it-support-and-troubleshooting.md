#Lab 5: Windows IT Support and Troubleshooting

##1. Lab Overview

Objective: Practice practical Windows IT support and troubleshooting techniques, including system inspection, resource monitoring, Windows service management, Event Viewer analysis, application troubleshooting, and network diagnostics.

Environment:

- Operating System: Windows 10 Home, Version 22H2
- Hardware: HP laptop
- Processor: AMD Ryzen 5 3500U
- Memory: 8 GB RAM
- Virtualization: Enabled
- Tools: Task Manager, System Information, Event Viewer, Services console, PowerShell

##2. System Information and Hardware Inspection

###Tasks performed

- Opened System Information (msinfo32).
- Inspected Windows version using winver.
- Identified CPU, installed memory, and system specifications.

###Findings

- Windows 10 Home, Version 22H2.
- OS Build 19045.
- AMD Ryzen 5 3500U processor.
- 4 physical cores and 8 logical processors.
- 8 GB installed RAM.

###Skills demonstrated

- Identifying hardware and operating system specifications.
- Gathering system information for troubleshooting and support documentation.

##3. Performance Monitoring

###Tasks performed

- Used Task Manager to inspect CPU, memory, disk, and network utilization.
- Sorted running processes by memory usage.
- Observed Resource Monitor behavior during normal system activity.

###Observations

- CPU utilization was generally between 5% and 9%, with occasional spikes.
- Memory usage was approximately 4.2 GB, with around 1.7 GB available during one observation.
- Google Chrome was among the largest memory-consuming applications.
- Microsoft Defender and Windows Explorer were also visible in the process list.
- Chrome's multiple processes were consistent with its multi-process architecture.

###Analysis

Memory availability was relatively limited, but the observations alone did not establish a memory-related performance fault.

A proper diagnosis would require correlating user-reported symptoms with memory pressure, disk activity, paging, and process behavior.

###Skills demonstrated

- Monitoring system resource utilization.
- Identifying resource-intensive processes.
- Distinguishing normal operating behavior from potential performance problems.

##4. Windows Services Investigation

###Tasks performed

- Opened the Services management console (services.msc).
- Inspected the DHCP Client service.
- Used PowerShell to inspect service status and configuration.

###Findings

- DHCP Client service was running.
- Startup type was Automatic.

###Analysis

The DHCP Client service helps Windows obtain and maintain network configuration information, including IP addressing and related network settings.

###Skills demonstrated

- Inspecting Windows services.
- Understanding service status and startup types.
- Using PowerShell to gather service information.

##5. Event Viewer Investigation — Dropbox Updater

###Objective

Investigate recurring Windows service errors and determine whether they represented an active application problem.

###Tools used

- Event Viewer (eventvwr.msc)
- Services console (services.msc)
- PowerShell:
  - Get-Service
  - Get-CimInstance Win32_Service
  - Get-Process
  - Test-Path

###Findings

During inspection of the Windows System event log, the following events were observed:

- TPM-WMI Error 1796
- Time-Service Warning 134
- Service Control Manager Error 7024

Further investigation identified the Event 7024 error as relating to the Dropbox Update Service.

The service had the following characteristics:

- Service state: Stopped
- Startup type: Automatic
- Executable path: Dropbox updater executable
- Executable file existence: Confirmed using Test-Path
- The same service error appeared repeatedly in historical event logs.

###Troubleshooting performed

- Identified the service associated with Event ID 7024.
- Inspected its status and startup configuration.
- Verified that the updater executable existed at the configured path.
- Opened Dropbox and checked its application status.
- Observed a Dropbox update notification.
- Confirmed that Dropbox reported its synchronization status as "Up to date."
- Inspected related Dropbox processes using PowerShell

###Conclusion

The Dropbox application appeared to function normally during testing. However, recurring historical updater service errors were identified.

The exact cause of the service termination was not established, and no confirmed permanent fix was applied.

The issue was documented for monitoring rather than making unnecessary changes to a functioning application.

Troubleshooting lesson: An error in Event Viewer does not automatically mean an application is currently broken. Correlate logs with service status, application behavior, and user impact before changing system configuration.

##6. Windows Network Diagnostics

###Objective

Inspect network configuration and test DNS resolution and TCP connectivity.

###Tools used

- ipconfig /all
- nslookup
- Test-NetConnection

###Findings

IP configuration

- DHCP was enabled.
- IPv4 subnet mask: 255.255.255.0 (/24).
- Default gateway and DHCP server were identified.
- The gateway was also configured as the DNS resolver.
- Private IP addresses have been omitted from this report.

DNS testing

Command:

```
nslookup google.com
```

Result:

- DNS lookup successful.
- DNS server identified as the network gateway.
- Multiple IPv4 and IPv6 addresses returned.
- Response identified as non-authoritative.

TCP connectivity testing

Command:

```
Test-NetConnection google.com -Port 443
```

Result:

```
TcpTestSucceeded : True
```

This confirmed that a TCP connection to Google's HTTPS port (443) could be established.

###Analysis

The tests demonstrated that:

- The computer had an active DHCP configuration.
- DNS resolution was functioning during testing.
- TCP connectivity to a remote HTTPS endpoint was successful.

Skills demonstrated

- Inspecting DHCP and IP configuration.
- Understanding IPv4 subnetting.
- Troubleshooting DNS resolution.
- Testing TCP connectivity to a specific port.
- Interpreting command-line diagnostic results.

##7. Troubleshooting Knowledge Check

Scenario: A user can access a website using its IP address but cannot access it using its domain name.

Likely initial investigation: DNS resolution.

Reasoning: Successful access by IP suggests that basic IP connectivity may be working, while failure using the domain name points toward a possible name-resolution problem.

This demonstrates symptom-based troubleshooting rather than randomly changing network settings.

##8. Key Commands Reference
|Command | Purpose |
|msiinfo32 | Open detailed system information |
|winver | Check windows version and build |
|services.msc | Open windows services |
|eventvwr.msc | Open Event Viwer |
|ipconfig /all | Inspect complete network configuration |
|nslookup google.com | Test DNS resolution |
|Test-NetConnection google.com -Port 443 | Test TCP connectivity |
|Get-Service | Inspect windows services |
|Get-CimInstance win32_service | Inspect detailed service configuration |
|Get-Process | Inspect running processes |
|Test-Path | Check whether a file or directory exists|

##9. Skills and Competencies Developed

- Windows operating system administration fundamentals.
- Hardware and system information inspection.
- Performance monitoring and resource analysis.
- Windows service troubleshooting.
- Event Viewer investigation.
- PowerShell-based system diagnostics.
- DNS and DHCP troubleshooting.
- TCP connectivity testing.
- Evidence-based troubleshooting and documentation.

##10. Final Reflection

This lab provided hands-on experience with common Windows IT support tasks.

A key lesson was to avoid treating every warning or error as a problem requiring immediate intervention. Effective troubleshooting involves gathering evidence, identifying the affected component, checking whether users are impacted, testing possible explanations, and documenting the outcome.

The Dropbox updater investigation reinforced the importance of distinguishing a historical service error from a confirmed current application failure.

`Lab Status`: Completed
