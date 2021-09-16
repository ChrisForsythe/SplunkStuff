# Indexed Fields

It has become something of a truism that "Indexed field extraction is faster", but it turns out that this is not the case.  There are very limited cases where it provides significant benefit, but misuse will be significantly slower than search time extraction.

## What does it mean?

First, a brief explanation of what it means for fields to be indexed versus search time.  If the fields are indexed, then they are written into the SPlunk tsidx files at the time the data is indexed (okay, it's part of a specific stage of the indexing process).

Search time field extraction is done, as the name implies, at the time of searching.  But searching breaks down the terms in your search to isolate the data that has the key you are looking for, the value you are looking for even without the search time field extraction, so it remains efficient.

## Hidden Indexed Fields

The key value of indexed fields is metadata.  Fields can be added to the data that do not represent anything in the event itself.  The source, sourcetype and index are three such fields.  Additional fields can be added at the time of ingest.  Yes, eventtypes.conf and tags.conf can also add a similar effect.

## Okay, the breakdown

All of these presume the best case scenario, that the search is done prior to the first pipe.

* If you are searching for a bare string, there is no benefit to indexed fields
* If you are searching for a key value pair (`key=value`) and the value is not commonly found except in that key, there is no benefit to indexed fields
* If you are searching for a key value pair that is not present in the raw data, indexed fields are the only way to do that
* If you are searching for a key value pair that the value is very common outside that key (e.g. `mykey=2`), then indexed fields are beneficial.

If your search for a key value pair is after the first pipe, then the above list may not apply, it all depends on what commands you run as to how much it helps.  That's why searches should try and put filtering terms at the start of the search as much as possible.