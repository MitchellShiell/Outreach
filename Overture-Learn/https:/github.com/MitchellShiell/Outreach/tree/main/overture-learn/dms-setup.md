# DMS Setup Tutorial

Script tutorial on setting up the DMS on a server linked to a secure domain. 

## Structure 

- [Getting Started](#part-1-getting-started)
- [Introduction](#introduction)
    - [What you'll need](#what-youll-need)
- [Pre deployment Configuration](#part-2-pre-deployment-configuration)
    - [Generating an SSL Certificate](#ssl-certificate)
    - [Setting up Identity Providers](#identity-providers)
    - [Installing Docker](#docker)
    - [Installing the DMS](#dms-installation)
- [Deployment](#part-3-deployment)
- [Post Deployment Verification & Configuration](#part-3-post-deployment-configuration)
    - [Ego](#ego)
    - [Song](#song)
    - [Score](#score)
    - [Maestro](#maestro)
    - [Elasticsearch](#elasticsearch)
    - [Arranger](#arranger)
    - [DMS UI](#dms-ui)

</br>

## Part 1: Getting Started

</br>

### Introduction

Hello and welcome to our tutorial on setting up the Overture Data Management System on a VM. 

In this video, we'll be walking you through the steps to get the Overture DMS up and running on your server. 

This tutorial is structured into 4 segments: pre-requeisties, pre-deployment configuration, deployment, post-deployment verification & configuration. 

Whether you are a beginner or are an experienced user this tutorial will provide you with the knowledge you need to get your data management system set up from start to finish. 

Let's get started

### What you'll need

Before we dive into the main content of this tutorial, there are a few prerequisites that we need to cover. These are things that you should get in place before you continue following along with the tutorial. 

For the majority of these pre-requisites we recomend contacting the IT support available to you at your instituition.

First you will need a server to deploy the DMS to. A few points about the server we are deploying to:

- The server needs to be running Linux or MacOS
- We recommend that you have atleast 6 CPU cores, 8GB of memory and 20GB of disk space.
- You will need to have administrator privledges

The second prerequisite is a cloud storage provider that we will connect our DMS to for our data storage and retrieval. 

- The DMS does come with a free MinIO service, if you chose to use this then all the configuration will be done automatically during deployment
- If you plan to use an alternate storage provider, we support We support  Amazon S3, Microsoft Azure Storage, or OpenStack with Ceph. 

I'll link the prerequisite steps for configuring alternate storage providers here and in the description. 

<!--Discorse or Slack-->

*If you have any questions feel free to contact us at contact@overture.bio or leave a comment below*

</br>

## Part 2: Pre Deployment Configuration

</br>

Now that we've covered the prerequisites, lets get started with the main content of the tutorial.

We will start by setting up our DMS execuable, and then making sure we have everything we need to get our DMS running smoothly.

I'm going to ssh into my server 

```bash
ssh -i mitchell_keypair.pem ubuntu@142.1.177.53
```

Make sure my server is up to date

```bash
apt update
```

### DMS Installation

Lets first downlaod and setup our DMS executable

Step 1: We will use the following curl command:

```bash
curl https://raw.githubusercontent.com/overture-stack/dms/<x.y.z>/src/main/bin/dms-docker > dms
```

If we go to the DMS github repository

```bash
www.github.com/overture-stack/dms
```

You can see that at the time of this video the latest verions of the DMS is 1.2.0. 

Step 2: lets make the DMS file executable

```bash
chmod +x dms
```
Step 3: lets make this file usable from anywhere by moving it to the local binary folder

```bash
mv dms /usr/local/bin/
```

Step 4: lets see if everything is working as it should

```bash
dms -h
```

You should see the help menu print out, this command also generates the dms directory which we can confirm exists by 

```bash
cd ~/.dms
```

While we are here I will upload and save my logo file to the assets folder within the dms directory. I'm going to make sure I name the file correctly dms_logo.png

This file can be a JPG, PNG, or SVG.


Awesome, now that the DMS executable is installed and we've uploaded our logo, we can proceed with our our pre-deployment configuration. 

### SSL Certificate

Since we are linking a domain to a server that potentially handles sensitive information it is important to have an SSL certificate. 

The Overture DMS requries a SSL certificate generated using Certbot.

Lets go to certbot.eff.org

Certbot is a tool that makes it easy to obtain and install SSL certificates from the Let's Encrypt Certificate Authority.

To get started lets ensure that your version of snapd is up to date

```bash
 snap install core; snap refresh core
```

Run this command on the command line on the machine to install Certbot.

```bash
snap install --classic certbot
```

Execute the following instruction on the command line on the machine to ensure that the certbot command can be run.

```bash
ln -s /snap/bin/certbot /usr/bin/certbot
```

I'll run this command to get a certificate.

```bash
certbot certonly --standalone
```

Once the certificate is issued you will see a message indicating that the certificate has been successfully obtained.

Lets copy an paste the path to that certificate in our notes for later.

---

### Identity Providers

One component of our data management system is Ego our authorization and authentication microservice.

we'll need to obtain a client ID and client secret from the Google API console so that ourselves, other admins, and our DMS users can login in with there gmails accounts, assuming they have authorized credentials. 

For the sake of time we will we will focus on obtaining a client ID and client secret with the google API console but i'll link the documentation for setting up client ID and secerets our other identity providers APIs.

Step 1: 

Log in to the Google API Console using your Google account.

Step 2: 

Go to Dashboard and select the existing project where you want your application to exist, or create a new one

Step 3: 

Go to the OAuth consent screen to register your application with your project. Since we are putting our DMS on a server for external users to access we will lick external and then click create.

Step 4:

I will add in my authorized domains, developer contact infromation and then hit save and continue

Step 5: 

Go to credentitals and click the "Create credentials" button and select "OAuth client ID" from the dropdown menu.

Step 6: Select the appropriate application type in this case web application and enter a name for the client

Step 7: In the "Authorized redirect URIs" field, enter the URL where you want Google to redirect the user after they have granted or denied permission.

this is important you are going to want type this in as follows:

```
https://<yourDomain>/ego-api/oauth/login/google
```

Step 8: Click the "Create" button.

Step 9: On the next page, you will see your client ID and client secret. Make sure to save these somewhere safe, as you will need them later

That's it! You now have a client ID and client secret that you can use to authenticate your application with Google's API. 

### Docker

Next we will walk you through the process of installing Docker, a popular containerization platform, on our server.

You will want to follow dockers documentation linked here as the steps may be diffent depending on the operating system your server is running on.

I'm running on Ubuntu

Step 1: Add the Docker GPG key

```bash
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
```

Step 2: 

Add the docker repository to your systems software sources

```bash
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu focal stable"
```

Step 3: we will run apt update

```bash
apt update
```

step 4: install docker

```bash
apt install docker-ce
```

step 5: Start the Docker Daemon:

```bash
systemctl start docker
```

step 5: verify that docker has been installing and is running

```bash
docker run hello-world
```

This command should download and run a test container, displaying a message indicating that Docker is working properly.


The DMS requires us to initialize Docker swarm which is quite simple using the command

```bash
docker swarm init
```

If successful we should see the following message

```bash
swarm initialized: current node (eoiw3io2ion3oinri4o90490v) is now a manager.
```

### DMS Questionaire 

To finish up our configuration I'll type the following command, this will start up the interactive configuration questionaire.

```bash
dms config build
```

Follow the questionnaire, from start to finish, default values are displayed in the brackets, if you just hit enter your response will be saved as the default value. No for each step there is a convienent url linking to the relevant documentation. 

Ego

- authorization and authentication

Song

- metadata indexing service

Score

- data transfer service

elastic search service

- we use elastic search to index our data for easy search and retrival from our front-end UI's

maestro

- takes the stored data and indexes into elastic-search, we need to provide an alias or a name for an index
arranger

DMS UI

- The end user data portal where users can search explore and download submitted data

Now that our configuration is complete we are ready to deploy our DMS. 

## Part 3: Deployment

Likely the easiest of all steps, execute the following command

```bash
dms cluster start
```

As the deployment executes status messages will appear as each service is spun up. 

It may take some time for the deployment to exit successfully, the relatively larger services may take some time to complete (e.g. Ego API, Elasticsearch)

Once complete you should see a success message "Deployment completed successfully insluding a link to the relevant documentation for our next segement, post-deployment verification.

## Part 4: Post-Deployment Configuration

```bash
docker service ls
```

Check that Ego API is running
```bash
https://<myDomain>/ego-api/swagger-ui.html
```

Check and Configure Ego UI
```bash
```

Login and Configure Ego UI
```bash
https://<myDomain>/ego-ui
```

Check Song API is running
```bash
https://<myDomain>/song-api/swagger-ui.html
```

Check MinIO is running (Optional)
```bash
https://<myDomain>/minio
```

Check Maestro API is running
```bash
https://<myDomain>/maestro/api-docs
```



Check Elasticsearch is running (install elastichead)
```bash
https://<myDomain>/elasticsearch
```

Add project ot Arranger-ui
```bash
https://<myDomain>/arranger-ui/
```

Check the data portal is running
```bash
https://<myDomain>
```
