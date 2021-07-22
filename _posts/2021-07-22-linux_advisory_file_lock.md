# Linux advisory file lock

Linux exposes a file-locking primitive with the command `flock`.
It is advisory in that the kernel does not enforce exclusive file
access - it is entirely up to the processes to check the lock status
before doing any mutually-exclusive file I/O operation.

```
#!/bin/bash

F=/tmp/lockfile
exec 200<$F
flock -sn 200
RETVAL=$?
if [ $RETVAL -ne 1 ] ; then
  echo "Failed to acquire lock; check conflict using 'lsof $F'"
  exit $RETVAL
else
  echo "sleeping with locked $F"
  sleep 60
fi
exit 0
```

`flock -s` acquires shared-lock. Replace it with `flock -x` to acquire
exclusive lock.

Typically, you would write your code such that reader/consumer
can proceed as long as it can acquire the shared-lock.
But a writer/producer must acquire exclusive-lock before
it can proceed so that it does not change the resource state
while there are one or more readers.

Linux offers another lock primitive via syscall `fcntl()` which
does not have CLI interface. Debian/Ubuntu dpkg/apt uses this lock
for exclusive access. See [apt/apt-pkg/contrib/fileutl.cc](https://salsa.debian.org/apt-team/apt/-/blob/main/apt-pkg/contrib/fileutl.cc#L272-315)

## References

- <https://dmorgan.info/posts/linux-lock-files/>
- <https://copyconstruct.medium.com/bash-redirection-fun-with-descriptors-e799ec5a3c16>
- <https://saveriomiroddi.github.io/Handling-the-apt-lock-on-ubuntu-server-installations/>
