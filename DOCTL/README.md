# Setting Up an Arch Linux Droplet Using `doctl` and Cloud-init

## Introduction

This tutorial will guide you through creating and configuring an Arch Linux droplet on DigitalOcean using the `doctl` command-line tool. We’ll automate the server setup using cloud-init for tasks like creating users and installing packages. By the end, you'll have a fully configured server with secure SSH access.

**Why This Tutorial?**

- Using `doctl` automates the process of creating and managing droplets, which is more efficient than using the DigitalOcean console for each task.
- `cloud-init` allows for repeatable and automated configurations, which is ideal for deploying servers at scale.

---

## Prerequisites

Before you begin, ensure that you have the following:

- **DigitalOcean Account**: You will need to create an API token.
- **SSH Key**: Generate an SSH key on your local machine to securely connect to the droplet.
- **`doctl` Command-line tool**: Installed and configured on your machine.
- **Basic command-line knowledge**: Familiarity with terminal commands.

---

## Step 1: Generate SSH Keys

We will use SSH keys to securely authenticate with the server instead of passwords.

bash



`ssh-keygen -t rsa -b 4096`

![Uploading SSH Key](generate-ssh-key.png)

Follow the prompts to save the key to your default directory (`~/.ssh/id_rsa`). Once generated, upload the **public** key to your DigitalOcean account.

1. Copy your public SSH key:
    
    bash
    
    
    
    `cat ~/.ssh/id_rsa.pub`
    
2. Go to your DigitalOcean dashboard, navigate to **Account > Security**, and add your key under **SSH Keys**.
![Uploading SSH Key](assets/ssh-key-upload.png)


**Why SSH?**  
SSH keys provide enhanced security by eliminating the need for passwords. They are also a best practice for secure remote server access.

---

## Step 2: Install and Configure `doctl`

`doctl` is DigitalOcean’s command-line tool that allows you to manage droplets and other resources directly from your terminal. Install it using `pacman` on an Arch Linux machine:

bash


`sudo pacman -S doctl`

Next, authenticate `doctl` with your DigitalOcean account:

bash



`doctl auth init`


![Authenticating doctl](assets/doctl-auth.png)

You’ll need to enter your API token, which can be created on your DigitalOcean API Tokens page.

---

## Step 3: Create a Droplet Using `doctl`

Now, we’ll create a new droplet with Arch Linux using `doctl`. The following command will create the droplet with a specified region, size, and the `cloud-init` configuration.

bash


`doctl compute droplet create my-arch-droplet \   --region nyc3 \   --image arch-linux \   --size s-1vcpu-1gb \   --ssh-keys <your-ssh-key-fingerprint> \   --user-data-file cloud-init.yaml \   --wait`


![Creating Droplet with doctl](assets/doctl-droplet-create.png)

### Breakdown of the Command:

- `--region nyc3`: Specifies the region (New York in this case) where the droplet will be created.
- `--image arch-linux`: Specifies the Arch Linux operating system.
- `--size s-1vcpu-1gb`: Creates a droplet with 1 virtual CPU and 1GB of RAM.
- `--user-data-file`: Uses a `cloud-init` configuration file to automate the server setup.

Find your SSH key fingerprint using:

bash

`doctl compute ssh-key list`


![Droplet List](assets/doctl-droplet-list.png)

**Why Use `doctl`?**  
`doctl` allows you to automate droplet creation, which is crucial for scaling infrastructure. It’s also easier for scripting and integration with CI/CD pipelines.

---

## Step 4: Automate Server Setup with Cloud-init

`cloud-init` is a powerful tool for automating the initial configuration of a droplet. We’ll create a basic configuration to set up a new user, install essential packages, and configure SSH.

### cloud-init Configuration:

Create a `cloud-init.yaml` file with the following content:

yaml



`#cloud-config users:   - name: newuser     sudo: ALL=(ALL) NOPASSWD:ALL     shell: /bin/bash     ssh-authorized-keys:       - ssh-rsa AAA...your-public-key... packages:   - htop   - git ssh_pwauth: false disable_root: true`

**Explanation**:

- `users`: Creates a new user named `newuser` with sudo privileges and SSH access.
- `packages`: Installs `htop` and `git`.
- `ssh_pwauth: false`: Disables password authentication for SSH.
- `disable_root: true`: Disables root login via SSH for security.

---

## Step 5: Connecting to Your Droplet

Once the droplet is created, you can connect to it via SSH using the new user created by `cloud-init`.

1. Find your droplet’s IP address:
    
    bash
    
    
    
    `doctl compute droplet list`
    

2. Use the following command to connect:
    
    bash
    
    
    
    `ssh newuser@<droplet-ip>`
    

**Troubleshooting**:

- If you’re unable to connect, ensure that your SSH key is added correctly, and check your firewall settings.

---

## Conclusion

In this tutorial, we used `doctl` to automate the creation of an Arch Linux droplet and `cloud-init` to configure the server automatically. This approach is ideal for cloud deployments where automation and scalability are key.

**What’s Next?**

- You can extend this setup by adding more packages to `cloud-init`, configuring additional users, or deploying applications.

---

## Resources

- - DigitalOcean. (n.d.). [DigitalOcean Documentation](https://docs.digitalocean.com). DigitalOcean. Retrieved September 27, 2024, from [https://docs.digitalocean.com](https://docs.digitalocean.com)
- Cloud-init. (n.d.). [Cloud-init Documentation](https://docs.cloud-init.io/en/latest/). Cloud-init. Retrieved September 23, 2024, from [https://docs.cloud-init.io/en/latest/](https://docs.cloud-init.io/en/latest/)
- DigitalOcean. (n.d.). [How to Use Cloud-Init to Automate Initial Server Setup on Ubuntu 20.04](https://www.digitalocean.com/community/tutorials/how-to-use-cloud-config-for-your-initial-server-setup). DigitalOcean Community Tutorials. Retrieved September 27, 2024, from [https://www.digitalocean.com/community/tutorials/how-to-use-cloud-config-for-your-initial-server-setup](https://www.digitalocean.com/community/tutorials/how-to-use-cloud-config-for-your-initial-server-setup)
- DigitalOcean. (n.d.). [Doctl Documentation](https://docs.digitalocean.com/reference/doctl/). DigitalOcean. Retrieved September 25, 2024, from [https://docs.digitalocean.com/reference/doctl/](https://docs.digitalocean.com/reference/doctl/)

---

### **Git Instructions**

Ensure you make regular commits with clear messages. Here’s an example of how to structure your Git workflow:

1. **Initialize Git**:
    
    bash
    
    
    `git init`
    
2. **Add your files**:
    
    bash
    
    
    `git add README.md assets/`
    
3. **Make regular commits**:
    
    bash
    
    
    
    `git commit -m "Added tutorial sections with SSH and cloud-init setup"`
    
4. **Push to GitHub**:
    
    bash
    
    
    `git push -u origin master`
    




---






    