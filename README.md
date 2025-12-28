# Docker Installation using Bash Wrapper + Ansible Playbook

This repository provides an **interactive and OS-agnostic Docker installation solution** using:

* A **Bash wrapper script** to select the remote user and privilege escalation method
* An **Ansible playbook** that installs and verifies Docker on
  **RedHat-family** and **Debian-family** Linux distributions

The solution is designed for **enterprise environments**, including systems using **sudo** or **dzdo (Centrify/Delinea)** for privilege escalation.

---

## 📌 Features

* Interactive user selection (predefined + custom user support)
* Supports **sudo** and **dzdo** become methods
* Works on:

  * RHEL / CentOS / Rocky / Alma (7, 8, 9)
  * Ubuntu / Debian
* Automatically configures Docker repositories
* Backs up existing Docker repo files with timestamps
* Handles both `yum` and `dnf`
* Ensures `/tmp` permissions are safe for Ansible
* Verifies installation using `docker run hello-world`
* Includes recovery logic if Docker fails to start initially

---

## 📂 Repository Structure

```
.
├── install_docker.sh        # Interactive Bash wrapper
├── install_docker.yml       # Ansible playbook
├── hosts                    # Ansible inventory
└── README.md
```

---

## 🛠 Prerequisites

### Control Node

* Bash 4+
* Ansible 2.13+
* SSH access to managed hosts

### Managed Nodes

* Python installed
* Internet access to Docker repositories
* sudo or dzdo configured for the selected user

---

## ▶️ How It Works

### 1️⃣ Bash Wrapper (`install_docker.sh`)

The Bash script performs:

* Interactive selection of:

  * **Remote Ansible user**
  * **Become method (`sudo` or `dzdo`)**
* Executes the Ansible playbook with correct parameters

Example flow:

```
Select Remote Ansible User:
1) aduser01
2) aduser02
...
8) other

Select Remote Become Method:
1) sudo
2) dzdo
```

The selected values are passed to Ansible using `--extra-vars`.

---

### 2️⃣ Ansible Playbook (`install_docker.yml`)

#### OS Detection

* Uses `ansible_os_family` and `ansible_distribution_major_version`

#### RedHat Family

* Installs prerequisites using:

  * `yum` for RHEL/CentOS 7
  * `dnf` for RHEL/CentOS 8+
* Adds Docker CE repository
* Installs Docker packages

#### Debian Family

* Installs required apt dependencies
* Adds Docker GPG key securely
* Configures Docker APT repository
* Installs Docker packages

#### Post-Installation

* Enables and starts Docker service
* Runs `docker run hello-world`
* Automatically retries with service reload if needed

---

## 🚀 Usage

### Step 1: Update Inventory

Edit the `hosts` file with your target servers.

### Step 2: Run the Script

```bash
chmod +x install_docker.sh
./install_docker.sh
```

You will be prompted to:

* Choose a remote user
* Choose sudo or dzdo
* Enter SSH and become passwords

---

## 🔐 Security Considerations

* Existing Docker repo files are **backed up with timestamps**
* `/tmp` permissions are enforced as `1777`
* No credentials are hardcoded
* Supports enterprise privilege escalation tools

---

## 🧪 Verification

Successful installation is confirmed when you see output similar to:

```
Hello from Docker!
This message shows that your installation appears to be working correctly.
```

---

## ❗ Notes

* For **Centrify/Delinea environments**, ensure:

  * dzdo is properly configured
  * User has Docker-related permissions
* Proxy environments may require additional configuration
* Tested primarily on x86_64 systems

---

## 👤 Author

**Sandeep Reddy Bandela**

Automation | Linux | Ansible | Infrastructure Engineering

