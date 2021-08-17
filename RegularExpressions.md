# Regular Expressions in Splunk

Splunk utilizes Regular Expressions (regex) in multiple places. This document does not cover how to use regex itself, just the differences between PCRE (Perl Compatible Regular Expressions) and usage of regex in Splunk


## Areas of Regex Usage



###### REX command

The rex command is used within Splunk for inline field creation/parsing at search time. The key differences between PCRE and regex within rex are:

Some examples include:

###### REGEX Command

The regex command within Splunk is used for filtering based on a regex. The rules from the rex command apply to the regex command.

###### Transforms.conf

Placeholder text for lorum ipsum