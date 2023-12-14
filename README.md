# Building an Azure Cloud-SOC-Honeynet with Live Traffic

![image](https://github.com/KlausSecureShield/Cloud-SOC-Azure-Honeynet/assets/153767032/39d30daa-10ad-4f85-acc9-31f732b8987d)


## Introduction

In this project, I created a small honeynet using Microsoft Azure. I gathered log information from different sources and stored it in a Log Analytics workspace. I took this data and used Microsoft Sentinel to create attack maps, set off alerts, and manage incidents.

First, I measured some security metrics in a not-so-secure virtual environment for 24 hours. After that, I implemented some security measures to make the environment more secure. Then, I measured the metrics again for another 24 hours. The results and metrics are explained below.

## The architecture of the mini honeynet in Azure consists of the following components:

- Virtual Network (VNet)
- Virtual Machines (VMs) (2 windows, 1 linux)
  
  ```Also within one of the Windows VMs, I installed and setup a Microsoft SQL server and password SQL account to see if anyone on the internet will seek it out and try to attack it, a scenario that many entities may not consider - (more on this later.)```
  ```The second Windows VM was set up as an outside attacker, so I could also pose as a potential bad actor against the exposed VMs as well.```
- Network Security Groups (NSGs) (for the Virtual Machines network traffic)
- Log Analytics Workspace (to store all the data)
- Microsoft Sentinel (for attack maps, incidents and alerts)
- Azure Storage Account

## The (Azure KQL query) Event Logs used to monitor events:
- SecurityEvent (for the Windows Vm Event Logs)
- Syslog (for the Linux Event Logs)
- SecurityAlert (for the Log Analytics "workspace" alerts)
- SecurityIncident (for Incidents created by Sentinel)


## Architecture Before Hardening / Security Controls
For the "BEFORE" metrics, all resources were originally deployed and exposed to the internet. The Virtual Machines had both their Network Security Groups and built-in firewalls configured to be wide open, ```to simulate how bad network settings (with just a few clicks) can instantly make any machine extremely vulnerable```. 
All other resources were deployed with public endpoints visible to the Internet, such as (Storage, MSSQL servers etc.)

![image](https://github.com/KlausSecureShield/Cloud-SOC-Azure-Honeynet/assets/153767032/610a7348-1275-4d6f-b834-ca054fd3694c)



## Architecture After Hardening / Security Controls

For the "AFTER" metrics, Network Security Groups were hardened by blocking ALL traffic with the exception of my admin workstation, and all other resources were protected by their built-in firewalls as well as Private Endpoints.

![image](https://github.com/KlausSecureShield/Cloud-SOC-Azure-Honeynet/assets/153767032/30289f44-e249-49f1-9777-7f8f298a375d)



## Attack Maps, 24Hours Before Hardening on the left & 24Hours After Hardening on the Right, for the Windows VM.
![image](https://github.com/KlausSecureShield/Cloud-SOC-Azure-Honeynet/assets/153767032/40583f5b-65eb-4143-bf06-17668a23cb24)
```One interesting observation was that during the hardening process 3 attack attempts still made their way through the system, this may be due to a Brute force bot constantly pinging the Windows Vm's IP address. This specific attempt eventually went away after all of the firewall settings were up.``` 

## Attack Maps, 24Hours Before Hardening on the left & 24Hours After Hardening on the Right, for the Linux VM.
![image](https://github.com/KlausSecureShield/Cloud-SOC-Azure-Honeynet/assets/153767032/25fb5ff7-fe20-4d5d-a95b-81c2e920daae)
```The Linux Vm attack maps showed similar behavior, attacks immediately became less frequent, however a few attempts still made their way to its IP address, however all brute force attempts went way after the 24 hour period.``` 

## Attack Maps, Microsoft SQL server
![image](https://github.com/KlausSecureShield/Cloud-SOC-Azure-Honeynet/assets/153767032/109fed3a-2661-43b0-b4ad-1d0c4a9e45b4)
```This is the attack map for the Microsoft SQL server on the Windows Vm over a 30 day period, this section was added to show some key differences in the attack locations specifically on Microsoft SQL Servers. Initally after being setup, all of the internet traffic only gravitated to the Windows and Linux VMs and were more sporadic, however after a few days, a more consistent type of brute force attacks seem to come from more sophisticated countries like South Korea, Russia and specific areas in the United States, this information should be concerning to any entity runnning SQL servers, to ALWAYS have their machines and networks behind Subnets and Advanced Firewalls with longer more complexed passwords at the very LEAST, no exceptions, because it seems from this traffic map, that just having an enabled SQL database opens you up to a more targeted and advanced type of threat, as I will show in the next picture below and then explain```  

![image](https://github.com/KlausSecureShield/Cloud-SOC-Azure-Honeynet/assets/153767032/5eb79d82-cf25-46e9-a1ee-ccdb699b0462)

```This picture above is the attack map for the same Microsoft SQL Server 24 hours before hardening my network. Notice once again similar threat behavior as mentioned above, the attacks are still persistent but only from a few key locations that seem to be more tech savvy, and not more random like the Windows and Linux attack maps. I fell like this detail should not be overlooked as it was not a planned part of this project and can be valuable metrics to someone reading this.```

 
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


