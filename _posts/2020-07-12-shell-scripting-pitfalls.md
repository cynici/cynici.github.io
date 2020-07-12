# Shell scripting pitfalls

The ubiquity of [POSIX-compliant](https://en.wikipedia.org/wiki/POSIX)
shell interpreters makes shell scripts the most convenient and obvious
tool of choice for automating tasks. The wide availability of existing
recipes and how-tos on the Internet has lowered the barrier of entry
even more.

In spite of the standard, a number of obscure but crucial
details have eluded many shell programmers - details which make it
extremely difficult to write scripts that can securely
handle potentially malicious input or behave correctly in an environment
vastly different from the programmers'.

You'll agree with me after reading the
[Rich's sh (POSIX shell) tricks](https://www.etalabs.net/sh_tricks.html).
For me, the article has demystefied a number of shell oddities that
have tripped me up in the past.

