# Custom Alerts
Unfortunately, Splunk documentation in this area is in bad need of an overhaul.  This document outlines some lessons learned the hard way.

## Payload

Splunk delivers the configuration of the alert as well as the location of the events and other items, all in an XML (or JSON) structure that is sent to STDIN.  In XML terms, expect to see a root level element called `<alert>` that will contain several descendent elements of importance.

### results_file

The `<results_file>` element contains the path to a gzipped CSV file containing the events that triggered the alert.

### configuration

The `<configuration>` element contains the configuration of the alert.

### session_key

If you need to authenticate inside Splunk, you will have a `<session_key>` element.

## Scripts
Your script should read the STDIN and do whatever it needs to do.  

### Arguments

Splunk expects to always send the `--execute` option to the alert action.  Check for it

### STDOUT/STDERR

Write messages about what the alert did to STDOUT and STDERR as usual.  Let Splunk take those up.