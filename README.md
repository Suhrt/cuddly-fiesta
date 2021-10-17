Cuddly Fiesta is the web iframe for STAT prescription maker. The STAT prescription maker enables doctors to digital prescriptions, easily & fast.

<br /><br />
# Integration Guide #
<br /><br />
## Step 1: Obtain auth key ##

To obtain the auth_key contact [dr.suhrt@gmail.com](mailto:suhrt2@gmail.com?subject=Cuddly%20Fish%20auth%20key)

<br />

## Step 2: Create doctor's profile ##

```
POST https://stat-api.link/create-profile

body:  {
   "auth_key": String,
   "profiles": {
         "user_id": String,
         "name": String,
         "qualifications": String,
         "medical_council":String,
         "reg_no": String
      }
}
```

This will return an internal reference id.

```
body: {
  "stat_id": "4efbad10-d014-40b4-9d22-b06414044ef6"
}
```

OR an error.

NOTE: 

⚠️ user_id should be unique to your organization

ℹ️ for bulk createion of profiles contact [dr.suhrt@gmail.com](mailto:suhrt2@gmail.com?subject=Cuddly%20Fish%20auth%20key)


<br /><br />

## Step 3: Session Authentication ##

Cuddly fiesta & STAT leaves the doctor authentication and verification to the website opening the iFrame. However each session is authenticated with a JWT token. To get the jwt token:

```
POST https://stat-api.link/token

body: {
  "auth_key": String,
  "user_id: String //use your org's user id 
}

```

This will return a jwt token valid for 30min. If valid this will return


```

body: {
  'jwt_token': String
}

```
NOTE: 

⚠️ a profile with the user_id must exist with STAT

⚠️ we will be turning on IP whitelisting shortly.

<br /><br />
## Step 4: Open an iFrame and Pass information ##
Cuddly fiesta uses iFrame to embed the STAT webpage into the browser.

Create an iframe 
```
    <iframe id="cuddly-fiesta" frameBorder="0"> </iframe>
```

css
```
html, body {
  height: 100%;
  width: 100%;
  margin: 0;
  padding: 0;
}

iframe {
  width: 100%;
  height: 95vh;
}

*{
    box-sizing: border-box;
}
```

javascript 
<br/>
Get the iframe refence and set the source
```
var iframe = document.getElementById('cuddly-fiesta');
iframe.src = 'https://stat-rx.netlify.app';
```
<br>

The iframes raises an event with status ready, on recieving this send the patient information and token.
Once the doctor finishes writing the prescription the iframe raises an event with status done.  

```
window.addEventListener("message", (event) => {
  
  var status = event.data.status;
  
  if (status == 'ready'){
    contentWindow.postMessage({
      "patientName": "String",
      "patientAge": "String",
      "patientSex": "String",
      "token": "String"
    }, '*')
  }
    
  if(status == 'done'){
      let rx_url = event.data.prescription_url
      let rx_password = event.data.prescription_password
      //Store url and handle navigation
  }
    
}, false);

```

Examples:
1. Request for registering users & generating tokens:  [![Run in Postman](https://run.pstmn.io/button.svg)](https://god.gw.postman.com/run-collection/17756415-70efa4d9-d3ae-481d-9fd4-2d3437d40660?action=collection%2Ffork&collection-url=entityId%3D17756415-70efa4d9-d3ae-481d-9fd4-2d3437d40660%26entityType%3Dcollection%26workspaceId%3D7631ec9c-9cfc-4a30-8344-451e39828421)
2. Web app with Iframe embedded: https://glitch.com/edit/#!/organic-honored-share?path=README.md%3A1%3A0
