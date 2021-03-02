# aiware-sbg

<h1>WORKFLOW</h1>

<h2>Ingest via RSS</h2>

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
  
  
