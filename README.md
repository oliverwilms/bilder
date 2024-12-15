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
```
```
