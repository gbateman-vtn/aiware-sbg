# aiware-sbg



<h1>Veritone API Flow </h1>

1. 'createTDOWithAsset' - Create TDO
2. `createJob` - Create a cognition job.
3. `checkJobStatus` - Check the status of a job.
4. `getAssets` - Retrieve output of a completed job.

<h2>Create TDO with Asset</h2>

[GraphQL Schema Definition](https://api.veritone.com/v3/graphqldocs/createtdowithasset.doc.html)

```
mutation {
 createTDOWithAsset(input: {
   startDateTime: 1602797281,
   updateStopDateTimeFromAsset: true
   assetType:"media"
   contentType: "video/mp4"
   uri:"<FILE_URL>"
 }){
   id
 }
}
```

# Automate Studio Workflow Job
Uses Automate Studio Flow deployed as an engine
**Sinclair Cognitive Profile Flow 1**  engineId: "f0f112fe-935f-4d6e-82f9-effd55e867ea" that runs 5 cognitive engines with 1 API call.

```
mutation createSingleEngineJob {
  launchSingleEngineJob(
    input: {      
      targetId: "TDO_ID(from CreateTDOWithASset)"
      engineId: "f0f112fe-935f-4d6e-82f9-effd55e867ea",
      #Speechmatics, eContext Content Classification, eContext Entity Extraction, Visua Logo Recognition, Google OCR
     
    }
  ) {
    # targetId
    id
  }
}

```

# Checking job status

You can poll for job status updates or use webhook notifications. (detailed below)

```
query queryJobStatus {
  job(id:"JOB_ID") {
    id
    status
    targetId
    tasks{
      records{
        id
        status
        engine{
          id
          name
        }
        # taskOutput - include this field for a more verbose output
      }
    }}}
```
# Retrieving TDO Assets

Include the TDO ID and all assets will be returned including media link and engine results.

```
query getAssets {
  temporalDataObject(id: "TDO_ID") {
    primaryAsset(assetType: "media") {
      id
      signedUri
    }
    assets {
      records {
        sourceData {
          engine {
            id
            name
          }
        }        
        id
        createdDateTime
        assetType
        signedUri
      }
    }
  }
}
}}}
```



  
  
