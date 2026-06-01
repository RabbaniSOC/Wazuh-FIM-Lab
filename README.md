# Multi-Endpoint File Integrity Monitoring (FIM) Lab with Wazuh SIEM

## 📌 Project Overview

This project demonstrates the deployment of a containerized **Wazuh SIEM Stack** (Manager, Indexer, and Dashboard) used to monitor file integrity across multiple operating system environments.

The lab showcases real-time security monitoring across:

* **Windows 11 Pro** (Host Endpoint)
* **Ubuntu 26.04 LTS (WSL2)** (Linux Endpoint)

By implementing **File Integrity Monitoring (FIM)**, the environment captures and logs real-time file operations such as:

* File Creation
* File Modification
* File Deletion

The solution leverages native operating system monitoring mechanisms:

* **Inotify** on Linux
* **ReadDirectoryChangesW** on Windows

---

# 🏗️ Lab Architecture

## Components

### Wazuh SIEM Stack

* Wazuh Manager
* Wazuh Indexer
* Wazuh Dashboard

### Endpoints

* Windows 11 Host Agent
* Ubuntu WSL2 Agent

### Manager IP

```text
192.168.1.158
```

### Monitored Directories

**Windows Agent**

```text
C:\FIM-Test
```

**Ubuntu WSL Agent**

```text
/home/linux/test
```

---

# 🛠️ Phase 1: Deploy Wazuh SIEM Stack

Deploy the Wazuh containers using Docker Compose:

```bash
docker compose up -d
```

Verify running containers:

```bash
docker ps
```

Access the manager container shell:

```bash
docker exec -it <container_id> bash
```

---

# 🐧 Phase 2: Ubuntu WSL2 Agent Installation

## Check Network Configuration

```bash
ip addr
hostname -I
```

## Install Wazuh Agent

```bash
sudo apt update
sudo apt install wazuh-agent=4.8.2-1 -y
```

## Register Agent with Manager

```bash
sudo /var/ossec/bin/agent-auth -m 192.168.1.158
```

## Restart Agent

```bash
sudo /var/ossec/bin/wazuh-control restart
```

---

## Configure File Integrity Monitoring

Edit:

```bash
sudo nano /var/ossec/etc/ossec.conf
```

Add:

```xml
<directories check_all="yes" report_changes="yes" realtime="yes">
/home/linux/test
</directories>
```

Restart the agent:

```bash
sudo /var/ossec/bin/wazuh-control restart
```

Create the monitored directory:

```bash
mkdir -p ~/test
```

---

# 🪟 Phase 3: Windows Agent Installation

## Download Wazuh Agent

```powershell
Invoke-WebRequest -Uri https://packages.wazuh.com/4.x/windows/wazuh-agent-4.8.2-1.msi -OutFile $env:TEMP\wazuh-agent.msi
```

## Install Agent

```powershell
msiexec.exe /i $env:TEMP\wazuh-agent.msi /q WAZUH_MANAGER="192.168.1.158" WAZUH_AGENT_NAME="soc-lab-endpoint"
```

## Start Service

```powershell
NET START WazuhSvc
```

---

## Configure File Integrity Monitoring

Edit:

```text
C:\Program Files (x86)\ossec-agent\ossec.conf
```

Add:

```xml
<directories check_all="yes" report_changes="yes" realtime="yes">
C:\FIM-Test
</directories>
```

Restart the service:

```powershell
Restart-Service WazuhSvc
```

Create the monitored directory:

```powershell
mkdir C:\FIM-Test
```

---

# 🔍 Validation and Attack Simulation

## Linux Endpoint Testing

Execute the following commands:

```bash
touch ~/test/file1.txt
echo "modified" >> ~/test/file1.txt
rm ~/test/file1.txt
```

Expected Results:

* File creation alert
* File modification alert
* File deletion alert

---

## Windows Endpoint Testing

Execute the following commands:

```powershell
echo hello > C:\FIM-Test\test.txt
echo modified >> C:\FIM-Test\test.txt
del C:\FIM-Test\test.txt
```

Expected Results:

* File creation alert
* File modification alert
* File deletion alert

---

# 📊 Dashboard Results

## Active Agents

Verify both agents are connected:

* Windows Agent
* Ubuntu WSL2 Agent

**Screenshot:** `images/01-agents-connected.png`

---

## Linux FIM Alerts

Verify alerts generated from:

```text
/home/linux/test
```

**Screenshot:** `images/02-linux-fim-alerts.png`

---

## Windows FIM Alerts

Verify alerts generated from:

```text
C:\FIM-Test
```

**Screenshot:** `images/03-windows-fim-alerts.png`

---

## Alert Investigation

Review:

* Event timestamp
* Agent ID
* File path
* Action performed
* Rule triggered

**Screenshot:** `images/04-alert-details.png`

---

# 🎯 Skills Demonstrated

* Wazuh SIEM Deployment
* Docker Containerization
* Linux Administration
* Windows Administration
* File Integrity Monitoring (FIM)
* Security Monitoring
* Alert Investigation
* Endpoint Security
* SOC Analyst Operations
* Incident Detection

---

# 📚 Technologies Used

* Wazuh 4.8.2
* Docker
* Docker Compose
* Windows 11 Pro
* Ubuntu 26.04 LTS (WSL2)
* Linux Inotify
* Windows ReadDirectoryChangesW
* SIEM
* File Integrity Monitoring

---

# 🚀 Outcome

This project successfully demonstrates real-time File Integrity Monitoring across Windows and Linux endpoints using Wazuh SIEM. Security events generated from file creation, modification, and deletion activities are centrally collected, analyzed, and visualized through the Wazuh Dashboard, simulating real-world SOC monitoring workflows.
