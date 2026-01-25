---
date: "20240322"
publish: "true"
---

# SSH to Your Remote Server without IP

This article doesn't intend to specify the details of the SSH Remote procedure. It's merely a step-by-step tutorial. I believe it would be clearer to keep the steps straightforward.

## Checking on the remote server

### ✅ Ensure the SSH service is running

Check the status of the SSH service:

```shell
sudo systemctl status ssh
```

The output shows the status info of the SSH service,  with the text `Active: active (running)`indicating that the SSH service is running on the server.

### ✅ Check the SSH port number

Keeping the default SSH port at 22 is convenient for personal use if security isn't a concern.

So let's check the SSH configuration file that contains the port number:

```shell
grep Port /etc/ssh/sshd_config
```

The following output indicates that the SSH service is listening on the default port `22`.

```shell
#Port 22
```

### ✅ Find the `<remote_server_username>`

Run the following command:

```shell
whoami
```

It will print out the username

```shell
train # ← it is the `<remote_server_username>`
```
### ✅ Find the public IP address as  `<remote_server_public_ip>`

Run the following command:

```shell
curl https://icanhazip.com
```

It will print out the public IP of my server

```shell
172.16.254.3 # ← it is the `<remote_server_public_ip>`
```

## Checking on the Local Machine

### ✅ Generate SSH Key Pair:

Run the following command:

```shell
ssh-keygen
```

### ✅ Authenticate  local machine to  remote machine

Run the following command:

```shell
ssh-copy-id <remote_server_username>@<remote_server_public_ip>
```

The two variables `<remote_server_username>` and `<remote_server_public_ip>` were mentioned before.

It's my first time connecting to the remote server. My SSH client displays a prompt about the remote server's authenticity and fingerprint information.

```
The authenticity of host '<remote_server_public_ip> (<remote_server_public_ip>)' can't be established.
ECDSA key fingerprint is SHA256:Jw/3FIJwpp3FIJw/3FIJw/3FppLVppmjWs.
Are you sure you want to continue connecting (yes/no/[fingerprint])?
```

Typing `yes` and pressing Enter will:
- Allow the SSH client to accept the remote server's host key.
- Add the remote server's host key to the `know_host` file in my local machine's `.ssh` directory.

Then my SSH client display another prompt for requiring to enter the password.

```
<remote_server_username>@<remote_server_public_ip>'s password:
```

After entering the correct password just one time, `ssh-copy-id` copies my public key to the remote server's `~/.ssh/authorized_keys` file. And I will never entering password when I want to connect server with SSH.

## Connect Remote Server

Run the following command:
```shell
ssh <remote_server_username>@<remote_server_public_ip>
```



## SSH Login without the IP

### ✅ Editing SSH config file

1️⃣ Create `~/.ssh/config` file, or edit it if the file exists. 

2️⃣ Use the following format to add my `<alias_name>`, `<remote_server_public_ip>`, `remote_server_username`.

```config
Host <alias_name>
	HostName <remote_server_public_ip>
	User <remote_server_username>
```

3️⃣ save the file.

### ✅ Connecting Only with Alias Name

Just typing `ssh <alias_name>` on the local machine allows me to connect my remote server without having to remember any of its details from now on! Quite a relief for the mental burden, right?

## Summary

The content above covered the steps and detailed explanations. Now, let's briefly review all the steps for a quick setup in a real-world environment.

Preparation checklist: 
- The `<remote_server_username>`: such as `train`
- The `<remote_server_public_ip>`: such as `172.16.254.31`
- The SSH `<alias_name>`: such as `mylinuxlab`

Step 1: Run `sudo systemctl status ssh` 

Step 2: Run `ssh-keygen`.

Step 3: Run `ssh-copy-id train@172.16.254.31`.

Step 4: Edit file `~/.ssh/config` as following and save it.

```config
Host trainlinuxlab
	HostName 172.16.254.31
	User train
```

Step 5: Run `ssh trainlinuxlab`.

Step 6: Close SSH connection by typing `exit` and Enter.

## So I Think I Know SSH?

Connecting to a remote server using SSH is relatively straightforward. 

However, the scenario becomes more complex when looking into aspects such as configuration, and security.

If I want to get the hang of SSH skills, the next aspects of SSH knowledge I should learn include:
- Learn how to configure the SSH config file
- Learn common SSH options
- Grasp SSH security best practices

Maybe the title of my next article about SSH will be "So I Think  Know SSH" 😃😃😃.

## Q & A

### Q: what is `systemctl`?
A: `systemctl` is a command line to control and manage the `systemd` system. The `ctl` is short for "**c**on**t**ro**l**".
### Q: What does `ssh-keygen` do?
A: It generates an RSA key pair by default. This key pair contains a public key and a private key,  which are stored in `~/.ssh/` directory.
### Q: what are `~/.ssh/id_rsa.pub` and `~/.ssh/id_rsa`?
A: `~/.ssh/id_rsa.pub` and `~/.ssh/id_rsa` are the public and private keys generated by running `ssh-keygen`.
The local machine's public key is used to be shared and copied to the SSH service of the remote server.
The local machine's private key is used to authenticate the local machine to the remote systems that have the local machine's public key.

****### Q: What is `sshd`?
A: The `sshd` stands for OpenSSH Server Daemon.

### Q: What is `/etc/ssh/sshd_config`?
A: It is a configuration file for the `sshd`. 

### Q: What does `ssh-copy-id` do?
A: It will copy the local public key to the `autorized_keys` file on the remote server.

### Q: How to close the SSH connection?

##### A: Type `ssh` and press Enter.
