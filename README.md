# Powershell-Notes



-> Filtering
```
YourCommand | select x,y
```

-> Foreach
```
$folder=Get-ChildItem -Path "C:\" -Recurse
ForEach ($var in $folder) {
	Get-Acl -Path $var
}
```

-> Search for a word inside a file or directory
```
Select-String -Path "C:\file.txt" -Pattern 'searchword'
Select-String -Path "C:\*.txt" -Pattern 'searchword' -CaseSensitive
Get-ChildItem -Path "C:\" -Recurse | Select-String -Pattern 'searchword' 
Select-String -Path "C:\*.txt" -Pattern 'searchword' -CaseSensitive
```

-> Get information about the file
```
Get-Acl -path "C:\file.tx"
Get-Acl -path "C:\file.tx", "C:\file1.tx"
```

-> Create, Rename and Move item
```
New-Item -Path 'C:\tmp' -ItemType Directory
New-Item -Path 'C:\tmp' -ItemType "file"

Rename-Item "C:\tmp" newfolder
Rename-Item -Path "C:\tmp" -NewName "newfolder"

Move-Item -Path "C:\tmp" -Destination "C:\test\newfolder" -forcr
-force to overwrite if exists
```

-> Create a user menu
```
function DisplayMenu {
	Clear-Host
	Write-Host @"
  
	|  POWERSHELL CONSOLE - USER MENU               | 
	|    1) PING                                    |
	|    2) DISPLAY MESSAGE                         |
	|    3) EXIT                                    |

	"@

	$MENU = Read-Host "OPTION"
	Switch ($MENU)
	{
		1 {
		#OPTION1 - PING
		$OPTION1 = Read-Host "HOST"
		Test-Connection -ComputerName $OPTION1
		Start-Sleep -Seconds 2
		DisplayMenu
		}
		2 {
			#OPTION2 - DISPLAY MESSAGE
			$OPTION2 = Read-Host "MESSAGE"
			Write-Host "MESSAGE: $OPTION2"
			Start-Sleep -Seconds 2
			DisplayMenu
		}
		3 {
			#OPTION3 - EXIT
			Write-Host "Bye"
			Break
		}
		default {
			#DEFAULT OPTION
			Write-Host "Option not available"
			Start-Sleep -Seconds 2
			DisplayMenu
			}
	}
}
DisplayMenu
```


-> Get the Hash of a file
```
Get-FileHash "C:\tmp\test.txt"

Get-FileHash -Algorithm MD5 "C:\tmp\test.txt"
Get-FileHash -Algorithm SHA1 "C:\tmp\test.txt"
Get-FileHash -Algorithm SHA256 "C:\tmp\test.txt"
Get-FileHash -Algorithm SHA384 "C:\tmp\test.txt"
Get-FileHash -Algorithm SHA512 "C:\tmp\test.txt"

Get-ChildItem -Path "C:\tmp" | Get-FileHash

Get-FileHash "C:\tmp\*.exe"
Get-ChildItem -Path "C:\tmp" | WHERE {$_.Name -match "txt"}  | Get-FileHash
Get-ChildItem -Path "C:\tmp" | WHERE {$_.Name -match "pdf|txt"}  | Get-FileHash

$HASH="6DABDDF21BB2398EF44943B6095302ECDF536C198B9252DEC719BF6500CF1514"
$FILE="C:\tmp\test.txt"
$FILEHASH=(Get-FileHash $FILE).hash
if ($HASH -eq $FILEHASH) { Write-output "HASH: OK" } else { Write-output "HASH: NOT OK"}

```


-> Add text to a file, if file does not exist, create it 
```
Set-Content C:\tmp\test.txt "Text for testing"
Set-Content C:\tmp\test.txt "Text for testing"
```

