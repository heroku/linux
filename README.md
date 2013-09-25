This is a github clone of the heroku kernel build and config for EC2.  If we need to patch the kernel, it will be recorded here.

To build .debs for this:

```bash
cp heroku-config .config
echo > .scmversion
CONCURRENCY_LEVEL=4 fakeroot make-kpkg --append-to-version=-ec2 --revision=1heroku1 binary-arch
```

After building debian packages, you will want to upload them to most likely S3 somewhere, so bob-production can build you a template image.  I use s3cmd on an amazon account for that, as follows, for example:
```bash
KVER=3.11-1-ec2_1heroku1
s3cmd mb -P s3://linux-$KVER #make public bucket
s3cmd put -P linux*$KVER*.deb s3://linux-$KVER
```

After that, you will want to edit/push a matching build_template_KVER.sh in
user_data in our bob-production app, and then have bob build you a template. As
of the time of this writing, you can copy one of the 3.x build_template*.sh
scripts to a new one, matching your target kernel release, and edit the
KERNEL_BUCKET, KERNEL_FLAVOR_VERSION, and KERNEL_BASE_VERSION lines (near line
106, immediately before  the 'arch kernel packages' stanza, to match your
freshly uploaded kernel version.  If darwin hasn't been broken, you will then
soon have a new, completed build in bob, which you can then share out to
devclouds & staging.
