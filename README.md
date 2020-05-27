## Motivation

  This was motivated by switching laptops and having to setup a new local development environment. I began to explore options so that my development machine mattered less. Essentially moving my [dotfiles](https://www.freecodecamp.org/news/dive-into-dotfiles-part-1-e4eb1003cff6/) to reproducible cloud environment.
  
  Some options that provide similar functionality are [AWS Cloud9](https://aws.amazon.com/cloud9/), [Visual Studio Codespaces](https://visualstudio.microsoft.com/services/visual-studio-codespaces/), and [GitHub Codespaces](https://github.com/features/codespaces/). VS Codespaces is pretty cool, but I have found it difficult to connect to a repository [outside of GitHub](https://docs.microsoft.com/en-us/visualstudio/online/reference/repository).
  
  Another motivating factor - Docker has been a large contributor to battery drain on my newer MBP. Docker is also the main cause of the fan kicking in and the laptop becoming very hot. Moving docker to a Linux VPS increased my battery life and eliminated the fan/heat concerns.

## [What is AWS Lightsail](https://aws.amazon.com/lightsail/)

   `Lightsail is an easy-to-use cloud platform that offers you everything needed to build an application or website, plus a cost-effective, monthly plan. Whether you’re new to the cloud or looking to get on the cloud quickly with AWS infrastructure you trust, we’ve got you covered.`

## [Why not EC2?](https://aws.amazon.com/premiumsupport/knowledge-center/lightsail-export-linux-instance-ec2/)

   `We can take advantage of Lightsail's easy-to-use platform and low, predictable pricing to launch, manage and scale your application, while always having the option to easily move some or all of your application to EC2, as your needs grow.`

## Goals

  * Spin up a linux VPS (Virtual Private Server) that will run our containerized API and DB for our development environment.
  * SSH into our VPS.
  * Clone our GitLab API Repository (using SSH). We will use an actual API repository from an EdTech SaaS that John and Jordan have been working on.
  * Install docker engine and docker compose.
  * Install repository tooling.
  * Start the API!
  * Customize!
  * Remote Development using SSH

## Create Linux VPS

**Why?** Allows us to move processor intense operations, like running a Docker Container, off our local machine. Some quicks wins are consitent and reproducible development environments, hardware comes interchangable, longer laptop battery life, and ability to use less expensive hardware (Chromebook for example).

  1. [Log into AWS Lightsail](https://lightsail.aws.amazon.com/) and click **Create Instance**
  2. **Select a Platform** => Linux/Unix
  3. **Select a blueprint** => **OS Only** => Ubuntu 18.04 LTS
  4. **Launch Script** => Leave blank for now, but we want to automate this eventually!
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

  1. [Install Docker Engine](https://docs.docker.com/install/linux/docker-ce/ubuntu) => `$ sudo docker run hello-world`
    
   Docker engine installed! :neckbeard:
  
  2. [Install Docker Compose](https://docs.docker.com/compose/install/#install-compose-on-linux-systems) => `$ docker-compose --version`
  
  Docker Compose is now installed! :sunglasses:
  
  3. Add a docker user group to run commands without sudo.
  
  ```
   // Create the docker group.
   $ sudo groupadd docker

   // Add your user to the docker group.
   $ sudo usermod -aG docker $USER

   // Activate group changes.
   $ newgrp docker

   // Verify that you can run docker commands without sudo.
   $ docker run hello-world  
  ```
  
  4. Use docker-compose to run `yarn` and install dependencies => `$ docker-compose run hapi yarn`
  
  [If docker daemon is not running..](https://github.com/docker/for-linux/issues/281) 
  
  ```  
   Check docker status => $ systemctl status docker
   Stop docker service => $ sudo systemctl stop docker
   Start docker daemon => $ sudo systemctl start docker
  ```
 
 Docker is good to go! :metal:

## Install repository tooling

**Why?** In this section, we will install tooling that is required for the API project we are using in this demo. Not all projects will require the same tools, but we wanted to demonstrate a real world example.

  1. [Install Google Cloud SDK](https://cloud.google.com/sdk/docs/downloads-interactive#linux)
  2. Create `~/student-hub-developer-service-account.json` and paste service account. This service account allows engineers to connect to GCP and interact with Development project cloud storage, etc.
  3. Authenticate with GCP using developer service account => `$ gcloud auth activate-service-account <service-account-email> --key-file=./student-hub-developer-service-account.json`
  4. Install Postgres Client Tools => `$ sudo apt install postgresql-client-10`
  5. Import Postgres dumpfile from cloud storage and import. This allows engineers to have a local environment with production load data. We will eventually randomize this data during import, but not necessary right now.

## Start the API!

**Why?** Because we want to make sure it works!

  1. Start API in detached mode => `$ docker-compose up -d`
  2. Follow logs => `$ docker-compose logs -f`
  3. Demo hitting the API from Postman => :sob:
  3. Under **Networking** Tab, add a rule for port _3005_. You can restrict access to specific IP addresses.

## Customize

**Why?** In this section, we will customize our Linux VPS

  1. [Install ZSH](https://github.com/ohmyzsh/ohmyzsh/wiki/Installing-ZSH)
  2. [Install Oh My ZSH](https://github.com/ohmyzsh/ohmyzsh)
  3. Set ZSH for ubuntu user => `$ sudo chsh -s $(which zsh) ubuntu`
  4. Exit SSH and SSH In => We should see _Oh My ZSH_ as the default shell
  5. [Install starship](https://computingforgeeks.com/how-to-install-starship-shell-prompt-for-bash-zsh-fish/)
  6. [Install fnm](https://github.com/Schniz/fnm)
  7. [Install Yarn](https://classic.yarnpkg.com/en/docs/install#debian-stable)
  
## Remote Development using SSH

**Why?** VS Code can be used to [open a remote folder](https://code.visualstudio.com/docs/remote/ssh) allowing us to develop against a VPS.

  1. Install VS Code [Remote Development Extension Pack](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.vscode-remote-extensionpack)
  2. Open the Command Palette `CMD + SHIFT + P` => `Remote-SSH: Add New SSH Host...` => `ssh ubuntu@<Public IP>`
  3. Select the appropriate SSH Config to update
  4. Select the appropriate directory on the VPS.
  
VS Code is connected! Our development environment now lives in the cloud! :fire:
