# Verify and make bootable USB device from downloaded image

Instructions are here:

- https://www.debian.org/CD/verify
- https://keyring.debian.org/


While importing the GPG key, you may get the following error:

```
$ gpg --keyserver keyring.debian.org --recv-keys 0x673A03E4C1DB921F
gpg: failed to start the dirmngr '/usr/bin/dirmngr': No such file or directory
gpg: connecting dirmngr at '/run/user/1000/gnupg/S.dirmngr' failed: No such file or directory
gpg: keyserver receive failed: No dirmngr
```

You need to install `dirmngr`.

```
sudo apt-get install dirmngr
```

Check the downloaded file for corruption with `sha512sum`.

```
$ sha512sum -c SHA512SUMS --ignore-missing
debian-live-9.6.0-amd64-xfce.iso: OK
```

Then check the signature files. You may need to get the signing key from `keyring.debian.org`. Check the [debian verify page](https://www.debian.org/CD/verify) for updated IDs and fingerprints.

```
$ gpg --keyserver keyring.debian.org --recv 6294BE9B
$ gpg --verify SHA512SUMS.sign
```

GPG will warn you that the key is not trusted. That's correct. Verify the fingerprints against the ones show on the page now. You may also check the fingerprints obtained through a different internet connection, eg. your smartphone's 3G.

When everything is verified properly, make your bootable USB stick. Be careful and select the proper `<device>`, otherwise headaches will ensue.

```
sudo dd if=debian-live-9.6.0-amd64-xfce.iso of=/dev/<device> bs=4M status=progress; sync
```


