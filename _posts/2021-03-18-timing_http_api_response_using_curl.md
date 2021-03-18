# Timing HTTP API response using Curl 

Create a curl template

```text
time_namelookup:  %{time_namelookup}s\n
        time_connect:  %{time_connect}s\n
     time_appconnect:  %{time_appconnect}s\n
    time_pretransfer:  %{time_pretransfer}s\n
       time_redirect:  %{time_redirect}s\n
  time_starttransfer:  %{time_starttransfer}s\n
                     ----------\n
          time_total:  %{time_total}s\n
```

Run curl in a loop

```sh
for i in {0..1000}; do 
  curl -w "@curl.txt" -o /dev/null -s "https://api.example.com/"
done
```
