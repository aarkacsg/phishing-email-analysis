# Indicators of Compromise

The following indicators were identified during the simulated Cloudora payroll phishing investigation.

| Type | Indicator | Context |
|---|---|---|
| Domain | `cloudora-hr-portal[.]example` | Attacker-controlled lookalike payroll domain |
| Subdomain | `login[.]cloudora-hr-portal[.]example` | Credential-harvesting subdomain |
| URL | `hxxps://cloudora-hr-portal[.]example/payroll/login` | Payroll credential-phishing URL |
| URL | `hxxps://login[.]cloudora-hr-portal[.]example/verify` | Credential-phishing verification URL |
| IP Address | `198.18.44.10` | PayrollPhish-A sending infrastructure |
| IP Address | `198.18.44.23` | PayrollPhish-A sending infrastructure |
| IP Address | `198.18.51.7` | PayrollPhish-B sending infrastructure |
| IP Address | `198.18.7.200` | Infrastructure used for successful access to compromised accounts |

## Notes

All indicators in this investigation are synthetic training data.

The domains and IP addresses use reserved infrastructure and do not represent real malicious systems.

Domains and URLs have been defanged to prevent accidental navigation.
