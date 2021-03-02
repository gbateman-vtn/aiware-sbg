# aiware-sbg



<h1>Veritone API Flow </h1>

1. 'createTDOWithAsset' - Create TDO
2. `createJob` - Create a cognition job.
3. `checkJobStatus` - Check the status of a job.
4. `retrieveEngineOutput` - Retrieve the output of a completed job.

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


  
  
