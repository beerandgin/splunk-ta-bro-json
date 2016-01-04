# Splunk Add-on for Bro IDS (JSON version)

This TA is a branch of the original TA distributed by Splunk; however, it utilizes Bro's built-in JSON log writer.

## Why JSON instead of tab-separated files?

The original TA utilizes the `INDEXED_EXTRACTIONS` directive which dramatically increases the amount of storage on disk.  At times, this setting can cause the compression ratio of total size on disk to raw data to exceed 3:1 -- which is the inverse of what is supposed to happen.  This occurs because the app tells Splunk to index each and every field from every log file that is indexed from Bro.

If your Bro installation is large, this is a problem.  For network architecture or cyber security teams, Bro is a mainstay.  Most shops elect to turn on most, if not all of the protocol analyzers and may actually add to the log files already being generated.

Moving away from the `INDEXED_EXTRACTIONS` setting and utilizing search time field extractions using the `KV_MODE = JSON` directive, the amount of disk usage by Bro within a Splunk deployment decreases dramatically.  This is especially true in clustered environments where data and index replication occur and consume additional storage space.