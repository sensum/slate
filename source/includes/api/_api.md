
# Emotion AI API

## Introduction
The Sensum Emotion AI API enables you to access our emotional intelligence platform.  Our API is designed to be RESTful, responding to HTTP requests with bodies in JSON format. All requests require that the `Content-Type: application/json` header be specified.
The API is also cross-origin resource sharing ready.
The Emotion AI SDK handles many of these requests and responses natively. It can however be useful to utilise the API directly.


> Scroll down for code samples, example requests and responses. Select a language for code samples from the tabs above or the mobile navigation menu.

## URI Structure

The Sensum Emotion AI API uses URI resources to provide access to its services. To use a RESTful API, your application will use HTTP Methods(GET, POST, etc.) to request and parse a response. The Emotion AI API uses JSON for communication between your application and the server.

An example URI:
<a href="">https://api.sensum.co/v0/testdata</a>

## Authorization

Sensum Emotion AI uses a combination of an API Key and AWS Signature v4 signing to authorize access to the API. You can register a new API Key by contacting us.

Sensum Emotion AI expects each call to contain the following headers to gain access: 

 * Content-Type: `application/json`
 * Authorization: `$AWSv4Signature`
 * X-API-Key: `$YourAPIKey(For trial usage use "PublicDemoKeyForDocumentation")`
 
To calculate the value for the Authorization header you must calculate a hash of your request, add extra information, then add the AWS secret key in order to create a signing key and then use this to sign the request.
To learn more about generating the Signature please read the <a href="https://docs.aws.amazon.com/general/latest/gr/signature-v4-examples.html">AWS Documentation on Signature v4</a>

When using the SDK, the signature will be automatically generated when making API calls through it.

<aside class="notice">Replace the above authorization header with the AWS Signature generated after login and X-API-Key with your personal API key.</aside>

## Available Metrics

Below are the metrics that the Emotion AI API can analyse and the units that the data is posted in

|Metric Name|Unit|
|-----------|----|
|heartrate|bpm |
|breathingrate|bpm|
|temperature|<sup>o</sup>C, assumed to be ambient/external|
|skintemperature|<sup>o</sup>C|
|gsr| Siemens<sup>*</sup>|
|location_latitude|deg|
|location_longitude|deg|
|location_altitude|m|
|location_accuracy|or location_accuracy_h/v if available|
|location_speed|m/s|
|acceleration|linear acceleration in m/s<sup>2**</sup>|
|acceleration_x|m/s<sup>2</sup>|
|acceleration_y|m/s<sup>2</sup>|
|acceleration_z|m/s<sup>2</sup>|

<sup>*</sup> The GSR Conductance unit "Siemens" is the inverse of the skin resistance; some devices return GSR as resistance in Ohms and this must be converted before upload, i.e. if a device returns values in x kOhms, the conversion is 1/(1000*x)

<sup>**</sup> All acceleration values should exclude gravity and be in m/s<sup>2</sup> i.e. using the userAcceleration iOS method rather than the acceleration method

## Send text data to analyse emoji and text sentiment  

This endpoint allows users to send strings of text to our service for emotional sentiment analysis. 

The service will return a JSON object that contain Positivity, Negativity and Emotionality values for emojis and text.



### HTTP Request

`POST https://api.sensum.co/v0/sentiment`

<aside class="warning">
To perform this operation, you must be authenticated by means of the following headers:
API Key, Authorization.
</aside>

###Glossary

|Term|Description|
|----|-----------|
|Positivity| The level of positive emotion expressed in an input(Scale: 0 to +1)|
|Negativity| The level of negative emotion expressed in an input(Scale: 0 to +1)|
|Emotionality| The overall strength of emotion contained in an input(Scale: -1 to +1)*|

* Values greater than 0 imply positive feelings, values less than 0 imply negative feelings while 0 implies no emotional response. 


> Code samples

```javascript
var headers = {
  'Content-Type':'application/json',
  'Authorization' : 'AWS Sig v4 Key',
  'x-api-key' : 'PublicDemoKeyForDocumentation'

};

var data = {
  "text":"👌👌👌"
};

$.ajax({
  url: 'https://api.sensum.co/v0/sentiment',
  method: 'post',
  data: JSON.stringify(data);
  headers: headers,
  success: function(data) {
    console.log(JSON.stringify(data));
  }
})
```

