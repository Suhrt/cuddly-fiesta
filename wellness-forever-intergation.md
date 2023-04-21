Cuddly Fiesta is the web iframe for STAT prescription maker. The STAT prescription maker enables doctors to digital prescriptions, easily & fast.


# Integration Guide #


## Step 1: Obtain auth key & whitelist IP ##

  

To obtain the auth_key & whitelist your IP contact [suhrt@stat.org.in](mailto:suhrt@stat.org.in?subject=Cuddly%20Fish%20auth%20key) 


  

## Step 2: Create doctor's profile ##

A doctors profile can be created by using the below API **or by sharing an excel with the required information.**

```

POST https://stat-api.link/register_user


body: {
"auth_key": String,
"profiles": {
"user_id": String,
"name": String,
"qualifications": String,
"medical_council":String,
"reg_no": String,
"documents": []String //base64 encoded images of qualifications
 }
}
```
Success
```
body: {
"stat_id": "4efbad10-d014-40b4-9d22-b06414044ef6"
}

```
⚠️ user_id should be unique to your organization

  
 
## Step 3: Session Authentication ##

Cuddly fiesta & STAT leaves the doctor authentication and verification to the website opening the iFrame. 

However each session (s written) is authenticated with a JWT token valid for 30min. To get the jwt token:

```
POST https://stat-api.link/token
body: {
"auth_key": String,
"user_id: String //use your org's user id
}
```
Success 200
```
body: {
'jwt_token': String
}
```

NOTE:
⚠️ a profile with the user_id must exist with STAT

  
## Step 4: Open an iFrame and Pass information ##

Cuddly fiesta uses iFrame to embed the STAT webpage into the browser. *For 
Wellness Forever we suggest embedding the iframe in the order page, when
a prescription is missing.*

HTML
```
<iframe id="cuddly-fiesta" frameBorder="0"> </iframe>
```
css
```
id-of-iframe-parent {
	height: 100%;
	width: 100%;
	margin: 0;
	padding: 0;
}
iframe {
	width: 100%;
	height: 100%;
}
*{
	box-sizing: border-box;
}

```
javascript
Get the iframe reference and set the source
```
var iframe = document.getElementById('cuddly-fiesta');
iframe.src = 'https://stat-rx.netlify.app';
```

The iFrame raises an event with status ready, on receiving this send the patient information, drug to be prescribed & token.

The iframe will then display a prescription to review and make changes to.

Once the doctor finishes writing the prescription the iframe raises an event with status done, returning the prescription url & password.

```
window.addEventListener("message", (event) => {
var status = event.data.status;
if (status == 'ready'){
contentWindow.postMessage({
"patientName": "String",
"patientAge": "String",
"patientSex": "String",
"patientPhoneNumber": "String",
"patientAddress: "String",
"drugs": [{
	"genericName": String,
	"brandName": String,
	"id": String //if available,
	"quantity": int,
	"quantityUnit": String,
	"instructions": String,
	}],
"investigations": []String,
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

Additional endpoint are available to delete / modify a doctor's information.