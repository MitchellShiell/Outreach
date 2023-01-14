#### [Back to Wiki](https://github.com/MitchellShiell/Outreach/wiki/Overture-Learn)

# DMS Setup Tutorial

Script tutorial on setting up the DMS on a server linked to a secure domain. 

Using Iterm with oh-my-zsh spaceship theme
 - cmd +, + , + (3x mag for easy to read text)
 - Terminal Dimension is 83x39 (dimension is specific to the magnification setting above)
 - Centered

Chrome brower in full screen mode on second desktop so i can swipe back and fourth as required and therefore don't need to move the terminal around (gives flexibility and consistency when editing)

Part 4 cheat sheet

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

### Docker

First we will need to have Docker, a popular containerization platform, installed to our server.

You will want to follow dockers documentation linked here as the steps may be diffent depending on the operating system your server is running on.

https://docs.docker.com/get-docker/

I'm running on Ubuntu.. run through docker steps quickly

This command should download and run a test container, displaying a message indicating that Docker is working properly.

The DMS requires us to initialize Docker swarm which is quite simple using the command

```bash
docker swarm init
```

If successful we should see the following message

```bash
swarm initialized: current node (eoiw3io2ion3oinri4o90490v) is now a manager.
```

---

clear

---

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

### clear

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


---

### Cut

---


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

You should see the help menu print out,

you can see some links to our documentation as well.

this command also generates the dms directory which we can confirm exists by 

```bash
cd ~/.dms
```

While we are here I will upload and save my logo file to the assets folder within the dms directory. I'm going to make sure I name the file correctly dms_logo.png

```bash
mv $HOME/dms_logo.png
```

This file can be a JPG, PNG, or SVG.

Awesome, now that the DMS executable is installed and we've uploaded our logo, we can proceed

---

### Clear

---

### DMS Questionaire 

To finish up our configuration I'll type the following command, this will start up the interactive configuration questionaire.

```bash
dms config build
```

Follow the questionnaire, from start to finish, default values are displayed in the brackets, if you just hit enter your response will be saved as the default value. No for each step there is a convienent url linking to the relevant documentation. 

This questionaire is divided into seperate sections representing each services or component within overture

---

### Can Cut

---

**cluster mode & gateway**

The cluster mode determines whether i am running locally or in the server mode, If you want detailed infromation on each step their will be linked documentation at every step of this questionaire

The gateway is an ingress controller exposing all the overture servers through one port, to access this we need a domain name setup in advance. you can see there are examples for these questions in the brackets. 

So in this section i will put down my domain name

Next I'll need the absolute path for the SSL certificate, if you followed along with certbot you can click enter as the default seen in sqaure brackets is indeed the path of our SSL cert

---

### Can Cut

---

**Ego**

Next up is Ego which is our authentication and authorization service, it will need some validity periods for our API keys and JWT.

I will be going with the default options, if you want some more detailed information on this configuration step I recommend using the link found under the section title

Our next choice is going to be choosing which OAuth identity providers we want to configure our DMS to accept

This will allow users and admins to use these single sign on identity providers with our services

We have already setup our client and secret IDs for google, and again for the sake of time we will only configure for google but please follow the documentations linked here if you wish to include the other OAuth providers.

---

### Can Cut

---

**Song**

Okay next up is song our metadata indexing service

Not a lot of configurations required for installation and intial deployment for song however their is tons of cool things we can do to configure it later on

**Score**

Next up is score, our data transfer microservices

here it's asking if i have an exiting S3 object storage service, to use with Score, Now if we don't have an existing services and go with No it will by default provide the free MinIO service that comes bundled with the DMS. 

Do I want to create credentials automatically, Yup

Next it will ask about the name of my data buckets, for this case i'm fine with the default names

---

### Can Cut

---

**elastic search service**

we use elastic search to index our data for easy search and retrival from our front-end UI's

The default username for ealstic search is `elastic` for this step I'll just need to input a password

---

### Can Cut

---

**maestro**

Maestro takes the song metadata and indexes into elastic-search

For this step we need to provide an alias or a name for an index, again I will go with the defaults

---

### Can Cut

---

**DMS UI**

The end user data portal where users can search explore and download submitted data

