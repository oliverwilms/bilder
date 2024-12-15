# Make iris-http-calls to Epic on FHIR

I have started working on utilizing [Epic on FHIR](https://fhir.epic.com/) about a month ago.
## Creating a Public Private Key Pair
```
mkdir /home/ec2-user/path_to_key
openssl genrsa -out ./path_to_key/privatekey.pem 2048
```
For backend apps, you can export the public key to a base64 encoded X.509 certificate named publickey509.pem using this command...
```
openssl req -new -x509 -key ./path_to_key/privatekey.pem -out ./path_to_key/publickey509.pem -subj '/CN=medbank'
```
where '/CN=medbank' is the subject name (for example the app name) the key pair is for. The subject name does not have a functional impact in this case but it is required for creating an X.509 certificate.

## Epic on FHIR is a free resource for developers who create apps
I registered my app “medbank” so that I could obtain a Client ID
<img width="1411" alt="Screenshot" src="https://github.com/oliverwilms/bilder/blob/main/Epic_on_FHIR_1.png">
I cut out Client IDs and edited Non-Production JWK Set URL to protect the real IP address.
<img width="1411" alt="Screenshot" src="https://github.com/oliverwilms/bilder/blob/main/Epic_on_FHIR_2.png">

Epic's documentation stated, your application makes a HTTP POST request to the authorization server's OAuth 2.0 token endpoint to obtain access token. I tried to write code, but I never succeeded in obtaining an access token.

I called InterSystems WRC for help. 

We set up an OAuth2 client using the "JWT Authorization" grant type and "private key JWT" for authentication.

We then tried running this on the terminal using IsAuthorized() and GetAccessTokenJWT(), but it responded saying "invalid client ID".

A couple days later, we saw that the grant_type was actually supposed to be client_credentials, so we switched to using that by switching from GetAccessTokenJWT() to GetAccessTokenClient() and that made it work.

## I want to implement Epic on FHIR as a use case for iris-http-calls
I used Docker to deploy iris-http-calls in AWS.
```
sudo docker build --no-cache --progress=plain . -t oliverwilms/iris-http-calls 2>&1 | tee build.log
sudo docker run -d -p57700:52773 oliverwilms/iris-http-calls
```
I copied private and public key files with read access for IRIS
```
chmod 644 privatekey.pem
sudo docker cp ./privatekey.pem container_name:/home/irisowner/dev/ 
sudo docker cp ./publickey509.pem container_name:/home/irisowner/dev/
chmod 600 privatekey.pem
```
I created %SYS.X509Credential in IRIS
```
Set oX509Credentials = ##class(%SYS.X509Credentials).%New()
Set oX509Credentials.Alias = "medbank"
Set tSC = oX509Credentials.LoadCertificate("/home/irisowner/dev/publickey509.pem")
Do $System.Status.DisplayError(tSC)
Set tSC = oX509Credentials.LoadPrivateKey("/home/irisowner/dev/privatekey.pem")
Do $System.Status.DisplayError(tSC)
Set tSC = oX509Credentials.%Save()
Do $System.Status.DisplayError(tSC)
```
