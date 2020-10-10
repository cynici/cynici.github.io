# curl with Host header

Sometimes, it is handy to be able to test a web server running locally
which is configured to serve different contents (or backends/upstream) 
based on the HTTP `Host` header in the request.

For HTTP, you could 

```sh
curl -v -H 'Host: www.site1.com' localhost
```

But the above will not work for HTTPS even when you specify *https* 
because the webserver will insist on using `localhost` to match 
the SSL/TLS certificate. Instead, you need to:

```sh
curl -v --resolve 'www.site1.com:443:127.0.0.1' https://www.site1.com/
```
