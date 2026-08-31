# Timeline 

## Investigation Timeline

| Time | Source | Activity | Significance |
|---|---|---|---|
| 06:35 | File System | `Source` and `Staging` directories created | Investigation workspace established |
| 06:45:43 | File System | `project-notes.txt` created | Controlled source artifact |
| 06:46:39 | File System | `sample-data.txt` created | Controlled source artifact |
| 06:48:06 | File System | `employee-list.txt` created in Source | Controlled source artifact |
| Before staging | Hash | Source file SHA-256 calculated | Baseline integrity established |
| 06:55:41 | File System | `employee-list.txt` appeared in staging | Collection/staging evidence |
| 06:56:49 | File System | `project-notes.txt` appeared in staging | Collection/staging evidence |
| 07:05:05 | File System | `sample-data.txt` appeared in staging | Collection/staging evidence |
| 07:10:32 | File System | Archive created | Archive preparation |
| 07:11:23 | File System | Archive last modified | Archive preparation completed |
| After archive | Hash | Archive SHA-256 calculated | Archive identity established |
| After archive | File System | Archive expanded | Contents verified |
| 07:37 | File System | TransferSimulation directory created | Local transfer simulation |
| After 07:37 | File System | Archive copied to TransferSimulation | Local transfer simulation |
| After transfer | Hash | Archive SHA-256 recalculated | Integrity verified |
| During investigation | Sysmon Event ID 1 | PowerShell process events observed | Process telemetry |
| During investigation | Sysmon Event ID 3 | Network activity observed | Network context |
| During investigation | Event ID 11 | Targeted file search | No relevant result |
| During investigation | Event ID 4104 | Targeted PowerShell search | No relevant result |
| During investigation | Event ID 4688 | PowerShell process search | No relevant result |
| Final | File System | Laboratory directory removed | Cleanup completed |

