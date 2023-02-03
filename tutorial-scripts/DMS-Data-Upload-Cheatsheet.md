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
./bin/sing submit -f example-payload.json
```
## Submiting the manifest

```bash
./bin/sing manifest -a a4142a01-1274-45b4-942a-01127465b422 -f /some/output/dir/manifest.txt  -d /submitting/file/directory
```


