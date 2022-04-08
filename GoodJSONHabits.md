# Good JSON Habits

The question regularly comes up, what are good habits or patterns of behavior for ingesting JSON?

## The Habits
### The entire event should be one JSON object

Ensure each event, the entirety of the event is a single JSON object.  Don't prepend a timestamp outside the event, or have some key value pairs with some keys holding JSON.  Either go all JSON for the whole event, or no JSON at all.

### The first field should be the timestamp for easier recognition

While you can tell Splunk where to look for the timestamp, make it easy on Splunk and yourself.  Make the very first field the timestamp.

### Make the timestamp a well known format (ISO-8601)

There are jokes about time formats, but really, use ISO-8601.  Do not use shorthand abbreviations for timezone like "cst".  They are not unique across the world.  Either use the actual offset (e.g. -0700), or one of the styles of formal names.  Even better if you can have your logs written in UTC, but it is realized that in some cases that may not be feasible (e.g. apps required to run in a specific time zone and their own logs).

### Make the JSON not pretty-printed

Pretty printing, where the JSON event is not written as a single line, but spread over many lines with indents, makes it easier for a human to read, but complicates data ingest significantly.  By using a single line per JSON event, you cause the default line breaker to work of a newline.

### One JSON object per event

Some applications like to create a JSON object that just contains an array of objects, with each inner object being one log event.  Don't do that.  Instead, just log the inner objects.  If you have to, preprocess the log to strip out the outer layers.

### Check for binary data inside the JSON

A few applications like to encode in one of the fields of the JSON either pure binary data, or MIME encoded binary data.  Both of those are unlikely to be of value.  Don't log such things unless you really mean it.

### Don't have stringified JSON as the contents of one of the fields.

A strange practice sometimes seen is to have one JSON field contain a string that is a string of a JSON object, known as stringified JSON.  Expand that out so that Splunk (and you) can easily process the data.  

### Ensure the JSON is well formed

This should go without saying, but it often is a problem.  The JSON should all be valid JSON.  Avoid edge case JSON as documented by http://seriot.ch/projects/parsing_json.html  If you need one of the edge cases that are exemplified there as not consistently understood, seriously reconsider what you are doing and why.