

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

3. Testing Threat Intel Alert Pipeline

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

Step 4.1: Developing the Remediation Script

Install the jq utility package for parsing nested JSON data structures, and establish the custom remediation
script file at /var/ossec/active-response/bin/remove-threat.sh:

<img width="506" height="47" alt="Screenshot 2026-07-09 001147" src="https://github.com/user-attachments/assets/2354140e-809f-41e9-abfe-9ccdc3b7a546" />

Remediation Script Architecture (Bash):

<img width="540" height="526" alt="Screenshot 2026-07-09 001324" src="https://github.com/user-attachments/assets/0eb73880-589a-42b4-ab03-b94c682d5c55" />

<img width="720" height="215" alt="image" src="https://github.com/user-attachments/assets/53077214-88a7-494c-88d1-80038af09d3b" />

Assign administrative execution privileges to the automation binary and cycle the local host agent process:


<img width="475" height="49" alt="Screenshot 2026-07-09 001525" src="https://github.com/user-attachments/assets/67c54c02-0265-4dcd-a685-632cf823d4e1" />

<img width="720" height="77" alt="image" src="https://github.com/user-attachments/assets/fa74cf28-64b4-4762-81f9-b74ba790610d" />

Step 4.2: Mapping Active Response Rules on the Manager

To bind the containment script execution pipeline to standard analytical threat logs, inject the command
wrapper definitions directly above the Active Response triggers section inside the manager ossec.conf file:

* sudo nano /var/ossec/etc/ossec.conf

 You will find a area like this

 <img width="720" height="393" alt="image" src="https://github.com/user-attachments/assets/b36e34d7-76c9-4aad-a441-4a05b4b29ff6" />

 edit the code before active response starts

 <img width="480" height="258" alt="Screenshot 2026-07-09 002035" src="https://github.com/user-attachments/assets/cdb52106-ad55-4df8-bfd5-0fda8c7c3bd0" />

 <img width="720" height="408" alt="image" src="https://github.com/user-attachments/assets/e7647a20-f58b-4545-9d8d-e13f9c4721c6" />

 *  systemctl restart wazuh-manager

5. Validation, Containment, and Analytical Diagnostics
To validate the complete automated defense pipeline, a secondary EICAR test object download sequence is
initialized inside the target endpoint boundary directory. Within fractions of a second, the active defense
routine intercepts the threat signature, triggers an engine validation pass, and clears the file trace natively
from disk space

<img width="720" height="410" alt="image" src="https://github.com/user-attachments/assets/60d1566a-1fc6-4b9f-97f2-7d4e4fab43e4" />

Review the log aggregations on the security console to observe the definitive operational state indicating
successful eradication.

<img width="720" height="395" alt="image" src="https://github.com/user-attachments/assets/1af11d82-babc-4cc7-a6bd-8962a46d6eae" />
