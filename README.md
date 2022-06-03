# PowerShell Toolbox

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

## Install

### Windows

* https://docs.microsoft.com/fr-fr/powershell/scripting/install/installing-powershell-on-windows
* https://docs.microsoft.com/en-us/windows/terminal/

### VSCode extensions & configuration

```
code --install extension ms-vscode.powershell
```
