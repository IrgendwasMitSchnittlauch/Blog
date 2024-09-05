# Personal PowerShell collection
> _Published: 2024-09-05_<br>
> _Author: Schnittlauch_

There are many best practices and other useful and helpful tips on PowerShell. I would like to use this post to document all relavant snippets and scripts related to PowerShell.

## Quick links and sharings:
> [!TIP]
> Feel free to create your own bookmark with relavant snippets and codelines. Make sure it fits for your daily business!

## Hashtable lookups
_Thanks to [Brooks Peepin's blog post](https://brookspeppin.com/2021/11/10/how-to-use-powershell-hash-tables-to-quickly-search-large-data-sets/), which helped me understanding hashtables._ <br>
Define an array where to take data from
```powershell
#Get array to start with
$mailboxes = Get-Mailbox -ResultSize Unlimited
```
Choose a key, in this example the mailadress is the key
```powershell
$mail = @{} #This creates an empty hash table
foreach ($item in $mailboxes){
    #loops and sets KEY = EmailAdress; Needs to be Param of $item
    $mail[$item.EmailAddress] = $item   #Email = KEY, $item = Properties
}
```
Access by calling the item with key
```powershell
$item["John.Doe@Contoso.com"]
```

## Windows Forms
Messageboxes to show warnings, errors or dialogs
```powershell
Add-Type -AssemblyName System.Windows.Forms
[System.Windows.Forms.MessageBox]::Show("Sorry, your user is not an Administrator","Insufficent rights",0,"error",[System.Windows.Forms.MessageBoxButtons]::OK)
```
Also usable to create custom GUIs<br>
[Check out this script for further details](https://github.com/IrgendwasMitSchnittlauch/PowerShell/blob/main/GUI.Projects/GUI.Template.ps1)

## Out-Gridview -Passthru
Great when using out-gridview to select data; **super underrated**
```powershell
Get-Service | Out-Gridview -Passthru | Stop-Service
```
## Comment-Help
_Thanks to Visual Studio_ 
```powershell
<#
.SYNOPSIS
    A short one-line action-based description, e.g. 'Tests if a function is valid'
.DESCRIPTION
    A longer description of the function, its purpose, common use cases, etc.
.NOTES
    Information or caveats about the function e.g. 'This function is not supported in Linux'
.LINK
    Specify a URI to a help page, this will show when Get-Help -Online is used.
.EXAMPLE
    Test-MyTestFunction -Verbose
    Explanation of the function or its result. You can include multiple examples with additional .EXAMPLE lines
#>
```
## Params
_copied by [original MS Docs](https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_functions_advanced_parameters?view=powershell-7.4)_
```powershell
param(
    [Parameter(Mandatory=$true, ValueFromPipeline=$true)]
    #4 digits from 0-9
    [ValidatePattern("[0-9]{4}")]
    [string[]]$ComputerName
)
```
I'll keep updating this blog, each time I'll face a new challenge which is 'document worthy' again. :)

---
> **About the author**
> 
> Florian Scholz alias Schnittlauch is a german based Microsoft it system engineer, who loves powershell automation, Azure and Microsoft infrastructure services.
> After work Florian spents a lot of his time as a voluntary firefighter aswell as cooking with his wife.
