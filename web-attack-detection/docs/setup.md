# Setup and reproduction

This documents the completed lab. Wazuh server and agent installation are prerequisites. Replace hostnames and SSH forwarding ports for your own environment. The supplied Compose file is an override for the official DVWA repository, not a standalone application definition. No image digest or DVWA Git commit was recorded, so future upstream images may behave differently.

## Endpoint and DVWA

On Based U, with Docker Engine, Compose and Git already installed:

```bash
git clone https://github.com/digininja/DVWA.git ~/DVWA
sudo mkdir -p /var/log/dvwa
```

Copy this project's configs/compose.override.yaml into ~/DVWA/compose.override.yaml. Review any existing override before replacing it. In the lab, none existed. Keep the upstream 127.0.0.1:4280:80 binding.

```bash
cd ~/DVWA
sudo docker compose config --quiet
sudo docker compose up -d
sudo docker compose ps
curl --max-time 10 -s -o /dev/null -w 'HTTP %{http_code}\n' http://127.0.0.1:4280/login.php
sudo tail -n 5 /var/log/dvwa/access.log
```

The bind mount hides the image's /var/log/apache2 symlinks to stdout/stderr. Apache writes regular files visible at /var/log/dvwa on the endpoint. This changes where the web logs are available; use the host files for subsequent inspection. Log rotation was not configured in this iteration.

## Agent collection

On Based U, back up /var/ossec/etc/ossec.conf. Insert the supplied configs/agent-localfile.xml localfile block inside ossec_config, outside other sections. Do not replace the whole agent configuration, and do not add the source twice.

```bash
sudo /var/ossec/bin/wazuh-logcollector -t
```

Only after a successful check:

```bash
sudo systemctl restart wazuh-agent
systemctl is-active wazuh-agent
sudo grep -aF '/var/log/dvwa/access.log' /var/ossec/logs/ossec.log | tail -n 5
```

Analyzing file confirms source registration; it does not independently confirm delivery to the server.

## Server rules

On the Wazuh server, confirm IDs 100110 and 100111 do not conflict with existing custom rules. Back up an existing dvwa_lab_rules.xml if present, then install the supplied rules/dvwa_lab_rules.xml at /var/ossec/etc/rules/dvwa_lab_rules.xml. Preserve suitable ownership and permissions for Wazuh to read it. Leave built-in rules unchanged.

```bash
sudo /var/ossec/bin/wazuh-analysisd -t
sudo /var/ossec/bin/wazuh-logtest
```

Test the lines in tests/log-samples.log one per input line, following tests/results.md. Start a new logtest session after editing rules. Exit with Ctrl+C. Once validation succeeds, restart the Manager and wait for active:

```bash
sudo systemctl restart wazuh-manager
systemctl is-active wazuh-manager
```

## Windows browser access

The lab's VirtualBox NAT Network forwarded Windows 127.0.0.1:2222 to endpoint 10.0.2.4:22, 2223 to server 10.0.2.6:22, and 8443 to server 10.0.2.6:443. These are environment-specific, not requirements for all installations.

In Windows CMD:

```cmd
ssh -p 2222 -L 127.0.0.1:4280:127.0.0.1:4280 daryn@127.0.0.1
```

Keep the tunnel open and visit http://127.0.0.1:4280/setup.php. For a fresh lab, Create / Reset Database initializes the test database and also resets existing DVWA data. Use the DVWA lab defaults admin / password; these are intentionally public training credentials, not personal credentials. Set DVWA Security to Low.

## Live checks

On Based U, send the marker after the Manager is active:

```bash
curl --max-time 10 -s -o /dev/null -w 'HTTP %{http_code}\n' http://127.0.0.1:4280/wazuh-pipeline-check-20261025
```

Expect HTTP 404. On the server, check alerts.json for rule 100110 and source /var/log/dvwa/access.log. A sudo log that merely contains the marker is not the delivery proof.

In the browser SQL Injection page, submit id 1 and record the result. Then submit `1' OR '1'='1` once. Check the actual request on Based U:

```bash
sudo grep -F '"GET /vulnerabilities/sqli/?' /var/log/dvwa/access.log | tail -n 3
```

On the server:

```bash
sudo grep -F '"100111"' /var/ossec/logs/alerts/alerts.json | tail -n 1
```

In Dashboard Threat Hunting events, filter `rule.id: "100111" AND agent.id: "001"` and choose the correct time range. Check agent, URL, source file and timestamp. New live requests are required after deployment; a logtest match alone is not delivery evidence.
