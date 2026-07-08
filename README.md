1.	INTRODUCTION & PROJECT SCOPE
   
Modern Security Operations Centers (SOCs) require automated orchestration to minimize the window of vulnerability between threat ingress and active mitigation.This technical report details the full deployment,testing, and implementation of an automated malware threat intelligence and remediation workflow.By integration of Wazuh SIEM and the VirusTotal Threat Intelligence API,the SOC team successfully achieved zero-latency local threat purging on target endpoints.

The project scope encompasses real-time File Integrity Monitoring (FIM) over vulnerable system directories,programmatic hash submission to third-party multi-engine intelligence scanners,and localized execution of active mitigation playbooks to permanently sever malicious footprinting.
