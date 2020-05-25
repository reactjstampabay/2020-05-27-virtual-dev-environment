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

### Spin up a linux VPS

**Why?** Allows us to move processor intense operations, like running a Docker Container, off our local machine. Some quicks wins are consitent and reproducible development environments, hardware comes interchangable, longer laptop battery life, and ability to use less expensive hardware (Chromebook for example).

  1. [Create Lightsail Instance](https://lightsail.aws.amazon.com/)
