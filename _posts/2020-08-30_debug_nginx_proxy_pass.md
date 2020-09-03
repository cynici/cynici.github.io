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

## Location matching optional trailing slash

Use the idiom described in <https://serverfault.com/a/476368>

```nginx
location ~ ^/api/v2/hubs(?:/(.*))?$ {
  proxy_pass http://backend/api/hubs/$1;
}
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

## Testing reverse proxy with curl

Here is a Bash function that uses *curl* to test a reverse proxy.
It prints an output line only if the HTTP status does not equal the expected value.

To use it, copy the function into a script

```bash
# Specify the reverse proxy's actual IP or hostname e.g. 'localhost'
_proxy="${1:-}"

function Curl {
    # Return value is curl exit status, often 0
    # Output string is the useful HTTP status
    [ $# -ge 3 ] || {
        echo "usage: Curl host uri_path expected_status [curl_options]" >&2
        exit 1
    }
    local _host="$1"; shift
    local _path="$1"; shift
    local _expect="$1"; shift

    # Output only HTTP status code
    local _status=$(curl --max-time 1 --silent -o /dev/null --head --write-out '%{http_code}' --header "Host: ${_host}" $@ "http://${_proxy:-$_host}${_path}")
    if [ "${_status}" = "${_expect}" ]; then
        return 0
    else
        echo "$_status: curl --header 'Host: ${_host}' $@ 'http://${_proxy:-$_host}${_path}'" >&2
        return 1
    fi
}
```

In the same script, you can use the function as follows:

```bash
Curl api.example.com /api/v2/clients/2/order 401
Curl api.example.com /clients/3/parcel 403 -u user:password
Curl api.example.com /clients/3/order 404 -u user:password -XPOST
```

```
