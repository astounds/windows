# Windows
Installation and configuration instructions for Windows 10 (Version 1909).

<!--
Fix drive previously used for unix partitions with `diskpart`.

```cmd
DISKPART> list disk
DISKPART> select disk 1
DISKPART> clean
DISKPART> create partition primary
DISKPART> active
DISKPART> format fs=Fat32 quick
```
-->

## Preparations
Download the latest [Windows 10](https://www.microsoft.com/en-us/software-download/windows10) image
and create the installation media using [Rufus](https://rufus.ie/).

Create the file `\sources\ei.cfg` on the installation media.

```ini
[EditionID]
Professional
[Channel]
Retail
[VL]
0
```

Create the file `\sources\pid.txt` on the installation media.

```ini
[PID]
Value={windows key}
```

Copy this repo and the latest graphics drivers installer to the installation media.

Set the BIOS date and time to the current local time.

**Keep the system disconnected from the network.**

## Installation
Boot the installation media.

```
Language to install: English (United States)
Time and currency format: {Current Time Zone Country}
Keyboard or input method: {Current Hardware Keyboard}
```

Choose a single word username starting with a capital letter to keep the `%UserProfile%` path
consistent and free from spaces.

Verify Windows flavor and version with `Start > "winver"`.

## Setup
Modify and execute [setup/system.ps1](setup/system.ps1) using the "Run with PowerShell" context menu
repeatedly until it stops rebooting the system.

Unpin everything from the start menu and taskbar.

```ps1
cd windows\script
powershell -NoProfile -ExecutionPolicy Bypass -File Win10.ps1 -include Win10.psm1 UnpinStartMenuTiles
powershell -NoProfile -ExecutionPolicy Bypass -File Win10.ps1 -include Win10.psm1 UnpinTaskbarIcons
```

List remaining apps.

```ps
Get-AppxPackage | Select Name,PackageFullName | Sort Name
```

Uninstall unwanted apps.

```ps
Get-AppxPackage "Microsoft.3DBuilder" | Remove-AppxPackage
```

Reboot the system.

## Drivers & Updates
Disable automatic driver application installation.

```
Start > "Change device installation settings"
◉ No (your device might not work as expected)
```

1. Reboot the system.
2. Connect to the Internet.
3. Install Windows updates.
4. Repeat the "Setup" step.

Install Windows Subsystem for Linux.

```ps
cd windows\script
powershell -NoProfile -ExecutionPolicy Bypass -File Win10.ps1 -include Win10.psm1 InstallLinuxSubsystem
```

Reboot the system.

## Settings
Settings that have yet to be incorporated into the [setup/system.ps1](setup/system.ps1) script.

### Keymap (Optional)
1. Install [res/keymap.zip](res/keymap.zip) to input German characters on a U.S. keyboard.
2. Configure Windows language preferences in Settings.
3. Reboot the system.

<!--
Use the [Microsoft Keyboard Layout Creator](https://www.microsoft.com/en-us/download/details.aspx?id=22339) to
create new keyboard layouts.
-->

### Devices
```
Typing
+ Spelling
  ☐ Autocorrect misspelled words
+ Typing
  ☐ Add a period after I double-tap the Spacebar

AutoPlay
+ Choose AutoPlay defaults
  Removable drive: Open folder to view files (File Explorer)
  Memory card: Open folder to view files (File Explorer)
```

### Network & Internet
```
Proxy
+ Automatic proxy setup
  ☐ Automatically detect settings
```

### Personalization
```
Background
+ Background
  Browse: (select picture)

Colors
+ Choose your color
  Windows colors: Navy blue

Lock screen
+ Preview
  Browse: (select picture)

Start
+ Start
  ☐ Show app list in Start menu
```

### Time & Language
```
Region
+ Region
  Country or region: United States
  Regional format: English (United States)
+ Additional date, time, & regional settings > Change date, time, or number formats
  Formats > Additional settings...
  + Numbers
    Digit grouping symbol: ␣
    Measurement system: Metric
  + Currency
    Currency symbol: €
    Positive currency format: 1.1 €
    Negative currency format: -1.1 €
    Digit grouping symbol: ␣
  + Time
    Short time: HH:mm
    Long time: HH:mm:ss
  + Date
    Short date: yyyy-MM-dd
    Long date: ddd, d MMMM yyyy
    First day of week: Monday
  Administrative
  + Copy settings...
    ☑ Welcome screen and system accounts
    ☑ New user accounts
```

Reboot the system.

### Privacy
```
General
+ Change privacy options
  ☐ Let Windows track app launches to improve Start and search results
```

## Lock Screen
Disable Lock Screen after personalization.

```cmd
cd windows\script
powershell -NoProfile -ExecutionPolicy Bypass -File Win10.ps1 -include Win10.psm1 DisableLockScreen
```

## Windows Libraries
Move unwanted Windows libraries.

1. Right click on `%UserProfile%\Pictures\Camera Roll` and select `Properties`.<br/>
   Select the `Location` tab and change the path to `%AppData%\Pictures\Camera Roll`.
2. Right click on `%UserProfile%\Pictures\Saved Pictures` and select `Properties`.<br/>
   Select the `Location` tab and change the path to `%AppData%\Pictures\Saved Pictures`.

Hide `Captures` directory.

```cmd
md "%UserProfile%\Videos\Captures"
attrib +s +h "%UserProfile%\Videos\Captures"
```

## Indexing Options
Configure Indexing Options to only track the "Start Menu" and rebuild the index.

## Firewall
Disable all rules in Windows Firewall except the following entries.

```
wf.msc
+ Inbound Rules
  Connect
  Core Networking - …
  Delivery Optimization (…)
  Hyper-V …
  Network Discovery (…)
+ Outbound Rules
  Connect
  Core Networking - …
  Hyper-V …
  Network Discovery (…)
```

Enable inbound rules for `File and Printer Sharing (Echo Request …)`. Modify `Private,Public`
rules for inbound and outbound IPv4 and IPv6 Echo Requests and select "Any IP address" under
`Remote IP address` in the `Scope` tab.

## Microsoft Software
Configure Microsoft Edge.

```
Settings
+ General
  Choose a theme: Dark
  Open Microsoft Edge with: A specific page or pages
    about:blank
  Open new tabs with: A blank page
  Show the home button: Off
  Show sites I frequently visit in "Top sites": Off
  Show definitions inline for: Off
  Ask me what to do with each download: Off
+ Privacy & security
  Show search and site suggestions as I type: Off
  Show search history: Off
  Use page prediction: Off
+ Passwords & autofill
  Save form data: Off
```

<!--
Configure Microsoft Outlook 2016.

```cmd
reg add "HKCU\SOFTWARE\Microsoft\Office\16.0\Outlook\Setup" /v "DisableOffice365SimplifiedAccountCreation" /t REG_DWORD /d 1 /f
```
-->

Configure Microsoft Photos.

```
Settings
+ Viewing and editing
  Linked duplicates: Off
  Mouse wheel: ◉ Zoom in and out
```

## Applications
Install third party software.

* [7-Zip](http://www.7-zip.org)
* [Chrome](https://www.google.com/chrome/)
* [Affinity Photo](https://affinity.serif.com/photo)
* [Affinity Designer](https://affinity.serif.com/designer)
* [MagicaVoxel](https://ephtracy.github.io/)
* [Blender](https://www.blender.org/)
* [SFXR](http://www.drpetter.se/project_sfxr.html)
* [MPV](https://mpv.srsfckn.biz/)
* [OBS](https://obsproject.com/download)
* [HxD](https://mh-nexus.de/en/downloads.php?product=HxD20)
* [CFF Explorer](http://www.ntcore.com/exsuite.php)
* [Sysinternals Suite](https://technet.microsoft.com/en-us/sysinternals/bb842062.aspx)

<!--
* [Gimp](https://www.gimp.org/)
* [FontForge](https://fontforge.github.io/en-US/downloads/windows-dl/)
-->

### LMMS
Install [LMMS](https://lmms.io/) and configure paths on first startup.

```
LMMS Working Directory: %UserProfile%\Music\Workspace
GIG Directory: %UserProfile%\Music\Workspace\Samples\GIG
SF2 Directory: %UserProfile%\Music\Workspace\Samples\Fonts
LADSPA Plugin Directories: %UserProfile%\Music\Workspace\Plugins\LADSPA
```

### Git
Install [Git](https://git-scm.com/downloads) with specific settings.

```
Select Destination Location
  C:\Program Files\Git

Select Components
  ☐ Windows Explorer integration
  ☑ Git LFS (Large File Support)
  ☐ Associate .git* configuration files with the default text editor
  ☐ Associate .sh files to be run with Bash

Select Start Menu Folder
  ☑ Don't create a Start Menu folder

Choosing the default editor used by Git
  [Select other editor as Git's default editor]
  Location of editor: C:\Program Files (x86)\Vim\vim81\gvim.exe
  [Test Custom Editor]

Adjusting your PATH environment
  ◉ Use Git from Git Bash only

Choosing HTTPS transport backend
  ◉ Use the OpenSSL library

Configuring the line ending conversions
  ◉ Checkout as-is, commit as-is

Configuring the terminal emulator to use with Git Bash
  ◉ Use Windows' default console window

Configuring file system caching
  ☑ Enable file system caching
  ☑ Enable Git Credential Manager
  ☑ Enable symbolic links
```

Add `C:\Program Files\Git\cmd` to `Path`.

### Vim
Install [gVim](http://www.vim.org).

```
Select the type of install: Minimal
```

Create configuration directory.

```cmd
git clone git@github.com:qis/vim %UserProfile%\vimfiles
```

Register gVim in Explorer context menus.

```cmd
set gvim=C:\Program Files (x86)\Vim\vim81\gvim.exe
set gvimfile=\"%gvim%\" \"%1\"
reg add "HKCR\*\shell\gvim" /ve /d "Edit with Vim" /f
reg add "HKCR\*\shell\gvim" /v Icon /d "%gvim%,0" /f
reg add "HKCR\*\shell\gvim\command" /ve /d "%gvimfile%" /f
```

<!--
```cmd
set gvim=C:\Program Files (x86)\Vim\vim81\gvim.exe
set gvimtabfile=\"%gvim%\" -\-remote-tab-silent \"%1\"
reg add "HKCR\*\shell\gvimtab" /ve /d "Edit with Vim (Tab)" /f
reg add "HKCR\*\shell\gvimtab" /v Icon /d "%gvim%,0" /f
reg add "HKCR\*\shell\gvimtab\command" /ve /d "%gvimtabfile%" /f
```
-->

<!--
## Server
Install software for Windows Server administration.

* [SQL Server Management Studio](https://msdn.microsoft.com/en-us/library/mt238290.aspx)
* [Remote Server Administration Tools for Windows 10](https://www.microsoft.com/en-us/download/details.aspx?id=45520)

Configure the WinRM client.

```cmd
Get-NetConnectionProfile
Set-NetConnectionProfile -InterfaceIndex {InterfaceIndex} -NetworkCategory Private
Enable-PSRemoting -SkipNetworkProfileCheck -Force
Set-Item WSMan:\localhost\Client\TrustedHosts -Value "*" -Force
```

Configure the WinRM server.

```ps
Enable-PSRemoting -SkipNetworkProfileCheck -Force
Set-NetFirewallRule -Name "WINRM-HTTP-In-TCP-PUBLIC" -RemoteAddress Any
Set-Item WSMan:\localhost\Client\TrustedHosts -Value "*" -Force
```

Configure the WinRM server to accept HTTPS connections.

```ps
winrm enumerate winrm/config/listener
New-SelfSignedCertificate -DnsName "{DomainName}" -CertStoreLocation Cert:\LocalMachine\My
cmd /C 'winrm create winrm/config/Listener?Address=*+Transport=HTTPS @{Hostname="{DomainName}"; CertificateThumbprint="{Thumbprint}"}'
netsh advfirewall firewall add rule name="Windows Remote Management (HTTPS-In)" dir=in action=allow protocol=TCP localport=5986
```

Connect over HTTP.

```ps
Enter-PSSession -ComputerName host.domain -Port 5985 -Credential administrator@domain
```

Connect over HTTPS.

```ps
$soptions = New-PSSessionOption -SkipCACheck
Enter-PSSession -ComputerName host.domain -Port 5986 -Credential administrator@domain -SessionOption $soptions -UseSSL
```
-->

<!--
## Control Panel
Add Control Panel shortcuts to the Windows start menu (use icons from `C:\Windows\System32\shell32.dll`).

[Control Panel Command Line Commands](https://www.lifewire.com/command-line-commands-for-control-panel-applets-2626060)
-->

<!--
## Anti-Virus
Suggested third party anti-virus exclusion lists.

```
Excluded Processes

%ProgramFiles(x86)%\Microsoft Visual Studio\2017\Professional\Common7\IDE\devenv.exe
%ProgramFiles(x86)%\Microsoft Visual Studio\2017\Professional\Common7\IDE\PerfWatson2.exe
%ProgramFiles(x86)%\Microsoft Visual Studio\2017\Professional\Common7\IDE\VcxprojReader.exe
%ProgramFiles(x86)%\Microsoft Visual Studio\2017\Professional\VC\Tools\MSVC\14.12.25827\bin\HostX86\x64\CL.exe
%ProgramFiles(x86)%\Microsoft Visual Studio\2017\Professional\VC\Tools\MSVC\14.12.25827\bin\HostX86\x64\link.exe

Excluded Directories

%ProgramFiles(x86)%\Microsoft Visual Studio\
%ProgramFiles(x86)%\Windows Kits\
%UserProfile%\AppData\Local\lxss\
C:\Workspace\
```

## VLANs
<https://downloadcenter.intel.com/download/25016/Intel-Network-Adapter-Driver-for-Windows-10>
-->

## Symbolic Link Privilege
Allow user to create symbolic links.

```
secpol.msc
+ Security Settings > Local Policies > User Rights Assignment
  Create symbolic links
  + Add User or Group...
    1. Enter own user name.
    2. Click on "Check Names".
    3. Click on "OK".
```

Update group policy.

```cmd
gpupdate /force
```

Reboot the system.

## Windows Subsystem for Linux
Install a WSL distro from <https://aka.ms/wslstore>, launch it and download config files as **user** and **root**.

```sh
curl -L https://raw.githubusercontent.com/qis/windows/master/wsl/.bashrc -o .bashrc
curl -L https://raw.githubusercontent.com/qis/windows/master/wsl/.profile -o .profile
curl -L https://raw.githubusercontent.com/qis/windows/master/wsl/.tmux.conf -o .tmux.conf
exit # required to apply settings and release the shell history file
```

Configure [sudo(8)](http://manpages.ubuntu.com/manpages/xenial/man8/sudo.8.html) with `sudo EDITOR=vim visudo`.

```sh
# Locale settings.
Defaults env_keep += "LANG LANGUAGE LINGUAS LC_* _XKB_CHARSET"

# Profile settings.
Defaults env_keep += "MM_CHARSET EDITOR PAGER CLICOLOR LSCOLORS TMUX SESSION"

# User privilege specification.
root  ALL=(ALL) ALL
%sudo ALL=(ALL) NOPASSWD: ALL

# See sudoers(5) for more information on "#include" directives:
#includedir /etc/sudoers.d
```

Create `/etc/wsl.conf`.

```sh
[automount]
enabled=true
options=case=off,metadata,uid=1000,gid=1000,umask=022
```

Add the following line to `/etc/mdadm/mdadm.conf` (fixes some `apt` warnings).

```sh
# definitions of existing MD arrays
ARRAY <ignore> devices=/dev/sda
```

Modify the following lines in `/etc/pam.d/login` and `/etc/pam.d/sshd` (disables message of the day).

```sh
#session    optional    pam_motd.so motd=/run/motd.dynamic
#session    optional    pam_motd.so noupdate
```

Execute `chmod -x /etc/update-motd.d/*{-help-text,-motd-news}` to reduce spam.<br/>

**IMPORTANT**: Completely restart `bash.exe` to apply `/etc/wsl.conf` settings.

Initialize **user** and **root** home directory structure.

```sh
mkdir -p ~/.config
ln -s /mnt/c/Workspace ~/workspace
ln -s /mnt/c/Users/Qis/Documents ~/documents
ln -s /mnt/c/Users/Qis/Downloads ~/downloads
ln -s /mnt/c/Users/Qis/.gitconfig ~/.gitconfig
ln -s /mnt/c/Users/Qis/vimfiles ~/.config/nvim
ln -s .config/nvim ~/.vim
mkdir -p ~/.ssh; chmod 0700 ~/.ssh
for i in authorized_keys config id_rsa id_rsa.pub known_hosts; do
  ln -s /mnt/c/Users/Qis/.ssh/$i ~/.ssh/$i
done
chmod 0600 /mnt/c/Users/Qis/.ssh/* ~/.ssh/*
rm -f ~/.bash_history ~/.bash_logout ~/.viminfo
touch ~/.config/nviminfo ~/.config/viminfo
```

Install packages.

```sh
sudo apt update
sudo apt upgrade -y
sudo apt dist-upgrade
sudo apt autoremove
sudo apt autoclean
sudo apt install -y p7zip-full tree pv pwgen
sudo apt install -y imagemagick pngcrush webp
```

<!--
Install youtube-dl.

```sh
sudo curl -L https://yt-dl.org/downloads/latest/youtube-dl -o /usr/local/bin/youtube-dl
sudo chmod 0755 /usr/local/bin/youtube-dl
```

Install [mintty/wsltty](https://github.com/mintty/wsltty) or [qis/console](https://github.com/qis/console)
to have a better command prompt and terminal emulator experience.
-->

<!--
Install [VcXsrv](https://github.com/ArcticaProject/vcxsrv/releases) for Xorg application support.
-->

<!--
Install an international locale.

```sh
sudo curl -L https://raw.githubusercontent.com/qis/windows/master/wsl/en_XX -o /usr/share/i18n/locales/en_XX
sudo tee -a /etc/locale.gen <<EOF
en_XX.UTF-8 UTF-8
EOF
sudo locale-gen
```
-->

## Development
Follow the [development](development.md) guide to set up a developer workstation.

## Start Menu
![Start Menu](res/start.png)

<!--
Re-register Start Menu and Conrtana in PowerShell as Administrator.

```ps
Get-AppXPackage -AllUsers | Foreach { `
  Add-AppxPackage -DisableDevelopmentMode -Register "$($_.InstallLocation)\AppXManifest.xml" `
}
```

Fix broken Start Menu search.

<https://www.windowscentral.com/how-reset-start-menu-layout-windows-10>
-->
