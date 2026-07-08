

1. Executive Summary

   This technical report outlines the engineering process for integrating the Wazuh SIEM/EDR platform with the VirusTotal Threat Intelligence API. The primary objective is to     implement real-time File Integrity Monitoring (FIM) across managed endpoints, automatically query unknown or suspicious file hashes against VirusTotal database                 infrastructure, and execute real-time automated containment via customized Active Response scripts. The integration was successfully simulated using an Ubuntu Linux agent      and verified using standard EICAR test malware variants.
2. Infrastructure & Component Overview
   
   The implementation architecture relies on the following major components orchestrated to enable automated detection and remediation:

    <img width="672" height="358" alt="image" src="https://github.com/user-attachments/assets/41581e49-487f-4096-ae9d-325feb82d5d6" />


