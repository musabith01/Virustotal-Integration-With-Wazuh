

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

*  <img width="655" height="155" alt="Screenshot 2026-07-08 232804" src="https://github.com/user-attachments/assets/1937e803-6b1a-45d4-91f2-fa3d193da42f" />


 <img width="720" height="405" alt="image" src="https://github.com/user-attachments/assets/471d1634-9bf0-48fd-8557-e8c15bc93d11" />

   <img width="720" height="398" alt="image" src="https://github.com/user-attachments/assets/25011685-d094-4e2d-a3b4-b80585f917fa" />

   Restart the agent machine

   * systemctl restart wazuh-agent
 
  Check the FIM working in /tmp/malware

  *  echo "hello world" >> /tmp/malware/hello.txt

<img width="720" height="81" alt="image" src="https://github.com/user-attachments/assets/8717d2c5-b1d1-4818-9a25-663a7fceed1a" />

 Step 2.3: Configuring VirusTotal Threat Intelligence on Wazuh Manager

 Locate the integration blocks and insert the configuration below, replacing the placeholder text with an active,
authenticated VirusTotal API Key:

<img width="638" height="128" alt="Screenshot 2026-07-08 235644" src="https://github.com/user-attachments/assets/d1dc3868-6b44-4ea7-a99a-c8338b0ff218" />


Establish a secure SSH session into the core Wazuh Manager server to configure the outbound VirusTotal
API lookup block inside the global configuration file (ossec.conf):

copy this code and paste it in /var/ossec/etc/ossec.conf

so connect the ssh service of server in ubuntu agent.

<img width="360" height="95" alt="Screenshot 2026-07-08 235755" src="https://github.com/user-attachments/assets/b45cb65e-4e29-41bd-8f62-9fcafef503a3" />


    
<img width="720" height="592" alt="image" src="https://github.com/user-attachments/assets/cae50de2-42d3-47cf-85b9-501ff8b2056c" />

* nano /var/ossec/etc/ossec.conf

<img width="720" height="380" alt="image" src="https://github.com/user-attachments/assets/b946638f-2773-4b06-bf38-772445e2ee11" />

 Replace with your API key got from virustotal

 Apply the global settings by initializing a full manager service restart command:

 * systemctl restart wazuh-manager

4. Testing Threat Intel Alert Pipeline

  An operational test is executed by downloading a benign standard antivirus verification string (EICAR) directly
  into the monitored directory path:

  * curl https://secure.eicar.org/eicar.com -o /tmp/malware/eicar

<img width="720" height="84" alt="image" src="https://github.com/user-attachments/assets/0bdf2edf-7da7-41eb-92c9-faeabf89c526" />

Once written to disk, the FIM engine generates an alert payload which triggers an automatic hash submission
to VirusTotal. The response is reflected directly inside the Threat Hunting dashboard.

<img width="720" height="383" alt="image" src="https://github.com/user-attachments/assets/567956ab-c7ec-4044-bf1f-22eb4e527827" />

We can see the Virustotal alert in Threathunting window.

Also we can see the virustotal result

<img width="720" height="398" alt="image" src="https://github.com/user-attachments/assets/eecaf449-880c-488d-807e-116fa5eac00f" />




