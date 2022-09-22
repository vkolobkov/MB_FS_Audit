# MB_FS_Audit

Created using VisualStudio 2022 for .Net Core 6

Requirements: Admin privilege to modify Audit policy.

To install service run "Malwarebytes.Audit.FileSystem.Service.exe -install" (dash is optional).
Creates service with the name "MB_FS_Audit" and start it afterwards.
For uninstallation run "Malwarebytes.Audit.FileSystem.Service.exe -uninstall".
Stops the "MB_FS_Audit" service and delete it.
Don't like the way it was done(.Net Framework has better solution as well as wixToolset)...

Startup logs are written in "configuration.log.txt" in same directory as executable.(Separate log for configuration because at that moment regular config is not loaded yet.)
Application logs are written to "log%date%.txt"(rolling based on day and size) file in same directory as executable.
Audit logs are written to "fs.audit%date%.txt"(rolling based on day and size) file in same directory as executable.
All logs except first one are configured from "appsettings.json".

Folders for audit are configured through file "audit.settings.json". Can be modified at runtime.
Example:
{
  "AuditSettings": {
    "Directories": [
      "C:\\AuditTest\\Test1",
      "C:\\AuditTest\\Test2",
      "C:\\AuditTest\\Test3"
    ]
  }
}

On startup of the service there is a check for Local Audit Policy through "advapi32.dll". In case if audit for "Object Access" Success is switched off, service will try to switch it on. Would be great to restore policy configuration on uninstall, yeah next time.

When audit is applied for the folder new security settings are applied as well, same as above would be great to restore original state.

For auditing fyle system actions FileSystemWatcher is used to gather initial events, afterwards another worker takes those events and aggregate them with data from Windows Security Log. There are some magic numbers with timing, i don't like them but to gather whole info i postpone 1 second in case if something was not written at the time of FSW event.

Well maybe i have forget something not sure, anyway, if there would be any questions - just let me know.
