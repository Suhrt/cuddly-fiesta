Cuddly Fiesta is the web iframe for STAT prescription maker. The STAT prescription maker enables doctors to digital prescriptions, easily & fast.

<br /><br />
# Integration Guide #
<br /><br />
## Step 1: Obtain auth key ##

To obtain the auth_key contact [dr.suhrt@gmail.com](mailto:suhrt2@gmail.com?subject=Cuddly%20Fish%20auth%20key)

<br />

## Step 2: Create doctor's profile ##

```
POST example.com/create-profile

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

This will return

```
body: {
  "status": "ok"
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
POST example.com/token

body: {
  "auth_key": String,
  "user_id: String 
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
iframe.src = 'https://example.com/';
```
<br>

The iframes raises an event with status ready, on recieving this send the patient information and token.
Once the doctor finishes writing the prescription the iframe raises an event with status done.  

```
var contentWindow = iframe.contentWindow;

window.addEventListener("message", (event) => {
  var status = event.data.status;
  if (staus == 'ready'){
    contentWindow.postMessage({
      "patientName": String,
      "patientAge": String,
      "patientSex": String,
      "token": String
    }, 
  'https://example.com')
  }
  
  if(status == 'done'){
    "precription_url": event.data.url,
    "prescription_password": event.data.password,
    //redirect
  }
  
}, false);
```
