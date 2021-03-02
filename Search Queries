API call to search metadata from any category
	https://docs.veritone.com/#/apis/search-quickstart/
  

#File Data 
'''
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

...
