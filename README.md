## [What is AWS Lightsail](https://aws.amazon.com/lightsail/)

   `Lightsail is an easy-to-use cloud platform that offers you everything needed to build an application or website, plus a cost-effective, monthly plan. Whether you’re new to the cloud or looking to get on the cloud quickly with AWS infrastructure you trust, we’ve got you covered.`

## [Why not EC2?](https://aws.amazon.com/premiumsupport/knowledge-center/lightsail-export-linux-instance-ec2/)

   `We can take advantage of Lightsail's easy-to-use platform and low, predictable pricing to launch, manage and scale your application, while always having the option to easily move some or all of your application to EC2, as your needs grow.`

## Goals

  * Spin up a linux VPS (Virtual Private Server) that will run our containerized API and DB for our development environment.
  * SSH into our VPS.
  * Clone our GitLab API Repository (using SSH). We will use an actual API repository from an EdTech SaaS that John and Jordan have been working on.
  * Install docker engine and docker compose.
  * Install Google Cloud SDK.
  * Install PostgreSQL client tools.
  * Install all required tooling on our VPS.
  * Demonstrate how VS Code can be used to [open a remote folder](https://code.visualstudio.com/docs/remote/ssh) so we can develop against our API.

## Create Linux VPS

**Why?** Allows us to move processor intense operations, like running a Docker Container, off our local machine. Some quicks wins are consitent and reproducible development environments, hardware comes interchangable, longer laptop battery life, and ability to use less expensive hardware (Chromebook for example).

  1. [Log into AWS Lightsail](https://lightsail.aws.amazon.com/) and click **Create Instance**
  2. **Select a Platform** => Linux/Unix
  3. **Select a blueprint** => **OS Only** => Ubuntu 18.04 LTS
  4. **Launch Script** => Leave blank for now
  5. **SSH Key** => We will use the default for this lab
  6. **Snapshots** => We will leave disabled for this lab
  7. **Choose Instance Plan** => _Select whichever option you prefer_
  8. **Identify Instance** => Add an instance name
  9. **Click Create Instance**
  
  This will take a few minutes to spin up.
  
## SSH into Linux VPS

**Why?** SSH will allow us to securely connect to our linux server through the command line and also later with VS Code. 

  1. Click into our Instance and navigate to the **Connect** tab. Note your _Public IP_ and _User name_
  2. Download the [Default SSH Key](https://lightsail.aws.amazon.com/ls/webapp/account/keys) as _lightsail-dev-environment.pem_
  3. Move _lightsail-dev-environment.pem_ to `~/.ssh` => `$ mv /Users/<user>/Downloads/lightsail-dev-environment.pem ~/.ssh/lightsail-dev-environment.pem`
  4. Add host to `~/.ssh/config` and link private key file `~/.ssh/lightsail-dev-environment.pem`
  5. Open `$ vim ~/.ssh/config` or Create `$ touch ~/.ssh/config`
  
  ```
  Host <Public IP>
    UseKeychain yes
    AddKeysToAgent yes
    IdentityFile ~/.ssh/lightsail-dev-environment.pem
  ```
  6. Type **yes** to continue connect and to add host to `~/.ssh/known_hosts`
  7. Ok, let us SSH! => `$ ssh <User name>@<Public IP>`
  8. **WARNING: UNPROTECTED PRIVATE KEY FILE!** => `$ chmod 600 ~/.ssh/lightsail-dev-environment.pem`
  9. Seriously, let us SSH! => `$ ssh <User name>@<Public IP>`
  
  We are now connected to our Linux VPS :bowtie:

## Clone GitLab Repository

**Why?** We will use this Linux environment as a development environment, so we will need access to our repositories, just like we would on a physical laptop. We are using GitLab for this demo, but the same concept would apply to GitHub.

  1. Create SSH Key => `$ ssh-keygen -t ed25519 -C "lightsail-dev-environment"` => Press Enter as needed to select defaults
  2. Copy _Public SSH Key_ => `$ cat .ssh/id_ed25519.pub` and copy/paste (note: we could have installed xclip)
  3. [Open GitLab SSH Settings](https://gitlab.com/profile/keys) and Paste / Save the public SSH Key
  4. Test things are working! => `$ ssh -T git@gitlab.com`
  5. Type **yes** to continue connect and to add host to `~/.ssh/known_hosts`
  
  We are now connected to GitLab via SSH  <img src="https://emoji.slack-edge.com/T02592416/hipster-tanuki/94529b8ed5f5dd4a.png" alt="drawing" width="20"/>
  
  6. Create and cd into `code` directory => `$ mkdir code` `$ cd code` 
  7. Clone API Repository => `$ git clone git@gitlab.com:student-hub/student-hub-api.git`. We are cloning a private repository, so we can demo against an actual production api. At this step, you would be able to clone any of your GitLab repos.
  8.  Check out repo => `$ cd student-hub-api` `$ ls`

## Install docker engine and docker compose

**Why?** This repo uses _docker-compose_ for local development. We will need to install _docker-engine_ and _docker-compose_ in order to start the API.

  1. [Install Docker Engine](https://docs.docker.com/install/linux/docker-ce/ubuntu)
  2. [Install Docker Compose](https://docs.docker.com/compose/install/#install-compose-on-linux-systems)
