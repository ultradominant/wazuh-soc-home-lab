# Troubleshooting notes

## Troubleshooting and limitations

- **Clock drift:** The endpoint clock was almost a day behind. Chrony had a valid NTP source but a large remaining correction. `sudo chronyc makestep` applied the correction, and the corrected system time was written to the VM RTC. The cause of the original drift has not been confirmed.
- **FIM timestamp interpretation:** After correcting the clock, the file's Epoch modification time `1792151328` matched 11:48:48 UTC. The alert timestamp was `2026-10-16T16:48:48.468+0500`, representing the same second. However, in the retrieved JSON, `syscheck.mtime_after` was serialized as `2026-10-16T16:48:48` without an offset and indexed as UTC. The dashboard therefore displayed it five hours ahead. I used the offset-aware alert timestamp and source Epoch values for this comparison. The component responsible for this conversion has not been isolated; this is an observed limitation of this lab, not a confirmed general Wazuh defect. The timestamp handling remains unresolved.
- **Endpoint OS:** The supplied Ubuntu 22.04 SCA policy was skipped on the Ubuntu 26.04 endpoint. This project does not claim a completed CIS assessment.
- **Service delays:** Temporary SSH and service-query delays occurred. Services subsequently responded, but the underlying cause was not established.
- **Scope:** These are small, controlled tests, not a production deployment or a complete attack simulation. Alert counts are not equivalent to login-attempt counts, and MITRE mappings are not proof of malicious activity.

