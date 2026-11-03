#Lab 2: Windows Network Troubleshooting

##Objective
Practice diagnosing network connectivity and HTTPS response times using built-in Windows tools and curl.

##Environment

- Operating system: Windows
- Network connection: Wi-Fi
- Tools: `ipconfig`, `nslookup`, `ping`, `tracert`, `route print`, `Test-NetConnection`, and `curl.exe`

##Tests Performed

###1. IP configuration and routing

Used `ipconfig` and `ipconfig /all` to inspect the local IPv4 configuration, subnet mask, default gateway, DHCP server, and DNS server.

Used `route print -4` to inspect the IPv4 routing table and identify the default route.

###2. DNS resolution
Used `nslookup google.com`.

Result: Name resolution succeeded.

###3. Connectivity

Used `ping` to test the default gateway and `8.8.8.8`.

Result: Both responded during the tests. A 20-packet `ping` to `8.8.8.8` reported 0% packet loss.

###4. Route tracing

Used `tracert -d 8.8.8.8`.

Result: The destination responded. One intermediate hop timed out, while later hops responded. This did not establish an end-to-end connectivity failure.

###5. TCP connectivity

Used `Test-NetConnection` to test an HTTPS connection to a website on TCP port 443.

Result: TcpTestSucceeded was True.

###6. HTTPS response timing

Used `curl.exe` to measure total request time for Google and Microsoft.

```
Google: 0.375 s and 0.345 s

Microsoft: 0.297 s and 0.359 s
```

Result: All four requests completed successfully. The measured times varied between runs.

##Findings

DNS resolution, ICMP connectivity to the tested public IP, TCP connectivity to an HTTPS endpoint, and HTTPS requests to two websites worked during testing.

An intermediate traceroute timeout did not prevent the destination from responding.

##Limitations

These tests represent a limited snapshot. They do not prove that all applications and websites are working correctly or that the connection is always stable. The HTTPS timing measurements alone cannot identify the cause of perceived browsing slowness.

##Troubleshooting Approach

Collected evidence before recommending configuration changes. Distinguished successful tests from conclusions that would require additional evidence.

##Next Steps

If slowness recurs, compare affected and unaffected applications, repeat measurements while the issue is occurring, and inspect local resource usage before changing network settings.
