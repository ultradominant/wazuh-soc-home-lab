# SQL injection investigation

## Question and finding

Could I collect a DVWA request in Wazuh and detect a SQL injection pattern that the installed built-in rules did not alert on in the baseline test? For the captured encoded OR 1=1 payload, yes: custom rule 100111 matched in logtest and generated a live agent alert visible in Dashboard.

## Evidence sequence

1. DVWA and MariaDB ran on Based U. Apache combined logs were exposed to the host through a bind mount. Wazuh Agent registered /var/log/dvwa/access.log.
2. Diagnostic rule 100110 matched a unique 404 path. The live alert identified agent 001 and the expected source file, confirming the collection path.
3. A normal id=1 request returned one lab record. The injected value `1' OR '1'='1` returned five test users. These observations support successful alteration of query behavior within DVWA.
4. The captured SQLi request at 13:14:33 UTC decoded as web-accesslog. Before rule 100111 was added, logtest selected 31100 at level 0. This is a baseline result for this event and ruleset, not a claim that Wazuh misses every SQLi variant.
5. The custom rule was tested, including normal input and a documented miss. The final version uses PCRE2 \x26 for the ampersand boundary; the earlier &amp; version did not match the tested event. We did not independently inspect the parser internals to establish the precise cause.
6. After deployment, a new browser request at 18:39:17 UTC generated a rule 100111 alert timestamped 23:39:18.336 +0500. The alert identified linux-endpoint, 10.0.2.4, web-accesslog and /var/log/dvwa/access.log. It was visible in Dashboard.

The roughly one-second difference in displayed timestamps is an observation from one event, not a latency benchmark. UTC and UTC+5 represent consistent times after conversion.

## Analyst conclusion

This is a true positive for the declared lab SQLi attempt. The application response supports a successful lab injection; the access-log alert alone cannot establish database extraction or the full impact of a real incident. No production compromise, password theft, persistence or host takeover was demonstrated.

The source 172.18.0.1 is the Docker-side peer seen by Apache. It cannot be used to attribute the action to an external attacker. The HTTP status 200 describes the response, not whether the content was malicious. Repeated displayed ID text on DVWA is the submitted input, not five distinct numeric database IDs.

## Detection boundary

The rule checks the decoded request URL and a specific quoted 1=1 pattern on /vulnerabilities/sqli/. It does not inspect SQL execution, response bodies, POST bodies or Referer. The 2=2 variant was missed. Other routes, alternate operators, comments, double encoding and additional syntax are not established coverage. The broad query prefix also does not enforce that the pattern belongs specifically to the id parameter. These limitations should guide any future revision and regression tests.

See [test results](../tests/results.md) and the [evidence index](../docs/evidence.md).
