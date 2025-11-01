# 🐳 GitLab Docker Compose | Register GitLab Runner using Docker Compose.

This guide shows how to run a GitLab Runner inside a Docker container using Docker Compose and register it with your GitLab instance. By using Docker Compose, you can easily manage the GitLab Runner as a service, persist its configuration, and isolate it from your host system.

## Register runner

- admin > CICD > runners
  - Name: project
  - Tick: Run untagged jobs
- Copy code
  - gitlab-runner register  --url http://gitlab.devopsinaction.lab  --token glrt-ioashbXxMybzYJX1XPfKHW86MQp0OjEKdToxCw.01.12077hr96


## Verify URL
```bash
docker exec -it gitlab_runner \
curl http://gitlab.devopsinaction.lab
```
If using a hostname, add it either:
- In /etc/hosts of the runner container
- Using --docker-extra-hosts during registration


##  Register runner

```bash
#!/bin/bash
TOKEN="sCv8-i1GD7azizd6aSwa"
# Without Extrahost & DNS (Tell direct link of gitlab)
GITLAB_URL="http://192.168.1.100:8081"

# With Host entry (network_mode: 'host': Append host entry in runner_container) + Extrahost(Add host entry in Runner configuration as well)
#192.168.1.100 gitlab.devopsinaction.lab
#GITLAB_URL="http://gitlab.devopsinaction.lab:8081"
#EXTRA_HOST="gitlab.devopsinaction.lab:192.168.1.100"

# With DNS+Proxy (enable DNS in compose)
# Disable host /etc/hosts
#GITLAB_URL="http://gitlab.devopsinaction.lab"
# Optional (Add if you are using internal DNS)
#EXTRA_HOST="gitlab.devopsinaction.lab:192.168.1.102"

docker exec -it gitlab_runner \
gitlab-runner register \
  --non-interactive \
  --url "$GITLAB_URL" \
  --registration-token "$TOKEN" \
  --executor "docker" \
  --docker-image "docker:latest" \
  --docker-network-mode "host" \
  --docker-extra-hosts "$EXTRA_HOST" \
  --docker-volumes "/cache" \
  --docker-volumes "/var/run/docker.sock:/var/run/docker.sock" \
  --description "docker-runner-host" \
  --tag-list "docker,host,local" \
  --run-untagged="true" \
  --locked="false"

```

Tips:
- --docker-extra-hosts helps resolve GitLab hostname if not using DNS.
- --docker-volumes "/var/run/docker.sock:/var/run/docker.sock" allows Docker-in-Docker builds.
- --run-untagged="true" ensures untagged jobs are picked up.

##  Verify Runner
```bash
docker exec -it gitlab_runner \
gitlab-runner list
```

```bash
docker exec -it gitlab_runner \
gitlab-runner verify
```

## Check in GitLab:
```bash
http://gitlab.devopsinaction.lab/admin/runners
```

## Networking Considerations
- IP with Port
- Domain with port with host entry
- Domain with port with Internal/external DNS + Port
- Domain with port with reverse proxy


## Create test project and add ci file

```
# .gitlab-ci.yml
build:
  image: python:alpine
  script:
    - python --version

```


## 🔗 Additional Resources

* 📘 [Run GitLab Runner in a container](https://docs.gitlab.com/runner/install/docker/)


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




