# OWASP-ZAP
## Hands-on DAST Lab with Juice Shop

![alt text](image.png)

This repository demonstrates Dynamic Application Security Testing (DAST) using OWASP ZAP against the intentionally vulnerable OWASP Juice Shop application.

The goal is to understand how ZAP works in real terms by attacking a running web application the same way an external attacker would.

### 🔐 What is OWASP ZAP?
#### OWASP ZAP (Zed Attack Proxy) is a web security testing tool. 
- It does DAST (Dynamic Application Security Testing)
- It tests a running application, not source code
- It works by intercepting and manipulating HTTP requests and responses

### Simple Analogy
- Your app = a house 🏠
- SAST tools = check the blueprint 📐
- ZAP = tries doors & windows to break in 🔓

#### ZAP can :
- Record traffic
- Analyze requests/responses
- Replay & modify requests
- Actively attack the application

### ⚔️ Core Idea: HTTP is the Battlefield
#### Every web app is just:

- Requests → GET /login, POST /api/orders
- Responses → HTML / JSON / headers / cookies / status codes

ZAP sits in the middle and watches everything.

### 🧪 Lab Architecture
```
Browser  →  OWASP ZAP  →  Juice Shop App
```

Both ZAP and Juice Shop run in Docker containers on the same Docker network.

### 📌 Prerequisites

Ubuntu EC2 

Docker installed

Port 3000, 8080, 8090 open in Security Group

### 🚀 STEP 1 – Install Docker (Ubuntu)
```
sudo apt update
sudo apt install -y ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc


sudo tee /etc/apt/sources.list.d/docker.sources <<EOF
Types: deb
URIs: https://download.docker.com/linux/ubuntu
Suites: $(. /etc/os-release && echo "${UBUNTU_CODENAME:-$VERSION_CODENAME}")
Components: stable
Signed-By: /etc/apt/keyrings/docker.asc
EOF


sudo apt update
sudo apt install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

- Enable Docker:
```
sudo systemctl start docker
sudo systemctl status docker
```

- Add user to Docker group:
```
sudo usermod -aG docker $USER
newgrp docker
```

### 🚀 STEP 2 – Run OWASP Juice Shop

- Pull and run the vulnerable app:
```
docker pull bkimminich/juice-shop


docker run --rm -itd \
--name juice-shop \
-p 3000:3000 \
bkimminich/juice-shop
```
#### Access the app:

- Local: http://localhost:3000

- EC2: http://:3000

### 🌐 STEP 3 – Create Docker Network
```
docker network create zap-net
```

#### Attach Juice Shop to network:
```
docker network connect zap-net juice-shop
```
### 🛡️ STEP 4 – Run OWASP ZAP (GUI Mode)

Official ZAP Docker WebSwing image: https://www.zaproxy.org/docs/docker/webswing/

```
docker run -u zap -d \
--name zap \
--network zap-net \
-p 8080:8080 \
-p 8090:8090 \
ghcr.io/zaproxy/zaproxy:stable \
zap-webswing.sh
```
#### Ports Used
| Port | Purpose |
|------|---------|
| 8080 | ZAP Web UI |
| 8090 | Proxy Port |

### 🖥️ STEP 5 – Access ZAP UI
#### Open in browser:
```
http://localhost:8090/zap
```

### 🎯 STEP 6 – Configure Target in ZAP
- Open Quick Start

- Choose Automated Scan

- Target URL:

```
http://juice-shop:3000
```

#### ⚠️ Important:
Use container name, not localhost

- ZAP runs inside Docker

### 🧨 What ZAP Will Find
- ZAP can detect:
- SQL Injection
- XSS
- Missing security headers
- Insecure cookies
- Broken authentication

Juice Shop is intentionally vulnerable, so many alerts are expected.

#### ⭐ If this repo helped you, give it a star!
