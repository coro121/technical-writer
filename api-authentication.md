# Authenticate

To use Skyflow's [Management API](/management/), [Data API](/record/), or [SDKs](/sdks/), you need a JWT bearer token (recommended) or an API Key to authenticate your API calls. JWT Bearer tokens and API keys allow scoped and permission-sensitive access to your account and the vaults it has. JWT Bearer tokens are time-limited, and API keys are long-lived.

**Note**:
Skyflow's bearer tokens match the RFC's [Authorization Bearer Token Header](https://tools.ietf.org/html/rfc6750#section-2.1) specification.

## Prerequisites

- Sign in to your Skyflow account. If you don't have an account, sign up for a free trial.

- A device with the following tools available:
  - A terminal that can run bash commands
  - curl
- A bearer token to authenticate API calls. For a short-lived token, use the following process. To generate tokens from service accounts, see Authenticate.

- Skyflow account, vault, and workspace details:

  1. In Studio, click vault menu icon > View vault details.
  1. Note your Account ID, Vault ID, and Vault URL values.

- Your environment's Management API URL:

  - Trial or Production: https://manage.skyflowapis.com
  - Staging: https://manage.skyflowapis-preview.com

## Create a service account

When generating tokens using a Skyflow SDK or Python script, you must create a service account. A service account is an identity for machine access to your vault. The service account's roles, and the policies attached to those roles, decide the level of access a service account has to a vault.

**Note**: You must have Vault Owner permissions to create a service account.

If you already have a service account, skip to the method you want to use to generate a bearer token.

1. In Studio, find the vault you want to access and click **Open**.
2. In the side navigation, click **Access**.
3. Click **Service accounts**.
4. Click **Add service account**.
5. For **Name** enter a value. For example, "Authenticate".
6. For **Service account admins**, select the admins of your service account.
7. Click **Next**.
8. For **Authentication**, select your authentication type.

**Note**: Unless you have a valid business need, use JWT bearer tokens to authenticate.

9. Optional: To enforce context-aware authentication, select **Inject context_identifier in bearer token**.
10. Click **Next**.
11. For **Assignments**, select the resource and roles for which you want to assign to the service account.
12. For roles, select the role for which you want to assign to the resource.
13. Click **Create Service Account**.
14. Your browser downloads a *credentials.json* file. Store this file in a secure location. You'll need it to generate bearer tokens.

## Generate a bearer token

You can generate a bearer token with an SDK, Python script, or (if you're in a trial environment) through Skyflow Studio. In production environments, we recommend using Skyflow-provided SDKs.

### Use an SDK

When you integrate your backend systems with one of Skyflow's SDKs, you can use service account credentials to generate bearer tokens.

Bearer tokens generated from SDKs are valid for 60 minutes and let you make API calls allowed by the policies associated with the service account.

#### Step 1: Install the SDK

Now that you have your _credentials.json_ file, it's time to prepare the SDK in the language of your choice.

**Go**

Make sure your project is using Go Modules:

```bash
go mod init
```

Then reference skyflow-go in a Go program with `import`:

```go
import (
  saUtil "github.com/skyflowapi/skyflow-go/serviceaccount/util"
  Skyflow "github.com/skyflowapi/skyflow-go/skyflow/client"
  "github.com/skyflowapi/skyflow-go/skyflow/common"
  logger "github.com/skyflowapi/skyflow-go/commonutils/logwrapper"
)
```

**Java**

##### Gradle

Add the following dependency to your project's build file:

`implementation 'com.skyflow:skyflow-java:1.5.0'`

##### Maven

Add the following dependency to your project's POM:

```xml
<dependency>
    <groupId>com.skyflow</groupId>
    <artifactId>skyflow-java</artifactId>
    <version>1.5.0</version>
</dependency>
```

**Node.js**

Run the following command:

```bash
npm install skyflow-node
```

**Python**

Run the following command:

```bash
pip install skyflow
```

#### Step 2: Generate the bearer token

With the SDK installed, you can generate bearer tokens by passing your _credentials.json_ file into an appropriate language-specific function.

**Go**

The Go SDK has two functions that can take _credentials.json_ and return a bearer token:

- `GenerateBearerToken(filepath)` takes the path to _credentials.json_ as input.
- `GenerateBearerTokenFromCreds(credentials)` takes the body of _credentials.json_ as a string as input.

##### Example

```go
package main

import (
    "fmt"
    saUtil "github.com/skyflowapi/skyflow-go/serviceaccount/util"
)

var bearerToken = ""

func GetSkyflowBearerToken() (string, error) {

  filePath := "<PATH_TO_CREDENTIALS.JSON>"
  if saUtil.IsExpired(bearerToken) {
    newToken, err := saUtil.GenerateBearerToken(filePath)
    if err != nil {
      return "", err
    } else {
      bearerToken = newToken.AccessToken
      return bearerToken, nil
    }
  }
  return bearerToken, nil
}
```

**Java**

The Java SDK has two functions that can take _credentials.json_ and return a bearer token:

- `Token.generateBearerToken(filepath)` takes the path to _credentials.json_ as input.
- `Token.generateBearerTokenFromCreds(credentials)` takes the body of _credentials.json_ as a string as input.

##### Example

```java
import com.skyflow.errors.SkyflowException;
import com.skyflow.serviceaccount.util.Token;
import com.skyflow.entities.ResponseToken;

public class TokenGenerationUtil {

    private static String bearerToken = null;

    public static String getSkyflowBearerToken() {
        try {
            String filePath = "<PATH_TO_CREDENTIALS.JSON>";
            if(Token.isExpired(bearerToken)) {
                ResponseToken response = Token.generateBearerToken(filePath);
                bearerToken = response.getAccessToken();
            }
        } catch (SkyflowException e) {
            e.printStackTrace();
        }

        return bearerToken;
    }
}
```

Node.js

The Node.js SDK has two functions that can take _credentials.json_ and return a bearer token:

- `GenerateBearerToken(filepath)` takes the path to _credentials.json_ as input.
- `GenerateBearerTokenFromCreds(credentials)` takes the body of _credentials.json_ as a string as input.

##### Example

```javascript
import { generateBearerToken, isExpired } from "skyflow-node";

let filepath = 'PATH_TO_CREDENTIALS.JSON';
let bearerToken = '';

function getSkyflowBearerToken() {
    return new Promise(async (resolve, reject) => {
        try {
            if (!isExpired(bearerToken)) resolve(bearerToken)
            else {
                let response = await generateBearerToken(filepath);
                bearerToken = response.accessToken;
                resolve(bearerToken);
            }
        } catch (e) {
            reject(e);
        }
    });
}
```

**Python**

The Python SDK has two functions that can take _credentials.json_ and return a bearer token:

- `generate_bearer_token(filepath)` takes the path to _credentials.json_ as input.
- `generate_bearer_token_from_creds(credentials)` takes the body of _credentials.json_ as a string as input.

##### Example

```python
from skyflow.errors import SkyflowError
from skyflow.service_account import generate_bearer_token, is_expired

# cache token for reuse
bearerToken = ''
tokenType = ''
def token_provider():
    global bearerToken
    global tokenType
    if is_expired(bearerToken):
        bearerToken, tokenType = generate_bearer_token('<PATH_TO_CREDENTIALS.JSON>')
    return bearerToken, tokenType

try:
    accessToken, tokenType = token_provider()
    print("Access Token:", accessToken)
    print("Type of token:", tokenType)
except SkyflowError as e:
    print(e)
```

Once you have your bearer token, you can programmatically interact with Skyflow APIs. See [next steps](/api-authentication/#next-steps).

### Use Studio

You can generate bearer tokens through Studio for short-term use. Bearer tokens generated in Studio are valid for 24 hours and let you make API calls allowed by the policies associated with your account.

1. In Studio, click your account icon and choose **Generate API Bearer Token**.
2. Click **Generate Token**.

Studio copies the token onto your clipboard.

### Use a Python script

In production environments, generate bearer tokens using Skyflow-provided SDKs. However, you can use this Python script to test generating bearer tokens on your local machine. To execute the script, make sure you have the `credentials.json` file, downloaded during the service account creation.

**Note**: This guide uses Homebrew to run Python installation commands. Adapt your Python installation accordingly.

#### Step 1: Prepare your environment

From your terminal, run the following commands to install python and the appropriate libraries.

Install Python version 3.5 or later.

```bash
   brew install python
```

Install the following libraries:

```bash
pip3 install PyJWT

pip3 install requests

pip3 install cryptography
```

#### Step 2: Install the Python bearer token script

Now that you have your _credentials.json_ file, it's time to prepare the Python script for generating a bearer token. To get started, copy, and paste the following `getBearerToken.py` script into your IDE.

```python
import json
# Requests lib installation: 'pip install requests'
# PyJWT lib installation:
# 'pip install pyjwt[crypto]>=2.0.0' or
# 'pip install cryptography; pip install pyjwt>=2.0.0'
import jwt
import requests
import time


def getSignedJWT(credsFile):
   # credsFile is the filepath to your credentials.json file
   # Load the credentials.json file into an object called creds
   fd = open(credsFile)
   creds = json.load(fd)
   fd.close()

   # Create the claims object with the data in the creds object
   claims = {
       "iss": creds["clientID"],
       "key": creds["keyID"],
       "aud": creds["tokenURI"],
       "exp": int(time.time()) + (3600), # JWT expires in Now + 60 minutes
       "sub": creds["clientID"],
   }
   # Sign the claims object with the private key contained in the creds object
   signedJWT = jwt.encode(claims, creds["privateKey"], algorithm='RS256')
   return signedJWT, creds


def getBearerToken(signedJWT, creds):
   # Request body parameters
   body = {
       'grant_type': 'urn:ietf:params:oauth:grant-type:jwt-bearer',
       'assertion': signedJWT,
   }
   # Request URI (== https://api.skylfow.dev/v1/auth/sa/oauth/token)
   tokenURI = creds["tokenURI"]

   # Send the POST request using your favorite Python HTTP request lib
   r = requests.post(tokenURI, json=body)
   return r.text


jwtToken, creds = getSignedJWT('<PATH_TO_CREDENTIALS.JSON>')
bearerToken = getBearerToken(jwtToken, creds)
print(bearerToken)
```

Locate the `jwtToken, creds` parameter and enter the full path to your _credentials.json_ file.

```python
jwtToken, creds = getSignedJWT('<PATH_TO_CREDENTIALS.JSON>')
```

Save this file as _getBearerToken.py_ to a secure location. You'll need it to execute the script.

#### Step 3: Generate a bearer token

From your terminal, navigate to the folder with the _getBearerToken.py_ script and run the following command to generate a bearer token.

```bash
python3 getBearerToken.py
```

Skyflow validates the JWT assertion and returns a bearer token.

```javascript
   {
       "accessToken": "eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJodHRwczovL21hbmFnZS5za3lmbG93YXBpcy5kZXYiLCJjbGkiOiJ5NGIwZm....pOqmlI_CWY2V6MEBTqnVHuAo1-9MBSW8REp-mv_-mJqOe8TMb9dOImcXzM7jEpW79Fqs3-HCo-cUikWwy6tjjvVqHW-4pqG005pGzxrAt275Q2LU1pXwUfUM6idH9o2ydlpTp0-ujPQgVQXh8w9LsqE58Qtm4lRU8Sr8FMdx72qnuahD5Xoh1KL7D-DFZaYMrof9aTfUFUctUBzOUbL4_z2bEf2wkHouSPOZGI3uHIM54mjX013NkNXzMltP8GiP5GimC3PX-jA",
       "tokenType": "Bearer"
   }
```

Once you have your bearer token, you can programmatically interact with Skyflow APIs. See [next steps](/api-authentication/#next-steps).

## Get a bearer token for a client-side SDK

Skyflow's client-side SDKs doesn't have direct methods to generate bearer tokens. Bearer token generation typically involves sensitive operations that shouldn't happen in client-side environments (like browsers) because  of security concerns.

Bearer tokens are usually generated on a server, where you can securely store your application's credentials and use them to authenticate with the Management API to retrieve a token. You then pass the token to the client-side application, which can use a client-side SDK to interact with Skyflow.

Here's a general outline of the steps you would take to generate a bearer token and use it with a client-side SDK:

1. **Generate a bearer token with a server-side SDK:** Create a backend service with a [server-side SDK](#use-an-sdk) to handle authentication. This service uses your Skyflow credentials to authenticate and retrieve a bearer token. Make sure to store your credentials securely, and don't expose them to the client.
2. **Pass the token to the client:** Provide an endpoint in your backend service that the client can call to retrieve the bearer token. The client calls this endpoint to get the token when needed.
3. **Initialize Skyflow client:** In your client application, use the client-side SDK to initialize a Skyflow client. You need to pass a helper function to the `getBearerToken` parameter of the initialization method. This function should make an API call to your backend service to retrieve the bearer token.

Here's a sample JavaScript implementation of the `getBearerToken` function in your client code:

```javascript
const getBearerToken = () => {
  return new Promise((resolve, reject) => {
    // Make an API call to your backend service to get the bearer token.
    fetch('/path/to/your/backend/service/endpoint')
      .then(response => response.json())
      .then(data => {
        if (data && data.accessToken) {
          resolve(data.accessToken); // Resolve the promise with the token.
        } else {
          reject('Failed to retrieve access token'); // Reject the promise if there's an issue.
        }
      })
      .catch(error => {
        reject(error); // Reject the promise on network errors or other issues.
      });
  });
};
```

You would initialize the Skyflow client like this:

```javascript
const skyflowClient = Skyflow.init({
  vaultID: 'your-vault-id',
  vaultURL: 'your-vault-url',
  getBearerToken: getBearerToken,
  options: {
    logLevel: Skyflow.LogLevel.ERROR, // Optional
    env: Skyflow.Env.PROD, // Optional
  }
});
```

Remember that generating and handling bearer tokens requires careful security considerations, as these tokens provide access to your Skyflow vault. Always keep your credentials secure, and never expose them to the client-side.

## Enable API key-based authentication

**Note**: This feature is in beta. Contact your Skyflow representative to enable it.

API key-based authentication is an alternate but less secure method of authenticating service accounts.

When using API key-based authentication, remember the following practices:

- API keys should only be the preferred authentication mechanism if a specific use case warrants its need. For example, partners already authenticate via your SDKs, and you want them to refrain from authenticating again with Skyflow.
- API keys don't expire. In some instances, this is a convenience in terms of usability. However, API keys reduce security, and the onus is on you and your partners or customers to protect the API key and monitor its usage securely.
- Only use API key-based authentication to insert records into a Skyflow vault. Don't use API key-based authentication to read or delete data.

### Create a service account with API key-based authentication

When configuring your authentication method for a service account, you can use either JWT bearer tokens or API keys. You can generate API keys by calling the Create Service Account API while creating a service account. After configuring your service account, you can create, rotate, disable, or delete the key with the Management API.

**Note**: You can't generate bearer tokens with a service account configured to use API keys or create API keys with a service account configured to use bearer tokens.

Call the Service Accounts API with `apiKeyEnabled` set to `true`.

```bash
curl -s X POST "$MANAGEMENT_URL/v1/serviceAccounts" \
   -H "Authorization: $TOKEN" \
   -H "content-type: application/json" \
   -d '{
        "resource": {
            "ID": "'"$VAULT_ID"'",
            "type": "'"$VAULT"'"
        },
        {
        "serviceAccount": {
            "ID": "'"$SERVICE_ACCOUNT_ID"'",
            "name": "'"$SERVICE_ACCOUNT_NAME"'",
            "displayName": "'"$DISPLAY_NAME"'",
            "description": "'"$DESCRIPTION"'"
        },
        {
        "clientConfiguration": {
            "enforceContextID": true,
            "enforceSignedDataTokens": true
        },
        {
        "apiKeyEnabled": true
    }'
```

## Next steps

You can now use your bearer token to interact with Skyflow APIs.

If you're new to Skyflow, see [Get started with Skyflow](/get-started/). Otherwise, see the various ways you can use Skyflow APIs:

- [Data API](/record/)
- [Management API](/management/)
- [SDKs](/sdks/)
- [Postman](/postman-collection-setup/)
