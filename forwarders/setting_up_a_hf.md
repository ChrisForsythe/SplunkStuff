# Setting up a HF

## When to use a HF?
There are a few use cases where a HF makes sense.  Many of the common cases
seen where HFs are used actually do not call for a HF.  In some cases, it is
an unnecessary point of failure, in other cases, it actively hurts performance,
availability data integrity (therefore security is reduced, not increased).

### Modular inputs
Modular inputs require a full Splunk Enterprise environment, and thus cannot
run from a universal forwarder.  For example, the dbconnect app.

### Fractured Network
If your network is broken^Bsegmented to the point where you cannot route from
your universal forwarders to your indexer tier, then a heavy forwarder can
act as a proxy.  In this case, always have at least as many heavy forwarders
as you have indexers if at all possible.  You don't want the hourglass waist
in your architecture.

The most common legitimate use case for such is you are retrieving via modular
inputs (or similar) data from an internal resource to then forward to
Splunk cloud.  You don't want the Splunk managed resources (such as the IDF)
to have access to your internal network, so you should setup a HF on the
inside which then forwards the data.

### Mobile DCs
This use case is a new one that is not well understood.  In some circumstances,
a mobile collection of computers may go offline for a period of time before
coming back online in a different location with a different network connection.
In this scenario, a small data center's worth of computer systems collects
and processes the logs, caching them for some time in a centralized point
with plenty of disk, before forwarding on to the main indexing tier later.

## Operating System

The most important question is are you doing something that only supports one
operating system?  For example, are you trying to run an input that requires
Windows to operate, such as some of the Windows modular inputs?

If that factor does not determine your operating system sizing, then ask
what is the dominant operating system in your expertise.  Does anyone know
Linux?  If there is no one who knows Linux at all, then Windows is appropriate.

If you have a mix of operating systems, or sufficient background, pick Linux.
It is by far the best supported operating system. 

### Don't forget DNS

Always set an application CNAME up for your forwarder.  Use that heavily so
if you need to replace your heavy forwarder, you just swing the CNAME
around, but with a unique A record, you don't get your heavy forwarders
confused.

### Capacity

The most common capacity issue seen on heavy forwarders is dozens of python
scripts all launching the exact same time.  For example, if you have 54
unique AWS accounts, and are using the AWS app to query them and retrieve
data about each account, the resources in each account, etc., that will be
one python invocation, per account, per data type being retrieved.  Even a
fairly beefy system can be buried under that kind of CPU and memory load.

Space out the inputs.  At this time, there does not exist in Splunk the
ability to swing inputs over a variable period of time (sway time, splay
time, etc.)  Therefore, avoiding this kind of imbalance must be done
manually.

## Use an app
Don't do anything by the GUI

First, remember the rule of configuration, don't touch 
**$SPLUNK_HOME/etc/system/local**.  Always create apps instead.  The apps 
can be very simple, e.g.

```
$SPLUNK_HOME/etc/apps/hf_base
  /default
  /default/outputs.conf
```

But use the app.  Remember precedence matters.  If you use the GUI, you
are editing the etc/system/local (aka *esl*) location, which you cannot readily
override by automation later.

Another tip, do not create your app with any configuration files in the
app's **local** directory.  Save that for passwords (and related
parameters like **Pass4SymmKey**) only.

Why an app, you might ask?  It's so that your fourth and fifth forwarder
configuration updates can be tracked cleanly and much more readily automated.
It also means that when you build a new forwarder, you just deploy all the
apps associated with your heavy forwarder role, set a few passwords and you
are done.

### To Deployment Server or not
If you have a deployment server, use it for your HF.  Just make sure any
"All clients" apps really are appropriate for your heavy forwarder.  If you
use other methods to manage your UFs, you can also use them on your HF.

## Split authentication from outputs
Your HF probably needs the same authentication framework as your indexer
tier, so put that in a dedicated app, don't just copy the files over to your
base HF app.  If using SAML, that can be done by
assigning an admin role to the group for the Splunk admins in the
authentication.conf file.  If you are checking your configs into git, you
probably want to templatize your authentication.conf in some manner so you
can check in a single file.  (Alternative is to have a permanent dedicated
branch for each instance type, with cherry-picking updates into those branches.)

If you are using LDAP authorization and authentication, the procedure is
much the same as the SAML method.  Just follow the same process you used
to set up your indexer, but encode the resulting configuration files.

A few shortcut tips:

Set up one node by GUI, find the configs it writes and use those to create 
an app.  Then, use the app to configure another system.  Once you know that 
works, reconfigure the first one (destroying the GUI created files in
etc/system/local).

If you expect to have to override (using precedence) an app, make the app
start with a literal **z** character and put all the configs for it into 
the app's **default** directory.

The first layer expected to override the **z** app might start with a **y**
to show clear priority.

### Automating authentication system changes
You don't want to restart the forwarder every time you make a change to the
authentication system by configuration file, and you shouldn't do it by GUI
because then you aren't saving it elsewhere to rapidly rebuild your
forwarder when it needs to be replaced.

First, create a role that has the *change_authentication* capability.
Assign a local user to that role.  Add no other roles, not even **user** to that
account.  Now, create a token for that role, recording the token.

Create an alias that uses that token to run the reload authentication command

    splunk reload authentication -token ABCD...

(Insert the actual token in place of the *ABCD...*)

### Automating local account setup

If you use local authentication, you have the added complexity of creating
users.  If using git, consider a template file triggered by a local git
merge hook to create/update the passwd file. 

Now, you can in automation call the above alias to reload authentication after
editing the **SPLUNK_HOME/etc/passwd** file.

In addition, you need to handle the passwords, which are hashed.  Fortunately,
there is a command line short cut, but remember, it will expose the password
briefly to process table information, so it may not be the best option...

    splunk hash-passwd $PASSWD

(Where **$PASSWD** is the actual password.)  Remember, shell interpretation
occurs, so special characters means surround the password in single quotes,
or otherwise protect it.

### Final words on local authentication automation

Find a SAML provider.  It really makes things easier.  Set it up for your
heavy forwarders even if you only expect two people to ever login.

## Monitoring

Right now, there is no role in the monitoring console for a heavy forwarder.
But the thing you are most interested in, the queue fill ratios, is 
exposed via the indexer role.  So create a special group of indexers for
your HFs, and add each HF to the monitoring console into that group.
