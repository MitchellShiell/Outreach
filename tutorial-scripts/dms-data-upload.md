#### [Back to Wiki](https://github.com/MitchellShiell/Outreach/wiki/Overture-Learn)

# DMS Data Upload Workflow

Script for tutorial covering a basic DMS data upload workflow.

Using Iterm with oh-my-zsh spaceship theme
 - cmd +, + , + (3x mag for easy to read text)
 - Terminal Dimension is 83x39 (dimension is specific to the magnification setting above)
 - Centered

Chrome brower in full screen mode on second desktop so i can swipe back and fourth as required and therefore don't need to move the terminal around (gives flexibility and consistency when editing)

## Structure


#### [Introduction](#part-0-introduction) :white_check_mark:
#### [Part 1 Setting up the Song and Score Clients](#part-1-setting-up-the-score-and-song-clients) :white_check_mark:
- [Download and Configure the Song Client](#downloading-and-configuring-the-song-client) :white_check_mark:
- [Download and Configure Score Client](#downloading-and-configuring-the-score-client) :white_check_mark:
#### [Part 2 Preparing your first payload](#part-2-preparing-our-first-payload)
- [Create a Study in song](#create-a-study-in-song)
- [Submitting an Analysis to Song](#submitting-an-analysis-to-song)
- [Create Manifest for Score](#create-manifest-for-score)
#### [Part 3 Uploading and Publishing](#part-3-uploading-and-publishing-the-analysis)
- [Upload Data via Score](#upload-data-via-score)
- [Publish Analysis](#publish-analysis)
- [Index Study via Maestro](#index-study-via-maestro)
- [Verify the Data in the Portal](#verify-the-data-in-the-portal)



## Introduction 

<!--Revisit once tutorial is complete-->

In this video I'll be demonstrating and explaining a basic data upload workflow using Overtures data management system.

This video tutorial is split up into 3 segments.

We'll be using demo data that you can download from the link in the description. If you have not already you can check out our Overture overview video as well as our Overture setup video that proceeded this tutorial. 

I'll link both those here. 

Okay with that out the way lets get started
 
***

</br>

## Clear - Cut

</br>

***

<br/>

## Part 1 Setting up the Score and Song Clients 

To submit data to our DMS portal we will need to use the Overture components Song and Score.

As explained in previous videos score is our file transfer and object storage microservice

while song is our metadata tracking and validation service.

There are two ways to interact with Song and Score, the first is through the command line interface and the second is using the swagger UIs.

In this video we will cover both methods as we go. 

Lets start by downloading and configuring the song and score clients. 

***

</br>

## Clear - Cut

</br>

***

</br>


### Downloading and Configuring the Song Client

</br>

Song is providing a metadata management and storage system to easily track and manage files in a secure and validated environment, against a standard or custom schema which we will discuss later.

Lets begin by downloading and unziping the latest Song client, 

I've linked a seperate document that includes each command I'll be using so you can easily copy and paste as we go through.

From the command line terminal I will enter the following command.

Download

```bash
wget -O song-client.tar.gz https://artifacts.oicr.on.ca/artifactory/dcc-release/bio/overture/song-client/[RELEASE]/song-client-[RELEASE]-dist.tar.gz
```

Unzip

```bash
tar xvzf song-client.tar.gz
```

```bash
cd song-client-5.0.2
```

Within the song client directory we'll be updating the 'song-client-5.0.2/conf/application.yml' file.

I'll first input my domain under the serverUrl field

next I'll update the study ID field with the name of my study  in this case `mystudy-123`

In song ***a study*** is simply a group of data that belongs together therefore will be submitted and indexed together. 

I will review this concept again as we get into our data upload workflow.

Our final field to update here is going to be our accessToken. 

To aquire this we will quickly check in on our user and application permissions within Ego

```yml
client:
  serverUrl: http://localhost:80/song-api
  studyId: ABC123
  programName: sing
  debug: false
  accessToken: 36099917-45b1-49f4-b91e-68a655eb6708
```

***

</br>

## Clear - Cut

</br>

***

</br>

to access the ego ui we will go to our domain /ego-ui

From here we will check the left navigation, click Groups and verify that the dms-admin group has been created and is assigned the permissions: DMS.WRITE, SONG.WRITE, SCORE.WRITE.

Perfect these permissions will be required to perform our data upload operations if they are not set you can edit them by click edit in the top left of the right hand panel.

That is our appliations authorizations lets check our Users and verify that we as users can do the same. Perfect as you can see we are able to DMS.WRITE, SONG.WRITE, SCORE.WRITE.

Now that we have verified we have the correct permissions, we can head to our DMS UI to generate our API.

This API Key is used for all subsequent operations with the various Overture service APIs you will interact with. once generated it will be availabe how ever long you specificed when you first configured your DMS. In this case 29 days.

Now that we have the api we will update the song application.yml file

okay, with that saved we can move on to our next step, downloading and configuring the score client

***

</br>

## Clear - Cut

</br>

***

### Downloading and Configuring the Score Client

Next,we will download and configure the Score client. This command-line client is used to upload and download data files to and from your configured object storage service. 

Very similar to the song setup, we will first download and unzip the latest Score client from the command line within our server

```bash
wget -O score-client.tar.gz https://artifacts.oicr.on.ca/artifactory/dcc-release/bio/overture/score-client/[RELEASE]/score-client-[RELEASE]-dist.tar.gz
```

unzip

```bash
tar xvzf score-client.tar.gz
```

```bash 
cd score-client-5.8.1
```

We will next open the application.properties file from `score-client/5.8.1/conf/application.properties`

from here we will set `access token` to the API Key we saved eariler via the DMS UI.

Next lets uncomment the line defining our `metadata.url` and set it to our domain with the song-api

```bash
# The location of the metadata service (SONG)
metadata.url=http://dmstutorial.cancercollaboratory.org/song-api
```

we will also uncomment the line defining our `storage.url` and set it to our domain with the score-api

```bash 
# The location of the object storage service (SCORE)
storage.url=http://dmstutorial.cancercollaboratory.org/score-api
```

if you are setting up locally these steps are very much the same however you will need to add local directories to these fields, if you run into any issues be sure to reference the documentation on our website.

Now that we have our song and score client configured we are ready to prepare our first payload. 

***

</br>

## Clear - Cut

</br>

***

<br/>

## Part 2 Preparing our first payload

<br/>

### Create a Study in song

**What is a study**

Our first step in our data upload workflow is going to be creating ***a study*** in song.

In song ***a study*** is simply a group of data that belongs together therefore will be submitted and indexed together.

For this video our study is all the data we are submitting from the `mystudy-123` example data folder.

With song we can create a study in our command line using our client or using the swagger UI

***

</br>

## Clear - Cut

</br>

***

***To create your study via cURL in our terminal we will enter the following command:**

```bash
curl -X POST "https://dmstutorial.cancercollaboratory.org/song-api/studies/mystudy-123/" -H  "accept: */*" -H  "Authorization: bearer <API KEY>" -H  "Content-Type: application/json" -d "{  \"description\": \"string\",  \"info\": {  },  \"name\": \"string\",  \"organization\": \"string\",  \"studyId\": \"mystudy-123\"}"
```

We will leave the description, info, name, organization blank (can be filled in optionally if you want).

If successful, we will get this status response:

```bash
{
  "message": "Successfully created study 'mystudyID-123'"
}
```

 This indicates to us that the study was created in Song



***

</br>

## Clear - Cut

</br>

***

***To Create a Study Using Swagger UI we will need to go to the Song API's Swagger UI:***

https://dmstutorial.cancercollaboratory.org/song-api/swagger-ui.html

Under Study click the POST /studies/{studyId}/ CreateStudy endpoint.

Click Try it out then select the Authorize button in the top right.

Under Authorization, I'll enter the Bearer <API Key> and click Authorize. (this is the same API key we obtained from our DMS UI)
 
In the study body, we will update the studyId field, by replacing "string" with "mystudyID-123".
In the studyId field, enter ABC123.

Click Execute. 

If successful, we will get the same status response, this indicates to us that the study was created in Song:

```bash
{
  "message": "Successfully created study 'mystudyID-123'"
}
```

***

</br>

## Clear - Cut

</br>

***

### Submiting an Analysis to Song

Next, we must submit an analysis to Song. 

What is an ***analysis?***

When we submit data (in this case a variant call file) we also need to submit the metadata associated with that file. In Song this combination of data and metadata is called the ***analysis.***

All the metadata is recorded within a JSON file. This JSON file contains references to the data files, typically sequences reads or VCFs and all the associated metadata.

Here is a simple example of a JSON that could be submitted to Song

```json
1 {
2  "studyId": "mystudyID-123",
3  "analysisType": {
4    "name": "variant_call"
5  },
6  "samples": [
7    {
8      "submitterSampleId": "exammple-sample-id",
9      "matchedNormalSubmitterSampleId": null,
10      "sampleType": "Amplified DNA",
11      "specimen": {
12        "submitterSpecimenId": "exammple-specimen-id",
13        "specimenType": "Normal",
14        "tumourNormalDesignation": "Normal",
15        "specimenTissueSource": "Blood derived"
16      },
17      "donor": {
18        "submitterDonorId": "exammple-donor-id",
19        "gender": "Male"
20      }
21    }
22  ]
23 }
```

{1-23} represents the metadata JSON object

2 the studyId name has the value pair associated with our studies name (all data to be submitted and indexed together.)

3 the analysis type name value pair is a nested object that references a seperate JSON document (we will get to this soon)

6-22 the samples JSON name value pair is an array of recorded metadata for each sample being submitted, in this case one sample. Submitting two samples would look like the following

```json
1 {
2  "studyId": "mystudyID-123",
3  "analysisType": {
4    "name": "variant_call"
5  },
6  "samples": [
7    {
8      "submitterSampleId": "exammple-sample-id-1",
9      "matchedNormalSubmitterSampleId": null,
10      "sampleType": "Amplified DNA",
11      "specimen": {
12        "submitterSpecimenId": "exammple-specimen-id-2",
13        "specimenType": "Normal",
14        "tumourNormalDesignation": "Normal",
15        "specimenTissueSource": "Blood derived"
16      },
17      "donor": {
18        "submitterDonorId": "exammple-donor-id",
19        "gender": "Male"
20      }
21    },
22     {
23     "submitterSampleId": "exammple-sample-id-2",
24     "matchedNormalSubmitterSampleId": null,
25      "sampleType": "Amplified DNA",
26      "specimen": {
27        "submitterSpecimenId": "exammple-specimen-id-2",
28        "specimenType": "Normal",
29        "tumourNormalDesignation": "Normal",
30        "specimenTissueSource": "Blood derived"
31      },
32     "donor": {
33        "submitterDonorId": "exammple-donor-id",
34        "gender": "Female"
35      }
36    }
37  ]
38 }
```

Same studyID, same analysis type, just a new list item included in under the `samples` nested array being uploaded. 

Within each sample we can see the recorded metadata including `submitterSampleId` , 
`matchedNormalSubmitterSampleId`, `sampleType`, `specimen`, `submitterSpecimenId`, `specimenType`...

lastly each sample object contains a nested `donor` object that includes some simple metadata about the donor in this case it only contains a `submitterDonorId` and the gender of the donor. 

<!--no mention of file data-->

At this point you may be asking, how do I know how to fill out this information? This is where the analysis type comes into play.

At the top of our submitted JSON we defined our Analysis type. An analysis type is a seperate JSON document that describes the expected structuring and formatting of the subitted JSON file this is also refered to as the JSON Schema. 

**The JSON Schema**

So our metadata is submitted to Song as a JSON file. 

To insure the consistency and integrety of the data all submitted metadata is verified against a JSON schema. 

This ensures that all submitted data meets the desired structure and allowed values. 

To validate metadata at the time of submission, Song refreneces the JSON Schema (this is the value pair we used under the `analysistype` name within in our submitted JSON file). 

The JSON Schema provides a vocabulary for the structural validation of JSON formatted data, for example, ensuring that required fields are present, or that the contents of a field matches the desired data type or allowed values.

A schema is composed of two portions:

1. A base data model <!--(that is defined for patient data)-->

2. A dynamic schema uploaded by a Song administrator. The dynamic schema is extremely flexible, in order to encode any desired rules that submitted data must comply to.

Song administrators, can model the data inside the analysis type folder (the schema). 

An analysis type can contain any variety of information that needs to be recorded about a file type, defined in the submitted analysis JSON.

For demonstration purposes here, we will simply submit a default analysis type that is already pre-built called variantCall.

The JSON schema that our submitted metadata will be validated against looks like the following:

http://json-schema.org/draft-07/schema#

***

</br>

## Clear - Cut

</br>

***

**how do i submit it**

Once you have formatted the payload correctly, use the song-client submit command to upload the payload.

```bash
./bin/sing submit -f example-payload.json
```

If your payload is not formatted correctly, you will receive an error message detailing what is wrong. Please fix any errors and resubmit. If your payload is formatted correctly, you will get an analysisId in response:

```json
{
  "analysisId": "a4142a01-1274-45b4-942a-01127465b422",
  "status": "OK"
}
```

At this point, since the payload data has successfully been submitted and accepted by Song, it is now referred to as an analysis. The newly created analysis will be state UNPUBLISHED.

***

</br>

## Clear - Cut

</br>

***

### Create Manifest for Score

**what is an manifest** 

Use the returned analysis_id from step 2 to generate a manifest for file upload. This manifest will be used with the score-client in the next step. The manifest establishes a link between the analysis-id that has been submitted and the data file on your local systems that is being uploaded. Using the song-client manifest command, define

The analysis id using -a parameter.
The location of your input files with the -d parameter.
The output file path for the manifest file with the -f parameter. Note: this is a FILE PATH not a directory path.

**how do i submit it**

```bash
./bin/sing manifest -a a4142a01-1274-45b4-942a-01127465b422 -f /some/output/dir/manifest.txt  -d /submitting/file/directory

Wrote manifest file 'manifest.txt' for analysisId 'a4142a01-1274-45b4-942a-01127465b422'
```

***

</br>

## Clear - Cut

</br>

***

<br/>

## Part 3 Uploading and publishing the analysis

<br/>

### Upload Data via Score

With the manifest generated, use it to upload your raw data files to score:

Switch to your home directory and from there, initiate an upload by executing the Score client with this command:

```bash
./score-client-<latest-release-number>/bin/score-client upload --manifest ./<directory>/manifest.txt
```

Where <directory> is where you previously generated and stored the manifest file.

If successful, each file in the manifest will be 100% uploaded, and the score-client will indicate the upload has completed:

```bash
Uploading object: '/home/ubuntu/songdata/input-files/example.vcf.gz.idx' using the object id e98daf88-fdf8-5a89-9803-9ebafb41de94
100% [##################################################]  Parts: 1/1, Checksum: 100%, Write/sec: 1000B/s, Read/sec: 0B/s
Finalizing...
Total execution time:         3.141 s
Total bytes read    :               0
Total bytes written :              24
Upload completed
Uploading object: '/home/ubuntu/songdata/input-files/example.vcf.gz' using the object id 440f4559-e905-55ec-bdeb-9518f823e287
100% [##################################################]  Parts: 1/1, Checksum: 100%, Write/sec: 7.8K/s, Read/sec: 0B/s
Finalizing...
Total execution time:         3.105 s
Total bytes read    :               0
Total bytes written :              52
Upload completed
```

***

</br>

## Clear - Cut

</br>

***

### Publish Analysis

Once the input files are uploaded, you can finally publish your analysis so that it can be indexed into Elasticsearch and consumed by downstream Overture services.

Switch to your home directory and from there, publish the analysis by executing the Song client with this command:

```bash
./song-client-<latest-release-number>/bin/sing publish -a <ANALYSIS ID
```

Where <ANALYSIS ID> is the Analysis ID you captured earlier from submitting an analysis to Song.

If successful, the Song client will indicate that the analysis is successfully published:

```bash
AnalysisId 3ecae260-db0e-450d-8ae2-60db0e950d15 successfully published
```

***

</br>

## Clear - Cut

</br>

***

### Index Study via Maestro

***Through CLI***

Now that the study is published, its data must be indexed into Elasticsearch so it can be consumed by Arranger and the DMS UI. Indexing is done via the Maestro service.

Index Study Using cURL
To create your study via cURL:

Open a command-line terminal session.

Enter the following command:

```bash
curl -X POST "http://<url>/maestro/index/repository/<repositoryCode>/study/<studyId>" -H  "accept: */*" -d ""
```

For `repositoryCode`, this must be set to `song.overture`.

***Through Swagger UI***

https://<myDomain>/maestroy/api-doc

Under management-controller, click the POST /index/repository/{repositoryCode}/study/{studyId} endpoint.

Click Try it out.

In studyId, enter ABC123, the study you created earlier.
In repositoryCode, you must enter `song.overture.`
Click Execute.

If successful, either the cURL command or the Swagger UI will return a successful response indicating the study is created in Song:

[
  {
    "indexName": "file_centric_1",
    "failureData": {
      "failingIds": {}
    },
    "successful": true
  }
]

***

</br>

## Clear - Cut

</br>

***

### Verify the Data in the Portal

### Outro






