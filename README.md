This is a github clone of the heroku kernel build and config for EC2.  If we need to patch the kernel, it will be recorded here.

To build .debs for this:

```bash
cp heroku-config .config
CONCURRENCY_LEVEL=4 fakeroot make-kpkg --append-to-version=-ec2 --revision=1heroku1 binary-arch
```