```javascript--nodejs
const request = require('node-fetch');

const headers = {
  'Content-Type':'application/json',
  'Authorization' : 'AWS Sig v4 Key',
  'x-api-key' : 'PublicDemoKeyForDocumentation'

};

var data = {
  "text":"👌👌👌"
};


fetch('https://api.sensum.co/v0/sentiment',
{
  method: 'POST',
  body : body, 
  headers: headers
})
.then(function(res) {
    return res.json();
}).then(function(body) {
    console.log(body);
});
```


```python
import requests
headers = {
  'Content-Type':'application/json',
  'Authorization' : 'AWS Sig v4 Key',
  'x-api-key' : 'PublicDemoKeyForDocumentation'
}


data = {
  "text":"👌👌👌"
}

r = requests.post('https://api.sensum.co/v0/sentiment', data=data, headers = headers)

print r.json()
```


### Responses

Status|Meaning|Description
---|---|---|
200|[OK](https://tools.ietf.org/html/rfc7231#section-6.3.1)|200 response

### Examples

Please refer to the code samples for request and response examples 

> Text Only - Unemotional

> Request

```json
{
  "text": "This is nothing"
}
```

> Response

```json
{
  "emoji_sentiment": null,
  "text_sentiment": {
    "compound": 0.0, //Aggregate Emotionality Score from -1,+1
    "neg": 0.0, // Negativity Score from 0 to +1
    "neu": 1.0, // Neutrality Score from 0 to +1
    "pos": 0.0  // Positivity Score from 0 to +1
   }
}
```
> Text Only - Emotional

> Request

```json
{
  "text": "This was a very good test"
}
```
> Response

```json
{
  "emoji_sentiment": null,
  "text_sentiment": {
    "positivity": 0.444,
    "negativity": 0.0,
    "emotionality": 0.4927
  }
}
```
> Emoji

> Request

```json
{
  "text":"👌👌👌"
}
```

> Response

```json
{
  "text_sentiment": {
    "negativity": 0.0,
    "positivity": 0.0,
    "emotionality": 0.0
  },
  "emoji_sentiment": {
    "positivity": 0.6575529733,
    "negativity": 0.0936431989,
    "emotionality": 0.4236068641
  }
}
```

## Retrieve previously recorded data

This endpoint allows the user to retrieve previously entered data by providing a start time, an end time and the metrics to be retrieved.

### HTTP Request
`GET https://api.sensum.co/v0/data/`

> Code samples

```javascript
var headers = {
  'Content-Type':'application/json',
  'Authorization' : 'AWS Sig v4 Key',
  'x-api-key' : 'PublicDemoKeyForDocumentation'
};

var params = {
	'start' : '2017-08-15',
	'end' : '2017-08-25',
	'metrics' : ['heartrate','breathingrate']
};

$.ajax({
  url: 'https://api.sensum.co/v0/data/',
  method: 'get',
  data : params, 
  headers: headers,
  success: function(data) {
    console.log(JSON.stringify(data));
  }
})
```

```javascript--nodejs
const request = require('node-fetch');

const headers = {
  'Content-Type':'application/json',
  'Authorization' : 'AWS Sig v4 Key',
  'x-api-key' : 'PublicDemoKeyForDocumentation'

};

fetch('https://api.sensum.co/v0/data?start=2017-08-15&end=2017-08-25&metrics=['heartrate','breathingrate']',
{
  method: 'get',

  headers: headers
})
.then(function(res) {
    return res.json();
}).then(function(body) {
    console.log(body);
});
```

```python
import requests
headers = {
  'Content-Type':'application/json',
  'Authorization' : 'AWS Sig v4 Key',
  'x-api-key' : 'PublicDemoKeyForDocumentation'
}

params = {
	'start' : '2017-08-15',
	'end' : '2017-08-25',
	'metrics' : ['heartrate','breathingrate']
}


r = requests.get('https://api.sensum.co/v0/data/', params = params, headers = headers)

print r.json()
```

### Parameters

Parameter|Type|Required|Description
---|---|---|---|
start|string|true|<a href = http://www.cl.cam.ac.uk/~mgk25/iso-time.html> Datetime compatible</a> start time for query
end|string|true|End time for query
metrics|string|true|List of strings of requested metrics

### Responses

Status|Meaning|Description
---|---|---|
200|[OK](https://tools.ietf.org/html/rfc7231#section-6.3.1)|Successful request

<aside class="warning">
To perform this operation, you must be authenticated by means of the following headers:
X-API-Key, Authorization
</aside>

## Retrieve available records

This endpoint allows the user to retrieve a list of available records based on the supplied query information.

### HTTP Request
`GET https://api.sensum.co/data/records.json`

> Code samples

```javascript
var headers = {
  'Content-Type':'application/json',
  'Authorization' : 'AWS Sig v4 Key',
  'x-api-key' : 'PublicDemoKeyForDocumentation'
};

var params = {
	'start' : '2017-08-15',
	'end' : '2017-08-25',
	'metrics' : ['heartrate','breathingrate']
};

$.ajax({
  url: 'https://api.sensum.co/v0/data/records.json',
  method: 'get',
  data: 'params',
  headers: headers,
  success: function(data) {
    console.log(JSON.stringify(data));
  }
})
```

```javascript--nodejs
const request = require('node-fetch');

const headers = {
  'Content-Type':'application/json',
  'Authorization' : 'AWS Sig v4 Key',
  'x-api-key' : 'PublicDemoKeyForDocumentation'
};

fetch('https://api.sensum.co/v0/data/records.json?start=2017-08-15&end=2017-08-25&metrics=['heartrate','breathingrate']',
{
  method: 'get',

  headers: headers
})
.then(function(res) {
    return res.json();
}).then(function(body) {
    console.log(body);
});
```

```python
import requests
headers = {
  'Content-Type':'application/json',
  'Authorization' : 'AWS Sig v4 Key',
  'x-api-key' : 'PublicDemoKeyForDocumentation'
}


params = {
	'start' : '2017-08-15',
	'end' : '2017-08-25',
	'metrics' : ['heartrate','breathingrate']
}


r = requests.get('https://api.sensum.co/v0/data/records.json', params = params, headers = headers)

print r.json()
```

### Parameters

Parameter|Type|Required|Description
---|---|---|---|
start|string|true|Datetime compatible start time for query
end|string|true|End time for query
metrics|string|true|List of strings of requested metrics

### Responses

Status|Meaning|Description
---|---|---|
200|[OK](https://tools.ietf.org/html/rfc7231#section-6.3.1)|Successful request

 > Example Response

```json
{
  "data": {
    "acceleration_x": [
      {"time": 1501761816636,"value": 0.00222260966538161},
      {"time": 1501761817632, "value": -0.0132070300688734},
      {"time": 1501761818631, "value": -0.0390399978164584},
      {"time": 1501761819632, "value": -0.0268580912779318},
      ...
      ],
   "acceleration_y": [...],
  },
  "metrics":
    [
      "acceleration_x","acceleration_y", "acceleration_z", 
      "location_altitude", "location_horizontalaccuracy", "location_latitude", 
      "location_longitude", "location_speed", "location_verticalaccuracy"]
  }
```
<aside class="warning">
To perform this operation, you must be authenticated by means of the following headers:
X-API-Key, Authorization
</aside>

## Retrieve available metrics

This endpoint allows the user to retrieve a list of available metrics in the requested records.

### HTTP Request
`GET https://api.sensum.co/v0/data/metrics.json`

> Code samples

```javascript
var headers = {
  'Content-Type':'application/json',
  'Authorization' : 'AWS Sig v4 Key',
  'x-api-key' : 'PublicDemoKeyForDocumentation'
};

var params = {
	'start' : '2017-08-15',
	'end' : '2017-08-25',
	'metrics' : ['heartrate','breathingrate']
};

$.ajax({
  url: 'https://api.sensum.co/v0/data/metrics.json',
  method: 'get',
  data: params,
  headers: headers,
  success: function(data) {
    console.log(JSON.stringify(data));
  }
})
```

```javascript--nodejs
const request = require('node-fetch');

const headers = {
  'Content-Type':'application/json',
  'Authorization' : 'AWS Sig v4 Key',
  'x-api-key' : 'PublicDemoKeyForDocumentation'

};

fetch('https://api.sensum.co/v0/data/metrics.json?start=2017-08-15&end=2017-08-25&metrics=['heartrate','breathingrate']',
{
  method: 'get',

  headers: headers
})
.then(function(res) {
    return res.json();
}).then(function(body) {
    console.log(body);
});
```

```python
import requests
headers = {
  'Content-Type':'application/json',
  'Authorization' : 'AWS Sig v4 Key',
  'x-api-key' : 'PublicDemoKeyForDocumentation'
}

params = {
	'start' : '2017-08-15',
	'end' : '2017-08-25',
	'metrics' : ['heartrate','breathingrate']
};


r = requests.get('https://api.sensum.co/v0/data/metrics.json', params= params, headers = headers)

print r.json()
```

### Parameters

Parameter|Type|Required|Description
---|---|---|---|
start|string|true|Datetime compatible start time for query
end|string|true|End time for query
metrics|string|true|List of strings of requested metrics

### Responses

Status|Meaning|Description
---|---|---|
200|[OK](https://tools.ietf.org/html/rfc7231#section-6.3.1)|Successful request

> Example Response

```json
{
    "metrics":[
      "heartrate",
      "breatingrate"
    ]
}
```
<aside class="warning">
To perform this operation, you must be authenticated by means of the following headers:
X-API-Key, Authorization
</aside>

## Retrieve available wide-format records

This endpoint allows the user to retrieve a wide-format array of time series records for the available metrics, filled with null values for unavailable values.

### HTTP Request
`GET https://api.sensum.co/v0/data/wide.json`

> Code samples


```javascript
var headers = {
  'Content-Type':'application/json',
  'Authorization' : 'AWS Sig v4 Key',
  'x-api-key' : 'PublicDemoKeyForDocumentation'

};

var params = {
	'start' : '2017-08-15',
	'end' : '2017-08-25',
	'metrics' : ['heartrate','breathingrate']
};


$.ajax({
  url: 'https://api.sensum.co/v0/data/wide.json',
  method: 'get',
  data: params,
  headers: headers,
  success: function(data) {
    console.log(JSON.stringify(data));
  }
})
```

```javascript--nodejs
const request = require('node-fetch');

const headers = {
   'Content-Type':'application/json',
  'Authorization' : 'AWS Sig v4 Key',
  'x-api-key' : 'PublicDemoKeyForDocumentation'

};

fetch('https://api.sensum.co/v0/data/wide.json?start=2017-08-15&end=2017-08-25&metrics=['heartrate','breathingrate']',
{
  method: 'get',

  headers: headers
})
.then(function(res) {
    return res.json();
}).then(function(body) {
    console.log(body);
});
```

```python
import requests
headers = {
  'Content-Type':'application/json',
  'Authorization' : 'AWS Sig v4 Key',
  'x-api-key' : 'PublicDemoKeyForDocumentation'
}

params = {
	'start' : '2017-08-15',
	'end' : '2017-08-25',
	'metrics' : ['heartrate','breathingrate']
};


r = requests.get('https://api.sensum.co/v0/data/wide.json', params = params, headers = headers)

print r.json()
```

### Parameters

Parameter|Type|Required|Description
---|---|---|---|
start|string|true|Datetime compatible start time for query
end|string|true|End time for query
metrics|string|true|List of strings of requested metrics

### Responses

Status|Meaning|Description
---|---|---|
200|[OK](https://tools.ietf.org/html/rfc7231#section-6.3.1)|Successful request

<aside class="warning">
To perform this operation, you must be authenticated by means of the following headers:
X-API-Key, Authorization
</aside>


## Send data for events analysis

This endpoint allows the user to send data to the Emotion AI service for analysis. The response will return a series of significant events.   

### HTTP Request 

`POST https://api.sensum.co/v0/events`

> Code samples

```javascript
var headers = {
  'Content-Type':'application/json',
  'Authorization': 'AWS_Sig v4 Key',
  'x-api-key': 'Public'

};

var data = {
    "records": {
      "heartrate": [
        {
          "time": 1502807187332,
          "value": 111.77347523527911
        },
        {
          "time": 1502807188332,
          "value": 112.89604978090439
        },
        {
          "time": 1502807189332,
          "value": 112.37719504311998
        },
        {
          "time": 1502807190332,
          "value": 113.68469103590627
        },
        {
          "time": 1502807191332,
          "value": 113.67799449012763
        },
        {
          "time": 1502807192332,
          "value": 112.71988545819869
        },
        {
          "time": 1502807193332,
          "value": 113.05775062793727
        },
        {
          "time": 1502807194332,
          "value": 114.53499763344529
        },
        {
          "time": 1502807195332,
          "value": 115.4964191594706
        },
        {
          "time": 1502807196332,
          "value": 115.31744641217797
        }
      ]
    }
  }; 

$.ajax({
  url: 'https://api.sensum.co/v0/events',
  method: 'post',
  data: JSON.stringify(data),
  headers: headers,
  success: function(data) {
    console.log(JSON.stringify(data));
  }
})
```

```javascript--nodejs
const request = require('node-fetch');
const inputBody = '{
    "records": {
      "heartrate": [
        {
          "time": 1502807187332,
          "value": 111.77347523527911
        },
        {
          "time": 1502807188332,
          "value": 112.89604978090439
        },
        {
          "time": 1502807189332,
          "value": 112.37719504311998
        },
        {
          "time": 1502807190332,
          "value": 113.68469103590627
        },
        {
          "time": 1502807191332,
          "value": 113.67799449012763
        },
        {
          "time": 1502807192332,
          "value": 112.71988545819869
        },
        {
          "time": 1502807193332,
          "value": 113.05775062793727
        },
        {
          "time": 1502807194332,
          "value": 114.53499763344529
        },
        {
          "time": 1502807195332,
          "value": 115.4964191594706
        },
        {
          "time": 1502807196332,
          "value": 115.31744641217797
        }
      ]
    }
  } 
';
const headers = {
  'Content-Type':'application/json',
  'Authorization' : 'AWS Sig v4 Key',
  'x-api-key' : 'PublicDemoKeyForDocumentation'

};

fetch('https://api.sensum.co/v0/events',
{
  method: 'POST',
  body: inputBody,
  headers: headers
})
.then(function(res) {
    return res.json();
}).then(function(body) {
    console.log(body);
});
```

```python
import requests
headers = {
  'Content-Type':'application/json',,
  'Authorization' : 'AWS Sig v4 Key',
  'x-api-key' : 'PublicDemoKeyForDocumentation'
}

data = {
    "records": {
      "heartrate": [
        {
          "time": 1502807187332,
          "value": 111.77347523527911
        },
        {
          "time": 1502807188332,
          "value": 112.89604978090439
        },
        {
          "time": 1502807189332,
          "value": 112.37719504311998
        },
        {
          "time": 1502807190332,
          "value": 113.68469103590627
        },
        {
          "time": 1502807191332,
          "value": 113.67799449012763
        },
        {
          "time": 1502807192332,
          "value": 112.71988545819869
        },
        {
          "time": 1502807193332,
          "value": 113.05775062793727
        },
        {
          "time": 1502807194332,
          "value": 114.53499763344529
        },
        {
          "time": 1502807195332,
          "value": 115.4964191594706
        },
        {
          "time": 1502807196332,
          "value": 115.31744641217797
        }
      ]
    }
  } 


r = requests.post('https://api.sensum.co/v0/events', params = data, headers = headers)

print r.json()
```

> Body parameter

```json
{
    "records": {
      "heartrate": [
        {
          "time": 1502807187332,
          "value": 111.77347523527911
        },
        {
          "time": 1502807188332,
          "value": 112.89604978090439
        },
        {
          "time": 1502807189332,
          "value": 112.37719504311998
        },
        {
          "time": 1502807190332,
          "value": 113.68469103590627
        },
        {
          "time": 1502807191332,
          "value": 113.67799449012763
        },
        {
          "time": 1502807192332,
          "value": 112.71988545819869
        },
        {
          "time": 1502807193332,
          "value": 113.05775062793727
        },
        {
          "time": 1502807194332,
          "value": 114.53499763344529
        },
        {
          "time": 1502807195332,
          "value": 115.4964191594706
        },
        {
          "time": 1502807196332,
          "value": 115.31744641217797
        }
      ]
    }
  }
```
### Responses

Status|Meaning|Description
---|---|---|
200|[OK](https://tools.ietf.org/html/rfc7231#section-6.3.1)|Successful request

### Response Headers

Status|Header|Type|Format|Description
---|---|---|---|---|
200|Access-Control-Allow-Origin|string||

> Example responses

```json
{
    "events":{
        "heartrate":[
            {"time":1502807187000,"value":"min","severity":0.03176537181134584},
            {"time":1502807196000,"value":"max","severity":0.03176537181134584}
        ]
    },
    "stats":{
        "heartrate":{
            "avg":113.55359048765672,
            "duration":9,
            "max":115.4964191594706,
            "min":111.77347523527911,
            "std":1.175057115344551,
            "percentiles":{"10":112.3168230623359,"50":113.36787255903245,"90":115.33534368690724}}
    },
    "records":{
        "arousal":[
            {"time":1502807187332,"value":5.359549491038813e-15},
            {"time":1502807188332,"value":0.052921639437207874},
            {"time":1502807189332,"value":0.028461220785887004},
            {"time":1502807190332,"value":0.09010062973692541},
            {"time":1502807191332,"value":0.08978493383721622},
            {"time":1502807192332,"value":0.044616707881203976},
            {"time":1502807193332,"value":0.06054471797280525},
            {"time":1502807194332,"value":0.13018671519236316},
            {"time":1502807195332,"value":0.17551110237516387},
            {"time":1502807196332,"value":0.16707377298958567}
        ]
    },
    "exec_time":0.12196207046508789
}
```
<aside class="warning">
To perform this operation, you must be authenticated by means of the following headers:
X-API-Key, Authorization
</aside>


## Get test data 

This endpoint allows the user to generate a series of test data streams that can be fed into the events endpoint to test the analysis service. When testing the events endpoint only POST the "records" JSON object in the request body. 

### HTTP Request
`GET https://api.sensum.co/v0/testdata`


> Code samples

```javascript
var headers = {
   'Content-Type':'application/json',
  'Authorization' : 'AWS Sig v4 Key',
  'x-api-key' : 'PublicDemoKeyForDocumentation'

};

var params = {
	'n' : '10',
	'freq' : '1',
	'values' : ['heartrate']
};

$.ajax({
  url: 'https://api.sensum.co/v0/testdata',
  method: 'get',
  data : params,
  headers: headers,
  success: function(data) {
    console.log(JSON.stringify(data));
  }
})
```

```javascript--nodejs
const request = require('node-fetch');

const headers = {
  'Content-Type':'application/json',
  'Authorization' : 'AWS Sig v4 Key',
  'x-api-key' : 'PublicDemoKeyForDocumentation'

};

var params = {
	'n' : '10',
	'freq' : '1',
	'values' : ['heartrate']
};

fetch('https://api.sensum.co/v0/testdata?n=10&freq=1&values=['heartrate']',
{
  method: 'GET',
  data: params,
  headers: headers
})
.then(function(res) {
    return res.json();
}).then(function(body) {
    console.log(body);
});
```

```python
import requests
headers = {
  'Content-Type':'application/json',
  'Authorization' : 'AWS Sig v4 Key',
  'x-api-key' : 'PublicDemoKeyForDocumentation'
}

params = {
	'n' : '10',
	'freq' : '1',
	'values' : ['heartrate']
}

r = requests.get('https://api.sensum.co/v0/testdata', params = params, headers = headers)

print r.json()
```


### Parameters

Parameter|Default|Type|Required|Description
---|---|---|---|---|
values|[heartrate]|string|false|An array of the metrics to be generated
freq|1|string|false|Frequency of generated records in seconds. Determines timestamps
n|100|string|false|Number of records to be generated per metric


### Responses

Status|Meaning|Description
---|---|---|
200|[OK](https://tools.ietf.org/html/rfc7231#section-6.3.1)|Successful request

### Response Headers

Status|Header|Type|Format|Description
---|---|---|---|---|
200|Access-Control-Allow-Origin|string||

> Example responses

```json
{
    "data":{
        "records":{
            "heartrate":[
                {"time":1502807389973,"value":150.97562499389198},
                {"time":1502807390973,"value":152.35592250838624},
                {"time":1502807391973,"value":152.7272449519015},
                {"time":1502807392973,"value":153.35354662656178},
                {"time":1502807393973,"value":153.78404886070086},
                {"time":1502807394973,"value":155.3441181915241},
                {"time":1502807395973,"value":155.18001608899218},
                {"time":1502807396973,"value":154.5414565968463},
                {"time":1502807397973,"value":156.35855725243886},
                {"time":1502807398973,"value":156.83569789892923}
            ]
        }
    },
    "params":{"n":10,"values":["heartrate"],"freq":1}
}
```
<aside class="warning">
To perform this operation, you must be authenticated by means of the following headers:
X-API-Key, Authorization
</aside>

## Errors

The Sensum Emotion AI API uses the following error codes:


Error Code | Meaning|
---------- | -------|
400 | Bad Request - Your Request may have caused an error|
401 | Unauthorized - This error will likely occur if the Cognito Authorization Header (AWS Signature v4) is either missing or invalid.|
403 | Forbidden -This error will likely occur if the API Key Header is either invalid or missing.|
405 | Method Not Allowed - You have attempted to make a request using a HTTP Method that is invalid for the requested resource.|
429 | Too Many Requests - You have made more requests than is allowed under the usage plan.|
500 | Internal Server Error - There is an error with our service
503 | Service Unavailable - Our service is down for maintenance. Please try again later.


