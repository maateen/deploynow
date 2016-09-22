# Table of Contents

- [Start Deployment](#start-deployment)
	- [Creating a Deployment User](#creating-a-deployment-user)
	- [Creating Deployment Path](#creating-deployment-path)
	- [Adding DeployNow Public Key to your Server](#adding-deploynow-public-key-to-your-server)
	- [Adding your Deploy Key to GitHub/GitLab/BitBucket](#adding-your-deploy-key-to-githubgitlabbitbucket)
	- [Setting up Git in your Deployment Path](#setting-up-git-in-your-deployment-path)

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
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQCyaT6/9bjDBdVh9pKSsYEcHfimbiH83ikeSD9JiCHsvcxDecnFbHfsxlpVRNuqIuqRVrJtn5SOCwJk4K+t3UYQxJYQvbMbif57DfFyCQNQYo8y283cSHMTDf8HW/lBQe+NKNQC1zGkXAWuE0Q7k6C0P02WGYWVhbufIj1SNu3VTHZZtbjtscBgans3Z6n7TKt9Xd0k5kKQWY3MQctuJ9BXMezDQgm5Yl4gcQPZVJE3x5RCGbqbf8OJU3OXxpaAddj/xnR4xg4WVIfT7pJEyZE192a4A9u7/NGd0XN12bL6oJ7EDD6R3DK9wUYbw3yCSzaI44tH9aRHlNAoN5h0NUAnpnENFZV7HkgiNFB+2hQrJAbJgaJZzDOv/ZBulIB4Y3QvUNmxVZhtC52Eymdwcjexy5H8oJjlfL1XQHzb8Rbumtg6eQ3MEni3BzyvK7vDr4j8TPvX4/Fzfr/pE7nFmD6MbgiIW+b4Q24rG0k/gYBEWSaoVFiYwV7RqPM94mOZbhFjUGtQDUvcaQCL+7r+OqNqwjHs43UI8omEdvsQj9MqnV7lRc3iBmsK+3/Xjdg4H3eLzxdaiATpk77RV/1a3Q4GrfcxL9g5tX0IfaTuaKL3WkVvYx3j/XRCGJ/2gphTY4knlPVnvKt5RnYGhXBw971lqxc/Ht8z7byf5n1un/Yodw== maateen@outlook.com
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

Now login to your server and play the following commands (never forget to change the **deploynow** to your deployment user):

```
su deploynow
```

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

## Setting up Git in your Deployment Path

Now login to your server and play the following commands (never forget to change the **deploynow** to your deployment user):

```
su deploynow
```

You should install git as our bot will use it. Then initialize git in your deployment path:

```
git init
```
```
git remote add origin [SSH URL of the repository]
```

That's all. We will do the rest.
