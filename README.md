# Windows DFIR Lab 67 — Staging to Exfiltration Investigation

## Overview

This lab investigates the relationship between data collection, staging, archive preparation, and possible exfiltration from a Windows DFIR perspective.

The exercise used only harmless laboratory text files. Three controlled files were created under a dedicated source directory and their metadata and SHA-256 hashes were collected. The files were then copied into a separate staging directory and their hashes were compared to the originals.

The staged files were compressed into `staged-data.zip`. The archive was hashed and expanded successfully to verify its contents. A local `TransferSimulation` directory was then created and the archive was copied there to represent a controlled transfer step without sending the data outside the workstation.

The investigation also reviewed PowerShell history, Sysmon process creation, Sysmon network connections, PowerShell Event ID 4104, Sysmon Event ID 11, and Security Event ID 4688.

The available evidence established controlled staging and local transfer simulation. It did not establish actual external data exfiltration.

## Investigation Objectives

- Establish the original source files and their metadata.
- Calculate SHA-256 hashes before staging.
- Copy controlled data into a dedicated staging location.
- Verify that staged files match the originals.
- Calculate the total size of staged data.
- Create and analyze a controlled archive.
- Verify archive contents after extraction.
- Track the archive using its SHA-256 hash.
- Simulate a local transfer without sending data externally.
- Review process, file, PowerShell, and network telemetry.
- Examine PowerShell command history for supporting evidence.
- Distinguish collection, staging, archive preparation, transfer simulation, and actual exfiltration.
- Document troubleshooting issues encountered during the exercise.
- Determine whether the available evidence supports confirmed exfiltration.

## Investigation Scenario

A Windows workstation is being assessed for possible data staging and exfiltration activity. Several files are gathered into a common directory and compressed into an archive, creating a pattern that could be associated with preparation for data transfer.

The analyst needs to determine whether the observed activity represents ordinary file handling, controlled staging, or preparation for an actual exfiltration event.

The investigation therefore examines:

- Source files and their metadata.
- File hashes before and after copying.
- Staging directory contents.
- Archive creation and verification.
- Transfer simulation.
- Process telemetry.
- PowerShell activity.
- Network telemetry.
- Command history.

The exercise uses only dummy laboratory data and performs the transfer simulation locally. No external destination is used.

## Lab Environment

| Component | Details |
|---|---|
| Operating System | Windows |
| Investigation Type | Windows DFIR |
| Source Directory | `C:\StagingExfilLab\Source` |
| Staging Directory | `C:\StagingExfilLab\Staging` |
| Archive | `C:\StagingExfilLab\staged-data.zip` |
| Transfer Simulation | `C:\StagingExfilLab\TransferSimulation` |
| Sysmon Event ID 1 | Available |
| Sysmon Event ID 3 | Available |
| Sysmon Event ID 11 | Targeted search returned no relevant result |
| PowerShell Event ID 4104 | Targeted search returned no relevant result |
| Security Event ID 4688 | Search returned no relevant result |
| PowerShell History | Available |

## Controlled Data

The laboratory used three harmless text files:

```text
employee-list.txt
project-notes.txt
sample-data.txt
```

Their contents were controlled laboratory strings and did not contain real sensitive information.

## Source File Metadata

The final source directory contained:

| File | Size | Creation Time | Last Write Time |
|---|---:|---|---|
| `employee-list.txt` | 30 bytes | 31-08-2026 06:48:06 | 31-08-2026 06:48:06 |
| `project-notes.txt` | 41 bytes | 31-08-2026 06:45:43 | 31-08-2026 06:45:43 |
| `sample-data.txt` | 35 bytes | 31-08-2026 06:46:39 | 31-08-2026 06:46:39 |

Total source/staged file size:

```text
106 bytes
```

## Source File Hashes

The SHA-256 hashes collected from the source files were:

```text
employee-list.txt
8EE0018B7174C84E6EB985415500AC90E8854896C40198287E2C3D43AEC1CFED

project-notes.txt
CA2267E594BB6B7F2B9411C5CFA6F17E29BFFBEBBF6B70939C203FC2C69A7400

sample-data.txt
C7CB55985097E062284239CFD948F772F23A43D5305F0B21A34EEC4C4F23EF12
```

