# Authentication investigation: CLIENT01

## Verified outcome

Windows Security events from the domain-joined CLIENT01 reached Wazuh agent 002. A controlled incorrect-password attempt produced event 4625 and rule 60122 (level 5). A successful runas session as SOCLAB\freddy.flores produced event 4624 and rule 60118 (level 3). These are separate controlled tests, not evidence of an attack sequence.

| Field | Failed attempt | Successful attempt |
|---|---|---|
| Target | SOCLAB\freddy.flores | SOCLAB\freddy.flores |
| Initiating subject | CLIENT01\labadmin | SOCLAB\freddy.flores |
| Computer | CLIENT01.soclab.test | CLIENT01.soclab.test |
| Windows event | 4625 | 4624 |
| Event record ID | 31845 | 32061 |
| Logon type | 2 | 2 |
| Source | ::1 (local loopback) | ::1 (local loopback) |
| Status / substatus | 0xc000006d / 0xc000006a | Not present |
| Wazuh rule / level | 60122 / 5 | 60118 / 3 |
| Windows timestamp UTC | 2026-11-24 20:27:32.9807488 | 2026-11-26 04:48:47.3576772 |
| Wazuh timestamp UTC | 2026-11-24 20:27:35.680 | 2026-11-26 04:48:49.120 |

The successful event is November 25 at 23:48 Eastern, explaining the November 26 UTC index date. The successful event explicitly reports Elevated Token: No. The process identity screenshot independently showed soclab\freddy.flores on CLIENT01.

The failed event describes an unknown username or bad password; its substatus is consistent with the intentionally incorrect password. Neither the T1531 mapping on the failed alert nor T1078 on the successful alert proves malicious activity. Wazuh rule levels are alert metadata, not a conclusion that this lab activity was an attack.

## Configuration and reproduction

CLIENT01 was moved into SOCLab/Workstations. The linked GPO, `SOCLab - Workstation Security Auditing`, enables the advanced-audit subcategory override, Logon Success and Failure, Logoff Success, and Special Logon Success. On CLIENT01, elevated gpresult and auditpol output verified application. DC01 output was not used as proof of workstation policy application.

Wazuh agent 4.14.8 was installed on CLIENT01 using manager 192.168.122.243. WazuhSvc was Running and dashboard agent 002 was Active at 192.168.122.51.

On CLIENT01, confirm hostname before running `runas /user:SOCLAB\freddy.flores cmd.exe`. One intentionally incorrect password tests failure; a correct password starts a session whose identity can be checked with whoami and hostname. Never include passwords in commands, screenshots, or exports.

Dashboard searches:

```text
agent.id:"002" AND data.win.system.eventID:"4625"
agent.id:"002" AND data.win.system.eventID:"4624" AND data.win.eventdata.targetUserName:"freddy.flores"
```

Choose a time range covering the test. The first search returned no historical results with Last 24 hours; extending the range recovered the saved alert. The manager.name filter was initially suspected, but later results and exported data confirmed wazuh-server was correct.

## Evidence

- [Full failed event](../evidence/client01-failed-logon-4625.json)
- [Full successful event](../evidence/client01-successful-logon-4624.json)
- [Failed-event summary CSV](../evidence/client01-failed-logon-summary.csv)

The CSV contains only displayed summary columns. JSON preserves the target account, logon type, source, record ID, and timestamps. Exports include synthetic lab identifiers and private VM addresses; no passwords are included.

## Troubleshooting and limits

An account check showed Enabled=True, LockedOut=False, PasswordExpired=True and pwdLastSet=0. Temporarily clearing ChangePasswordAtLogon restored sign-in after a reset; the underlying password-change failure was not conclusively diagnosed. This is not a recommendation to disable password policy.

Wazuh dashboard access was recovered with the supported single-admin password reset tool. Direct indexer authentication subsequently returned HTTP 200. Credentials and installer archives are excluded from this repository. A private credential rotation remains advisable following credentials shared during troubleshooting.

DC01 is not yet enrolled in Wazuh. Account/group-change monitoring, OU deletion-protection verification, and final documentation review remain outstanding. This exercise verifies workstation authentication collection, not comprehensive domain monitoring.
