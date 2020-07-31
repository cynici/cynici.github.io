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
*more than two* is a recurring theme here. It could have been rephrased as
*three or more*, <https://en.wikipedia.org/wiki/Communication_complexity>.

---
:gift: **Understated benefits of centralise logging**

As soon as you implement centralised logging, every developer in your team 
will feel motivated, if not peer-pressured, to adopt a common logging standard,
error handling, as well as some sort of application/service/module naming convention
because logs are now easily available for all to review.  

---

## Sentry

If you never had a centralised logging solution in place, chances are you would want
to start with a solution that delivers *notesworthy* information from your application - the log
message is for human consumption. You could get by with tagging a log level to each message.
I recommend you get started with Sentry, either [on-premise](https://github.com/getsentry/onpremise)
or [hosted](https://sentry.io/welcome/).

Sentry *is not* designed for log aggregation where you would want the solution to parse 
the log message for structure and semantics and break it down into as many fields as
possible so that you can perform analysis by arbitrary field(s).

Log aggregation is a cornerstone of observability which you might need to discover
unknown unknowns when your application fails. But that is an orthogonal discourse
in [Software Reliability Engineering](https://landing.google.com/sre/books/)


What I like about it

- Client-side library in many popular languages
- Different authentication provider through 3rd-party extension
- Handles arbitrary log message containing newlines i.e. log4j, etc.
- Alert conditions to suppress duplicate, Sentry maintains the state of every alert
- A single place to update on-call duty roster to respond application error
- Link to git repository issue board, e.g. Github and Gitlab

What it doesn't do

- Repeating myself here for emphasis a typically log aggregation solution would offer
  arbitrary multi-dimensional analysis and visualization e.g. map widget, as you get in 
  [ELK](https://www.elastic.co/what-is/elk-stack) or 
  [Graylog](https://www.graylog.org/), which are a lot more resource intensive
  
- On-call escalation [Opsgenie](https://www.atlassian.com/software/opsgenie)






