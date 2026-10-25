# Observed test results

Results below were observed in the user's Wazuh 4.14.8 lab. Packaging checks do not constitute another Wazuh run. Each line number refers to log-samples.log; submit one complete line at a time to a fresh server logtest session after rule edits.

| Line | Sample origin | Meaning | Observed final rule | Level | Custom SQLi match |
|---|---|---|---|---|---|
| 1 | Captured normal browser request | id=1 | 31100 | 0 | No |
| 2 | Captured attack request, replayed | quoted OR 1=1 | 100111 | 7 | Yes |
| 3 | Synthetic logtest input | normal id=2 | 31100 | 0 | No |
| 4 | Synthetic logtest input | SQLi in Referer only | 31108 | 0 | No |
| 5 | Synthetic logtest input | alternative quoted OR 2=2 | 31100 | 0 | No, coverage miss |
| 6 | Captured diagnostic request | unique 404 marker | 100110 | 5 | Not applicable |

Before installing rule 100111, line 2 selected 31100 at level 0 in logtest. After installation it selected 100111 at level 7. Baseline refers to SQLi detection: the unrelated diagnostic rule 100110 already existed. A new browser SQLi request after deployment also generated a real 100111 alert from agent 001, visible in alerts.json and Dashboard.

For the three selected benign/request-context cases (lines 1, 3 and 4), rule 100111 did not fire. These are limited negative tests, not a measured operational false-positive rate. The alternative malicious sample was missed. Do not infer general accuracy from these few selected and partly rule-development samples. The synthetic 2=2 sample was not executed against the application in this test sequence.

`firedtimes` is a session/rule counter and must not be interpreted as attack count. Level-0 results do not produce the tested alert. Match results describe detection, not blocking or exploit success.

## Replaying samples

On the Wazuh server run `sudo /var/ossec/bin/wazuh-logtest`, paste one sample line and inspect Phases 2 and 3. Exit with Ctrl+C. Saving a rule is sufficient for a new logtest session; apply it to live events only after configuration validation and a Manager restart. Do not inject synthetic lines into the real Apache access log and present them as live HTTP evidence.

For future work, broaden quoted tautology coverage and test the change against both these regressions and a separate unseen set. Other paths and POST-body visibility require separate design and validation.
