I Cut my Kali Linux Boot Time in Half:

While working in my home lab, I used the GParted live ISO (booted in VMware) to expand my virtual disk and resize the root partition. After ejecting the ISO and rebooting into Kali, I noticed the boot process had become painfully slow.

Diagnosis:
I ran these commands to investigate:
- systemd-analyze time  → Showed kernel + userspace taking well over 2 minutes.
- systemd-analyze critical-chain  → Revealed over 1min delays on services like plymouth-quit-wait.service.
- journalctl -b | grep -i timeout → Exposed a 90-second timeout waiting for a device with a missing UUID (starting a0e7e006-...).

The Cause:
During the GParted session, a stray or invalid UUID entry had been added to /etc/fstab. systemd was hanging while trying to mount this non-existent device.
(Note: The default /dev/sr0 CD-ROM entry was also present with noauto, so it wasn't the primary cause of the delay.)

The Fix:
- I backed up fstab first : sudo cp /etc/fstab /etc/fstab.bak
- I edited the file: sudo nano /etc/fstab, and commented out the old UUID line by adding # at the beginning.

Rebooted and verified with: systemd-analyze time

Results:
- Total boot time: ~56 seconds (down from over 2 minutes)
- Userspace: ~13 seconds
- No more timeout errors in the logs


Key Takeaways:
- After using live ISOs or resizing disks, always review /etc/fstab for unexpected entries.
- UUIDs are great for persistent mounts, but verify they exist using blkid.
- Add nofail or noauto to non-critical mounts to prevent boot hangs.
- systemd-analyze and journalctl are essential for diagnosing boot performance issues.
