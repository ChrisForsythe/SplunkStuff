This repository is meant to host general purpose examples of how to perform tasks within Splunk. Dashboards in simplexml and the new json format. Some standards in coding should be attempted to be adhered to in order to make things easier.

The intention of this small list style suggestions is to make it easier to understand what is happening where possible. Your commit/push privs will not be revoked due to lack of adherence by any means.

If you have suggestions for improvements to the guide you can either find me or write up an issue on github.

## Before pushing code.
Be sure to try to hit ctrl+\ or cmd+\ on a mac to let the editor tidy up the code in splunk web. 

For dashboards where you have edited the simplexml by hand, you can do this same thing by editing the code through a panel.

## Comments
Comments should be written in the new format, starting with \`\`\` and ending with \`\`\`


## Formatting commands
Stats, eval, replace and any other command which can be formatted in the following manner should be:

```
| stats
    count(field1) AS field1,
    sum(field2) AS field2,
    dc(field3) AS field3
    BY
    field4,
    field5,
    field6
```

The above implements the following:

- Commas after every line where possible.
- A new line for each stats and the by fields.
- The BY is capitalized.

While this adds lines 7 lines of code in the above example, it also makes it easier to read what is happening.

A related example would be the case statement:

```
| foo=case(
    condition1,value1,
    condition2,value2,
    condition3,value3,
    condition4,value4
)
```

In this way, the case statement has each condition on a line of its own, never
sharing with another line.  If you choose to put values on their own line,
superindent them to make it clear which is the condition, which is the value:

```
| eval foo=case(
  condition1,
    value1,
  condition2,
    value2,
  condition3,
    value3,
  condition4,
    value4
)
```
Especially with long or complex case statements, it can be easy to lose track
of what the condition versus value is.  Your key guide should be legibility.
