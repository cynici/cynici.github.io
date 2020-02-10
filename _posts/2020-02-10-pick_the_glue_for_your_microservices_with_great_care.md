# Pick the glue for your microservices with great care

*Fine prints don't really matter until they do.*

Recently I had to update a system that comprises three services that are loosely-coupled in a common work queue pattern:
- Two unrelated services, A and B, which independently submit job requests whenever
- All job requests go into one first-in-first-out queue
- Worker service C executes job requests in the queue sequentially

Initially, for a quick proof-of-concept, a minimalist (read: naive) and lightweight Python library named RQ was chosen 
as the standard API for all three services to implement the worker queue functionality. Another selling point of RQ is
that it uses Redis underneath the hood, so services A, B and C can run on different servers. However, RQ comes with 
this fine print which didn't seem like a deal-breaker at that time:
> it does not speak a portable protocol, since it depends on pickle to serialize the jobs

Feeling smug with the microservice architecture, I thought the exercise to migrate service A from Python 2 to 3 
would be a stroll in the park. Unfortunately, soon after deploying the updated service A to production, I started getting exceptions from service C 
as it tries to consume job requests from the work queue:
> ValueError: unsupported pickle protocol: 4

It turned out that Python 3.7 uses the new pickle protocol version 4 while service C (still on Python 2.7) 
only understands protocol version 2.

To make matter worse, the shortsighted RQ library does not let allow the pickle protocol version to be specified explicitly 
to guard against breaking changes like this.

I guess you catch the drift now. When I upgrade service C, service B will also scream murder.

So, despite a sound microservice design, my poor choice of a seemingly insignificant piece of underlying software has plunged 
the system into a dependency hell, which could have been avoided if I had paid more attention to the fine print.

The take-home from this lesson are the criteria of a good glue, apart from it fulfilling all functional requirements:
- availability of sane and stable API for common languages
- architecture independence
- traceability
- explicit protocol versioning and/or backward compatibility
- *"Keep it as simple as possible but not any simpler"*, quoting Einstein
