# Backup your files with `tar`

First of all shoutout to John Gilmore and all the people who contributed to `tar` - it is such a useful tool and part of [GNU](https://www.gnu.org/). It is specifically useful for backups and packages. `tar` by itself stands for "taped archive" and is also how the command on the commandline interface is called by.

Mainly it "bundles" together a whole directory tree into one easier-to-handle file - the famous tarball. On top of that the commandline tool also has a variety of compression algorithms to apply to the tarball. 

We want to backup our data of our home directory. We want to optimize:
- Size, to keep around as many backups as possible and upload it to the cloud.
- Speed, we don't want to keep our Machine running overnight for this task. 

The magic command we use is:
```bash
tar -I "zstd -5" -cpf  /backups/backup-$(date +%d-%m-%Y).tar.zst ~
```

## Our chosen Flags

- `-I "zstd -5"` specifies the exact compression algorithm `zstd` with compression level `5`. This is our preferred tradeoff to time consumption vs. compression size.
- `c` creates a new tarball.
- `p` keep file permissions.
- `f` specifies that a file is used for the output of the tarball.

**Notice: The order of the Flags is important!** (Or specifically the place of the `f`.)

## Further steps

- Encryption - probably we don't trust the place where our backup is laying around and we have sensitive information.