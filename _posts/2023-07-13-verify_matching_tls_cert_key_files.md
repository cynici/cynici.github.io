## Verify that TLS/SSL a certificate file matches a key file

Many online [instructions](https://www.ibm.com/support/pages/how-verify-if-private-key-matches-certificate) only work for RSA key encryption i.e.

```bash
openssl x509 -noout -modulus -in fullchain.pem | openssl md5
openssl rsa -noout -modulus -in privkey.pem | openssl md5
```

But certbot v2 and later has switched to use ECDSA [by default](https://community.letsencrypt.org/t/ecdsa-certificates-by-default-and-other-upcoming-changes-in-certbot-2-0/177013) causing verification method above fail. If you want to keep using RSA 2048-bit certificates, you can use the certbot CLI flags which have been available since Certbot 1.10.0: `--key-type rsa --rsa-key-size 2048`

Here is a [versatile method](https://security.stackexchange.com/a/141620) which works for all supported encryption algorithms:

```
openssl x509 -noout -pubkey -in fullchain.pem
openssl pkey -check -pubout -outform PEM -in privkey.pem
```