-> working with printers
```
Get-Printer | Format-Table -AutoSize

$MYPRINTER = "HPF0497B (HP DeskJet 2700 series)" 
$PRINTERTMP = (Get-CimInstance -ClassName CIM_Printer | WHERE {$_.Name -eq $MYPRINTER}[0]) -> get the default printer
$PRINTERTMP | Invoke-CimMethod -MethodName SetDefaultPrinter | Out-Null -> change the default printer

Get-Content -Path C:\TEST.txt | Out-Printer -> to print a file
Start-Process -FilePath "C:\TEST.pdf" -Verb print -> to print a file
Get-ChildItem -Path C:\tmp\*.pdf | ForEach-Object {Start-Process $_.FullName -Verb Print} -> print every pdf in the directory

$MYFILE = "C:\tmp\MYPDF.pdf"
$MYPRINTER = "Hewlett-Packard HP LaserJet 1022" 
$DEFAULTPRINTER = (Get-CimInstance -ClassName CIM_Printer | WHERE {$_.Default -eq $True}[0])
$PRINTERTMP = (Get-CimInstance -ClassName CIM_Printer | WHERE {$_.NAme -eq $MYPRINTER}[0])
$PRINTERTMP | Invoke-CimMethod -MethodName SetDefaultPrinter | Out-Null
Start-Process -FilePath $MYFILE -Verb print -PassThru -Wait
$DEFAULTPRINTER | Invoke-CimMethod -MethodName SetDefaultPrinter | Out-Null
```


########################
## Allow Runnig Scripts:
```
> Set-ExecutionPolicy [-ExecutionPolicy*] {Unrestricted | RemoteSigned | AllSigned | Restricted | Default | Bypass |Undefined} [[-Scope] {Process | CurrentUser | LocalMachine | UserPolicy | MachinePolicy}] [-Confirm] [-Force][-WhatIf] [<CommonParameters>]

> Get-ExectionPolicy
```

## Output
```
> Write-Host
> Write-Output (aliased to Echo and Write)
> Write-Host "Welcome" -BackgroundColor Black -ForegroundColor Re
> Write-Error
> Write-Verbose 
> Write-Information
> Write-Debug 
> Write-Progress 
> Write-Warning 

$VerbosePreference 
$InformationPreference
$DebugPreference
$ProgressPreference
$WarningPreference
-> Values
"SlientlyContinue" -> Message is not shown and execution continues
"Continue" -> Message is shown and execution continues
"Inquire" -> Message is shown and execution optionally continuess
"Stop" -> Message is shown and execution terminates 

```

## Create an alias
```
> Set-Alias -Name ping -Value Test-Connection
```

## The pipeline
```
> Get-ChildItem | Select-Object Name == gci | Select Name

> Get-ChildItem . | ForEach-Object {
    Write-Host File Found: $_.FullName
} == gci | % { Write-Host File Found $_.FullName }
```

## Comments
```
# Welcome 
<# Welcome to my long text #>
```

## Arrys
```
> $numbers = 1,2,3,4,5; $numbers = $numbers + 6; Write-Host $numbers

> $names = "Ibrahim,Alex,Mike,Kareem"; $name1,$name2,$name3 = $names.split(","); 
Write-Host name1: $name1 - name2: $name2 - name3: $name3
```

## Scope
```
$global:name1 = "Ibrahim"
$local:name2 = "Alex"
$private:name3 = "Mike"
$script:name4 = "Kareem"
```

## Remove a variable
```
Remove-Variable -Name name1 == rv name1 == Remove-Item Variable:\name1
```

## Operators
```
-eq 
-ne
-gt 
-ge 
-lt 
-le
-like
-notlike
-match
-notmatch
-contains
-notcontains
-in
-notin
-and
-or
-xor
-not
!
-replace -> "my text" -replace "my","your" -> output: "your text"
-split -> "ibrahim-Mike" -split "-" -> output:"ibrahim","Mike"
-join -> "ibrahim","Mike" -join "-" -> output:"ibrahim-Mike"
```

## Redirection Operators ????
```
1 for success output
2 for errors output
3 for warning output
4 for verbose output
5 for debug output
6 for information output

ipconfig 1> file.txt
ipconfig 2> file.txt
ipconfig 3> file.txt
ipconfig 4> file.txt
ipconfig 5> file.txt
ipconfig 6> file.txt
ipconfig 1-6>&2 file.txt 
```

## Read Input
```
$name = Read-Host "Enter You Name: "
```

