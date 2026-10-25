# Wazuh DVWA screenshot evidence

24 original images extracted from Wazuh.docx in document order. Files were renamed only; image bytes were preserved.

## Evidence index

| Document image | File | Evidence |
|---|---|---|
| 1 | [01-dvwa-security-low.png](../screenshots/01-dvwa-security-low.png) | DVWA security level Low. |
| 2 | [02-dvwa-containers-and-log-mount.png](../screenshots/02-dvwa-containers-and-log-mount.png) | DVWA and MariaDB running; bind mount and host log files. |
| 3 | [03-agent-log-collection.png](../screenshots/03-agent-log-collection.png) | Agent localfile configuration, active state and Analyzing file message. |
| 4 | [04-pipeline-rule-logtest.png](../screenshots/04-pipeline-rule-logtest.png) | Diagnostic rule 100110 in manual logtest. |
| 5 | [05-pipeline-alert-dashboard.png](../screenshots/05-pipeline-alert-dashboard.png) | Diagnostic alert in Dashboard. |
| 6 | [06-pipeline-alert-details.png](../screenshots/06-pipeline-alert-details.png) | Diagnostic alert details with agent and source. |
| 7 | [07-normal-request-result.png](../screenshots/07-normal-request-result.png) | Normal id=1 request returns one user. |
| 8 | [08-normal-request-access-log.png](../screenshots/08-normal-request-access-log.png) | Access log for the normal request. |
| 9 | [09-sqli-initial-result.png](../screenshots/09-sqli-initial-result.png) | Initial SQLi returns five lab users. |
| 10 | [10-normal-and-sqli-access-log.png](../screenshots/10-normal-and-sqli-access-log.png) | Actual request targets for normal and SQLi requests. |
| 11 | [11-sqli-baseline-logtest.png](../screenshots/11-sqli-baseline-logtest.png) | Before custom SQLi rule: 31100, level 0. |
| 12 | [12-custom-rules-final.png](../screenshots/12-custom-rules-final.png) | Final rules 100110 and 100111, using \x26. |
| 13 | [13-sqli-custom-logtest.png](../screenshots/13-sqli-custom-logtest.png) | SQLi sample matches 100111, level 7, in logtest. |
| 14 | [14-normal-id1-negative-logtest.png](../screenshots/14-normal-id1-negative-logtest.png) | Normal id=1 does not match 100111. |
| 15 | [15-sqli-live-result.png](../screenshots/15-sqli-live-result.png) | Live browser SQLi result after deployment. |
| 16 | [16-sqli-live-alert-terminal.png](../screenshots/16-sqli-live-alert-terminal.png) | Live alert from agent 001 in alerts.json. |
| 17 | [17-sqli-live-alert-dashboard.png](../screenshots/17-sqli-live-alert-dashboard.png) | Live SQLi alert 100111 in Dashboard. |
| 18 | [18-sqli-live-alert-details.png](../screenshots/18-sqli-live-alert-details.png) | Live SQLi alert details. |
| 19 | [19-normal-id2-test-input.png](../screenshots/19-normal-id2-test-input.png) | Synthetic normal id=2 input. |
| 20 | [20-normal-id2-test-result.png](../screenshots/20-normal-id2-test-result.png) | Synthetic id=2 result: 31100, level 0. |
| 21 | [21-referer-test-input.png](../screenshots/21-referer-test-input.png) | Synthetic SQLi in Referer only; requested URL is /security.php. |
| 22 | [22-referer-test-result.png](../screenshots/22-referer-test-result.png) | Referer-only test result: 31108, level 0. |
| 23 | [23-tautology2-test-input.png](../screenshots/23-tautology2-test-input.png) | Synthetic alternative tautology 2=2 input. |
| 24 | [24-tautology2-test-result.png](../screenshots/24-tautology2-test-result.png) | Alternative tautology result: 31100, level 0; documented detection gap. |

## Interpretation

Rule 100110 is a delivery check, not attack detection. Rule 100111 detects a narrow OR tautology pattern on the DVWA SQLi path. The captured encoded 1=1 payload matched in logtest and in a live agent alert. Normal id=1, synthetic id=2, and the Referer-only test did not match 100111. The synthetic 2=2 variant was missed. These few samples do not establish production precision, recall, or false-positive rate.

Screenshots 19–24 use synthetic log entries: they are parser/rule tests, not requests sent to DVWA. Screenshots 9 and 15 contain lab response evidence; an HTTP 200 or SIEM alert alone does not prove exploitation. The source address 172.18.0.1 reflects the Docker/SSH access arrangement and does not identify the Windows client.

This index describes the screenshot evidence. See README.md for the project overview.
