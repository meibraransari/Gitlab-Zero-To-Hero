# 🚀 GitLab SSL Setup with Nginx Reverse Proxy

## 🔐 GitLab Access without SSL over HTTP

- **GitLab URL:** [http://gitlab.devopsinaction.lab/users/sign_in](http://gitlab.devopsinaction.lab/users/sign_in)
- **Username:** `root`
- **Password:** `usvYV8PGTGF5fiwe6`



## 🧭 Overview

We’ll configure **SSL (HTTPS)** for GitLab using **Nginx as a reverse proxy**.
Here’s the workflow we’ll achieve:

```
           ┌────────────┐
           │   Client   │
           │ (Browser)  │
           └─────┬──────┘
                 │
          HTTPS :443 / :80
                 │
                 ▼
       ┌──────────────────────┐
       │   Nginx Reverse Proxy│
       │ (SSL termination)    │
       └────────┬─────────────┘
                │ HTTP :80
                ▼
        ┌────────────────┐
        │   GitLab Server│
        │ (Dockerized)   │
        └────────────────┘
```



## 🧰 Step-by-Step Setup

### 1️⃣ Stop Running Containers

```bash
docker compose down
```



### 2️⃣ Check Existing Configuration

```bash
cd /HDD2TB/iansari/gitlab
grep -vE "^#|^$" ./gitlab/config/gitlab.rb
```



### 3️⃣ Backup `gitlab.rb` Configuration

Make sure to back up before editing:

```bash
cp -a ./gitlab/config/gitlab.rb ./gitlab/config/gitlab_rb_original
```

Or move it instead:

```bash
mv ./gitlab/config/gitlab.rb ./gitlab/config/gitlab_rb_original
```



### 4️⃣ Update `gitlab.rb` File

Open and edit the configuration:

```bash
sudo nano ./gitlab/config/gitlab.rb
```

Replace content or update these lines:

```ruby
nginx['enable'] = true
nginx['client_max_body_size'] = '250m'
nginx['listen_addresses'] = ['127.0.0.1']
nginx['listen_port'] = 80
nginx['listen_https'] = false
nginx['proxy_set_headers'] = {
  "X-Forwarded-Ssl" => "on",
}
```


### 5️⃣ Runner SSL Setup

#### 🔧 Update `compose.yaml`

Change `external_url` to use HTTPS:

```bash
nano compose.yaml
```

Update environment section:

```yaml
environment:
  - external_url=https://${GITLAB_HOST_URL}
```

Start container
```bash
docker compose up -d
```

✨ **Now your GitLab instance runs securely behind an SSL-enabled Nginx reverse proxy!**


## 📦 NPM Reverse Proxy Setup (with SSL + Force HTTPS)

Goal: Configure "gitlab.devopsinaction.lab" SSL and force use HTTPS.
- 👉 https://npm.devopsinaction.lab/nginx/proxy


## 🔐 GitLab Access with SSL over HTTPS

- **GitLab URL:** [http://gitlab.devopsinaction.lab/users/sign_in](http://gitlab.devopsinaction.lab/users/sign_in)
- **Username:** `root`
- **Password:** `usvYV8PGTGF5fiwe6`


#### 🔧 Update Runner Configuration

Edit runner config:

```bash
nano gitlab/runner/config.toml
```

Change GitLab URL:

```toml
url = "https://gitlab.devopsinaction.lab"
```


### Restart GitLab Runner

Apply the changes:

```bash
docker restart gitlab_runner
```


## ✅ Summary

| Step | Action          | Description                                   |
| ---- | --------------- | --------------------------------------------- |
| 1    | Stop containers | Prepare for config changes                    |
| 2    | Check config    | Review existing GitLab setup                  |
| 3    | Backup          | Protect original `gitlab.rb`                  |
| 4    | Edit config     | Set Nginx to HTTP (reverse proxy handles SSL) |
| 5    | Update runner   | Point to HTTPS GitLab URL                     |
| 6    | Restart runner  | Apply new configuration                       |



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




