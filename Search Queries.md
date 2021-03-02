API call to search metadata from any category
	https://docs.veritone.com/#/apis/search-quickstart/
	
<h1>SEARCH EXAMPLES</h1>
https://docs.veritone.com/#/apis/search-quickstart/?id=sample-requests-and-responses
	
 
<h1>File Data</h1>

```
query getFileInfo{
  temporalDataObject(id:"1430371551") {
    folders {
      id
    }
    startDateTime
    id
    stopDateTime
    createdDateTime
    primaryAsset (assetType: "media") {
      id
      contentType
      signedUri
fileData{
  mediaDurationMs
}
    }
   
    jobs {
      count
      records {
        id
    
        }
      }
    }
  }

```
<h1>Logo Search</h1>
```
query{
  searchMedia(search:{
    offset: 0
    limit: 10
    index: ["mine"]
    query:{
        operator: "or"
        conditions: [{
            operator: "term"
            field: "logo-recognition.series.found"
            value: "New Balance"
            }]
    }
  }) {
    jsondata
  }
}
'''
