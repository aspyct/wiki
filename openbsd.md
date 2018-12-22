# OpenBSD


## Disk label conventions

- a: root of the system (on a boot disk)
- b: swap of the system (on a boot disk)
- c: whole OBSD partition
- i: points to the first MBR partition on the disk


## Formatting a USB drive to be a keydisk

Instructions assume usb drive has been assigned sd1 during boot. See dmesg.
Also, you might have to create the node:

```
cd /dev && sh MAKEDEV sd1
```

First, create the MBR:

```
# fdisk -iy sd1
# disklabel sd1
```

Then, create partitions as needed on the drive. I suggest making a big
MSDOS partition to still be able to use the key (beware untrusted machines),
and an OpenBSD partition at the end to store the keys.

In our example, the disk has 15.133.248 sectors of 512 bytes available.
That's roughly 8Gb. We'll devote 11Mo at the end of the disk to the keys.
We'll need around 22.000 sectors to do that.

The MBR will already contain 1 big OpenBSD partition spanning the whole disk.
We need to resize it.

```
# fdisk -e sd1
> edit 3

-- move partition to offset 15133248 - 22000 = 15111248
-- use all remaining space, i.e. 22000 sectors
-- Also, create the MSDOS partition at the beginning
-- Feel free to leave unused space for further things

> edit 1
-- Set size to 4g

-- Write and quit
> w
> q
```

Now we need to create the labels on the OBSD partition.

```
# disklabel -E sd1

-- Get some info on the drive
-- At this point, labels c and i should already exist
> p

-- Create the label a for the keydisk
> a a
offset: default
size: 2000
FS type: RAID

> w
> q
```


## Reset a FDE disk

If you're trying to setup a disk with bioctl that was already encrypted,
the command will fail. You need to detach the disk first.

That may require a `sh MAKEDEV` command as well.

Careful, this will possibly delete other data on your disk.

```
# bioctl -d sd3
# dd if=/dev/zero of=/dev/rsc0c bs=10m count=1
```

