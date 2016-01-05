# Splunk Add-on for Bro IDS (JSON version)

This TA is a branch of the original TA distributed by Splunk; however, it utilizes Bro's built-in JSON log writer.

## Why JSON instead of tab-separated files?

The original TA utilizes the `INDEXED_EXTRACTIONS` directive which dramatically increases the amount of storage on disk. At times, this setting can cause the compression ratio of total size on disk to raw data to exceed 3:1 -- which is the inverse of what is supposed to happen. This occurs because the app tells Splunk to index each and every field from every log file that is indexed from Bro.

If your Bro installation is large, this is a problem. For network architecture or cyber security teams, Bro is a mainstay. Most shops elect to turn on most, if not all of the protocol analyzers and may actually add to the log files already being generated.

Moving away from the `INDEXED_EXTRACTIONS` setting and utilizing search time field extractions using the `KV_MODE = JSON` directive, the amount of disk usage by Bro within a Splunk deployment decreases dramatically. This is especially true in clustered environments where data and index replication occur and consume additional storage space.

## How do I log in JSON format?

Simple. Add the following to your `$BRO_HOME/share/bro/site/local.bro` file:

> `@load tuning/json-logs`

Seriously, it's that simple. 

## What if we already logged Bro stuff to the default index from Splunk's official TA?

Yeah, I thought of that too.  This TA requires a new index called `bro_json` and will create new sourcetypes of `bro_json_*`, with `*` being the name of the log file Bro generates. For example, `bro_http` will be `bro_json_http`, `bro_conn` will become `bro_json_conn`, and so on. The events indexed into the `bro` index with their original sourcetypes will remain. The Splunk official TA can run alongside this TA and will not interfere.

To capture all `bro_http` events, use the following search:

> `eventtype="bro_http"`

Or:

> `sourcetype="bro*http"`

Either search string will search for `bro_http` or `bro_json_http` sourcetypes and return those events to you. Eventtypes have been remapped to account for the new sourcetypes, so a search of `eventtype="bro_http"` is mapped to `index="*" (sourcetype="bro_http" OR sourcetype="bro_json_http")`. Also, because the sourcetypes are different, the field extractions for each sourcetype (using Splunk_TA_bro and Bro_TA_json) will perform appropriate field extractions for each sourcetype. This is important for CIM tagging, for example.