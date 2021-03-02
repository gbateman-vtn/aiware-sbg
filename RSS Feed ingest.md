#Setting up RSS Source

1. Log into Veritone and Data Center ap for setting up Source
2. Click New
3. Source
	a. Give Name
	b. Select Podcast as source type
6. Save


#Create a scheduled Job

This needs to be done in GQL as Data Center does not have the ability to set up advanced scheduled jobs

1. Create a template for your RSS feed createJobTemplateForRSSToS3
2. Create Schedule Job using the template createScheduledJob

#createJobTemplateForRSSToS3
```
  mutation createJobTemplateForRSSToS3 {
  createJobTemplate(input:{
      clusterId:"rt-1cdc1d6d-a500-467a-bc46-d3c5bf3d6901"
      applicationId: "499251a8-000a-4af2-93a5-f70e08da4f30"
      jobConfig: {
         createTDOOnLaunch: false
         sourceData: {
            sourceId: 69867
         }
      }
      taskTemplates: [
       {
         # podcast
         engineId: "a725de38-f538-4c4a-b835-b0bd4ab1a255"
         payload: {
            podcastMode: "scan"
            sourceId: "69867"
         }
        ioFolders: [
          {
            referenceId: "adapterOutputFolder"
            mode: stream
            type: output
          }
        ]
      }
      {
         # Store to S3 media + sidecar
          engineId: "175fe60a-a30b-477a-a1ea-74c727ae6ab0"
          payload: {
            s3bucket: "s3-scmp-rssvideo-or-1"
            s3keyPrefix: "aiw/69867"
            s3accessKey: "QUtJQVFRMlpFN1pEVE5MRjVSUEc="
            s3accessSecret:"SUUydXVCRWZ2S0V5Mm5oWk9JK3U3bG1KZDNLQ1FDNUhNSTBYQ0E2Zg=="
            s3region: "us-west-2"
          }
          ioFolders: [
          {
            referenceId: "s3InputFolderForStoringMediaAndSidecar"
            mode: stream
            type: input
          }
        ]
      }]
    ##Routes : A route connect a parent output folder to a child input folder
    routes: [
      {  ## Adapter--> Store-S3
        parentIoFolderReferenceId: "adapterOutputFolder"
        childIoFolderReferenceId: "s3InputFolderForStoringMediaAndSidecar"
        options: {}
      }
    ]
  }){
    id
 }
}
```

key / secret key require base64 encoding:

In mac that can be done in terminal:echo -n 'superman:superpower' | base64
```
{
  "data": {
    "createJobTemplate": {
      "id": "20093922_EMvz8V9ENT"
    }
  }
}
```

#Create Schedule Job with Template

createScheduledJob
```
mutation createScheduledJob {
  createScheduledJob(input: {
    name: "RSS-ScheduledJobTest", 
    runMode: Recurring, 
    startDateTime:1600568607 
    organizationId: 17154
    recurringScheduleParts: [
      {
        repeatInterval: 1
        repeatIntervalUnit: Hours
       }
    ]
   jobTemplateIds:[
        "20093922_EMvz8V9ENT"
    ],
   }) 
  {
    id
  }
}
```
```
mutation createScheduledJob {
  createScheduledJob(input: {
    name: "RSS-ScheduledJobTest", 
    runMode: Recurring, 
    startDateTime:1600568607 
    organizationId: 17154
    recurringScheduleParts: [
      {
        repeatInterval: 1
        repeatIntervalUnit: Days
        startTime: "21:30"
       }
    ]
   jobTemplateIds:[
        "20094029_5875Kbn2l1"
    ],
   }) 
  {
    id
  }
}
```
```
{
  "data": {
    "createScheduledJob": {
      "id": "105873"
    }
  }
}
```
Daily
```
mutation createScheduledJob1 {
  createScheduledJob(input: {
    name: "RSS-ScheduledJobTest", 
    runMode: Recurring, 
    startDateTime:1604188800
    organizationId: 26954
      weeklyScheduleParts: [
        {
          scheduledDay: Sunday
          startTime: "04:00"
          stopTime: "05:00"
        }
        {
          scheduledDay: Monday
         startTime: "04:00"
          stopTime: "05:00"
        }
        {
          scheduledDay: Tuesday
          startTime: "04:00"
          stopTime: "05:00"
        }
        {
          scheduledDay: Wednesday
          startTime: "04:00"
          stopTime: "05:00"
        }
        {
          scheduledDay: Thursday
          startTime: "04:00"
          stopTime: "05:00"
        }
        {
          scheduledDay: Friday
          startTime: "04:00"
          stopTime: "05:00"
        }
        {
          scheduledDay: Saturday
          startTime: "04:00"
          stopTime: "05:00"
        }
      ]
    jobTemplateIds:[
        "20114612_yNldWDWp4G"
    ],
    }
  ) {
    id
    isActive
  }
}
```
Example of how a single asset looks in AWS

