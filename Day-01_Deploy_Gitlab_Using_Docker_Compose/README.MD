# 🐳 GitLab Docker Compose | Install GitLab in a Docker Container

## 🧭 Overview

This repository provides a **step-by-step guide** to setting up a **self-hosted GitLab CE** server using **Docker Compose**.  
It also covers setting up **GitLab Runner** with different executors — Shell, Docker, Docker-in-Docker (DinD).


## ⚙️ Prerequisites

- 🐧 Linux Server 
- 🐳 Docker & Docker Compose installed  
- 💾 Sufficient storage (Recommended: 500GB HDD or SSD)  
- 🌐 Static IP / Local DNS (e.g., Pi-hole, Nginx Proxy Manager)



## 📂 Setup Instructions

### 1️⃣ Create Storage Volume
```bash

export DOCKER_VOLUME_STORAGE=/HDD2TB/iansari/gitlab
sudo mkdir -p $DOCKER_VOLUME_STORAGE
sudo chmod -R 777 $DOCKER_VOLUME_STORAGE
cd $DOCKER_VOLUME_STORAGE
````

### 2️⃣ Configure Environment and Compose Files

Create and edit the environment and compose files:

```bash
#nano .env

# Storage
DOCKER_VOLUME_STORAGE=/HDD2TB/iansari/gitlab
# Gitlab
GITLAB_HOST=gitlab-ce
GITLAB_HOST_URL=gitlab.devopsinaction.lab
GITLAB_ROOT_PASSWORD=usvYV8PGTGF5fiwe6
GITLAB_CE_VERSION=18.5.1-ce.0
GITLAB_HTTP_PORT=8081
GITLAB_HTTPS_PORT=8043
GITLAB_SSH_PORT=2020
# Runner
GITLAB_RUNNER_VERSION=ubuntu-bbdc3f57
# DNS
CUSTOM_DNS=192.168.1.101
```

```yaml
# nano compose.yaml
services:
  gitlab:
    image: gitlab/gitlab-ce:${GITLAB_CE_VERSION}
    restart: always
    container_name: "${GITLAB_HOST}"
    hostname: "${GITLAB_HOST_URL}"
    environment:
      - external_url=http://${GITLAB_HOST_URL}
      - GITLAB_ROOT_PASSWORD=${GITLAB_ROOT_PASSWORD}
    ports:
      - ${GITLAB_HTTP_PORT}:80
      - ${GITLAB_HTTPS_PORT}:443
      - ${GITLAB_SSH_PORT}:22
    volumes:
      - ${DOCKER_VOLUME_STORAGE:-/mnt/docker-volumes}/gitlab/config:/etc/gitlab
      - ${DOCKER_VOLUME_STORAGE:-/mnt/docker-volumes}/gitlab/logs:/var/log/gitlab
      - ${DOCKER_VOLUME_STORAGE:-/mnt/docker-volumes}/gitlab/data:/var/opt/gitlab
#    dns:
#      - "${CUSTOM_DNS}"
    networks:
      gitlab:
        aliases:
          - ${GITLAB_HOST_URL}
    labels:
      version: "latest"
      description: "Deployed on 01-11-2025"

  # Gutlab Runner Must not be on the Gitlab server(Best Practices: Keep it in same LAN and on another server to build Jobs...)
  runner:
    image: gitlab/gitlab-runner:${GITLAB_RUNNER_VERSION}
    restart: always
    container_name: gitlab_runner
    network_mode: 'host'
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
      - ${DOCKER_VOLUME_STORAGE:-/mnt/docker-volumes}/gitlab/runner:/etc/gitlab-runner
#    dns:
#      - "${CUSTOM_DNS}"
    labels:
      version: "latest"
      description: "Deployed on 01-11-2025"

networks:
  gitlab:
    driver: bridge
    name: gitlab
    labels:
      - "purpose=gitlab-network"
```


Then start the GitLab container:

```bash
docker compose up -d
```

## 📋 Monitor Logs

### Follow all logs

```bash
docker compose logs -f
```

### Follow specific service logs

```bash
docker compose logs -f gitlab
docker compose logs -f runner
```


## 🧰 GitLab Commands

Check GitLab service status:

```bash
docker exec -it gitlab-ce gitlab-ctl status
```

Tail logs for specific GitLab services:

```bash
docker exec -it gitlab-ce gitlab-ctl tail <service>
docker exec -it gitlab-ce gitlab-ctl tail puma
```

Check when the server is ready:

```bash
docker logs gitlab-ce 2>&1 | grep -iE "Server is ready"
```


## 🌐 Access GitLab

### Set host file (local DNS)

```bash
echo "192.168.1.100 gitlab.devopsinaction.lab" | sudo tee -a /etc/hosts
```

*(See Pi-hole / DNS setup video for details)*
[DevOps in Action - How to Setup Pi-hole DNS within 60 seconds | Pi-hole Home Lab DNS Server setup 😊](https://youtu.be/FYuOSGJk7j0)

### Access directly (no reverse proxy)

```
http://gitlab.devopsinaction.lab:8081
```

**User:** `root`
**Password:** *(see below)*

## 🔑 Retrieve Initial Root Password in not set in variables

```bash
docker exec -it gitlab-ce cat /etc/gitlab/initial_root_password
```

Example output:

```
Password: gtuOEusvYV8PGTGF5fiwe6grMp7KMYqa4NSvr+q6hcc=
```


## 🛡️ Optional: Reverse Proxy Setup

To access GitLab with SSL and a friendly domain, use:


* [**Nginx Proxy Manager (NPM)** 😊](https://www.youtube.com/watch?v=rxmEFm7EPck&list=PL5Afhqcc17s2UCcuEyFnTMHbVkxl8EG_7)


## 🔐 Post-Installation Steps

### Change Root Password

In GitLab UI:
`Profile → Edit → Password`

Example:

```
usvYV8PGTGF5fiwe6
```

### Disable Sign-Up Restrictions
Navigate to:

```
/admin/application_settings/general/Sign-up restrictions
```

## 🧠 About This Project

This project is maintained by **DevOps In Action**, mainly focusing on practical, hands-on DevOps setups for CI/CD automation, containerization, and infrastructure management.

### 💼 Connect with Me 👇😊

* 🔥 [**YouTube**](https://www.youtube.com/@DevOpsinAction?sub_confirmation=1)
* ✍️ [**Blog**](https://ibraransari.blogspot.com/)
* 💼 [**LinkedIn**](https://www.linkedin.com/in/ansariibrar/)
* 👨‍💻 [**GitHub**](https://github.com/meibraransari?tab=repositories)
* 💬 [**Telegram**](https://t.me/DevOpsinActionTelegram)
* 🐳 [**Docker Hub**](https://hub.docker.com/u/ibraransaridocker)


### ⭐ If You Found This Helpful...

***Please star the repo and share it! Thanks a lot!*** 🌟




