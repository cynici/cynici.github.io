# Use DNS SRV records

If you manage a DNS server or zone, consider using DNS SRV records
as a means for service consumers (client) to find a service provider 
(server) e.g. API endpoints, database server, etc. so that you can
fail-over or scale out easily when the need arises.

Define separate SRV records based on logically distinct parts
of your application, e.g. microservices,  even if they all resolve
to the same server + port.

Think of a DNS SRV record as improved version of CNAME record where:

- service-type, protocol (tcp/udp) are explicit in the SRV name
- a successful query response includes the server CNAME, time-to-live,
  port number, numeric priority and weight

Your application may arbitrarily decide how to what to make of the
weight value when a response contains multiple answers.

Example SRV entries:

```
_postgresql._tcp.master.hello-world-application.example.com
_mysql._tcp.master-wordpress.example.com
_http._tcp.api.killer-app.example.com
```

Of course, you'll have to write your client-side in such a way that
they always perform DNS SRV lookup before connecting to a server.

Once you've adopted this method of indirection, you can move services
around servers.  The clients may be scattered all over the place  but
you can rest assured that they can find their way to the server
as soon as SRV records are updated.

