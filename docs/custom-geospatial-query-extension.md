## IBM Weather Operations Center extension for Custom Geospatial Queries </h1> 

IBM Weather Operations Center combines the power of geospatial analytics, alerts and dashboards into a single, modernized user experience. Release 1.0 empowers our customers with basic support for visualizing results of a geospatial query and share their findings operationally through dashboard interactive map.

__Action__: Contact your IBM representative to get your Weather Operations Center `API_KEY` and Geospatial Analytics user credentials. 

## Perform Data Exploration and Experimentation using Weather Operations Center - Geospatial Analytics Component

 Tutorials on how to construct and use geospatial queries are located here - https://pairs.res.ibm.com/tutorial/tutorials/api/index.html
 
 ### Identify Datasets
 
 Log into IBM Weather Operations Center, navigate to Geospatial Analytics and use Data Explorer to search for relevant datasets.
 
 ### Areas of Interest 
 
 Look for an address, draw a polygo or look for an area of interest (like "Austin Texas" or "New South Wales"). Select relevant layers, etc and perform experimental queries.
 
 ### Data Science Experiments
 
 Optionally, create a new project in a data science environment (e.g, IBM Watson Studio), import your business assets, paste in one (or more) of the experimental queries from prior step and do further experiments as necessary until you're comfortable with the final query referencing relevant layers, time intervals, etc. 

Proceed to Day 0 below once your geospatial query is finalized and you would like to operationalize it for viewing results within Dashboard Visualization component.

<b>Example geospatial query that is ready to be operationalized </b>: 

