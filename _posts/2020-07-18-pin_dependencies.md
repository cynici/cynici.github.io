# Pin dependencies

On a Friday afternoon, some Kubernetes pods failed to deploy
with newer images based on Python stack with the dreaded
[CrashLoopBackoff](https://managedkube.com/kubernetes/pod/failure/crashloopbackoff/k8sbot/troubleshooting/2019/02/12/pod-failure-crashloopbackoff.html)
status.

It took a bit of sleuth work to identify the cause - the new image is
dependent on another Python module developed in-house, explicitly pinned
with a specific version. Unfortunately, that wasn't enough.

Even though this module has not changed for over 2 years,
it has not pinned **every one** of its dependencies,
and one of them has a breaking change.

We were fortunate that the code path which uses this dependent module
was executed on start-up and crashes immediately. Otherwise, the issue
would have hidden itself like a ticking time-bomb, and many other
images would have included this broken module causing a worse impact.

When and how to pin your dependencies?

- <https://www.promptworks.com/blog/pin-all-dependencies>
- <https://before-you-ship.18f.gov/infrastructure/pinning-dependencies/>

