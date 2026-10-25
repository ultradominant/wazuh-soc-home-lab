# Troubleshooting notes

## Compose unavailable

Docker Engine was active but `docker compose` was unknown. Installing Ubuntu's docker-compose-v2 package enabled Compose 2.40.3. Package names differ between Ubuntu's repository and Docker's own repository.

## DNS failures

The endpoint could reach 1.1.1.1 but did not resolve Ubuntu mirror names. Temporarily setting interface DNS with `sudo resolvectl dns enp0s3 1.1.1.1 8.8.8.8` and flushing caches restored resolution. This was an observed workaround, not proof of a specific upstream fault. It may reset after reboot or network reconfiguration. `sudo resolvectl revert enp0s3` restores the interface's managed settings.

## Wrong host for localhost

DVWA listens on the endpoint loopback address. Running curl to 127.0.0.1:4280 on the Wazuh server gave HTTP 000 because no HTTP response was obtained there. The correct live marker request was executed on Based U. Windows accessed it through the existing NAT SSH port and an SSH tunnel.

## Apache logs in container output

The original access.log and error.log were symlinks to stdout and stderr. Mounting the host directory at /var/log/apache2 enabled persistent host-visible files. This iteration did not add rotation or retention management.

## Diagnostic rule selection

The first diagnostic rule used decoded_as alone and logtest selected built-in 31101. Making 100110 a child of 31101 with an exact URL match produced the intended diagnostic result. The diagnostic marker does not demonstrate SQLi detection.

## SQLi rule boundary

The initial expression ended with `(?:&amp;|$)` and did not match the sample. Replacing this with `(?:\x26|$)` made 100111 match. The final configuration and successful result are included; no unverified parser explanation is presented as fact.

## Slow host and restart

Windows memory utilization reached about 89–92%, and SSH became unresponsive during a Manager restart. After a pause the services were confirmed active, the server had 5.4 GiB available, and its swap was unused. Host memory pressure was a plausible contributor, not a proven root cause. Repeated restarts and forced VM termination were avoided during troubleshooting. The project used two VMs without Kali.
