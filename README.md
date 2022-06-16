# PowerShell Toolbox

Basics:

* ```Get-Member``` (alias ```gm```)
* ```Sort-Object -Property ... [-desc]``` (alias ```sort```)
* ```Select-Object [-Property ...] [-First 10]``` (alias ```select```)
* ```Get-Content file```

Use ```Select-Object``` to add/modify properties:

```
> get-module -listavailable| select -property @{n='Module';e={$_.Name}} -first 10
> get-module -listavailable| select -property name,@{n='Module';e={$_.Name}} -first 10
> get-module -listavailable| select -property *,@{n='Module';e={$_.Name}} -first 10
```

## Pipeline parameter binding

  1. first ```ByValue```: try to match output type from command A to one or more parameters to command B
  2. otherwhise ```ByPropertyName```: try to match output object properties name from command A to parameters name from command B

See:

```
> get-help get-command -Parameter Name

-Name <System.String[]>

...

    Required?                    false
    Position?                    0
    Default value                None
    Accept pipeline input?       True (ByPropertyName, ByValue)
    Accept wildcard characters?  true
```

Note that if ```ByValue``` match but fails, ```ByPropertyName``` is not attempted. In theses cases, forget pipeline binding and use subcommands with parenthesis

```
> get-module -listavailable | select -property @{n='Module';e={$_.Name}} -first 3 | get-command
=> Error because of match ByValue with -Name parameter, byPropertyName Module is not attempted

> get-command -Module (get-module -listavailable | select -property Name -first 3).Name
=> Ok explicitly passing string list to Module parameter

> get-command -Module (get-module -listavailable | select -ExpandProperty Name -first 3)
=> Ok, ExpandProperty unbox the Name property from the object and return it directly
```

## Help

```
help String [-Full]
help String -Examples

help about_*
help about_commonparameters
help about_preference_variables
```

Notes:

* ```Help``` (wrapper funtion) and ```Get-Help``` (cmdlet) search in help topics
* ````Get-Command``` search among all commands (note only PowerShell command)

```
Update-Help -UICulture en-US -ErrorAction SilentlyContinue -Force
```

## Execution Policy

It is not a true security settings but a means to preventing users from **unintentionally** running **scripts** (not commands !).

```
Get-ExecutionPolicy
```

Can be changed:

* by running ```Set-ExecutionPolicy``` which change the setting in HKLM
* by using a GPO which always take precedence over HKLM settings
* by running PowerShell.exe with -ExecutionPolicy parameter which always **take precedence over GPO**

Possible values: Restricted > AllSigned > RemoteSigned > Unrestricted > Bypass

## PSProviders & PSDrive

PSProviders are adapters used to create PSDrive interfaces giving access to data stores.

```
Get-PSProvider
Get-PSDrive
```

Most of cmdlets used to interacts with PSDrive have the word Item in their name, see ```Get-Command *Name*```. Depending of provider, Items can have properties and child items.

For example:

```
Get-ChildItem Env:*
Set_Item -Path Env:/MY_VARIABLE -Value 1

Get-ChildItem HKCU:*
```

To get/set the current working location and drive:

```
Get-Location
Set-Location C:\
Set-Location HKLM:\
Set-Location Env:\
```

## Exporting to a file

* ```... | Export-Csv file.csv```
* ```... | ConvertTo-Csv | Out-file file.csv``
* ```... | ConvertTo-Json | Out-file file.json```
* ```... | ConvertTo-Xml | Out-file file.xml```
* ```... | Export-Clixml file.xml``` (useful to serialize/deserialize PS objects)
* ```... | ConvertTo-Html | Out-file file.html```

## Modules

[PowerShell Gallery](https://www.powershellgallery.com/)

* ```Find-Module```
* ```Get-Module [-ListAvailable]```
* ```Install Module```
* ```Update-Module```

PS autodiscover modules in **PSModulePath** (can be disabled using ```$PSModuleAutoloadingPreference```).

```
$env:PSModulePath -split ';'
```

If module not in **PSModulePath**, use  ```Import-Module``` with full path.

```
# Import a module with path relative to calling script
$ScriptPath = Split-Path $MyInvocation.MyCommand.Path
Import-Module $ScriptPath\Modules\Builder.psm1
```

Explore module with:

* ```Get-Command -module```

## Miscellaneous useful cmdlets

* ```Compare-Object```

## Useful modules

* powershell-yaml

### SecretManagement

```
> Install-Module Microsoft.PowerShell.SecretManagement
> Install-Module Microsoft.PowerShell.SecretStore

> Register-SecretVault -Name LocalStore -ModuleName Microsoft.PowerShell.SecretStore -DefaultVault
> Get-SecretVault

Name       ModuleName                       IsDefaultVault
----       ----------                       --------------
LocalStore Microsoft.PowerShell.SecretStore True

> Set-Secret -Name MySecret -Vault LocalStore
> Get-Secret -Name MySecret -Vault LocalStore [-asPlainText]
```

## Install

### Windows

* https://docs.microsoft.com/fr-fr/powershell/scripting/install/installing-powershell-on-windows
* https://docs.microsoft.com/en-us/windows/terminal/

### VSCode extensions & configuration

```
code --install extension ms-vscode.powershell
```
