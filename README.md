
# Radial Simple Support API

Radial is currently developing a REST API to simplify the process of engineering simply supported beams. This is an extension of a tried and tested Excel workbook developed by Radial/Cantilever Connections.

The API exposes a single POST endpoint at: https://api.radialapp.com/simple-support

- [Radial Simple Support API](#radial-simple-support-api)
  * [Body (Inputs)](#body--inputs-)
      - [Load data](#load-data)
      - [Beam span](#beam-span)
      - [Maximum deflection limit](#maximum-deflection-limit)
      - [Sample Request Body](#sample-request-body)
  * [Headers](#headers)
  * [Response (Outputs)](#response--outputs-)
  * [Example Usage (Python)](#example-usage--python-)
  * [TODOS](#todos)
  * [License](#license)

## Body (Inputs)
The API expects a request body to be a JSON object containing:
1. Load data
2. Beam span
3. Maximum deflection limit

#### Load data
The load data is an array of JSON objects representing the unfactored loads applied to the beam. These loads may be either:

 - Point loads
 - Uniformly Distributed Loads (UDLs)
 - Partial UDLs (UDLs acting on part of the beam)

The type of load applied to the beam is communicated through the ```loadPattern``` property in the JSON load object. Each load object must also have a ```loadCase``` property (either DL, LL or WL - Dead, Live, Wind respectively) and a ```loadAmount``` property which is given in kN for Point loads and kN/m for the UDLs.

Other inputs are load-specific and may be seen in the sample request body below. Note: All positional inputs are put as percentages of the beam span. i.e. a ```loadPosition``` of 0.5 refers to a Point load in the middle of the beam.

#### Beam span
The beam span is to be input in metres.

#### Maximum deflection limit
The maximum deflection limit is to be input in millimetres.

#### Sample Request Body
Shown below is an example request body adhering to the above:

```yaml
{
  "loads": [
    {
      "loadPattern": "Point",
      "loadCase": "DL",
      "loadAmount": 10,
      "loadPosition": 0.25
    }, 
    {
      "loadPattern": "UDL",
      "loadCase": "DL",
      "loadAmount": 10
    },
    {
      "loadPattern": "PartialUDL",
      "loadCase": "LL",
      "loadAmount": 4,
      "loadStart": 0.5,
      "loadEnd": 0.8
    }
  ],
  "span": 5,
  "deflectionLimit": 10
}
```

## Load Combinations
Load combinations for serviceability and strength are input as follows:

Name | Limit state | DL | LL | WL 
--- | --- | --- | --- | ---
Ultimate dead load | Strength| 1.35 | 0 | 0
Dead and Live | Strength | 1.2 | 1.5 | 0
Dead | Serviceability | 1 | 0 | 0
Live | Serviceability | 0 | 1 | 0
Wind | Serviceability | 0 | 0 | 1

## Headers
The API is protected by an API key which must be included in the request header as follows:

x-api-key: "YOUR_API_KEY_HERE"

Please get in touch with Radial to obtain a valid key.


## Response (Outputs)

The response is a JSON object of the following form:

```yaml
{
    "result": {
        "UB": "250UB37.3",
        "UC": "200UC52.2",
        "PFC": "300PFC"
    }
}
```

## Example Usage (Python)

```python
import urllib2
import json

data = {
  "loads": [
    {
      "loadPattern": "Point",
      "loadCase": "DL",
      "loadAmount": 10,
      "loadPosition": 0.25
    }, 
    {
      "loadPattern": "UDL",
      "loadCase": "DL",
      "loadAmount": 10
    },
    {
      "loadPattern": "PartialUDL",
      "loadCase": "LL",
      "loadAmount": 4,
      "loadStart": 0.5,
      "loadEnd": 0.8
    }
  ],
  "span": 5,
  "deflectionLimit": 10
}

def getMembers(data):
    
    request = urllib2.Request("https://api.radialapp.com/simple-support")
    request.add_data(json.dumps(data))
    request.add_header("Content-type", "application/json")
    request.add_header("x-api-key", "YOUR-API-KEY-HERE")
    response = urllib2.urlopen(request)
    resJSON = json.load(response)
    return(resJSON['result'])

print(getMembers(data))

```

## TODOS

- [x] Point, UDL and Partial UDLs supported for Strength
- [x] Point and UDL supported for Serviceability
- [x] Partial UDL supported for Serviceablity
- [ ] Add percentage efficiency to the response 

## License
[MIT](https://choosealicense.com/licenses/mit/)