Using the above example you will get three files for each asset it finds in the feed

-media.mp4

-sidecar.json

-transcription.json

They always arrive in this order

https://s3-vtn-rssfeed-or-1.s3-us-west-2.amazonaws.com/s3/aiw-69492/org_17154_tdo_1200253347_job_20093922_5zcLb7A5Z8_task_20093922_5zcLb7A5Z8JGLXQ-media.mp4

https://s3-vtn-rssfeed-or-1.s3-us-west-2.amazonaws.com/s3/aiw-69492/org_17154_tdo_1200253347_job_20093922_5zcLb7A5Z8_task_20093922_5zcLb7A5Z8JGLXQ-sidecar.json

https://s3-vtn-rssfeed-or-1.s3-us-west-2.amazonaws.com/s3/aiw-69492/org_17154_tdo_1200253347_job_20093922_5zcLb7A5Z8_task_20093922_5zcLb7A5Z81JJVL-transcription.json

#Sidecar Example
```
{
	"url": "https://downloadmedia.gannett-cdn.com/authoring/video-renditions/f936ab1b-552e-4b3e-88f8-bd6576c4dade/9b04f4fb-4e0c-47b5-bbbf-dfd95efae47d/1080p_30fps.mp4",
	"contentType": "video/mp4",
	"contentLength": 24162563,
	"contentHash": "md5:8e41fe0def9450daf726091eef5eb4fe",
	"feedURL": "https://content-syndication-api.gannettdigital.com/content-syndication-api/v1/assets/search?apiKey=iGSpmehH7LgcqqqrLVcxOrgGmWPxHb1N",
	"jobId": "20093922_5zcLb7A5Z8",
	"taskId": "20093922_5zcLb7A5Z8JGLXQ",
	"recordingId": "1200253347",
	"metadata": "\u003citem\u003e\n            \u003cguid isPermaLink=\"false\"\u003e5851443002\u003c/guid\u003e\n            \u003ctitle\u003eChristina and Ant Anstead separate just a year after welcoming baby boy\u003c/title\u003e\n            \u003cpubDate\u003eMon, 21 Sep 2020 13:28:54 UTC\u003c/pubDate\u003e\n            \u003clink\u003ehttps://www.usatoday.com/videos/entertainment/celebrities/2020/09/21/christina-and-ant-anstead-make-difficult-decision-separate/5851443002/\u003c/link\u003e\n            \u003cdc:alternative\u003eChristina and Ant Anstead separate\u003c/dc:alternative\u003e\n            \u003cdc:abstract\u003eThe couple welcomed a baby boy, Hudson, in September 2019.\u003c/dc:abstract\u003e\n            \u003cdc:publisher\u003eUSA TODAY\u003c/dc:publisher\u003e\n            \u003cdc:creator\u003eUSA TODAY\u003c/dc:creator\u003e\n            \u003cdc:modified\u003eMon, 21 Sep 2020 13:28:54 UTC\u003c/dc:modified\u003e\n            \u003cmedia:category\u003eentertainment,celebrities\u003c/media:category\u003e\n            \u003cmedia:keywords\u003eCelebrities,Video Syndication - USAT\u003c/media:keywords\u003e\n                \u003c!--VIDEO--\u003e\n                \u003cdescription\u003eThe couple welcomed a baby boy, Hudson, in September 2019.\u003c/description\u003e\n                \u003cmedia:content bitrate=\"4044000\"\n                               audio-only=\"false\"\n                               display-name=\"\"\n                               height=\"1080\"\n                               width=\"873\"\n                               fileSize=\"24162563\"\n                               codec=\"H264\"\n                               container=\"VIDEO/MP4\"\n                               duration=\"46.000\"\n                               medium=\"video\"\n                               url=\"https://downloadmedia.gannett-cdn.com/authoring/video-renditions/f936ab1b-552e-4b3e-88f8-bd6576c4dade/9b04f4fb-4e0c-47b5-bbbf-dfd95efae47d/1080p_30fps.mp4\"\u003e\n                    \u003cmedia:thumbnail url=\"https://www.gannett-cdn.com/presto/2020/09/21/USAT/ad3c9bea-b33b-43ce-93fb-605725864b0e-VPC_ANSTEAD_SPLIT_DESK_THUMB.00_00_00_00.Still001.jpg\" type=\"image/jpeg\" width=\"120\" height=\"90\"/\u003e  \u003c!--thumbnail of video --\u003e\n                    \u003cmedia:thumbnail url=\"https://www.gannett-cdn.com/presto/2020/09/21/USAT/ad3c9bea-b33b-43ce-93fb-605725864b0e-VPC_ANSTEAD_SPLIT_DESK_THUMB.00_00_00_00.Still001.jpg\" type=\"image/jpeg\" width=\"480\" height=\"360\"/\u003e\n                    \u003cmedia:copyright\u003eUSA TODAY\u003c/media:copyright\u003e\n                    \u003cmedia:title\u003eChristina and Ant Anstead separate just a year after welcoming baby boy\u003c/media:title\u003e\n                    \u003cmedia:description\u003eThe couple welcomed a baby boy, Hudson, in September 2019.\u003c/media:description\u003e\n                    \u003cdc:identifier\u003e5851443002\u003c/dc:identifier\u003e\n                \u003c/media:content\u003e\n                \u003cmedia:group\u003e\n                    \n                    \u003cmedia:content bitrate=\"4044000\"\n                                   audio-only=\"false\"\n                                   display-name=\"\"\n                                   height=\"1080\"\n                                   width=\"873\"\n                                   fileSize=\"24162563\"\n                                   codec=\"H264\"\n                                   container=\"VIDEO/MP4\"\n                                   duration=\"46.000\"\n                                   medium=\"video\"\n                                   url=\"https://downloadmedia.gannett-cdn.com/authoring/video-renditions/f936ab1b-552e-4b3e-88f8-bd6576c4dade/9b04f4fb-4e0c-47b5-bbbf-dfd95efae47d/1080p_30fps.mp4\"\u003e\n                        \u003cmedia:thumbnail url=\"https://www.gannett-cdn.com/presto/2020/09/21/USAT/ad3c9bea-b33b-43ce-93fb-605725864b0e-VPC_ANSTEAD_SPLIT_DESK_THUMB.00_00_00_00.Still001.jpg\" type=\"image/jpeg\" width=\"120\" height=\"90\"/\u003e  \u003c!--thumbnail of video --\u003e\n                        \u003cmedia:thumbnail url=\"https://www.gannett-cdn.com/presto/2020/09/21/USAT/ad3c9bea-b33b-43ce-93fb-605725864b0e-VPC_ANSTEAD_SPLIT_DESK_THUMB.00_00_00_00.Still001.jpg\" type=\"image/jpeg\" width=\"480\" height=\"360\"/\u003e\n                        \u003cmedia:copyright\u003eUSA TODAY\u003c/media:copyright\u003e\n                        \u003cmedia:title\u003eChristina and Ant Anstead separate just a year after welcoming baby boy\u003c/media:title\u003e\n                        \u003cmedia:description\u003eThe couple welcomed a baby boy, Hudson, in September 2019.\u003c/media:description\u003e\n                    \u003c/media:content\u003e\n                    \u003cmedia:content bitrate=\"2544000\"\n                                   audio-only=\"false\"\n                                   display-name=\"\"\n                                   height=\"720\"\n                                   width=\"582\"\n                                   fileSize=\"15785309\"\n                                   codec=\"H264\"\n                                   container=\"VIDEO/MP4\"\n                                   duration=\"46.000\"\n                                   medium=\"video\"\n                                   url=\"https://downloadmedia.gannett-cdn.com/authoring/video-renditions/f936ab1b-552e-4b3e-88f8-bd6576c4dade/9b04f4fb-4e0c-47b5-bbbf-dfd95efae47d/720p_30fps.mp4\"\u003e\n                        \u003cmedia:thumbnail url=\"https://www.gannett-cdn.com/presto/2020/09/21/USAT/ad3c9bea-b33b-43ce-93fb-605725864b0e-VPC_ANSTEAD_SPLIT_DESK_THUMB.00_00_00_00.Still001.jpg\" type=\"image/jpeg\" width=\"120\" height=\"90\"/\u003e  \u003c!--thumbnail of video --\u003e\n                        \u003cmedia:thumbnail url=\"https://www.gannett-cdn.com/presto/2020/09/21/USAT/ad3c9bea-b33b-43ce-93fb-605725864b0e-VPC_ANSTEAD_SPLIT_DESK_THUMB.00_00_00_00.Still001.jpg\" type=\"image/jpeg\" width=\"480\" height=\"360\"/\u003e\n                        \u003cmedia:copyright\u003eUSA TODAY\u003c/media:copyright\u003e\n                        \u003cmedia:title\u003eChristina and Ant Anstead separate just a year after welcoming baby boy\u003c/media:title\u003e\n                        \u003cmedia:description\u003eThe couple welcomed a baby boy, Hudson, in September 2019.\u003c/media:description\u003e\n                    \u003c/media:content\u003e\n                    \u003cmedia:content bitrate=\"800000\"\n                                   audio-only=\"false\"\n                                   display-name=\"\"\n                                   height=\"480\"\n                                   width=\"388\"\n                                   fileSize=\"5191036\"\n                                   codec=\"H264\"\n                                   container=\"VIDEO/MP4\"\n                                   duration=\"46.000\"\n                                   medium=\"video\"\n                                   url=\"https://downloadmedia.gannett-cdn.com/authoring/video-renditions/f936ab1b-552e-4b3e-88f8-bd6576c4dade/9b04f4fb-4e0c-47b5-bbbf-dfd95efae47d/480p_30fps.mp4\"\u003e\n                        \u003cmedia:thumbnail url=\"https://www.gannett-cdn.com/presto/2020/09/21/USAT/ad3c9bea-b33b-43ce-93fb-605725864b0e-VPC_ANSTEAD_SPLIT_DESK_THUMB.00_00_00_00.Still001.jpg\" type=\"image/jpeg\" width=\"120\" height=\"90\"/\u003e  \u003c!--thumbnail of video --\u003e\n                        \u003cmedia:thumbnail url=\"https://www.gannett-cdn.com/presto/2020/09/21/USAT/ad3c9bea-b33b-43ce-93fb-605725864b0e-VPC_ANSTEAD_SPLIT_DESK_THUMB.00_00_00_00.Still001.jpg\" type=\"image/jpeg\" width=\"480\" height=\"360\"/\u003e\n                        \u003cmedia:copyright\u003eUSA TODAY\u003c/media:copyright\u003e\n                        \u003cmedia:title\u003eChristina and Ant Anstead separate just a year after welcoming baby boy\u003c/media:title\u003e\n                        \u003cmedia:description\u003eThe couple welcomed a baby boy, Hudson, in September 2019.\u003c/media:description\u003e\n                    \u003c/media:content\u003e\n                \u003c/media:group\u003e\n                \u003cmedia:thumbnail height=\"90\" url=\"https://www.gannett-cdn.com/presto/2020/09/21/USAT/ad3c9bea-b33b-43ce-93fb-605725864b0e-VPC_ANSTEAD_SPLIT_DESK_THUMB.00_00_00_00.Still001.jpg\" width=\"120\"/\u003e\n                \u003cmedia:thumbnail height=\"360\" url=\"https://www.gannett-cdn.com/presto/2020/09/21/USAT/ad3c9bea-b33b-43ce-93fb-605725864b0e-VPC_ANSTEAD_SPLIT_DESK_THUMB.00_00_00_00.Still001.jpg\" width=\"480\"/\u003e\n                \u003cbc:titleid\u003e\u003c/bc:titleid\u003e\n                \u003cbc:accountid\u003e\u003c/bc:accountid\u003e\n                \u003c!--END VIDEO--\u003e\n        \u003c/item\u003e"
}
```