## Staging Activity

The three source files were copied into:

```text
C:\StagingExfilLab\Staging
```

The staged files retained the same file sizes and corresponding Last Write times as their source copies.

Their Creation Times reflected the later staging operations.

This provided evidence that the files had been copied into a separate staging location rather than modified in place.

## Archive Creation

The staged files were compressed using:

```powershell
Compress-Archive `
-Path "C:\StagingExfilLab\Staging\*" `
-DestinationPath "C:\StagingExfilLab\staged-data.zip" `
-Force
```

The resulting archive was:

```text
C:\StagingExfilLab\staged-data.zip
```

Observed size:

```text
460 bytes
```

Creation time:

```text
31-08-2026 07:10:32
```

Last Write time:

```text
31-08-2026 07:11:23
```

## Archive SHA-256

The archive SHA-256 was:

```text
785941B735A569DEAE8A755976D0008331A10455E4106B124722A1CA70DC013D
```

This hash provides a stable identifier for the controlled staged archive.

## Archive Verification

The archive was expanded to:

```text
C:\StagingExfilLab\Test-Extraction
```

The extracted contents were:

```text
employee-list.txt
project-notes.txt
sample-data.txt
```

The file sizes matched the staged source files:

```text
employee-list.txt   30 bytes
project-notes.txt   41 bytes
sample-data.txt     35 bytes
```

This confirmed that the archive contained the expected controlled files.

## Transfer Simulation

A local transfer directory was created:

```text
C:\StagingExfilLab\TransferSimulation
```

The archive was copied into that directory:

```text
C:\StagingExfilLab\TransferSimulation\staged-data.zip
```

This was a local-only transfer simulation.

No external host or internet destination was used for the simulated transfer.

## Transfer Hash Verification

The SHA-256 of the transferred archive remained:

```text
785941B735A569DEAE8A755976D0008331A10455E4106B124722A1CA70DC013D
```

The matching hash demonstrated that the locally transferred archive was identical to the original staged archive.

## Sysmon Event ID 1

Sysmon Event ID 1 was queried for:

```text
powershell.exe
StagingExfilLab
```

Three matching process-creation events were returned:

```text
31-08-2026 06:34:47
31-08-2026 06:34:45
31-08-2026 06:34:15
```

These events demonstrate that Sysmon process telemetry was available during the exercise.

## PowerShell Event ID 4104

The targeted search used:

```text
StagingExfilLab
Compress-Archive
Copy-Item
employee-list.txt
project-notes.txt
sample-data.txt
```

No relevant Event ID 4104 result was returned.

Therefore, the supplied evidence did not establish script-block telemetry for the staging commands.

## Sysmon Event ID 11

Sysmon Event ID 11 was searched for:

```text
StagingExfilLab
staged-data.zip
employee-list.txt
project-notes.txt
sample-data.txt
```

No relevant result was returned.

Therefore, the file-creation search did not provide additional supporting telemetry for the controlled staging artifacts.

## Security Event ID 4688

Security Event ID 4688 was searched for:

```text
powershell.exe
```

No relevant result was returned.

Therefore, Security process-creation telemetry did not provide additional supporting evidence for the staging workflow.

## Sysmon Event ID 3

Sysmon Event ID 3 was available and returned numerous network connection events.

The evidence showed network activity throughout the period, but no connection was specifically attributed to the controlled archive or a data exfiltration process.

Therefore, the network telemetry did not establish external exfiltration.

## PowerShell History

Current-session PowerShell history contained the laboratory workflow, including:

- Directory creation.
- Controlled file creation.
- Metadata collection.
- SHA-256 hashing.
- File copying.
- Archive creation.
- Archive extraction.
- Event-log searches.
- Transfer simulation.

This provided a useful record of the controlled actions.

## Cleanup

The entire laboratory directory was removed:

```powershell
Remove-Item `
"C:\StagingExfilLab" `
-Recurse `
-Force
```

The final verification returned:

```text
False
```

for:

```powershell
Test-Path "C:\StagingExfilLab"
```

This confirmed cleanup of the controlled laboratory artifacts.

## Troubleshooting Findings

Two minor issues occurred during the exercise.

The first was an incorrect path:

```text
C:\StagingRxfilLab
```

