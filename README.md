# Building a SOC + Honeynet in Azure (Live Traffic)
![Cloud Honeynet / SOC](https://i.imgur.com/kPVdLSw.png)

## Introduction

In this project, I leverage Microsoft Azure to bulid a mini honeynet, create and configure other Azure resources, and ingest log sources from these resources into a Log Analytics workspace. These ingested logs are then used by Microsoft Sentinel to create incidents, trigger alerts, and build attack maps. For 24 hours, I measure some security metrics in the insecure environment I created. After 24 hours, I apply some security controls to harden the environment, measure metrics for another 24 hours, and present the results and key observations further below. Metrics were derived from the following log sources:

- SecurityEvent (Windows Event Logs)
- Syslog (Linux Event Logs)
- SecurityAlert (Log Analytics Alerts Triggered)
- SecurityIncident (Incidents created by Sentinel)
- AzureNetworkAnalytics_CL (Malicious Flows allowed into our honeynet)

## Architecture Before Hardening / Security Controls
![Architecture Diagram](https://i.imgur.com/zNxETMO.png)

## Architecture After Hardening / Security Controls
![Architecture Diagram](https://i.imgur.com/0saQTfy.png)

The mini honeynet architecture in Azure consists of the following components:

- Virtual Network (VNet)
- Network Security Group (NSG)
- Virtual Machines (2 windows, 1 linux)
- Log Analytics Workspace
- Azure Key Vault
- Azure Storage Account
- Microsoft Sentinel

For the "BEFORE" metrics, all resources were originally deployed and exposed to the internet. The Virtual Machines had both their Network Security Groups and built-in firewalls wide open, and all other resources are deployed with public endpoints visible to the Internet; aka, no use for Private Endpoints.

For the "AFTER" metrics, VM-attached Network Security Groups were hardened by blocking ALL traffic with the exception of my admin workstation. All other resources were protected by their built-in firewalls and established Private Endpoints.

## Attack Maps Before Hardening / Security Controls
![NSG Allowed Inbound Malicious Flows](https://i.imgur.com/tieyBGA.png)<br>
![Linux Syslog Auth Failures](https://i.imgur.com/btCBdxy.png)<br>
![Windows RDP/SMB Auth Failures](https://i.imgur.com/ZBqt5pt.png)<br>

## Metrics Before Hardening / Security Controls

The following table shows key metrics measured in our insecure environment over 24 hours:  

Start Time 2023-11-05 21:19:54  
Stop Time 2023-11-06 21:19:54

| Metric                   | Count
| ------------------------ | -----
| SecurityEvent            | 115602
| Syslog                   | 8234
| SecurityAlert            | 15
| SecurityIncident         | 259
| AzureNetworkAnalytics_CL | 3019

## Attack Maps After Hardening / Security Controls

```All map queries actually returned no results (satisfactorily) due to no instances of malicious activity for the 24 hour period after hardening.```

## Metrics After Hardening / Security Controls

The following table shows key metrics measured in our Azure environment over 24 hours, after applying security controls:

Start Time 2023-11-11 00:26:59  
Stop Time	2023-11-12 00:26:59

| Metric                   | Count
| ------------------------ | -----
| SecurityEvent            | 9037
| Syslog                   | 1
| SecurityAlert            | 0
| SecurityIncident         | 0
| AzureNetworkAnalytics_CL | 0

## Comparison of Before and After Metrics

From observing the difference in metrics between an insecure and hardened environment, key improvements and KPIs are summarized below:  

| Metric                   | Percent (%) change in alerts/incidents
| ------------------------ | -------------------------------------------
| SecurityEvent            | -92.18%
| Syslog                   | -99.99%
| SecurityAlert            | -100.00%
| SecurityIncident         | -100.00%
| AzureNetworkAnalytics_CL | -100.00%

## Recap / Conclusion

In this project, a mini honeynet was constructed in Microsoft Azure and log sources from the various resources built within the Azure environment were integrated into a Log Analytics workspace to be used by Microsoft Sentinel. Within Sentinel, alerts were triggered and incidents were created based on the ingested logs.  

Additionally, metrics measured from before and after hardening with security controls, including measurements taken after remediation steps and security measure implementations, reveal a clear improvement in overall security posture. The number of security events and incidents were drastically reduced after the security controls were applied, demonstrating their effectiveness and highlighting the importance of fundamental security hardening and establishing basic security controls within any environment to reduce risk towards organizational assets and critical systems.

It is worth noting that by no means should resources be exposed to the public Internet as demonstrated in this contained lab. Proper security controls and network configurations should be in place, especially for production or information-sensitive systems. It is also worth noting that if the resources within the network were heavily utilized by regular users, it is likely that more security events and alerts may have been generated within the 24-hour period following the implementation of the security controls.
