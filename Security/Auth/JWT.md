
JSON Web Token - is an open standard that defines a compact and self-contained way for securely transmitting information between parties as a JSON object.

Information can be verified and trusted because it is digitally signed.
JWTs can be signed using a secret or a public/private key pair.


## JSON Web Token Structure

- Header - typically consists of two parts: the type of the token, which is JWT, and the signing algorithm being used, such as HMAC SHA256 or RSA.

``` JSON
{
  "alg": "HS256",
  "typ": "JW"
}
```

Then, this JSON is Base64Url encoded to form the first part of the JWT.

- Payload - second part of the token that contains claims.
	- Claims are statements about an entity and additional data.
	- Three types of claims: registered, public, and private claims.

Registered Claims: These are a set of predefined claims which are not mandatory but recommended, to provide a set of useful, interoperable claims.
Some of the claims are: `iss` (issuer), `exp` (expiration time), `sub` (subject), `aud` (audience)

Public Claims: These can be defined at will by those using JWTs.
To avoid collisions, they should be defined in the IANA JSON Web Token Registry.

Private Claims: These are custom claims created to share information between parties that agree on using them and are neither `registered` or `public` claims.

Example payload:
```JSON
{
	"sub": "1234567890",
	"name": "John Doe",
	"admin": true
}
```

The payload is then Base64Url encoded to form the second part of the JSON Web Token.


- Signature

Typically looking like this:
```JWT
hhhh.pppp.ssss
```

