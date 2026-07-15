# Wazuh SOC Home Lab

This project explains how to install and configure a Security Operations Center (SOC) lab using Wazuh on the Linux system. The objective was to build a centralized log monitoring and threat detection setup, perform controlled attack simulations, and observe how security events are detected and displayed through a real-time dashboard.

## Lab Setup

| Machine | Role | OS |
|---|---|---|
| Wazuh Manager | SIEM server (Manager + Indexer + Dashboard) | Ubuntu 24.04 |
| Wazuh Agent | Monitored endpoint | Ubuntu 22.04 |
| Attacker | Runs the simulated attacks | Kali Linux |

VirtualBox, host-only network between the VMs, Wazuh 4.14.3.

## Attacks Simulated

- **SSH brute force** — Hydra against the agent (rule 5763, MITRE T1110)
- **File integrity** — modified `/etc/passwd`, caught by checksum monitoring (rule 550, MITRE T1565.001)
- **Port scan** — Nmap recon against the agent (rules 52002, 533)
- **Privilege escalation** — repeated failed `sudo`/`su` attempts (rules 5503, 5404, 5710)

## Repo Structure

```
wazuh-soc-homelab/
├── README.md
├── Wazuh_Configuration_Attack_Simulation_Log_Monitoring.pdf
```
Full steps and evidence are in the PDF.

## Setup

```bash
# Manager (Ubuntu 24.04)
sudo apt update && sudo apt upgrade -y
sudo apt install curl apt-transport-https unzip wget -y
curl -sO https://packages.wazuh.com/4.14/wazuh-install.sh
sudo bash ./wazuh-install.sh -a

# Agent (Ubuntu 22.04)
wget https://packages.wazuh.com/4.x/apt/pool/main/w/wazuh-agent/wazuh-agent_4.14.3-1_amd64.deb
sudo WAZUH_MANAGER='<manager-ip>' WAZUH_AGENT_GROUP='default' WAZUH_AGENT_NAME='<name>' \
  dpkg -i ./wazuh-agent_4.14.3-1_amd64.deb
sudo systemctl restart wazuh-agent
```

## Key Takeaways

- SIEM deployment and agent-based log forwarding
- Reading raw Wazuh alert fields to tell real detections from noise
- Mapping alerts to MITRE ATT&CK for prioritization
- Troubleshooting OS/Wazuh compatibility and VM resource limits

