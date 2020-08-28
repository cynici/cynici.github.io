# curl with Host header

Sometimes, it is handy to be able to test a web server running locally
which is configured to serve different contents (or backends/upstream) 
based on the HTTP `Host` header in the request.

For HTTP, you could 

```sh
curl -H 'Host: www.site1.com' localhost
```

But the above will not work for HTTPS even when you specify *https* 
because the webserver will insist on using `localhost` to match 
the SSL/TLS certificate. Instead, you need to:

```sh
curl --resolve 'www.site1.gov.bw:443:127.0.0.1' --header 'Host: www.site1.gov.bw' https://localhost
```
