# Building a Debian package for Grafana

Building a Grafana package requires some nasty modifications in your system,
so it's advised to build it in a chroot, for example using cowbuilder,
or in a container.
You also need your build system to have direct access to Internet.

The following instructions are using cowbuilder and the resulting package
can be used in Debian jessie, stretch or sid. However, this package
doesn't follow Debian guidelines and it's unsuitable for its inclusion
in the Debian archive.


Create a stretch chroot with cowbuilder

```
$ cowbuilder create --distribution stretch --mirror http://debian/debian
```

Login in the chroot
```
$ cowbuilder login
```

Install all the packages needed to build grafana: build dependencies and
extra packages:
```
$ apt-get update
$ apt-get install -y debhelper libfontconfig1-dev golang nodejs git dh-systemd ca-certificates wget
```

Now is where the ugly modifications come:
```
$ ln -s /usr/bin/nodejs /usr/bin/node
$ wget https://dl.yarnpkg.com/debian/pool/main/y/yarn/yarn_0.19.1_all.deb
$ dpkg -i yarn_0.19.1_all.deb
```

Get Grafana and get the 4.1.1 tag:
```
$ git clone https://github.com/grafana/grafana.git
$ cd grafana
$ git checkout v4.1.1 -b debian-4.1.1
```

Finally, clone this repository, copy the debian/ directory inside your grafana clone
and run from within grafana/
```
$ dpkg-buildpackage -b
```

Your package will be ready in a few minutes if you are lucky and your Internet link isn't
very slow!

# Acknowledgements
A bunch of the packaging has been taken from the current grafana 2.6 package,
thanks to the Debian pkg-go team!
