

1. Executive Summary

   This technical report outlines the engineering process for integrating the Wazuh SIEM/EDR platform with the VirusTotal Threat Intelligence API. The primary objective is to     implement real-time File Integrity Monitoring (FIM) across managed endpoints, automatically query unknown or suspicious file hashes against VirusTotal database                 infrastructure, and execute real-time automated containment via customized Active Response scripts. The integration was successfully simulated using an Ubuntu Linux agent      and verified using standard EICAR test malware variants.

2.  Step-by-Step Deployment Guide

Step 2.1: Endpoint Directory Provisioning

   To establish a controlled environment for testing malicious binaries, a designated sandbox directory is provisioned on the Ubuntu Agent machine with appropriate execution      and modification permissions

     * mkdir /tmp/malware 
     * chmod 777 /tmp/malware
     
   <img width="720" height="392" alt="image" src="https://github.com/user-attachments/assets/76c960c8-0f03-4915-83e9-f7ff32472e31" />

   Step 2.2: Deploying File Integrity Monitoring (FIM) Policies
The centralized monitoring configuration is pushed from the Wazuh Dashboard. Navigate to Agents
management → Groups, edit the default shared configuration profile, and append the following structured
XML block to enforce real-time auditing on the designated path:

*      <agent_config os="Linux">
   <syscheck>
   <directories realtime="yes" check_all="yes">/tmp/malware</directories>
   </syscheck>
   </agent_config>

   <img width="720" height="405" alt="image" src="https://github.com/user-attachments/assets/471d1634-9bf0-48fd-8557-e8c15bc93d11" />

   <img width="720" height="398" alt="image" src="https://github.com/user-attachments/assets/25011685-d094-4e2d-a3b4-b80585f917fa" />

   Restart the agent machine

   * systemctl restart wazuh-agent
 
  Check the FIM working in /tmp/malware

  *  echo "hello world" >> /tmp/malware/hello.txt

    <img width="720" height="81" alt="image" src="https://github.com/user-attachments/assets/2a68cc91-2b54-4d86-94b2-da8edb1e8693" />

     It is working


   




