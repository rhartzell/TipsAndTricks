# PowerShell Tips

Some very handy powershell scripts to get the job done
A collection of scripts and commands to troubleshoot issues very quickly.

## Curl equivalent in power shell
Basic GET
```
$custLogins = invoke-webrequest http://localhost:8000/MyService/api/v1/cust/2009/getCustomerLogins
```
more involved, hitting an API with basic auth header.
```
$username = "username";
$password = ConvertTo-SecureString –String "password" –AsPlainText -Force
$credential = New-Object –TypeName "System.Management.Automation.PSCredential" –ArgumentList $username, $password

$base64AuthInfo = [Convert]::ToBase64String([Text.Encoding]::ASCII.GetBytes(("{0}:{1}" -f $username,$password)))

$getProjectUri = "yourUri"
Invoke-RestMethod -Method Get -Uri $getProjectUri -Headers @{Authorization = "Basic $base64AuthInfo" } -Credential $credential -ContentType "application/json"
```
## Tailing log files
```
get-content PspSchedulingService-20230126.log -wait
```
## Grep Logs 
Unix/Linux has the very handy GREP function and the piping ability allows you to crunch through thousands of logs very very quickly.  As it turns out PowerShell has the very same power.  Some might even argue MORE power.

the Select-String command can be abbreviated as sls

## Find a string in a file or files
```
select-string "unable to to get chat status" -Path web.qa-*.log
```

## Use regular expressions to find in a file or files.
The following code will find email addresses in a file.
```
select-string -path *.txt20151201 -pattern "\b[A-Z0-9._%+-]+@[A-Z0-9.-]+\.[A-Z]{2,4}\b”
```

## Show lines of context for a string found in a file or files
```
select-string "unable to to get chat status" -Path web.qa-20190211.log -Context 5
```

## Count occurrences of text in a file or files.
```
PS C:\logs> select-string "unable to to get chat status" -Path web.qa-*.log | measure-object -line
 
Lines Words Characters Property
----- ----- ---------- --------
  260
```

## Deployment and Web Administration
List websites 
The script below lists the physical path of each web application in IIS. There are many other properties that can also be listed.
```
Import-Module WebAdministration
 
# path to IIS website
$iisPath = "IIS:\Sites\Default Web Site\"
 
foreach ($item in Get-Childitem $iispath | where {$_.Schema.Name -eq 'Application'})
{
    Write-Host $item.PhysicalPath
}
```

## Other useful tidbits
Set a timer in a script
```$ElapsedTime = [System.Diagnostics.Stopwatch]::StartNew()
  
write-host "Script Started at $(get-date)"
for ($i=1; $i -lt 10; $i++) {
    get-process | out-null
    sleep 1
    write-host "   Elapsed Time: $($ElapsedTime.Elapsed.ToString())"
    }
write-host "Script Ended at $(get-date)"
write-host "Total Elapsed Time: $($ElapsedTime.Elapsed.ToString())"
```

## Get Windows Processes
```
(Get-Process).Id| ForEach-Object{
    $a = @{}
    $a.PID= $_
    $a.SessionID =$(Get-Process -Id $_).SI
    $a.ProcessName= $(Get-Process -Id $_).ProcessName
    $a.ProcessDesc=$(Get-Process -Id $_).Description
    $a.NetObjCount= @(netstat -ano | Where-Object{$_ -match $a.PID}).count
    Write-Host "Process ID:" $a.SessionID "Process Name:" $a.ProcessName "Process Desc:" $a.ProcessDesc
}
 ```

## This also can be done like the Unix "Top" command
```
While(1) {ps | sort -des cpu | select -f 15 | ft -a; sleep 1; cls}
```

## Count Files in a directory
```
Get-ChildItem -Recurse -File | Measure-Object | %{$_.Count}
```
## Remove all GIT branches except master
```
git branch |`
  %{ $_.Trim() } |`
  ?{ $_ -ne 'master' -and $_ -ne '* master' } |`
  %{ git branch -D $_ }
```
