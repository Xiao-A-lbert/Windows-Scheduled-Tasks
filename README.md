# Windows Scheduled Tasks

<h2>Description</h2>
In this task, I created a scheduled task in my attacker Ubuntu shell then enumerated the task in task scheduler, as well as querying scheduled task in command prompt and pwoershell. I then used autoruns to enumerate previous created persistences. Finally I created a current snapshot baseline with an autoruns modeule in powershell to compare it to a true uncompromised baseline. This comparison enumerated 3 persistences installed into my windows VM.  


<h2>Languages and Utilities Used</h2>

- <b>Windows cmd</b>
- <b>Windows powershell</b>
- <b>linux CLI</b>


<h2>Environments Used </h2>

- <b>Unbuntu</b>
- <b>Windows 10 Enterprise</b> 

<br />
<br />
The command "schtasks /create /tn "SystemCleanup" /tr "C:\users\albert\downloads\notmalware.exe" /sc daily /st 09:00 /ru SYSTEM" creates a scheduled task named "SystemCleanup" that runs daily at 9:00 AM with SYSTEM privileges. Here's a breakdown of the command:
/tn "SystemCleanup": Sets the task name to "SystemCleanup".
/tr "C:\users\albert\downloads\notmalware.exe": Specifies the program to run.
/sc daily: Sets the schedule to run daily.
/st 09:00 : Sets the start time to 9:00 AM.
/ru SYSTEM: Runs the task with SYSTEM account privileges.

![1) admin cmd run notmalware, shell, create schtask](https://github.com/user-attachments/assets/e784d7ce-3e74-4b18-8df9-ba4fa1da71b6)

<br />
<br />
Running tasks schedule shows the newly created SystemCleanup. 

![2) task schedule as admin to see system cleanup](https://github.com/user-attachments/assets/43824cc1-b2bc-465c-bb3f-fee5192dbf2d)

<br />
<br />  
Doubleclicking SystemCleanup shows the triggers, next ru time, last run time, and the action with the file path to notmalwar.exe. 

![3) shows a apth to notmalware](https://github.com/user-attachments/assets/71a1bea1-1180-462a-b5e3-0bc1f0f7da9c)

<br />
<br />
This command "schtasks /query /tn "SystemCleanup" /v /fo list" will display detailed information about the "SystemCleanup" task in a list format. The parameters used are:
/query: Requests information about scheduled tasks.
/tn "SystemCleanup": Specifies the task name to query.
/v: Requests verbose output, providing more detailed information.
/fo list: Sets the output format to a list for easier reading.

![4) admin cmd schtasks query verbose in list format](https://github.com/user-attachments/assets/e3a2a04a-ee29-41ba-9086-fa07a9637b32)

<br />
<br />
In Autoruns, the scheduled tasks tab will show the SystemCleanup tasks as an ApacheBench CLU in red with a path to notmalware.exe.

![5) autoruns shows foreign schtask](https://github.com/user-attachments/assets/d9125cd1-8422-47f3-afda-3830d14141ac)

<br />
<br />
In Autoruns, the Services tab will show the BackupServcice tasks as an ApacheBench CLU in red with a path to notmalware.exe.

![6) autoruns shows foreign services](https://github.com/user-attachments/assets/8700ad2a-32d2-4afe-944e-0955c7243d3d)

<br />
<br />
In windows powershell, I deleted the CurrentState.ps1. 

![7) deleting currentstate snapchot](https://github.com/user-attachments/assets/c42f1d92-38c8-4a1e-8aa6-025344e7473f)

<br />
<br />
I ran the Get-PSAutorun -VerifyDigitalSignature |
Where { -not($_.isOSbinary)} |
New-AutoRunsBaseLine -Verbose -FilePath .\CurrentState.ps1 to create an updated snapshot as CurrentState.ps1.

![8) creating a new currentstate snapshot with autoruns script in powershell](https://github.com/user-attachments/assets/37b331e6-0c06-415b-934d-cad9566bccad)

<br />
<br />
Running "compare-autorunbaseline -referencebaselinefile .\baseline.ps1 -differencebaselinefile .\currentstate.ps1 -verbose" shows the NotABackdoor backdoor in the HKCU\..\Run registry, the BackupService in the HKLM\..\Services registry, and the SystemCleanup scheduled tasks . 

![9) identify malware persistence compared to baseline](https://github.com/user-attachments/assets/fc8f93a2-4045-48e0-9f16-bd610940d6b9)

<br />
<br />
