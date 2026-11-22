# SENAITE database (ZEO) on Wodby

What Wodby sets up for SENAITE's database on this service. It runs the ZEO server from the `wodby/senaite` image, the same image as the SENAITE service, so server and clients use the same ZODB release. The SENAITE service connects to it on port 8100, which is private to the environment. It runs as one replica, as a non-root user with a read-only root file system.

## Data

The database (`filestorage/Data.fs`) and the uploaded files (`blobstorage`) are stored on the `data` volume at `/data`. The database file grows with every change until it is packed; the SENAITE service packs it every week.

## Backups

The **SENAITE database and files** backup runs `senaite-backup` while the server runs: it copies the database up to its last committed transaction with Zope's `repozo` tool and adds the uploaded files, as one `.tar.gz` archive. The database copy is made in the container's temporary space and the archive is written to the `data` volume until it is uploaded, so both the node and the volume need free space about the size of the database.

To restore a backup, stop the app, empty `/data`, unpack the archive into `/data` so it contains `filestorage` and `blobstorage`, and start the app again. Don't unpack it while the server runs.

## Check the result

The server is probed by opening a connection to port 8100.