`` {\\\"spatial\\\":{\\\"type\\\":\\\"square\\\",\\\"aoi\\\":null,\\\"coordinates\\\":[38,-122,39,-121]},\\\"temporal\\\":{\\\"intervals\\\":[{\\\"start\\\":\\\"2020-08-01\\\",\\\"end\\\":\\\"2020-08-31\\\"}]},\\\"layers\\\":[{\\\"id\\\":\\\"51\\\",\\\"type\\\":\\\"raster\\\"}]}``


## Day 0: Register Analytics for visualization in Weather Operations Center - Dashboard Visualization Component 

### Generate JWT token
  
<b>Substitute your API_KEY and make the following request in a command-line window (PC) or a terminal (MAC) </b>: 

 `curl --request POST --url https://auth-b2b-twc.wsitrader.com/Auth/GetBearerForClient --header 'Content-Type: application/json' --header 'cache-control: no-cache' --data '{"apiKey":"<API_KEY>", "clientId":"ibm-agro-api"}'`

<b>Response</b>: 

  `{"access_token":"<JWT_TOKEN>","expires_in":xxx,"token_type":"Bearer","scope":"xxxxxxx"}`
 
<b>Action</b>:

  Copy the value corresponding to `<JWT_TOKEN>` from the repsonse above to use in the Import API call below.
 

### Registration Part # 1 - Platform metadata

<b>Substitute `JWT_TOKEN`, your `QUERY_PAYLOAD` and `ANALYTICS_NAME` below - and make the curl request in a command-line window (PC) or a terminal (MAC) </b>: 

`curl -X POST "https://foundation.agtech.ibm.com/v2/layer/analytics/metadata" -H "accept: application/json" -H "Authorization: Bearer <JWT_TOKEN>" -H "Content-Type: application/json; charset=UTF-8" -d "{\"pairsPayload\":\"<QUERY_PAYLOAD>\",\"analyticsName\":\"<ANALYTICS_NAME"}"`




<b>Example Response</b>:

`
[
  {
    "analyticsUuid": "1f2d5a9e-39c5-4ca4-bebc-14ac43646960",
    "layerId": "51",
    "baseComputationId": "1607533200_04490762"
  }
]`




### Registration Part # 2 - Visualization metadata


 Before making the sample request below, modify the <b>`id`</b> to be something unique, set the desired <b>`displayName`</b> and substitute the correct <b>`data Attribute uuid`</b> using the `analyticsUuid` value from the response above. Also, substitute JWT_TOKEN
 
 Everything else can be kept as is for an initial simplified scenario. Contact your IBM representative to discuss advanced geospatial analytics use-cases. 


`curl --location --request PUT 'https://api.wsitrader.com/api/v1/IMAP/put-layer-config-block' \
--header 'Authorization: Bearer <JWT_TOKEN>' \
--header 'Content-Type: application/json' \
--data-raw '{VIEWERSHIP_ROLE : 'ALL', CONFIG_BLOCK: {
    <b>id: 'customQuery-staging-test01'</b>,
    modelRegistryId: null,
    <b>displayName: 'Staging Test - Custom Query 01'</b>,
    provider: null,
    layerType: 'grid',
    isSelected: false,
    isActive: false,
    enableValidity: false,
    lastUpdatedUtc: null,
    coverageArea: 'Custom',
    dataAttributes: {
      url: 'https://foundation.agtech.ibm.com/v2',
      <b>uuid: '1f2d5a9e-39c5-4ca4-bebc-14ac43646960'</b>
    },
    menuIconUrl: null,
    legendUrl: '',
    styleProperties: {
      palette: {
        COLOR_STEPS: [
          { step: -1, rgba: [ 0, 0, 8, 255 ] },
          { step: 0, rgba: [ 11, 0, 251, 255 ] },
          { step: .2, rgba: [ 236, 0, 34, 255 ] },
          { step: .4, rgba: [ 250, 93, 7, 255 ] },
          { step: .6, rgba: [ 250, 249, 0, 255 ] },
          { step: .8, rgba: [ 0, 239, 0, 255 ] },
          { step: 1, rgba: [ 1, 49, 1, 255 ] }
        ]
      },
      unit: 'C',
      isInterpolated: true,
      extendMinimumColor: false,
      extendMaximumColor: true,
      invalidDataValue: -9999
    }
  }}'`


<b>Example response</b>: 

`Response: 200
Block Added.`






## Navigate to Weather Operations Center to verify the query results

1. Login to Weather Operations Center with your username and password by launching http://weatheroperationscenter.ibm.com - and navigate to Dashboard Visualization -> Interactive Map

![alt text](https://github.com/IBM/Weather-Operations-Center/blob/master/docs/resources/woc-maximo01.png?raw=true)


2. Click on Map Layers and Overlays:

![alt text](https://github.com/IBM/Weather-Operations-Center/blob/master/docs/resources/woc-maximo02.png?raw=true)


3. Select the Custom Layer under "Custom" tab in Map Layers and Overlays:

![alt text](https://github.com/IBM/Weather-Operations-Center/blob/master/docs/resources/woc-geospatial-04.png?raw=true)


4. Visualize and interact with the query results:

![alt text](https://github.com/IBM/Weather-Operations-Center/blob/master/docs/resources/woc-geospatial-05.png?raw=true)





## Day 1 and beyond


### For a subsequent query run, the query date range could change (for instance) to 2020-09-01 to 2020-09-30


<b>Call to</b>: 


`curl --location --request POST 'https://pairs.res.ibm.com/v2/query' \
--header 'Content-Type: application/json' \
--header 'Authorization: Basic KEY' \
--data-raw '{
    "spatial": {
        "type": "square",
        "coordinates": [
            38,-122,
            39,-121
        ]
    },
    "temporal": {
        "intervals": [
            {
                "start": "2020-09-01",
                "end": "2020-09-30"
            }
        ]
    },
    "layers": [
        {
            "id": "51",
            "type": "raster"
        } 
    ]
}'`




<b>Example Response</b>:


`{
    "id": "1607533200_04577287"
}
`



### Merge the new job with original baseComputationId from Day 0 - Registration Part # 1 above


<b>Merge Jobs</b>:

`
curl --location --request PUT 'https://pairs.res.ibm.com/v2/queryjobs/1607533200_04490762/merge/1607533200_04577287' \
--header 'Authorization: Basic KEY'`



As a consequence of the above operation, the output in Dashboard Visualization component would reflect computation from Sept 2020 (Day 1) rather than the original one from Aug 2020 (Day 0).


Navigate to Weather Operations Center and verify that the query results reflect computational change due to temporal movement from Day 0 to Day 1.





