# Investigation Notes — Lab 67 Staging to Exfiltration Investigation

## Investigation Overview

The investigation examined a controlled sequence in which harmless files were created, staged, compressed, locally transferred, and then removed.

The purpose was to determine which parts of a potential collection-to-exfiltration chain could be established from endpoint evidence.

## Source Directory

The source directory was:

```text
C:\StagingExfilLab\Source
```

It was created on:

```text
31-08-2026 06:35
```

## Controlled Files

The final source directory contained:

```text
employee-list.txt
project-notes.txt
sample-data.txt
```

Observed metadata:

```text
employee-list.txt
Length: 30 bytes
CreationTime: 31-08-2026 06:48:06
LastWriteTime: 31-08-2026 06:48:06

project-notes.txt
Length: 41 bytes
CreationTime: 31-08-2026 06:45:43
LastWriteTime: 31-08-2026 06:45:43

sample-data.txt
Length: 35 bytes
CreationTime: 31-08-2026 06:46:39
LastWriteTime: 31-08-2026 06:46:39
```

## Source SHA-256

```text
employee-list.txt
8EE0018B7174C84E6EB985415500AC90E8854896C40198287E2C3D43AEC1CFED

project-notes.txt
CA2267E594BB6B7F2B9411C5CFA6F17E29BFFBEBBF6B70939C203FC2C69A7400

sample-data.txt
C7CB55985097E062284239CFD948F772F23A43D5305F0B21A34EEC4C4F23EF12
```

## Staging

The files were copied into:

```text
C:\StagingExfilLab\Staging
```

The staged directory contained:

```text
employee-list.txt
project-notes.txt
sample-data.txt
```

Total size:

```text
106 bytes
```

## Staged File Metadata

The staged files had:

```text
employee-list.txt
Length: 30 bytes
CreationTime: 31-08-2026 06:55:41
LastWriteTime: 31-08-2026 06:48:06

project-notes.txt
Length: 41 bytes
CreationTime: 31-08-2026 06:56:49
LastWriteTime: 31-08-2026 06:45:43

sample-data.txt
Length: 35 bytes
CreationTime: 31-08-2026 07:05:05
LastWriteTime: 31-08-2026 06:46:39
```

The later Creation Times are consistent with copied artifacts being created in the staging directory.

## Archive Creation

The staged files were compressed into:

```text
C:\StagingExfilLab\staged-data.zip
```

Archive size:

```text
460 bytes
```

Creation Time:

```text
31-08-2026 07:10:32
```

Last Write Time:

```text
31-08-2026 07:11:23
```

## Archive Hash

SHA-256:

```text
785941B735A569DEAE8A755976D0008331A10455E4106B124722A1CA70DC013D
```

## Archive Verification

The ZIP was expanded into:

```text
C:\StagingExfilLab\Test-Extraction
```

The extracted files were:

```text
employee-list.txt
project-notes.txt
sample-data.txt
```

Their sizes matched the staged files.

This confirmed that the archive contained the expected controlled data.

## Transfer Simulation

The directory:

```text
C:\StagingExfilLab\TransferSimulation
```

was created at:

```text
31-08-2026 07:37
```

The staged archive was copied into that directory.

This represented a local transfer simulation only.

## Transfer Integrity

The SHA-256 of the transferred archive remained:

```text
785941B735A569DEAE8A755976D0008331A10455E4106B124722A1CA70DC013D
```

This confirmed that the local transfer did not alter the archive.

## Process Telemetry

Sysmon Event ID 1 returned matching PowerShell-related events:

```text
31-08-2026 06:34:15
31-08-2026 06:34:45
31-08-2026 06:34:47
```

These records demonstrate that Sysmon process-creation telemetry was functioning.

The supplied event descriptions did not provide enough information to independently establish a malicious staging process.

## PowerShell Event ID 4104

A targeted search for:

```text
StagingExfilLab
Compress-Archive
Copy-Item
employee-list.txt
project-notes.txt
sample-data.txt
```

returned no result.

Therefore, no relevant Script Block Logging evidence was established.

## Sysmon Event ID 11

A targeted file-creation query for:

```text
StagingExfilLab
staged-data.zip
employee-list.txt
project-notes.txt
sample-data.txt
```

returned no result.

Therefore, Event ID 11 did not provide additional supporting evidence for the controlled artifacts.

## Security Event ID 4688

Security Event ID 4688 was searched for:

```text
powershell.exe
```

No result was returned.

Therefore, 4688 did not provide supporting process evidence for the exercise.

## Sysmon Event ID 3

Sysmon Event ID 3 was available.

Numerous network connection events were observed during the period, including activity around the transfer simulation.

However, the available data did not establish a connection from the archive to an external destination.

Therefore, actual exfiltration was not established.

## PowerShell History

Current PowerShell history contained the controlled investigation workflow.

The history included:

```text
Directory creation
File creation
Metadata collection
Hash collection
Copy-Item
Compress-Archive
Expand-Archive
Event-log searches
Transfer simulation
```

This provided an additional record of the laboratory actions.

## Troubleshooting

### Incorrect Source Path

The command initially referenced:

```text
C:\StagingRxfilLab\Source\project-notes.txt
```

The correct path was:

```text
C:\StagingExfilLab\Source\project-notes.txt
```

The command succeeded after correction.

### Command Typo

An extraction verification command was initially entered as:

```text
Get-Child-Item
```

The correct command was:

```text
Get-ChildItem
```

The corrected command successfully displayed the extracted files.

## Cleanup

The lab directory was removed with:

```powershell
Remove-Item `
"C:\StagingExfilLab" `
-Recurse `
-Force
```

The cleanup was verified with:

```powershell
Test-Path "C:\StagingExfilLab"
```

Result:

```text
False
```

## Evidence Correlation

```text
Controlled Files
      |
      v
File Metadata
      |
      v
SHA-256
      |
      v
Copy to Staging
      |
      v
Hash Comparison
      |
      v
Archive
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
Process / Network Review
      |
      v
Cleanup
```

## Findings

1. Three controlled files were created.
2. Their metadata was recorded.
3. Their SHA-256 hashes were calculated.
4. The files were copied into a staging directory.
5. The staged data totaled 106 bytes.
6. The files were compressed into a ZIP archive.
7. The archive size was 460 bytes.
8. The archive SHA-256 was recorded.
9. The archive was successfully extracted.
10. The archive was copied to a local transfer-simulation directory.
11. The archive hash remained unchanged.
12. Sysmon Event ID 1 was available.
13. Sysmon Event ID 3 was available.
14. Sysmon Event ID 11 produced no relevant targeted result.
15. PowerShell Event ID 4104 produced no relevant targeted result.
16. Security Event ID 4688 produced no relevant result.
17. PowerShell history preserved the controlled activity.
18. The lab artifacts were successfully removed.
19. No external data exfiltration was established.

## Analyst Assessment

The evidence clearly demonstrates:

```text
Collection
    ↓
Staging
    ↓
Archive Preparation
    ↓
Local Transfer Simulation
```

It does not demonstrate:

```text
External Exfiltration
```

The correct conclusion is therefore that the exercise successfully simulated the preparation and transfer stages without performing actual exfiltration.
