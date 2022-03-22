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

### Language
While everyone thinks Python first, Python is hardly the only supported language.  In fact, it may be a bad choice because by default, you will likely end up using the Splunk built-in Python.  That means that you don't have the easy ability to add libraries that aren't provided by Splunk's own Python.  Ask yourself instead:
* What languages does the team know reasonably?
* What language well supports the problem to be solved?
Let those two questions guide the decision of language choice.  If you do choose python, consider either a wrapper script to call an external python, or another method (e.g. pyden) to ensure you can use your libraries you need.  If in doubt, test by

    splunk cmd python

then in the python prompt that comes up, import each library you wish to use.  If that errors out, you can't casually pip install the library, you need to think of an alternative approach.

### Arguments

Splunk expects to always send the `--execute` option to the alert action.  Check for it, consider using that as a way to disable actually taking action if missing.

### STDOUT/STDERR

Write messages about what the alert did to STDOUT and STDERR as usual.  Let Splunk take those up.