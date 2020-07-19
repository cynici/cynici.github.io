# Pin dependencies

On one Friday afternoon, some Kubernetes pods failed to roll out 
with an updated image based on Python stack - they ended up in the dreaded
[CrashLoopBackoff](https://managedkube.com/kubernetes/pod/failure/crashloopbackoff/k8sbot/troubleshooting/2019/02/12/pod-failure-crashloopbackoff.html)
status.

It took a bit of sleuth work to identify the cause - the new image is
dependent on another Python module developed in-house, explicitly pinned
with a specific version. Unfortunately, that wasn't enough.

Even though this module has not changed for over 2 years,
it has not pinned **every one** of its dependencies,
and one of them has a breaking change in a recent release.

We were fortunate that the code path which uses this dependent module
was executed on start-up and crashes immediately. Otherwise, the issue
would have hidden itself like a ticking time-bomb, and many other
images would have included this broken module causing a wider impact.

When and how to pin your dependencies?

- <https://www.promptworks.com/blog/pin-all-dependencies>
- <https://before-you-ship.18f.gov/infrastructure/pinning-dependencies/>

`pip freeze` does not include system packages provided by the distribution
e.g. python3-yaml, etc. Depending on the package management system of
the distribution, there are various ways to pin package version,
<https://help.ubuntu.com/community/PinningHowto>, etc. For stable releases,
the distribution packagers tend to be ultra conservative and do
their best to maintain compatiblity so it *might be* worth the risk
not to pin your packages to benefit from security and bug fixes.

ps. There is always an exception, when the benefits of tracking the
*head* of dependencies outweighs the costs, as in the case of
Netflix using *head* release of FreeBSD,
<https://archive.fosdem.org/2019/schedule/event/netflix_freebsd/attachments/slides/3103/export/events/attachments/netflix_freebsd/slides/3103/FOSDEM_2019_Netflix_and_FreeBSD.pdf>
