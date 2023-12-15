# Building a Azure Cloud, SOC+Honeynet using (Live Traffic)

![image](https://github.com/KlausSecureShield/Cloud-SOC-Azure-Honeynet/assets/153767032/39d30daa-10ad-4f85-acc9-31f732b8987d)


## Introduction

In this project, I created a small honeynet using Microsoft Azure. I gathered log information from various sources and stored it in a Log Analytics workspace. I took this data and used Microsoft Sentinel to create attack maps, trigger alerts, and manage incidents.

First, I measured some security metrics in a not-so-secure virtual environment for 24 hours. After that, I implemented some security measures to enhance the environment's security. Then, I measured the metrics again for another 24 hours. The results and metrics are explained below.

## The architecture of the mini honeynet in Azure consists of the following components:

- Virtual Network (VNet)
- Virtual Machines (VMs) (2 windows, 1 linux)
  
  ```Also, within one of the Windows VMs, I installed and set up a Microsoft SQL server and created a SQL user account with a password. The purpose was to see if anyone on the internet would attempt to find and attack it, an aspect that many entities might overlook. The second Windows VM was configured as an external attacker, allowing me to simulate the perspective of a potential malicious actor against the exposed VMs as well.```
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
For the "BEFORE" metrics, all resources were initially deployed and exposed to the internet. The Virtual Machines had both their Network Security Groups and built-in firewalls configured to be wide open. This setup simulated how poor network settings, achievable with just a few clicks, can instantly make any machine extremely vulnerable. All other resources were deployed with public endpoints visible to the Internet, including Storage, MSSQL servers, etc.

![image](https://github.com/KlausSecureShield/Cloud-SOC-Azure-Honeynet/assets/153767032/610a7348-1275-4d6f-b834-ca054fd3694c)



## Architecture After Hardening / Security Controls

For the "AFTER" metrics, Network Security Groups were strengthened by blocking ALL traffic, except for my admin workstation. Additionally, all other resources were safeguarded by their built-in firewalls, along with the implementation of Private Endpoints.

![image](https://github.com/KlausSecureShield/Cloud-SOC-Azure-Honeynet/assets/153767032/30289f44-e249-49f1-9777-7f8f298a375d)



## Attack Maps, 24Hours Before Hardening on the left & 24Hours After Hardening on the Right, ```Windows VM.```
![image](https://github.com/KlausSecureShield/Cloud-SOC-Azure-Honeynet/assets/153767032/40583f5b-65eb-4143-bf06-17668a23cb24)
```One interesting observation was that during the hardening process, three attack attempts still managed to make their way through the system. This could be attributed to a Brute Force bot continually pinging the Windows VM's IP address. Fortunately, this specific attempt eventually ceased after all the firewall settings were properly configured.``` 

## Attack Maps, 24Hours Before Hardening on the left & 24Hours After Hardening on the Right, ```Linux VM.```
![image](https://github.com/KlausSecureShield/Cloud-SOC-Azure-Honeynet/assets/153767032/25fb5ff7-fe20-4d5d-a95b-81c2e920daae)
```The Linux VM attack maps exhibited similar behavior, attacks significantly decreased immediately after the hardening process. However, a few attempts still reached its IP address. Fortunately, all brute force attempts ceased after the 24-hour period``` 

## Attack Maps, ```Microsoft SQL Server```
![image](https://github.com/KlausSecureShield/Cloud-SOC-Azure-Honeynet/assets/153767032/109fed3a-2661-43b0-b4ad-1d0c4a9e45b4)
```This is the attack map for the Microsoft SQL Server on the Windows VM over a 30-day period. This section was added to highlight key differences in attack locations, specifically focusing on Microsoft SQL Servers. Initially, after setting up the VMs and SQL server, all internet traffic primarily targeted the Windows and Linux VMs and was more sporadic. However, after a few days, a more consistent type of brute force attacks appeared, originating from more sophisticated countries like South Korea, Russia, and specific areas in the United States. This information should raise concerns for any entity running SQL servers. It emphasizes the importance of always having machines and networks behind subnets and advanced firewalls, with longer and more complex passwords at the very least—no exceptions. This traffic map indicates that merely having an enabled SQL database exposes you to a more targeted and advanced type of threat, as I will demonstrate in the next picture below and then explain.```  

![image](https://github.com/KlausSecureShield/Cloud-SOC-Azure-Honeynet/assets/153767032/5eb79d82-cf25-46e9-a1ee-ccdb699b0462)

```The picture above is the attack map for the same Microsoft SQL Server 24 hours before hardening my network. Once again, observe similar threat behavior as mentioned earlier. The attacks are persistent but only from a few key locations that appear to be more tech-savvy, unlike the more random patterns seen in the Windows and Linux attack maps. I feel like this detail should not be overlooked, as it was not a planned part of this project and can provide valuable metrics for someone reading this.```

 
## Metrics Before Hardening / Security Controls

The following table displays the metrics I measured in my insecure environment inside Microsoft Azure for 24 hours:
Start Time 2023-12-09 14:43:24
Stop Time 2023-12-10 14:43:24

| Metric                   | Count
| ------------------------ | -----
| SecurityEvent            | 13338
| Syslog                   | 13736
| SecurityAlert            | 18
| SecurityIncident         | 159


## Metrics After Hardening / Security Controls

The following table displays the metrics I measured in my Microsoft Azure environment for an additional 24 hours after implementing security controls:
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

In this project, a mini honeynet was built in Microsoft Azure, and log sources were integrated into a Log Analytics workspace. Microsoft Sentinel was utilized to trigger alerts and create incidents based on the ingested logs. Additionally, metrics were measured in the insecure environment before security controls were applied, and then again after implementing security measures. It is noteworthy that the number of security events and incidents were significantly reduced after the security controls were applied, demonstrating their effectiveness.


If you would like to know more, enjoyed my report, or have any comments or questions, please feel free to reach out to me here:  http://www.linkedin.com/in/klausSecOps-ln


