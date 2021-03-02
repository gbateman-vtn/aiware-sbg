API call to search metadata from any category
	https://docs.veritone.com/#/apis/search-quickstart/
	
	https://docs.veritone.com/#/apis/search-quickstart/?id=sample-requests-and-responses
	
 
#File Data 

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
#Logo Search
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
