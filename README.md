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
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQDmZtT5d/yowZaRX03vfiEPQcc3xMMKGOVoDAxyJSQ6Fda7sEUs50X041yU9RL19irInufgepOtvwqTX7S5FJyS8AgIKuz4SQvKIyEwyfeeC1Tz6CjocrHoWdqiFwwG8akaoZKaoA/3b1PxQQaO3CYl8c/9PyEUGEODk5eHqZqKKVfpOaMnFL7/xr7ec4JGD/esV9xQ23LzHXNC7dW1NT6TklJuNY5Rinee++ExwOMc1Iu3lJ++5oWrNwVPDB40Osz7s7Vm7Hl3IZ45LgyOVNKv4O2ZLjVx2D+YjgESCFZGc8CqFk6DKuLqy2vWRyalPLqH8xL4UphzE9knlrApnh6V1jW2BjKOIHIQlvoXerRQhLCh0VtQGpwQGXDqTQCofmMd5TJ7Q8f27vC5NJJicd4T+K334S8wCSb2nzB+1P6pB+Ug8BvMQqeNFGP3FhcAYU8QZ7S+VkiyY2zT5ic9qNy2TxP7siYW1jrIAqSMfWAl4N/tjPPYFA4Rxx3or25fy2TvyPp3hWO4x+tRLeEnF/VoWZ2WQgE+H5Y4GRXMY8KXrV/KyKDRKfnYzp6NNTSNxG+1FKH4iZv5WI881vpMtQ8beFeHuTEzwYhoICKGa7fczRAQBf9JuwLAHF9Jj2+PJ3EYEpDZ9TGWehx0mNKIbMMVa2/zw5I1UTZUsumQ6bT7pw== ugcoderbd@gmail.com
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
