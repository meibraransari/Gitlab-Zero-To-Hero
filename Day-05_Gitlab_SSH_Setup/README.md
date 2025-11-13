# 🔑 How to Set Up SSH Keys in GitLab | Step-by-Step Secure Git Access Guide (2025)


🔐 Unlock secure and seamless access to your GitLab repositories using SSH! 
This guide explains how to configure and test **SSH access** for a **GitLab instance running inside Docker**.  
In this step-by-step guide, we’ll walk you through the complete process of setting up SSH keys in GitLab — from key generation and configuration to troubleshooting connection issues.

Whether you're hosting GitLab in Docker or managing it on a self-hosted server, this tutorial covers everything you need to securely connect your local machine to GitLab without using passwords.


🧠 What You’ll Learn
- ✅ How SSH authentication works with GitLab
- ✅ How to generate SSH keys (RSA 4096) on your local machine
- ✅ How to add and configure SSH keys in GitLab
- ✅ How to connect Git to GitLab using SSH
- ✅ How to debug “Permission denied (publickey)” errors
- ✅ Bonus: Setting up SSH for Docker-based GitLab environments


## 🔐 Authentation Type
- See [HTTPS vs SSH](Auth_Type.md) 

## 🧠 Understanding Architecture

When GitLab runs in a Docker container, it provides:
- 🌐 **HTTP(S)** access via ports **80/443**
- 🔐 **SSH** access via port **22 (inside the container)**

We’ll map this port to the host and make sure GitLab recognizes it properly.


## ⚙️ Step 1: Free Port 22

Check which process (if any) is using port **22**:

```bash
netstat -tlpn | grep 22
````

If something else is bound to port 22 (e.g., system SSH), free it or use a different one (e.g., 2020).



## 🧩 Workflow of SSH Connection

```text
┌──────────────────────┐
│      My PC           │
│   (SSH Client)       │
└─────────┬────────────┘
          │
          ▼
┌──────────────────────────┐
│   DNS / Host Entry       │
│ gitlab.devopsinaction.lab│
└─────────┬────────────────┘
          │
          ▼
┌──────────────────────────┐
│      GitLab Server       │
│    192.168.1.100:22      │
└──────────────────────────┘
```


## 🧭 Step 2: Add Host Entry (if needed)

Check or add the host entry for GitLab if you are not using DNS.

Check using ping:

```bash
ping gitlab.devopsinaction.lab
```

```bash
cat /etc/hosts | grep gitlab.devopsinaction.lab
192.168.1.100 gitlab.devopsinaction.lab
```

Verify with a ping:

```bash
ping gitlab.devopsinaction.lab
```


## 🐳 Step 3: Check GitLab Container SSH Port

List the GitLab container and check port mapping:

```bash
docker ps | grep gitlab-ce
```

Example output:

```
0.0.0.0:2020->22/tcp
```


## 🧰 Step 4: Update Environment and Compose Files

Edit `.env`:

```bash
nano .env
```

Set the desired SSH port:

```bash
GITLAB_SSH_PORT=22
```

Then edit `compose.yaml`:

```yaml
ports:
  - ${GITLAB_SSH_PORT}:22
```


## 🧾 Step 5: Configure GitLab SSH Port

Edit GitLab configuration file:

```bash
nano /HDD2TB/iansari/gitlab/gitlab/config/gitlab.rb
```

Add the following line:

```ruby
################################################################################
#   SSH Configuration
################################################################################
gitlab_rails['gitlab_shell_ssh_port'] = 22
```


## 🔁 Step 6: Apply Configuration Changes

If `.env` or `compose.yaml` was modified:

```bash
docker compose down
docker compose up -d
```

Otherwise, if only `gitlab.rb` was updated:

```bash
docker exec -it gitlab-ce gitlab-ctl reconfigure
docker exec -it gitlab-ce gitlab-ctl restart
```

## 🧪 Step 7: Verify SSH Connectivity

Check that GitLab is listening on the expected port:

```bash
docker ps | grep gitlab-ce
telnet gitlab.devopsinaction.lab 22
```

## 🔑 Step 8: Generate SSH Key Pair

```bash
cd ~/.ssh
ssh-keygen -t rsa -f ./gitlab_id_rsa_key -b 4096 -C "This is used for GitLab root user"
cat ./gitlab_id_rsa_key.pub
```

Copy the public key and add it to GitLab:

> **GitLab → User → Preferences → SSH Keys → Paste & Add Key**

## 🧩 Step 9: Test SSH Access

```bash
ssh -i ./gitlab_id_rsa_key -T git@gitlab.devopsinaction.lab
```

✅ Expected output:

```
Welcome to GitLab, @yourusername!
```

## 🧰 Step 10: Clone a Repository

```bash
cd /tmp
git clone git@gitlab.devopsinaction.lab:root/runner-test.git
```

If you see:

```
Permission denied (publickey).
fatal: Could not read from remote repository.
```

Follow the steps below 👇

## 🧩 SSH Troubleshooting

### 1️⃣ Start SSH Agent & Add Key

```bash
eval $(ssh-agent -s)
ssh-add ~/.ssh/gitlab_id_rsa_key
ssh-add -l
```

### 2️⃣ Test Connection Again

```bash
ssh -T git@gitlab.devopsinaction.lab
```

### 3️⃣ Clone Repository Again

```bash
git clone git@gitlab.devopsinaction.lab:root/runner-test.git
cd runner-test
git pull
git remote -v
```



## ⚙️ Add key file in SSH Agent config to avoide above steps.

Edit or create `~/.ssh/config`:

```bash
nano ~/.ssh/config
```

Add:

```bash
Host gitlab.devopsinaction.lab
  HostName gitlab.devopsinaction.lab
  IdentityFile ~/.ssh/gitlab_id_rsa_key
  User git
```

Now you can connect simply by:

```bash
ssh git@gitlab.devopsinaction.lab
```

## 🔍 Debug Checklist

| Step | Check               | Command                                                        |
| ---- | ------------------- | -------------------------------------------------------------- |
| ✅ 1  | Key added in GitLab | `cat ~/.ssh/gitlab_id_rsa_key.pub`                             |
| ✅ 2  | Key loaded in agent | `ssh-add -l`                                                   |
| ✅ 3  | SSH config correct  | `cat ~/.ssh/config`                                            |
| ✅ 4  | Test connection     | `ssh -T git@gitlab.devopsinaction.lab`                         |
| ✅ 5  | Clone repo          | `git clone git@gitlab.devopsinaction.lab:root/runner-test.git` |


## 🎉 Success

If everything’s set up correctly, you’ll see:

```
Welcome to GitLab, @yourusername!
```

and be able to clone/push repositories over SSH securely 🎯


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




