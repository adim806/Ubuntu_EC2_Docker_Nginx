# AWS EC2 + Docker + Nginx Lab

> **Student Exercise** — Ubuntu EC2 Instance, Docker Installation, and Nginx Container

---

## Lab Overview

| Item | Details |
|---|---|
| Difficulty | Beginner |
| Estimated Time | 45–60 minutes |
| Technologies | AWS EC2, Ubuntu 24.04, Docker, Nginx, Security Groups |

This lab demonstrates how to launch a cloud virtual machine on AWS, install Docker, and serve a web page using an Nginx container — accessible from any browser via a public IP address.

---

## Architecture

```
Browser (HTTP)
     │
     ▼
EC2 Public IP (98.86.149.225)
     │  Security Group: port 80 open
     ▼
Ubuntu 24.04 EC2 Instance
     │  Docker Engine
     ▼
Nginx Container (port 80:80)
     │
     ▼
"Welcome to nginx!" page
```

---

## Steps Performed

### 1. Launch EC2 Instance
- AMI: **Ubuntu Server 24.04 LTS** (Free tier eligible)
- Instance type: **t2.micro** (Free tier eligible)
- Key pair created: `key-adi2.pem`
- Auto-assign public IP: **Enabled**

### 2. Configure Security Group

| Type | Protocol | Port | Source | Purpose |
|---|---|---|---|---|
| SSH | TCP | 22 | My IP | Secure admin access |
| HTTP | TCP | 80 | 0.0.0.0/0 | Public web access |

### 3. Connect via SSH

```bash
ssh -i key-adi2.pem ubuntu@98.86.149.225
```

### 4. Install Docker

```bash
# Update packages
sudo apt update
sudo apt install -y ca-certificates curl

# Add Docker GPG key
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

# Add Docker repository
sudo tee /etc/apt/sources.list.d/docker.sources <<EOF
Types: deb
URIs: https://download.docker.com/linux/ubuntu
Suites: noble
Components: stable
Architectures: amd64
Signed-By: /etc/apt/keyrings/docker.asc
EOF

sudo apt update

# Install Docker
sudo apt install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

# Validate
sudo docker --version
sudo docker run hello-world
```

**Docker version installed:** `29.5.2`

### 5. Run Nginx Container

```bash
sudo docker run -d --name nginx-lab -p 80:80 nginx:latest
```

```bash
sudo docker ps
```

Expected output:
```
CONTAINER ID   IMAGE          PORTS                    NAMES
9a5761e50b47   nginx:latest   0.0.0.0:80->80/tcp       nginx-lab
```

### 6. Validate in Browser

Opened `http://98.86.149.225` in a browser and confirmed the Nginx welcome page loaded successfully.

### 7. Cleanup

```bash
sudo docker stop nginx-lab
sudo docker rm nginx-lab
```

Then terminated the EC2 instance from the AWS Console:
**EC2 → Instances → Instance state → Terminate instance**

---

## Screenshots

### Nginx Container Running (Terminal)
<img width="1913" height="952" alt="terminal" src="https://github.com/user-attachments/assets/22213390-00b1-4019-aa5d-a3e1a6ae0904" />


### Nginx Welcome Page (Browser)
<img width="1909" height="779" alt="ngingx_browser" src="https://github.com/user-attachments/assets/f7b365cb-e16c-4cd0-a6e9-7e90223eba61" />


---

## Validation Checklist

- [x] EC2 instance launched and running
- [x] Public IPv4 address assigned
- [x] Security Group: SSH on port 22 from My IP
- [x] Security Group: HTTP on port 80 from 0.0.0.0/0
- [x] Docker installed successfully (v29.5.2)
- [x] Nginx container running
- [x] `docker ps` shows `0.0.0.0:80->80/tcp`
- [x] Nginx welcome page accessible via public IP
- [x] Resources cleaned up and EC2 terminated

---

## Key Concepts

**EC2 (Elastic Compute Cloud)** — AWS virtual machine service. Allows launching cloud servers in seconds using pre-built OS images (AMIs).

**Security Group** — AWS virtual firewall. Controls inbound/outbound traffic at the network level before it reaches the instance.

**Docker** — Containerization platform. Packages applications and their dependencies into isolated, portable containers.

**Nginx** — High-performance web server. Used here as a containerized HTTP server accessible over port 80.

**SSH Key Pair** — Asymmetric RSA key authentication. The private `.pem` file authenticates the user without a password.

---

## References

- [AWS EC2 Documentation](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/)
- [Docker Install on Ubuntu](https://docs.docker.com/engine/install/ubuntu/)
- [Docker CLI Reference](https://docs.docker.com/reference/cli/docker/)