The first config is an email where users can contact you for support!

Next I can customize the name of the data portal, default is boring

---

### Can Cut

---

**Arranger**

Okay so we are now given this large note,

So the next three fields are fields that will configure our arranger service

Arranger interfaces between the elastic search index and the DMS UI components so these configuration settings will allow us to customize our faceted search fields 

As arranger interacts with the data portal UI it needs to match the IDs we set, for now we will go with the defaults, you can always go back and view your configuration file but it's good to take note of whatever you name these files as you will need to referecne them later

---

### Clear

---

## Part 3: Deployment

Likely the easiest of all steps, execute the following command

```bash
dms cluster start
```

As the deployment executes status messages will appear as each service is spun up. 

It may take some time for the deployment to exit successfully, the relatively larger services may take some time to complete (e.g. Ego API, Elasticsearch)

Once complete you should see a success message "Deployment completed successfully insluding a link to the relevant documentation for our next segement, post-deployment verification.



---

### Cut

--- 

## Part 4: Post-Deployment Configuration and sanity checks

If all is well we should see replicas of each service,

So as we can see we have a success message but we do still want to confirm that the services docker have replciated on docker

```bash
docker service ls
```

some services deploy quickly while other might take longer, overall this is a fairly quick process.

Lets check out and verify our deployment

First we will check the Ego API is running

```bash
https://<myDomain>/ego-api/swagger-ui.html
```

If your not familliar with swagger-ui's they can be quite handy, all of the api endpoints are labeled here and if we click on each one we are provided information and interaction with each endpoint, super handy we will definitly interact with this and become more familair in future tutorials.


Next I will want to check out my Ego UI,

it's important we login now as their are currently no users in the system and we do require atleast one administrator.

Conviently enough the first login to Ego-UI will always be assigned to role of admin so we will want to do that we have freshly deployed our DMS. 

```bash
https://<myDomain>/ego-ui
```

So from this dashboard I can see that I am an admin, but I still don't have all the neccesassary permission as an admin. So What i will do is add a group and in this case i will select the DMS admin group which will grant me write permissions for the DMS, Score, and Song. This will be important later on.

speaking about song let's check that the Song API is running

```bash
https://<myDomain>/song-api/swagger-ui.html
```

So tracks and validates metadata across object storage, on that note lets check to see if our object storage is up and running for me and maybe you that means checking minIO.

Check MinIO is running (Optional)

```bash
https://<myDomain>/minio
```

I'll check my config.yaml for the credentials i need

perfect here we can see my two buckets were successfully created.

now to build the indexes for elastic search we want to make sure the maestro api is working

Check Maestro API is running
```bash
https://<myDomain>/maestro/api-docs
```

now lets make sure Elasticsearch is running (install elastichead)

For this i will install the chrome extention elastichead, for firefox you can downlaod elasticview, you may even have kabanas own proprietary elastic tool. I will be using elastic head, I'll be sure to link to the other extentions mentioned in the description.

```bash
https://<myDomain>/elasticsearch
```

click connect and then we will pull our credentials from our config.yaml file 

and now I am able to see and browse my file_centric indice and all the fields contained within. 

Now the fields of this index that will be displayed in our DMS is configured and handled by arranger. so lets now check that is up and running.

```bash
https://<myDomain>/arranger-ui/
```
up and running, nice, but we can see we don't have any projects, we will need to make one so that arranger is able to pull the fields we require from the elastic search index and into our portal.

Now we want to make sure our naming is consistent with the values we chose in our DMS confi questionaire. If we pull up the config.ymal file we can see the names we chose. since the DMS UI will be referencing these names we want them to be consistent in arrange.

> Add project

Project ID = file
Name = file
ES index = file_centric

this matches our config with the DMS-UI and the ES index

next we need to input a json metadata file to describe the configuratoin of our project.

To start off we will use a downloadable sample

<link>

Now the same fields we saw in elastic search are seen here in arranger

if i go to the table table I can pick the fields included and the order. 

Lastly I will access our data portal by just heading to my domain 

And from here we should see our customized faceted search, our logo, our custom name.


You'll notice there is no data. 

Not to worry, our next video we will run through a data upload workflow