## Filtering - used with the operators
```
Where-Object
where
?
$arrayNames | Where-Object/where/? { $_ -contains "i"}
```

## Sorting
```
Sort-Object
sort
$arrayNames | Sort-Object/sort
```

## Grouping
```
Group-Object
group
```

## Selecting
```
Select-Object
select
```

## ForEach tricks
```
> $nambers = ForEach ($number in 1..20){ Write-Host $number}
> (1..20).ForEach($_ * $_)
```

## ForEach-Object is like ForEach Statement but ForEach-Object take its input from pipeline
```
$ArryNames | ForEach-Object {$_}
```

## Switch tricks
```
switch -CaseSensitive ("condition"){"Ibrahim"{#do_somthing}}
switch -Wildcard ("condition"){"Ibr*"{#do_somthing}}
switch -Regix ("condition"){"^Ibrahim?"{#do_somthing}}
switch -Exect ("condition"){"^Ibrahim?"{#do_somthing}}
```

## Playing with String
```
"Hello `n`r People"
"Your Name is {0}" -f $name
"Hello `n`r People"
@"Text Will Be 		printed   on  screen with the spaces single quation for no expresions"@
@'Text Will Be 		printed	  on  screen with the spaces single quation for expresions'@
```

## Special Character
```
`0 -> Null
`a -> Alert/Beep
`b -> Backspace
`n -> NewLine 
`t -> Horizental Tab
`v -> Vertical Tab
`# -> Escape #
`$ -> Escape $
`` -> Escape `
`' -> Escape '
`" -> Escape "
```

## Hashtable
```
$myHashTable = @{
	key1 = "Value1"
	key2 = "Value2"
}
$myHashTable = $myHashTable + @{key2 = "Value2"}
$myHashTable.add("key4","value4")
$myHashTable.remove("key3","value3")
Write-Host $myHashTable.key2

foreach ($key in $myHashTable.keys){}
foreach ($key in $myHashTable.GetEnumerator()){$_.key1}
```

## Working With Objects
```
Get-Item . | Get-Member
(Get-Item .).GetType()

