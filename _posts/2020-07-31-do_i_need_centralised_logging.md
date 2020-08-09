# Do I need centralised logging

Everything comes at a cost. The answer often depends if the benefits are 
compelling enough to outweigh the cost, as is the case for centralised logging.

If you answer YES more than twice in the following questions, I would say
a centralised logging solution will pay for itself pretty quickly.

- Are there more than two developers in your team?

- Does you use more than two servers in production?

- Are there more than two repos that should send out alert in case of an exception?

- Do you use more than two programming languages, frameworks, platforms?

- Is it better (security, accountability, etc.) not to grant 
  every developer access to every server?

- Is it crucial for developers to have access to the logs?

You're most welcome to contribute to this list that I have just rattled off
the top of my head.

Taking a leaf from <https://thevaluable.dev/dry-principle-cost-benefit-example/>,
*more than two* is a recurring theme here.
See [Rule of Three](https://en.wikipedia.org/wiki/Rule_of_three_(computer_programming))
and <https://en.wikipedia.org/wiki/Communication_complexity>.

---
:gift: **Understated benefits of centralised logging**

As soon as you implement centralised logging, every developer in your team 
will feel motivated, if not peer-pressured, to adopt a common logging standard,
error handling, as well as some sort of application/service/module naming convention
because logs are now easily available for all to review.

When flying solo, a log message like this `unknown class type` might suffice
(not necessary good for your own sanity in the long run though). With centralised logging,
any curious peer would pester you to either write a more meaningful message and/or 
provide better inline documentation so that an on-call person would know what/how
to deal with the exception.

---

## Sentry

If you never had a centralised logging solution in place, chances are you would want
to start with a solution that delivers *notesworthy* information from your application - the log
message is for human consumption. You could get by with tagging a log level to each message.
I recommend you get started with Sentry, either [on-premise](https://github.com/getsentry/onpremise)
or [hosted](https://sentry.io/welcome/).

Sentry *is not* designed for log aggregation or metrics collection. Besides exception logging at its core,
Sentry extends into the space of [Application Performance Monitoring](https://sentry.io/for/performance/).
Opentracing appears to be on its roadmap too, starting with Javascript.

Log aggregation for observability is one cornerstone of 
[Software Reliability Engineering](https://landing.google.com/sre/books/).
Amongst others, it enables you to discover unknown unknowns when your application 
fails or under-performs. A good log aggregation solution can subsume the role of Sentry
but at a significantly higher cost in its implementation and maintenance.
To derive maximum benefit from the solution, in contrast to log monitoring,  your log messages
should have consistent structure that can be grokked automatically by rules so that each message
can be split into as many fields with associated data types as possible so that you can
perform statistical analysis by arbitrary field(s).

If I already have a monitoring solution e.g. Zabbix, Icinga, Nagios, Sensu, Graphite/carbon, etc.,
can I not retrofit it for the purpose of exception logging?

In theory, yes. While it is possible to write scripts to produce custom metrics to feed the monitoring agent,
or make the application feed the monitoring server directly, both approaches require non-trivial maintenance
effort to keep the plugins and metrics in sync with the application to reflect different error conditions.
So do not underestimate the instrumentation cost.

What I like about Sentry

- Client-side library in many popular languages
- Different authentication provider through 3rd-party extension
- Handles arbitrary log message containing newlines i.e. log4j, etc.
- Alert conditions to suppress duplicate, Sentry maintains the state of every alert
- A single place to update on-call duty roster to respond application error
- Link to git repository issue board, e.g. Github and Gitlab

What it doesn't do

- Repeating myself here for emphasis - a typically log aggregation solution would offer
  arbitrary multi-dimensional analysis and visualization e.g. map widget, as you get in 
  [ELK](https://www.elastic.co/what-is/elk-stack) or 
  [Graylog](https://www.graylog.org/), which are a lot more resource intensive
  
- Calendar-based rosting, unatended alert escalation [Opsgenie](https://www.atlassian.com/software/opsgenie)






