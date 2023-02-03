# DMS Data Upload Cheatsheet

## Downloading and Configuring the Song Client

1. Download, unzip and move to `song-client` directory

```bash
wget -O song-client.tar.gz https://artifacts.oicr.on.ca/artifactory/dcc-release/bio/overture/song-client/[RELEASE]/song-client-[RELEASE]-dist.tar.gz
```
```bash
tar xvzf song-client.tar.gz
```

```bash
cd song-client-5.0.2
```

2. Download, unzip and move to `score-client` directory


```bash
wget -O score-client.tar.gz https://artifacts.oicr.on.ca/artifactory/dcc-release/bio/overture/score-client/[RELEASE]/score-client-[RELEASE]-dist.tar.gz
```

```bash
tar xvzf score-client.tar.gz
```

```bash 
cd score-client-<latest-release-number>
```

## Creating a study

```bash
curl -X POST "https://dmstutorial.cancercollaboratory.org/song-api/studies/mystudy-123/" -H  "accept: */*" -H  "Authorization: bearer <API KEY>" -H  "Content-Type: application/json" -d "{  \"description\": \"string\",  \"info\": {  },  \"name\": \"string\",  \"organization\": \"string\",  \"studyId\": \"mystudy-123\"}"
```

## Submitting an analysis

```bash
song-client-5.0.2/bin/sing submit -f mystudyID-456/analyses/exampleVariantCall.json
```
## Create the manifest

```bash
song-client-5.0.2/bin/sing manifest -a <analysisID> -f mystudyID-456/manifest/manifest.txt  -d mystudyID-456/input-files/
```

## Upload to Score

```bash
score-client-5.8.1/bin/score-client upload --manifest mystudyID-456/manifest/manifest.txt
```

## Publish Analysis

```bash
song-client-5.0.2/bin/sing publish -a fa76e6f9-b6ec-468c-b6e6-f9b6ecc68c08
```

## Index Study
  
```bash
curl -X POST "https://dmstutorial.cancercollaboratory.org/maestro/index/repository/song.overture/study/mystudyID-456" -H "accept: */*" -d ""
```
