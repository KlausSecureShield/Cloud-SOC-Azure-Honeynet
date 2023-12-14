# Cloud-SOC-Azure-Honeynet

# Building a SOC + Honeynet in Azure (Live Traffic)
![image](https://github.com/KlausSecureShield/Cloud-SOC-Azure-Honeynet/assets/153767032/39d30daa-10ad-4f85-acc9-31f732b8987d)


## Introduction

In this project, I created a small honeynet using Microsoft Azure. I gathered log information from different sources and stored it in a Log Analytics workspace. I took this data and used Microsoft Sentinel to create attack maps, set off alerts, and manage incidents.

First, I measured some security metrics in the not-so-secure environment for 24 hours. After that, I implemented some security measures to make the environment more secure. Then, I measured the metrics again for another 24 hours. The results and metrics are explained below.

## The architecture of the mini honeynet in Azure consists of the following components:

- Virtual Network (VNet)
- Virtual Machines (VMs) (2 windows, 1 linux)
  
  ```Also within one of the Windows VMs, I installed and setup a Microsoft SQL server and passworded account to see if anyone on the internet will seek it out and try to attack it, a scenario that many entities may not consider - (more on this later)```
- Network Security Groups (NSGs) (for the Virtual Machines network traffic)
- Log Analytics Workspace (to store all the data)
- Microsoft Sentinel (for attack maps, incidents and alerts)
- Azure Storage Account

## The (Azure KQL queries) Event Logs used to monitor events:
- SecurityEvent (are for the Windows Vm Event Logs)
- Syslog (are for the Linux Event Logs)
- SecurityAlert (are for the Log Analytics "workspace" alerts)
- SecurityIncident (Incidents created by Sentinel)

## Architecture Before Hardening / Security Controls
![image](https://github.com/KlausSecureShield/Cloud-SOC-Azure-Honeynet/assets/153767032/610a7348-1275-4d6f-b834-ca054fd3694c)



## Architecture After Hardening / Security Controls
![image](https://github.com/KlausSecureShield/Cloud-SOC-Azure-Honeynet/assets/153767032/30289f44-e249-49f1-9777-7f8f298a375d)


For the "BEFORE" metrics, all resources were originally deployed and exposed to the internet. The Virtual Machines had both their Network Security Groups and built-in firewalls configured to be wide open ```to simulate how bad settings (with just a few clicks) can instantly make any machine extremely vulnerable```. 
All other resources were deployed with public endpoints visible to the Internet, such as (Azure Key Vault, Storage, MSSQL servers etc.)

For the "AFTER" metrics, Network Security Groups were hardened by blocking ALL traffic with the exception of my admin workstation, and all other resources were protected by their built-in firewalls as well as Private Endpoints.

## Attack Maps Before Hardening / Security Controls
![NSG Allowed Inbound Malicious Flows](https://i.imgur.com/1qvswSX.png)<br>
![Linux Syslog Auth Failures](https://i.imgur.com/G1YgZt6.png)<br>
![Windows RDP/SMB Auth Failures](https://i.imgur.com/ESr9Dlv.png)<br>

## Metrics Before Hardening / Security Controls

The following table shows the metrics I measured in my insecure environment inside Microsoft Azure for 24 hours:
Start Time 2023-12-09 14:43:24
Stop Time 2023-12-10 14:43:24

| Metric                   | Count
| ------------------------ | -----
| SecurityEvent            | 13338
| Syslog                   | 13736
| SecurityAlert            | 18
| SecurityIncident         | 159

## Attack Maps After Hardening / Notes

```All map queries actually returned no results due to no instances of malicious activity for the 24 hour period after hardening. A few attacks did register at the beginning of the hardening process, they occured due to bots that were relentlessly spamming the exposed (open to the internet) Windows and Linux VMs during the hardening transition and stopped occuring after hardening was completed.```

## Metrics After Hardening / Security Controls

The following table shows the metrics I measured in my insecure environment inside Microsoft Azure for another 24 hours, but after I applied security controls:
Start Time 2023-12-12 08:39:53
Stop Time	2023-12-13 08:39:53

| Metric                   | Count
| ------------------------ | -----
| SecurityEvent            | 3
| Syslog                   | 82
| SecurityAlert            | 0
| SecurityIncident         | 0


| Change after security environment           | Count
| ------------------------                    | -----
| SecurityEvent (Windows VMs)                 | -99.98%
| Syslog (Linux VMs)                          | -99.40%
| SecurityAlert (Microsoft Defender for Cloud)| -100%
| SecurityIncident (Sentinel Incidents)       | -100%

## Conclusion

In this project, a mini honeynet was constructed in Microsoft Azure and log sources were integrated into a Log Analytics workspace. Microsoft Sentinel was employed to trigger alerts and create incidents based on the ingested logs. Additionally, metrics were measured in the insecure environment before security controls were applied, and then again after implementing security measures. It is noteworthy that the number of security events and incidents were drastically reduced after the security controls were applied, demonstrating their effectiveness.

It is worth noting that if the resources within the network were heavily utilized by regular users, it is likely that more security events and alerts may have been generated within the 24-hour period following the implementation of the security controls.

If you would like to know more, enjoyed my report, have any comments or questions, please reach out to me here http://www.linkedin.com/in/klausSecOps-ln