instead of:

```text
C:\StagingExfilLab
```

The copy command failed until the correct path was supplied.

The second issue was a temporary typo:

```text
Get-Child-Item
```

instead of:

```text
Get-ChildItem
```

The corrected command completed successfully.

These issues were documented as operator errors and did not affect the final investigation findings.

## Evidence Correlation

The investigation followed:

```text
Source Files
     |
     v
Metadata + SHA-256
     |
     v
File Collection
     |
     v
Staging Directory
     |
     v
Hash Verification
     |
     v
Archive Creation
     |
     v
Archive Hash
     |
     v
Archive Extraction
     |
     v
Local Transfer Simulation
     |
     v
Process / PowerShell / Network Telemetry
     |
     v
Cleanup
```

## Staging vs Exfiltration

The investigation demonstrated the difference between preparation and confirmed data transfer.

```text
Collection
    ↓
Staging
    ↓
Archive
```

was established.

A local transfer simulation was also established:

```text
Archive
    ↓
TransferSimulation
```

However, there was no evidence showing:

```text
Staged Data
    ↓
External Network Destination
```

Therefore, actual exfiltration was not established.

## Key Findings

- Three harmless laboratory files were created.
- File metadata was collected.
- SHA-256 hashes were calculated.
- The files were copied into a staging directory.
- The staged data totaled 106 bytes.
- The staged files retained their corresponding source hashes.
- The files were compressed into a 460-byte ZIP archive.
- The archive SHA-256 was recorded.
- The archive was successfully extracted and verified.
- The archive was copied to a local transfer-simulation directory.
- The archive hash remained unchanged during the local transfer.
- Sysmon Event ID 1 was available.
- Sysmon Event ID 3 was available.
- Targeted Sysmon Event ID 11 search returned no relevant result.
- Targeted PowerShell Event ID 4104 search returned no relevant result.
- Security Event ID 4688 returned no relevant result.
- PowerShell history preserved the controlled workflow.
- The laboratory artifacts were successfully removed.
- No external exfiltration was established.

## DFIR Interpretation

The presence of files in a staging directory or the creation of an archive does not by itself prove data theft.

An analyst should distinguish:

```text
Collection
```

from:

```text
Staging
```

and from:

```text
Exfiltration
```

A stronger exfiltration finding would require evidence of a transfer to a destination outside the endpoint, together with process, account, timing, and network correlation.

## MITRE ATT&CK Relevance

Potentially relevant techniques in a real incident include:

**T1074 — Data Staged**

Relevant when collected data is staged before exfiltration.

**T1560 — Archive Collected Data**

Relevant when collected data is compressed or archived in preparation for transfer.

**T1041 — Exfiltration Over C2 Channel**

Relevant only when evidence establishes that collected data was actually transferred over an attacker-controlled command-and-control channel.

This controlled lab establishes staging and archive preparation but does not establish malicious use of these techniques.

## Evidence Limitations

- No external transfer was performed.
- No external destination was identified.
- Sysmon Event ID 11 did not provide relevant results.
- PowerShell Event ID 4104 did not provide relevant staging results.
- Security Event ID 4688 did not provide relevant process results.
- Sysmon Event ID 3 contained general network activity but did not establish archive exfiltration.
- The lab used dummy data only.

## Conclusion

The investigation successfully demonstrated the forensic distinction between data staging and confirmed exfiltration.

Three controlled files were created, hashed, copied into a staging directory, and compressed into an archive. The archive was then transferred only to a local simulation directory, and its SHA-256 remained unchanged.

Although process and network telemetry were available, the supplied evidence did not establish an external network transfer of the staged data. The correct assessment is therefore **controlled staging and local transfer simulation, with no confirmed exfiltration**.

## Skills Demonstrated

- Windows DFIR
- Data Staging Investigation
- Archive Analysis
- SHA-256 Integrity Verification
- File Metadata Analysis
- PowerShell Investigation
- PowerShell History Analysis
- Sysmon Event ID 1
- Sysmon Event ID 3
- Sysmon Event ID 11
- Security Event ID 4688
- PowerShell Event ID 4104
- Exfiltration Evidence Assessment
- Timeline Reconstruction
- False-Positive and Telemetry Analysis
