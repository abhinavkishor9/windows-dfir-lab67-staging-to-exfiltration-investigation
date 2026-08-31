# Troubleshooting Notes 

## 1. Incorrect Source Path

### Problem

The following path was initially used:

```text
C:\StagingRxfilLab\Source\project-notes.txt
```

PowerShell returned:

```text
Cannot find path
```

### Cause

The directory name contained a typo:

```text
StagingRxfilLab
```

instead of:

```text
StagingExfilLab
```

### Resolution

The correct command was:

```powershell
Copy-Item "C:\StagingExfilLab\Source\project-notes.txt" `
"C:\StagingExfilLab\Staging\"
```

The copy then completed successfully.

### DFIR Lesson

Path accuracy is important when reconstructing file movement.

A typo can make an existing artifact appear missing.

## 2. Temporary Command Typo

### Problem

The following command was entered:

```text
Get-Child-Item
```

PowerShell reported that the term was not recognized.

### Resolution

The correct command was:

```powershell
Get-ChildItem "C:\StagingExfilLab\Test-Extraction" -Recurse
```

This successfully displayed the extracted files.

### DFIR Lesson

Small command syntax errors should be documented separately from actual investigation failures.

## 3. Employee File Initially Created in the Wrong Location

An `employee-list.txt` file was initially created under:

```text
C:\StagingExfilLab\
```

before the intended source copy was created under:

```text
C:\StagingExfilLab\Source\
```

The final source directory contained the expected file.

The earlier root-level artifact was not used as the final source dataset.

### DFIR Lesson

When multiple similarly named artifacts exist, document which copy represents the intended evidence set.

## 4. Sysmon Event ID 11 Returned No Targeted Result

### Observation

The search for:

```text
StagingExfilLab
staged-data.zip
employee-list.txt
project-notes.txt
sample-data.txt
```

returned no relevant Event ID 11 result.

### Interpretation

File-creation telemetry did not provide additional evidence for the controlled artifacts.

### DFIR Lesson

A missing event does not mean the file did not exist. Direct filesystem evidence and historical event telemetry are separate sources.

## 5. PowerShell Event ID 4104 Returned No Targeted Result

### Observation

The targeted search for:

```text
StagingExfilLab
Compress-Archive
Copy-Item
employee-list.txt
project-notes.txt
sample-data.txt
```

returned no result.

### Interpretation

No relevant Script Block Logging evidence was identified.

### DFIR Lesson

Script Block Logging availability for one activity does not guarantee that every command will produce a matching result.

## 6. Security Event ID 4688 Returned No Result

### Observation

The query for:

```text
powershell.exe
```

returned no relevant Event ID 4688 result.

### Interpretation

Security process-creation telemetry did not provide additional support for the exercise.

## 7. Sysmon Event ID 1 Was Available

### Observation

Three PowerShell-related process-creation events were returned:

```text
31-08-2026 06:34:15
31-08-2026 06:34:45
31-08-2026 06:34:47
```

### Interpretation

Sysmon process telemetry was functioning.

### Limitation

The supplied event output did not expose enough detail to attribute these events directly to malicious staging.

## 8. Sysmon Event ID 3 Was Available

### Observation

Numerous network connection events were returned.

### Interpretation

Network telemetry was active.

### Limitation

No external transfer of the controlled archive was established.

Network activity alone does not prove exfiltration.

## 9. Archive Verification

### Observation

`Expand-Archive` completed successfully.

The extracted directory contained:

```text
employee-list.txt
project-notes.txt
sample-data.txt
```

### Interpretation

The archive was valid and contained the expected controlled files.

## 10. Archive Integrity Verification

The original archive hash was:

```text
785941B735A569DEAE8A755976D0008331A10455E4106B124722A1CA70DC013D
```

The local transfer-simulation copy returned the same hash.

### Interpretation

The archive remained unchanged during the simulated transfer.

## 11. Staging vs Exfiltration Confusion

### Problem

It can be tempting to classify archive creation as exfiltration.

### Correct Interpretation

The lab established:

```text
Files
 ↓
Staging
 ↓
Archive
 ↓
Local Transfer Simulation
```

It did not establish:

```text
Archive
 ↓
External Destination
```

### DFIR Lesson

Archive creation is preparation for transfer, not proof that a transfer occurred.

## 12. Safe Laboratory Boundary

The exercise intentionally did not:

- Upload the archive to the internet.
- Send the archive to an external server.
- Use cloud storage.
- Use an attacker-controlled destination.
- Attempt covert transfer techniques.

This preserved the investigation as a safe local DFIR exercise.

