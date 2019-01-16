MacFly, an incremental backup tool based on rsync and btrfs snapshot

You must use a full device to set a btrfs file system.

- with a an USB device, identify the /dev device

- with an iscsi device, login to your target, for instance:

  iscsiadm --mode discovery --type sendtargets --portal 192.168.100.7
  iscsiadm --mode node --portal 192.168.100.7 --login \
           --targetname iqn.2012-07.com.lenovoemc:storage.disque-reseau.MacFly

- you can use any filesystem (ext4, NFS, ...) by creating a disk image

  qemu-img create -f raw macfly.img 200G

Then create the btrfs filesystem:

  mkfs -t btrfs --force /dev/sdX

or

  mkfs -t btrfs --force macfly.img

Then find the UUID:

  blkid -t TYPE=btrfs -s UUID

or for the disk image:

  losetup -f macfly.img
  blkid --probe -t TYPE=btrfs -s UUID /dev/loop0

Create /etc/macfly and copy config and filter files in it.

Update /etc/macfly/config to set the correct UUID
Update /etc/macfly/filter to filter files and directory, you want to backup

Copy macfly-update to /etc/cron.hourly/

You can force the first backup by running /etc/cron.hourly/macfly-update.

Then an hourly backup will be done.
Hourly backups are kept during 24 hours,
Dayly backups are kept during 1 week,
Weekly backups are kept until disk space is exhausted.
