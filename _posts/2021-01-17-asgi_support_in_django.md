# ASGI support in Django

I was curious about the ASGI support that Django 3 offers an as alternative to WSGI. Here's my very short take of it.

If you have Python web application performance issue and you've not maxed out your database server,

- metrics point to under/over-used core, tune WSGI server e.g. 
  [uWSGI](https://uwsgi-docs.readthedocs.io/en/latest/ThingsToKnow.html)
- metrics point to [C10K problem](http://www.kegel.com/c10k.html), 
  consider replacing WSGI with ASGI instead of ditching Django framework and investment you've made in it. 
  <https://arunrocks.com/a-guide-to-asgi-in-django-30-and-its-performance/>
  
