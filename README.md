
# Windows Server Patching

## Overview

This repository documents Windows Server patch management, update deployment, post-patching verification, and troubleshooting procedures.

The purpose is to provide a structured reference for maintaining Windows Server systems through a controlled and documented patching process.

The documentation focuses on general administration practices and illustrative examples rather than production-specific configurations.

## Technologies and Tools

- Microsoft Windows Server
- Windows Update
- Windows Server Update Services (WSUS)
- Microsoft Update Catalog
- PowerShell
- Windows Event Viewer
- Services Console
- Remote Desktop
- Enterprise patch management platforms

## Patching Objectives

Windows Server patching helps to:

- Address known security vulnerabilities
- Improve operating system stability
- Apply supported security and quality updates
- Maintain compliance with organizational patching requirements
- Reduce exposure to known operating system issues
- Support reliable server operations

## Standard Patching Workflow

A typical patching process includes:

1. Identify servers scheduled for maintenance.
2. Review the applicable updates and security advisories.
3. Confirm the approved maintenance window.
4. Check server health and available disk space.
5. Verify that required backups and recovery procedures are available.
6. Review application dependencies and service requirements.
7. Deploy approved updates using the designated patch management method.
8. Restart the server if required.
9. Verify that the server and critical services are operating normally.
10. Document the patching outcome and any issues.

## Pre-Patching Checklist

Before installing updates, review the following:

| Check | Purpose |
|---|---|
| Server availability | Confirm the server is reachable |
| Operating system version | Identify the installed Windows Server version |
| Disk capacity | Confirm sufficient free space |
| Backup status | Verify a suitable recovery point exists |
| Pending restart | Identify whether a reboot is already required |
| Service status | Record the status of critical services |
| Maintenance approval | Confirm authorization to proceed |
| Application dependencies | Identify services affected by downtime |
| Recovery plan | Confirm rollback or recovery procedures |

## Windows Server Information

The following PowerShell commands can be used to review basic operating system information.

### Check Operating System Details

```powershell
Get-ComputerInfo |
    Select-Object WindowsProductName, WindowsVersion, OsBuildNumber
```

### Check Last Boot Time

```powershell
Get-CimInstance Win32_OperatingSystem |
    Select-Object LastBootUpTime
```

### Check Available Disk Space

```powershell
Get-PSDrive -PSProvider FileSystem
```

These commands are intended for general system inspection.

## Reviewing Installed Updates

### List Installed Hotfixes

```powershell
Get-HotFix |
    Sort-Object InstalledOn -Descending
```

### Review Recent Hotfixes

```powershell
Get-HotFix |
    Sort-Object InstalledOn -Descending |
    Select-Object -First 10
```

The output can help identify updates reported through the Windows hotfix inventory.

Note: `Get-HotFix` does not provide a complete inventory of every update type.

## Windows Update Services

Windows Server updates may be managed through different methods depending on the environment.

Common methods include:

- Windows Update
- Windows Server Update Services (WSUS)
- Microsoft Configuration Manager
- Microsoft Azure Update Manager
- Enterprise endpoint management platforms

The update method should follow the approved administration and change management process.

## Patch Deployment Procedure

### Step 1: Review Updates

Identify the applicable security and quality updates.

Review available information about:

- Security impact
- Known issues
- Prerequisites
- Restart requirements
- Application compatibility

### Step 2: Prepare the Server

Confirm that:

- A suitable backup is available
- Required services have been reviewed
- Disk space is sufficient
- Maintenance has been approved
- A recovery procedure is documented

### Step 3: Install Approved Updates

Use the organization's authorized update management platform.

Avoid deploying unapproved updates directly to critical production systems.

### Step 4: Restart if Required

Some Windows Server updates require a system restart.

Before restarting:

- Confirm maintenance approval
- Notify affected stakeholders where necessary
- Check application dependencies
- Confirm that service interruption is acceptable

### Step 5: Perform Post-Patching Checks

After the server restarts, confirm:

- The server is reachable
- Required services are running
- Application dependencies are available
- Event logs do not show relevant critical errors
- Installed updates are reported as expected
- Monitoring systems show normal operation

## Post-Patching Verification

### Check System Uptime

```powershell
Get-CimInstance Win32_OperatingSystem |
    Select-Object LastBootUpTime
```

### Check Automatic Services

```powershell
Get-Service |
    Where-Object {
        $_.StartType -eq 'Automatic' -and
        $_.Status -ne 'Running'
    }
```

Note: Some automatically configured services may be stopped by design. Review the result before treating it as an issue.

### Review Recent System Errors

```powershell
Get-WinEvent -FilterHashtable @{
    LogName   = 'System'
    Level     = 2
    StartTime = (Get-Date).AddHours(-24)
} -MaxEvents 20
```

### Review Recent Installed Hotfixes

```powershell
Get-HotFix |
    Sort-Object InstalledOn -Descending |
    Select-Object -First 10
```

## Common Patching Issues

### Updates Fail to Install

Possible causes:

- Insufficient disk space
- Update service problems
- Missing prerequisites
- Corrupted update components
- Network connectivity issues
- Conflicting software

Recommended checks:

1. Review the update error code.
2. Inspect relevant Windows Event Logs.
3. Verify available disk space.
4. Check update-related services.
5. Review Microsoft documentation for the specific error.
6. Retry only when the underlying issue has been addressed.

### Server Has a Pending Restart

A pending restart may affect subsequent update installations.

Recommended actions:

1. Check whether a restart is required.
2. Confirm the approved maintenance window.
3. Review application and service dependencies.
4. Restart through the approved process.
5. Verify the system after startup.

### Services Fail After Patching

Possible causes include:

- Application compatibility problems
- Service dependencies
- Changed system components
- Configuration or permission issues

Recommended actions:

1. Identify affected services.
2. Review service-specific event logs.
3. Check dependencies and startup configuration.
4. Compare the behavior with pre-patching checks.
5. Follow the approved recovery or escalation procedure.

## Patch Management Best Practices

- Use an approved patch management process.
- Test significant changes where practical.
- Maintain current backups and recovery procedures.
- Apply updates during approved maintenance windows.
- Review update prerequisites and known issues.
- Coordinate patching for dependent systems.
- Verify server and application health afterward.
- Record failed updates and remediation actions.
- Monitor systems after maintenance.
- Follow documented change management procedures.

## Example Patching Record

The following is a fictional example.

| Field | Example |
|---|---|
| Server | LAB-SRV-01 |
| Environment | Test |
| Operating System | Windows Server |
| Maintenance Type | Security and quality updates |
| Backup Check | Completed |
| Update Installation | Completed |
| Restart | Completed |
| Service Verification | Passed |
| Monitoring Verification | Passed |
| Final Status | Successful |

This example does not represent an actual production server.

## Troubleshooting Checklist

- [ ] Confirm server connectivity
- [ ] Check operating system version
- [ ] Review disk capacity
- [ ] Verify backup availability
- [ ] Review update installation status
- [ ] Identify update error codes
- [ ] Check pending restart requirements
- [ ] Review Windows Event Logs
- [ ] Verify critical services
- [ ] Confirm application availability
- [ ] Document the final outcome

## Security and Documentation

This repository uses generic administration procedures and illustrative examples.

Do not publish:

- Real production server names
- Internal IP addresses
- Administrator credentials
- Service account passwords
- Internal patching schedules
- Confidential infrastructure documentation
- Sensitive application or network configurations

## Conclusion

Windows Server patching is an important part of maintaining secure and reliable IT infrastructure.

A structured patching process includes preparation, approved deployment, post-update verification, troubleshooting, and documentation.

This repository provides a general technical reference for these activities.
