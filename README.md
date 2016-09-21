# Table of Contents

- [Start Deployment](https://github.com/maateen/deploynow#start-deployment)
	- [Creating a Deployment User](https://github.com/maateen/deploynow#creating-a-deployment-user)
	- [Creating Deployment Path](https://github.com/maateen/deploynow#creating-deployment-path)
	- [Adding DeployNow Public Key to your Server](https://github.com/maateen/deploynow#adding-deploynow-public-key-to-your-server)
	- [Adding your Deploy Key to GitHub/GitLab/BitBucket](https://github.com/maateen/deploynow#adding-your-deploy-key-to-githubgitlabbitbucket)

# Start Deployment

## Creating a Deployment User

Your can log in to your server as root. While it is possible to perform application deployments as root, we recommend that you take a slightly longer path and create a separate user with limited write-access to use in DeployNow. This will circumscribe destructive powers of a mistyped command and reduce the risks associated with accidentally exposing your credentials on the web. The following command will create a new user with login “deploynow" and no password set yet. This user will be added to group www-data as it is used in NginX. If you are using any Web Server except NginX, then change it as needed.

```
useradd -U -G www-data -m -c "Used by DeployNow for deployment automation" deploynow
```

With NginX, the server is configured in a way that all web services are run by user www-data, a member of www-data group. Generally you’ll want your application files to be writable by your deployment user only, limiting www-data to read access except for when the write permissions are required (logging, file uploads, caching, etc).

## Creating Deployment Path

Now you need to pick up the directory where your web app files are going to be stored (for instance, /var/www/html). It has to be readable by the members of www-data group, but only writable by the deployment user.

```
mkdir -p /var/www/html
```
```
chown deploynow:www-data /var/www/html
```
```
chmod 2755 /var/www/html
```

Note the 2755 access mode. It is recommended that you set the GUID bit on the application directory. This way all files created inside it will be automatically owned by group www-data (with read-only access).

## Adding DeployNow Public Key to your Server

Have you ever thought how DeployNow works? In simple, it login to your server with SSH and run necessary commands. So, to login via SSH key (without password). we need to have our public key on your server. You have to add our key manually. Here is our public key:

```
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQC3y8Kdxq9DYP0uPkydkwR3uC6J/lJEozNvcFMnJ5lnNsyrarJEmwxaTrguAGHbesWXXP0NSCQtDWEaI3P1BJr86E2lSna0IcjhVfyGUHDW7mtBvYv+D1To93WlkPBRU8BOzxnX4UrE6rklSQ4wUVIJsuCKnXQYgsB440YWFJ01N6b97nlKZJPTP5MYil5q1E291aLtz+CwBERr7AnW8Xi6L99yxiNHH3F1Q6R/CN+C4Lf8jIKb0nNo2Y2T6GnNeKDQ6+SadpXvBGMzghZn2Cw2iMEAJokSrHP/BZDfgmy4q2eKhDj+hEtkWOVf4rLmZjnhkgnys/HgAPPRVx5s+97u5Ke8vO4b3HlxrcDfwuAA1NSRqKyuGA8kmktileW/LzKZ7GagCpa8pcNF3eSmKNvAapWclz6yiUBk+swfIq33WntGgT1j1p8kuBoX8EmPCNsBadYJ9cnI5b43BzBpiPi7eZ4iv76rpZjfHqLU8jE37t6JojAi87f81W2DBe7VJZogS4oyocViyhEwDRilPBaVv9oL5yF70PQHtLv2hf9H2iHrS8IT35HDgRrnSG/KixzHXyoUCaaeflMHG5bL0t4P0hZT9zzQyFdsrwtKP2e0fEgnPL30esZnfJfbjH6vHWnwfxYF53H+deXpo2RjrP5mZ9CQXQjtiUi+y3dAVnF8JQ== ugcoder@yahoo.com
```

Now, login to your server as root. Then play the following commands (never forget to change the **deploynow** to your deployment user):

```
su deploynow
```
```
mkdir ~/.ssh
```
```
chmod 0700 ~/.ssh
```
```
nano ~/.ssh/authorized_keys
```

Here you will see a file to be opened. Paste our public key here. Press **Ctrl + X**, then press **Y** and at last press **Enter**.

## Adding your Deploy Key to GitHub/GitLab/BitBucket

You need to generate a deploy key and should add it to your repository for the deployment process. Let's generate it. Open Terminal. Paste the text below, substituting in your GitHub/GitLab/BitBucket email address.:

```
ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
```

When you're prompted to "Enter a file in which to save the key," press Enter. This accepts the default file location. After that, you will be asked to type a passphrase. Don't type anything, just press **Enter** twice.

Now login to your server as the deployment user, you have created before. Play the following command:

```
nano ~/.bashrc
```

A file will be opened. At the end of the file, add these lines:

```bash
if [ ! -S ~/.ssh/ssh_auth_sock ]; then
  eval `ssh-agent`
  ln -sf "$SSH_AUTH_SOCK" ~/.ssh/ssh_auth_sock
fi
export SSH_AUTH_SOCK=~/.ssh/ssh_auth_sock
ssh-add -l | grep "The agent has no identities" && ssh-add
```

Press **Ctrl + X**, then press **Y** and at last press **Enter**.