Get-Item . | Format-List -Property *
```

## Functions
```
function Welcome{
	param(
		[Parameter(Mandatory,Position=0)] -> Means this parameter must have a value
		[ValidateNotNull]
		[ValidateNotNullOrEmpty]
		[ValidateSet("Alex","Mike","Ibrahim",IgnoreCase)] -> to restrict value parameter to 3 values only
		[Alias("param1")]
		[ValidateLength(0,10)]
		[ValidatePattern("[a-z]*")]
		[String]$name
		
		[Parameter(Mandatory,Position=1)]
		[ValidateRange(10,40)] -> to restrict the age between 10 and 40
		[Int]$age

		[ValidateCount(0,3)] -> validate the amount of arguments passed in
		[ValidateScript({# Here you write a code to validate the entered value paramter $_ })]
		[Parameter(Mandatory=$true)]$lastname
	)
	Write-Host Your name $name and your age $age
}
==
function Welcome($name,$age){
	Write-Host Your name $name and your age $age
}
Welcome "Ibrahim" 21
Welcome -name "Ibrahim" -age 2
```

## Classes
```
[DateTime]::new -> to list all the constructors for the DateTime class
"name".CompareTo

class Person : FatherClass { # class Person will inherite all members from the FatherClass
	[string] $FirstName
	[string] $LastName
	static [Int] $Age # can be accessed without creating an object from the class 
	hidden [string] $NickName
	Person([string] $FName){$this.FirstName = $FName} # Constructor
	[string] Welcome(){
		return "Welcome, your full name is {0} {1}" -f $this.FirstName, $this.LastName
	}
	static [void] ReturnAge([Int] age){# your code}
}
$xperson = [Person]::new("Ibrahim12")
$xperson.FirstName = "Ibrahim"
$xperson.LastName = "Ibraaaaa"
$xperson.Welcome()
Get-Member -InputObject $xperson # will return all members of the type instance

```

## Modules
Mostly, each powershell module has a manifest, which contains metadata about the module. .psd1 extension
Powershell will look for module under "$Env:PSModulepath". Folder called "cal" and the module called "cal"
```
@{ # save in a saperated file as .psd1
RootModule = "MyCalculaterModule.psm1"
ModuleVersion = "1.0"
CompactiblePSEditions = @("Core")
GUID = "$random-GUID"
Author = "Ibrahim Ibraaaaa"
and so on 
}

[string] $name
function Add{
	[CmdletBinding()]
	param([Int]$n1,[Int]$n2)
	return $n1 + $n2
}
Export-ModuleMember -Function Add
Export-ModuleMember -Variable name
```

## Profile
Powershell profiles are not created automatically for user, so you need to create one: 
```
> New-Item -ItemType File $profile # to create a user powershell profile
> "Testing Profile" >> $profile # printing a text inside user profile
> $profile | Format-List -Force
```
For More search on google.....

## Playing with properties
```
Get-ChildItem -Path . | Select-Object Name, Length
Get-ChildItem -Path . | Select-Object @{Name="File_Name"}, Length # property name changed to File_Name
```

## Built-in Variables
```
$PSScriptRoot -> used inside scripts and define the full path to the script, value by default is $null
$Args
$PSItem -> Same as $_ contains the current objectin the pipeline object
$? -> contains the status of the last operation TRUE or FALSE
$error -> contains the most recent errors, to access the first one $error[0]
$null -> used for absent or undefined value and as an empty placeholder in an array
$pid
$PSVersionTable
Get-ChildItem env:
$OFS -> used with array to add special characters between the Items, while converting to string 

$numbers = 1,2,3,4,5,6; $numbers; $OFS = ",:;"; $numbers
```
For more search on google

## Splatting
To store parameters (as key-value pairs in a hashtable) as a single unit and passing these as to a cmdlet using splatting operator @
It allows us to reuse the paramters in many commands. For pipeline use @_
```
$Save_Splat = @{
	Path = ".\file.txt",
	Destination = "C:\standard_files"
}
Copy @Save_Splat


$FILE_VERSION = @{
	FileVersionInfo = $true
}
Get-Process @FILE_VERSION
```

## Sending Emails
Here you can put all parameters in a splat operator @ and pass them to the cmdlet
```
Send-MailMessage -From sender@gmail.local -Subject "Email Subject" -To reciever@gmail.local -SmtpServer "yourSMTPserver" -Body "Hello"

function Send_email{ # Send message with SMTPClient 
	$FROM = "sender@gmail.local"
	$TO = "reciever@gmail.local"
	$TextBody = Get-Content .\message.txt -RAW
	$Subject = "Email Subject"
	$SMTPServer = "yourSMTPserver"
	$SMTPClient = New-Object Net.Mail.SmtpClient($SmtpServer,25)
	$SMTPClient.EnableSsl = $false
	$SmtpClient.Send($FROM, $TO, $Subject, $Body)
}

```

## Remoting
```
Enter-PSSession $ip
Enter-PSSession $ip -Credential $(Get-Credential)

$Session = New-PSSession -ComputerName "RemoteComputerName"
Invoke-Command -ComputerName $session -ScriptBlock {Get-Date # your code to execute on remote machine}
$age,$name = 21,"ibrahim"; Invoke-Command -ComputerName $session -ArgumentList $name,$age -ScriptBlock {Write-Host $name - $age}

Enable-PSRemoting -Force -> to enable the powershell remoting on the server 

Set-Item WSMan:\localhost\Service\AllowUnencrypted $true
Set-Item WSMan:\localhost\Client\TrustedHosts "Declare ip and domains to trust"
Set-Item WSMan:\localhost\Client\TrustedHosts "192.0.0.1, 192.0.0.10,*any.local, *"

Remove-PSSession $session
```

## Background Jobs
```
$job = Start-Job -ScriptBlock {Get-Process}
$job = Start-Job -FilePath "myscript.ps1"
$job = Invoke-Command -ComputerName "ComputerName" -ScriptBlock {Get-Service winrm} -JobName "WinRM" -ThrottleLimit 16 -AsJob
$job = Start-Job -ScriptBlock {Get-Process} - Credential "Domain/user1" # start jobs as another user with prompt for password

$password = ConvertTo-SecureString -String "mypassword" -AsPlainText -Force  # start jobs as another user with no prompt 
$credentials = New-Object System.Management.Automation.PSCredential -ArgumentList @("Username",$password)
Start-Job -ScriptBlock {Get-Date} -Credential $credentials

Get-Job
$job | Wait-job | Recieve-Job # wait until the job finish to get the results
$job | Wait-job -Timeout 10# wait max 10 seconds
$job | Stop-Job
$job | Remove-Job
$job | Suspend-Job
$job | Resume-Job
```

## The keyword return
return keyword, every statement after the return will not be executed!
```
function welcome { $w = "Welcome"; return $w}
function welcome { $w = "Welcome"; $w; return}
function welcome { $w = "Hello"; $w}

ipconfig | Out-Null // output will be gone, no output
ipconfig > $null
```

## CSV Files
```
$products = Import-Csv .\products.csv
$products | forEach-object {// your code here}
```

## XML Files
```
(New-Object System.Xml.XmlDocument).load(Resolve-Path("\.file.xml"))
[xml] $xdoc = Get-Content ".\file.xml"
$xdoc = [xml] (Get-Content ".\file.xml")

$xdoc.attribute1 // accessing as an object
$xdoc.attribute1.attribute2
OR
$xdoc.SelectNodes("//attribute1//attribute2") // accessing with XPATH

$xml = @" Your XML content "@
```

## RESTful APIs - 96 page 
```
@params = @ {
	Uri = "your url to request"
	Method = "POST"
	Body = @{
		color = "red"
		message = "my message"
		message_format = "text"
	} | ConvertTo-Json
	Content-Type = "application/json"
}
Invoke-RestMethod @params

Invoke-RestMethod -Uri "your url to request"
```

## Select-String
```
$Text | Select-String -Pattern $pattern
```

## Aliases
```
Get-Alias -Definition Get-ChildItem

Get-Alias -Name p*

Set-Alias -Name ls -Value Get-ChildItem
```

## Progress
```
1..100 | ForEach-Object {
    Write-Progress -Activity "Copying files" -Status "$_ %" -Id 1 -PercentComplete $_ -CurrentOperation "Copying..."
    Start-Sleep -Milliseconds 500
}
```

## Powershell.exe
```
powershell.exe 
-File script.ps1 
-Command {}
-EncodedCommand "base64"
-ExectionPolicy $policy
-NoProfile
-NonInteractive
-WindowsStyle hidden
```

## Executable files
```
any.exe
& any.exe
& any.exe | Out-Null
Start-Process any.exe
Start-Process any.exe -Wait 

```

## Enforce Prequisites to run scripts
```
#requires -version 4
#requires -version >= 3
#requires -RunAsAdministrator
```

## Get-Help
```
Get-Help Get-Command
-Online
-Examples
-FullName
-Parameter Path

Get-Help Get-com*
```

## Modules
Using "CmdletBinding()" in functions make them act like cmdlet
```
Get-Module -ListAvailable
Import-Module $Module_Name
Get-Command -Module Microsoft.PowerShell.Management
```

## ErrorAction Parameters
The default value is Continue
Continue | Ignore | Inquire | SilentlyContinue | Stop | Suspend
```
Write-Error "The First Command" -ErrorAction Continue; Write-Error "The Second Command"
```

## Parameters Set
Used to enforce user to use that parameter.
if mandatory is false in 2 parameters, then you can only use one of them, both are not allowed!!!
```
Function AnyFunc(){
	Param{
		[Parameter(Mandatory=$true)]
		[String]$ip,
		
		[Parameter(ParameterSetName="Credentials", Mandatory=$false)]
		[String]$ComputerName = "localhost",

		[Parameter(ParameterSetName="Credentials", Mandatory=$true)] //required
		[String]$user,
		
		[Parameter(ParameterSetName="Credentials", Mandatory=$true)] //required
		[SecureString]$password
	}
}
AnyFunc -ip 192.168.178.1 -ComputerName //will not work, user and password are required
```

## Output
```

```

