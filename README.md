

1. Executive Summary

   This technical report outlines the engineering process for integrating the Wazuh SIEM/EDR platform with the VirusTotal Threat Intelligence API. The primary objective is to     implement real-time File Integrity Monitoring (FIM) across managed endpoints, automatically query unknown or suspicious file hashes against VirusTotal database                 infrastructure, and execute real-time automated containment via customized Active Response scripts. The integration was successfully simulated using an Ubuntu Linux agent      and verified using standard EICAR test malware variants.

2.  Step-by-Step Deployment Guide

Step 2.1: Endpoint Directory Provisioning

   To establish a controlled environment for testing malicious binaries, a designated sandbox directory is provisioned on the Ubuntu Agent machine with appropriate execution      and modification permissions

     * mkdir /tmp/malware 
     * chmod 777 /tmp/malware
     
   <img width="720" height="392" alt="image" src="https://github.com/user-attachments/assets/76c960c8-0f03-4915-83e9-f7ff32472e31" />


