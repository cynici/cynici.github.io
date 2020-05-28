# Use DNS SRV for service discovery

If you manage a DNS server or zone, consider using DNS SRV records
as a means for service consumers (client) to find a service provider 
(server) e.g. API endpoints, database server, etc. so that you can
fail-over or scale out easily when the need arises.

Define separate SRV records based on logically distinct parts
of your application, e.g. microservices,  even if they all resolve
to the same server + port.

Think of a DNS SRV record as improved version of CNAME record where:

- service type and Internet protocol family (tcp/udp) are explicit
  in a DNS SRV query
- a successful query response includes time-to-live, priority, weight,
  port number and CNAME

Your application may arbitrarily decide what to make of the
weight value when a response contains multiple answers.

Example SRV entries:

```
_postgresql._tcp.master.hello-world-application.example.com 300 IN SRV 0 1 5432 spof.example.com.

_mysql._tcp.master-wordpress.example.com 300 IN SRV 0 1 3306 spof.example.com.

_http._tcp.api.killer-app.example.com 300 IN SRV 0 1 80 spof.example.com.
```

Unlike CNAME, your client code can't just connect to a SRV record.
You'll have to write your client-side in such a way that
it always perform DNS SRV lookup before connecting to the server
port number by its CNAME as provided in the query response.

Having implemented this method of indirection, you have the flexibility
to services around different servers and ports. The clients may be
scattered all over the place  but you can rest assured that they
can find their way to the service provider as soon as SRV records are updated.

Even though non-hostname part of a DNS record may use underscore `_`
character but I recommend sticking to hyphen `-` and only use underscore
in the service type and protocol family of SRV records, for consistency
and your own sanity.
