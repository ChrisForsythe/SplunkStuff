# JSON lists
You have a JSON object that might look something like
{
    "accessRules": [
        {
            "name": "permit syslog",
            "protocol": "udp",
            "port": "514",
            "action": "allow",
            "source": "0.0.0.0/0",
            "destination": "syslog.example.com"
        },
        {
            "name": "permit splunk forwarder traffic",
            "protocol": "tcp",
            "port": "9997",
            "action": "allow",
            "source": "0.0.0.0/0",
            "destination": "splunk-indexers.example.com"
        },
        {
            "name": "permit syslHECog",
            "protocol": "tcp",
            "port": "8088",
            "action": "allow",
            "source": "0.0.0.0/0",
            "destination": "splunk-indexers.example.com"
        }
    ]
}

What's important here is that there is an object that forms one event, and that object has a list of objects inside it.  Don't overthink this.

## The Problem

We want to parse parallel fields and link them together, in the above example, perhaps the port and protocol for each of the objects in the list.

## The solution

The **spath** command is your friend here.

    ...
    | spath path=accessRules output=onerule
    | mvexpand onerule
    | spath input=onerule

What this snippet does is take the accessRules object and parses it separately and puts the results into the field onerule.  Each of the three (in the above example) objects are put in the onerule field, multivalue.  The mvexpand command then splits the event into multiple events based on the different values of the onerule field.  Now, a last spath command, this time input from the onerule field, will reparse that and interpret it, creating fields out of it for the events.

The result, given the above as a single event is you have three events, but each event has a separate name, protocol, port, action, source and destination field, keeping the linkage together just as described in the objects in the list.