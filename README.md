# Make iris-http-calls to Epic on FHIR (https://fhir.epic.com/)

Creating a Public Private Key Pair
```
mkdir /home/ec2-user/path_to_key
openssl genrsa -out ./path_to_key/privatekey.pem 2048
```
For backend apps, you can export the public key to a base64 encoded X.509 certificate named publickey509.pem using this command...
```
openssl req -new -x509 -key ./path_to_key/privatekey.pem -out ./path_to_key/publickey509.pem -subj '/CN=medbank'
```
where '/CN=medbank' is the subject name (for example the app name) the key pair is for. The subject name does not have a functional impact in this case but it is required for creating an X.509 certificate.

I registered my app “medbank” so that I could obtain a Client ID
<img width="1411" alt="Screenshot" src="https://github.com/oliverwilms/bilder/blob/main/Epic_on_FHIR_1.png">
I cut out Client IDs and edited Non-Production JWK Set URL to protect the real IP address.
<img width="1411" alt="Screenshot" src="https://github.com/oliverwilms/bilder/blob/main/Epic_on_FHIR_2.png">

Epic's documentation stated, your application makes a HTTP POST request to the authorization server's OAuth 2.0 token endpoint to obtain access token. I tried to write code, but I never succeeded in obtaining an access token.

I called InterSystems WRC for help. 

We set up an OAuth2 client using the "JWT Authorization" grant type and "private key JWT" for authentication.

We then tried running this on the terminal using IsAuthorized() and GetAccessTokenJWT(), but it responded saying "invalid client ID".

A couple days later, we saw that the grant_type was actually supposed to be client_credentials, so we switched to using that by switching from GetAccessTokenJWT() to GetAccessTokenClient() and that make it work.
```
```
