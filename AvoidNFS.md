# NFS

The Network File System (NFS) is not as good as you think.

## Locking
NFS doesn't support file locking in the POSIX sense.  Instead, a separate RPC to try and use the Network Lock Manager (NLM) is used.  It doesn't work the same.
  Remember, the Splunk forwarders do not know that they are looking at a NFS
mount, so strange behavior caused by network issues can cause odd behavior.

Why do you care?  You can't guarantee consistency of files read from NFS.

NFSv4.1 does add some significant improvements in locking.

### Mount options

Soft mounts (return an error on failure) are not supported at all, even for
forwarders.

Hard mounts will, by design, hang the request until the network mount returns.
There is no guarantee of consistent results, but it does reduce the uncaught
errors.

### Indexers
Don't.  Just, don't.  

The list (see References below) of things required for NFS is actually just a
smattering of what is required.

## Security
Almost no one uses the kerberos (KRB5) security modes as required for any NFS
security.  As a result, anyone who can talk to the mountd port, 2049, can forge
a mount request and get full access to the mount.  There is no actual
authentication of the incoming mount requests.

Even if you ignore that very serious problem, without the KRB5 security,
the client, not the server, decides who you are and what groups you are a
member of.  That information is transmitted as a part of the access request,
with a limit of fifteen group memberships.  

Kerberized NFS is very difficult to set up, to the point that it is a
unicorn, often talked about, seldom if ever seen.

Why do you care?  Anyone can access and alter any file on NFS unless you
mandate KRB5 security, which almost no one does.

### Why no one does KRB5

To setup KRB5 NFS, every client must be joined to the kerberos realm, and the
NFS server must be fully integrated into the realm.  While that sounds doable
on paper, many popular NFS appliances have significant issues with
implementation that have made it all but impossible.  

# References

* https://docs.splunk.com/Documentation/Splunk/8.2.0/Installation/Systemrequirements#:~:text=Considerations%20regarding%20Network%20File%20System
