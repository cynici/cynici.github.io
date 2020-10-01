# How to protect a file on Linux against accidental delete/modify

... even from superuser

Use `chattr`

```
touch foobar
lsattr foobar     # show attributes
chattr +i foobar  # set immutable attribute
lsattr foobar
# rm, edit foobar all you want, you will fail
chattr -i foobar  # clear immutable attribute
```

For once, [Wikipedia](https://en.wikipedia.org/wiki/Chattr) more readable than the venerable manpages.

Not to be confused with [ACLs](https://linuxconfig.org/how-to-manage-acls-on-linux) which only works 
when a filesystem is mounted with `acl` option.
