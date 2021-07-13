
# Radial Simple Support API

Radial is currently developing a REST API to simplify the process of engineering simply supported beams. This is an extension of a tried and tested Excel workbook developed by Radial/Cantilever Connections.

The API exposes a single POST endpoint at: https://api.radialapp.com/simple-support

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

The type of load applied to the beam is communicated through the **loadPattern** property in the JSON load object. Each load object must also have a **loadCase** property (either DL, LL or WL - Dead, Live, Wind respectively) and a **loadAmount** property which is given in kN for Point loads and kN/m for the UDLs.

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

In other words, the request body must contain an array of JSON objects representing the loads

## Headers
The API is protected by an API key which must be included in the request header as follows:

x-api-key: "YOUR_API_KEY_HERE"

Please get in touch with Radial to obtain a valid key.

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
- [ ] Partial UDL supported for Serviceablity

## License
[MIT](https://choosealicense.com/licenses/mit/)
