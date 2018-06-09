---
tags:
- java
- keytool
- openssl
- keystore
- pkcs12
- jks
date: "2016-08-23T23:30:26+03:00"
title: "Export Certificate and Private key from JKS"
---

I had to migrate web application written in Java to NodesJS,

Java web containers store certificates inside JKS Keystore where NodeJS requires certificate and private key in [PEM format](https://nodejs.org/api/https.html).


Exporting certificate directly from JKS is easy by using _keytool_, but exporting private key is not allowed,
To export the private key using Java, we need to interact with the keystore by writing some custom code using the Keystore java API,

the other alternative is simply convert the JKS into a [PKCS12](https://en.wikipedia.org/wiki/PKCS_12) and export the certificate & key using _openssl_


Convert JKS to PKCS12 format:

```
keytool -importkeystore -srckeystore mykeystore.jks -destkeystore mykeystore.p12 -deststoretype PKCS12
```

Note: The _-importkeystore_ parameter is only supported with the keytool that ships with Java ver 1.6+

you can view the content of the pkcs12 keystore by:

```
openssl pkcs12 -in mykeystore.p12
```

(Specifically look for the _friendlyName_ that corresponds to the _alias_ property of in _JKS_)

Export the certificate:

```
openssl pkcs12 -in mykeystore.p12 -nokeys -out cert.pem
```


Export the private key (unencrypted)

```
openssl pkcs12 -in mykeystore.p12  -nodes -nocerts -out key.pem
```

the _-nodes_ means "No DES", that is "do not encrypt the private key that will be exported to key.pem"


Make sure you keep the private key safe (recommended: _chmod 600 key.pem_)

