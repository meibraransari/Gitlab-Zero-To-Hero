# 📬 GitLab SMTP Configuration Guide

This guide provides SMTP configurations for different mail providers — useful for setting up GitLab email notifications.
Remember to replace placeholder values (`<USERNAME>`, `<PASSWORD>`, `<DOMAIN>`, etc.) with your actual credentials.


## 🎬 Video Demonstration

[![Watch on Youtube](https://i.ytimg.com/vi/fF_mgWTpZsk/maxresdefault.jpg)](https://youtu.be/fF_mgWTpZsk)


## Configuration
```
cd /HDD2TB/iansari/gitlab
# Take SMTP format from here: nano gitlab/config/gitlab_rb_original 
nano gitlab/config/gitlab.rb
```

## 🧪 Local SMTP (Example / MailHog / Test Server)

```ruby
################################################################################
# Local SMTP Configuration
################################################################################

gitlab_rails['smtp_enable'] = true
gitlab_rails['smtp_address'] = "192.168.1.100"
gitlab_rails['smtp_port'] = 1025
gitlab_rails['smtp_user_name'] = "admin"
gitlab_rails['smtp_password'] = "Admin@123"
gitlab_rails['smtp_authentication'] = "login"
# gitlab_rails['smtp_domain'] = "example.com"
# gitlab_rails['smtp_enable_starttls_auto'] = true
# gitlab_rails['smtp_tls'] = false
# gitlab_rails['smtp_pool'] = false
```


## ☁️ AWS SES (Amazon Simple Email Service)

```ruby
################################################################################
# AWS SES SMTP Configuration
################################################################################

gitlab_rails['smtp_enable'] = true
gitlab_rails['smtp_address'] = "email-smtp.ap-south-1.amazonaws.com"
gitlab_rails['smtp_port'] = 587
gitlab_rails['smtp_user_name'] = "YOUR_AWS_SES_SMTP_USERNAME"
gitlab_rails['smtp_password'] = "YOUR_AWS_SES_SMTP_PASSWORD"
gitlab_rails['smtp_domain'] = "yourdomain.com"
gitlab_rails['smtp_authentication'] = "login"
gitlab_rails['smtp_enable_starttls_auto'] = true

# GitLab Email Settings
gitlab_rails['gitlab_email_from'] = 'support@yourdomain.com'
gitlab_rails['gitlab_email_reply_to'] = 'noreply@yourdomain.com'
```


## ✉️ SendGrid SMTP Configuration

```ruby
################################################################################
# SendGrid SMTP Configuration
################################################################################

gitlab_rails['smtp_enable'] = true
gitlab_rails['smtp_address'] = "smtp.sendgrid.com"
gitlab_rails['smtp_port'] = 587
gitlab_rails['smtp_user_name'] = "apikey"   # Always use 'apikey' as the username for SendGrid
gitlab_rails['smtp_password'] = "YOUR_SENDGRID_API_KEY"
gitlab_rails['smtp_domain'] = "example.com"
gitlab_rails['smtp_authentication'] = "login"
gitlab_rails['smtp_enable_starttls_auto'] = true

# GitLab Email Settings
gitlab_rails['gitlab_email_from'] = 'support@example.com'
gitlab_rails['gitlab_email_reply_to'] = 'noreply@example.com'
```


## 📧 Gmail SMTP Configuration

```ruby
################################################################################
# Gmail SMTP Configuration
################################################################################

gitlab_rails['smtp_enable'] = true
gitlab_rails['smtp_address'] = "smtp.gmail.com"
gitlab_rails['smtp_port'] = 587
gitlab_rails['smtp_user_name'] = "your_email@gmail.com"
gitlab_rails['smtp_password'] = "your_app_specific_password"
gitlab_rails['smtp_domain'] = "gmail.com"
gitlab_rails['smtp_authentication'] = "login"
gitlab_rails['smtp_enable_starttls_auto'] = true

# GitLab Email Settings
gitlab_rails['gitlab_email_from'] = 'your_email@gmail.com'
gitlab_rails['gitlab_email_reply_to'] = 'noreply@gmail.com'
```

> ⚠️ **Important:**
> Gmail requires **App Passwords** (not your regular password).
> Create one under:
> **Google Account → Security → App Passwords**


## 💼 Office 365 (Microsoft 365) SMTP Configuration

```ruby
################################################################################
# Office 365 / Microsoft 365 SMTP Configuration
################################################################################

gitlab_rails['smtp_enable'] = true
gitlab_rails['smtp_address'] = "smtp.office365.com"
gitlab_rails['smtp_port'] = 587
gitlab_rails['smtp_user_name'] = "support@yourdomain.com"
gitlab_rails['smtp_password'] = "YourStrongPassword123"
gitlab_rails['smtp_domain'] = "yourdomain.com"
gitlab_rails['smtp_authentication'] = "login"
gitlab_rails['smtp_enable_starttls_auto'] = true

# GitLab Email Settings
gitlab_rails['gitlab_email_from'] = 'support@yourdomain.com'
gitlab_rails['gitlab_email_reply_to'] = 'noreply@yourdomain.com'
```

> 💡 **Tip:**
> If MFA (Multi-Factor Authentication) is enabled, create an **App Password** in your Microsoft 365 account to use here.


## 🧠 Apply Configuration Changes

After editing `gitlab/config/gitlab.rb`, apply the changes and restart GitLab services:

```bash
docker exec -it gitlab-ce /bin/bash
gitlab-ctl reconfigure
gitlab-ctl restart
or
docker restart gitlab-ce
docker logs gitlab-ce
```


## 🧾 (Optional) Test Email Delivery

You can test email delivery from the GitLab Rails console:

```bash
docker exec -it gitlab-ce /bin/bash
gitlab-rails console
Notify.test_email('youremail@yourdomain.com', 'Test Email from GitLab Server', 'This is a test email. from gitlab console').deliver_now
exit
```

```bash
# Check using forgot password
https://gitlab.devopsinaction.lab/users/password/new
harry@example.com
```

If successful, you’ll receive a test message in your inbox 🎉
- Check Inbox: https://webmail.devopsinaction.lab/


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




