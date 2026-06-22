# Programmatic STIG Remediations

## Remediations Table

| STIG ID        | Severity | Description                              | Language       | Link             |
| -------------- | -------- | ---------------------------------------- | -------------- | ---------------- |
| WN11-00-000135 | High     | Windows Defender Firewall Disabled       | PowerShell     | View Remediation |
| WN11-AC-000040 | High     | Password Complexity Disabled             | PowerShell     | View Remediation |
| WN11-AC-000035 | High     | Minimum Password Length Not Configured   | PowerShell     | View Remediation |
| WN11-AC-000010 | High     | Account Lockout Threshold Not Configured | PowerShell     | View Remediation |
| WN11-SO-000010 | High     | Guest Account Enabled                    | Command Prompt | View Remediation |
| WN11-00-000160 | High     | SMBv1 Protocol Enabled                   | PowerShell     | View Remediation |
| WN11-CC-000326 | Medium   | PowerShell Script Block Logging Disabled | PowerShell     | View Remediation |
| WN11-CC-000327 | Medium   | PowerShell Transcription Disabled        | PowerShell     | View Remediation |
| WN11-CC-000345 | Medium   | WinRM Basic Authentication Enabled       | PowerShell     | View Remediation |
| WN11-00-000170 | High     | SMBv1 Client Enabled                     | PowerShell     | View Remediation |

---

# Remediation Commands

## 1. Windows Defender Firewall Disabled

Run in PowerShell (Administrator):

```powershell
Set-NetFirewallProfile -Profile Domain,Public,Private -Enabled True
```

Verify:

```powershell
Get-NetFirewallProfile | Select Name,Enabled
```

All profiles should return True.

---

## 2. Password Complexity Disabled

Run in PowerShell (Administrator):

```powershell
secedit /export /cfg C:\secpol.cfg
```

Enable:

Computer Configuration → Windows Settings → Security Settings → Account Policies → Password Policy → Password must meet complexity requirements → Enabled

Verify:

```powershell
net accounts
```

---

## 3. Minimum Password Length Not Configured

Configure minimum password length to 14 characters:

```powershell
net accounts /minpwlen:14
```

Verify:

```powershell
net accounts
```

Minimum password length should be 14.

---

## 4. Account Lockout Threshold Not Configured

Configure lockout threshold:

```powershell
net accounts /lockoutthreshold:5
```

Verify:

```powershell
net accounts
```

Lockout threshold should be 5 invalid attempts.

---

## 5. Guest Account Enabled

Disable Guest account:

```cmd
net user Guest /active:no
```

Verify:

```cmd
net user Guest
```

Account active should show No.

---

## 6. SMBv1 Protocol Enabled

Disable SMBv1:

```powershell
Disable-WindowsOptionalFeature -Online -FeatureName SMB1Protocol -NoRestart
```

Verify:

```powershell
Get-WindowsOptionalFeature -Online -FeatureName SMB1Protocol
```

State should show Disabled.

---

## 7. PowerShell Script Block Logging Disabled

Enable Script Block Logging:

```powershell
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\Windows\PowerShell\ScriptBlockLogging" -Force
Set-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\Windows\PowerShell\ScriptBlockLogging" -Name EnableScriptBlockLogging -Value 1
```

Verify:

```powershell
Get-ItemProperty "HKLM:\SOFTWARE\Policies\Microsoft\Windows\PowerShell\ScriptBlockLogging"
```

EnableScriptBlockLogging should equal 1.

---

## 8. PowerShell Transcription Disabled

Enable PowerShell Transcription:

```powershell
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\Windows\PowerShell\Transcription" -Force
Set-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\Windows\PowerShell\Transcription" -Name EnableTranscripting -Value 1
```

Verify:

```powershell
Get-ItemProperty "HKLM:\SOFTWARE\Policies\Microsoft\Windows\PowerShell\Transcription"
```

EnableTranscripting should equal 1.

---

## 9. WinRM Basic Authentication Enabled

Disable Basic Authentication:

```powershell
winrm set winrm/config/service/auth '@{Basic="false"}'
```

Verify:

```powershell
winrm get winrm/config/service/auth
```

Basic should equal false.

---

## 10. SMBv1 Client Enabled

Disable SMBv1 Client:

```powershell
Set-SmbClientConfiguration -EnableSecuritySignature $true -RequireSecuritySignature $true -Force
```

Verify:

```powershell
Get-SmbClientConfiguration | Select EnableSecuritySignature,RequireSecuritySignature
```

Both settings should return True.

---

## Validation

After applying all remediations:

```powershell
gpupdate /force
```

Reboot the system and perform a new STIG/Tenable scan to verify compliance. Findings should move from Failed to Passed.
