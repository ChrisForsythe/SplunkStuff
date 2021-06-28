# Validating Dashboards

> This validation progress is still a work in progress 

While often, the javascript based GUI is fine for creating your XML, many times
that is not an acceptable option.  It is also nice to be able to integrate
automation of validation that no changes occurred to your XML that would break
your dashboards.

In theory, one can use 
**$SPLUNK_HOME/share/splunk/search_msparkle/exposed/schema/simplexml.rng**, in 
practice the file is broken and efforts to convince Splunk to fix this file 
have not been successful.

So, while a work in progress, this simplexml.rng is better than what Splunk
provides.  It is based on valid dashboards, the existing simplexml.rng and
the Javascript **parser_config.json** (which does not follow any known 
standard so cannot be directly used.)  

## Using the validator

There are two programs worth mentioning only.

* jing
* xmllint

The jing program is heavier with many more dependencies, but provides more
precise and verbose messages.  It is recommended primarily for those fixing
the simplexml.rng file.  Using jing is fairly straight forward:

    jing ${PATH_TO_FILE}/simplexml.rng ${APP_HOME}/*/data/ui/views/*.xml

The xmllint program is widespread, and can readily be used with automation.

    xmllint --relaxng ${PATH_TO_FILE}/simplexml.rng ${APP_HOME}/*/data/ui/views/*.xml

Both give useful exit codes for automation.
