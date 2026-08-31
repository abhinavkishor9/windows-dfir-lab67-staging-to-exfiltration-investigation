# Timeline — Lab 67 Staging to Exfiltration Investigation

## Timeline Purpose

This timeline documents the creation of controlled data, staging, hashing, archive preparation, local transfer simulation, telemetry review, troubleshooting, and cleanup.

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

## Phase 1 — Environment Initialization

### 06:35 — Directory Creation

The following directories were created:

```text
C:\StagingExfilLab\Source
C:\StagingExfilLab\Staging
```

This established the controlled workspace.

## Phase 2 — Controlled Data Creation

### 06:45:43

`project-notes.txt` was created.

### 06:46:39

`sample-data.txt` was created.

### 06:48:06

`employee-list.txt` was created in the intended Source directory.

The three files contained only laboratory data.

## Phase 3 — Integrity Collection

SHA-256 hashes were collected before staging.

The hashes were:

```text
employee-list.txt
8EE0018B7174C84E6EB985415500AC90E8854896C40198287E2C3D43AEC1CFED

project-notes.txt
CA2267E594BB6B7F2B9411C5CFA6F17E29BFFBEBBF6B70939C203FC2C69A7400

sample-data.txt
C7CB55985097E062284239CFD948F772F23A43D5305F0B21A34EEC4C4F23EF12
```

## Phase 4 — Staging

The source files were copied into the Staging directory.

Observed creation times in the staging directory included:

```text
06:55:41
06:56:49
07:05:05
```

The total staged file size was:

```text
106 bytes
```

The corresponding source and staged files retained matching content hashes.

## Phase 5 — Archive Preparation

### 07:10:32

The archive:

```text
C:\StagingExfilLab\staged-data.zip
```

was created.

Size:

```text
460 bytes
```

### 07:11:23

The archive's Last Write Time was recorded.

### Archive Hash

The SHA-256 was:

```text
785941B735A569DEAE8A755976D0008331A10455E4106B124722A1CA70DC013D
```

## Phase 6 — Archive Verification

The archive was expanded to:

```text
C:\StagingExfilLab\Test-Extraction
```

The extraction produced:

```text
employee-list.txt
project-notes.txt
sample-data.txt
```

The expected file sizes were preserved.

## Phase 7 — Transfer Simulation

### 07:37

The directory:

```text
C:\StagingExfilLab\TransferSimulation
```

was created.

The archive was copied there.

The transferred archive retained the same SHA-256:

```text
785941B735A569DEAE8A755976D0008331A10455E4106B124722A1CA70DC013D
```

This established local transfer integrity.

## Phase 8 — Process Telemetry

Sysmon Event ID 1 returned PowerShell process-create events:

```text
31-08-2026 06:34:15
31-08-2026 06:34:45
31-08-2026 06:34:47
```

These events established that process telemetry was available.

## Phase 9 — PowerShell Telemetry

A targeted Event ID 4104 search returned no relevant results.

Therefore, the staging and archive commands were not independently confirmed through the supplied Script Block Logging search.

## Phase 10 — File Creation Telemetry

A targeted Sysmon Event ID 11 search returned no relevant result.

Therefore, Event ID 11 did not provide additional support for the controlled file-creation timeline.

## Phase 11 — Security Telemetry

Security Event ID 4688 was searched for PowerShell.

No relevant event was returned.

## Phase 12 — Network Telemetry

Sysmon Event ID 3 produced numerous network connection events.

No evidence in the supplied results established the archive being sent to an external destination.

## Phase 13 — Troubleshooting

An incorrect path was first used:

```text
C:\StagingRxfilLab
```

The correct path:

```text
C:\StagingExfilLab
```

was then used successfully.

A temporary typo in `Get-Child-Item` was also corrected to `Get-ChildItem`.

## Phase 14 — Cleanup

The entire laboratory directory was removed:

```powershell
Remove-Item `
"C:\StagingExfilLab" `
-Recurse `
-Force
```

Verification:

```powershell
Test-Path "C:\StagingExfilLab"
```

Result:

```text
False
```

## Final Evidence Chain

```text
Controlled Source Files
        |
        v
Metadata
        |
        v
SHA-256
        |
        v
Copy to Staging
        |
        v
Hash Verification
        |
        v
Archive Creation
        |
        v
Archive SHA-256
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

## Final Evidence Summary

| Evidence Source | Result |
|---|---|
| Source files | 3 controlled files |
| Total staged size | 106 bytes |
| Archive size | 460 bytes |
| Source hashes | Recorded |
| Archive hash | Recorded |
| Archive extraction | Successful |
| Local transfer | Successful |
| Transfer hash | Matched original |
| Sysmon Event ID 1 | Available |
| Sysmon Event ID 3 | Available |
| Sysmon Event ID 11 | No relevant targeted result |
| PowerShell Event ID 4104 | No relevant targeted result |
| Security Event ID 4688 | No relevant result |
| PowerShell history | Available |
| External transfer | Not established |
| Cleanup | Successful |

## Final Assessment

The timeline establishes:

```text
Collection
    ↓
Staging
    ↓
Archive Preparation
    ↓
Local Transfer Simulation
```

It does not establish:

```text
External Exfiltration
```

## Investigation Conclusion

The laboratory successfully demonstrated how an analyst can reconstruct a potential staging-to-exfiltration chain while keeping the entire exercise local.

The evidence confirms that controlled data was staged, archived, and locally transferred. Network telemetry was available, but no external destination or confirmed transfer of the staged archive was established.

The appropriate DFIR conclusion is:

> Controlled data staging and archive preparation were demonstrated, but actual external data exfiltration was not confirmed.
