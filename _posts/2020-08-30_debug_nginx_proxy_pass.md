# Debug nginx proxy_pass

In nginx *location* directive, you're often recommended to include a trailing `/`
in examples. Remember, it only matches requests that also include the trailing `/`.

## Location match precedence

<https://stackoverflow.com/a/45129826>

```
1. = (exactly)
   location = /path

2. ^~ (forward match)
   location ^~ /path

3. ~ (regular expression case sensitive)
   location ~ /path/

4. ~* (regular expression case insensitive)
   location ~* .(jpg|png|bmp)

5. /
   location /path
```

## Logging proxy_pass

nginx `proxy_pass` provides little information even logging with *debug* level.
Rather as a bonus side-effect, consider using [OpenResty](https://openresty.org/en/),
nginx with Lua embedded.

Use Lua `print()` to log whatever variable values and include the Lua code within
nginx `server` directive.

Using the example here, the nginx log file will contain a line like this.
*upstream:* is present only if the request is forwarded to upstream

```
2020/08/21 08:56:32 [notice] 12#12: *11 [lua] log_by_lua(lua.include:6):5: 
 req: /api/v2/hubs/ while logging request, client: 172.17.0.1, 
 server: an.example.com, request: "GET /api/v2/hubs/ HTTP/1.1", 
 upstream: "http://18.200.164.136:80/api/hubs//", host: "an.example.com"
```
 
### Server stanza

```nginx
    server {
        listen       80;
        listen       [::]:80;
        server_name  an.example.com;
        location / {
          proxy_pass http://backend;
        }
        include lua.include;
    }
```

### lua.include

```nginx
log_by_lua_block {
    -- $proxy_host $proxy_port not available to Lua
    -- https://github.com/openresty/lua-nginx-module/issues/301
    print("req: ", ngx.var.request_uri)
}
```
