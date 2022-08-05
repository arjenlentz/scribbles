# On the subject of logging

This doc aims to capture the range of issues that are relevant to logging.

Lots of logging mechanisms, and aggregation and analysis tools exist, some decades old, some recent.
Mostly great stuff, but they don't appear to cover current insights in for instance security, forensics, and compliance.
And that, of course, is a problem.

Aspects are outlined as a "thought stream", so the logic trail is visible (and can be argued, if necessary), make it clear what the fundamental assumptions are, and how a particular conslusion is reached. This way, it is possible to identify and revisit an issue, should any of the underlying factors change later.

Along the way we will revisit some things that have indeed been around for a long time, but it's good to check in and validate that it's still the best way to go about it - i.e., the original reasoning still holds. In many cases, we have to go through the process to figure out what the original reasoning might have been, as it wasn't documented at the time. There might be other references (emails, etc - happy to receive pull requests with suitable references!) but it is felt that it's still good to capture all this in a narrative, in the one place.


## Time Zones and Daylight Savings

Time zones and daylight savings periods are simply a monster, they cause all kinds of grief when processing and analysing logs as well as when correlating logs from servers that "live in" different time zones.
Best practice is this rule: all servers should run in UTC. Yep, it's that simple. Do it.

For applications on those systems: where data needs to be presented to users in their local time, that is really an application issue, not a system issue. Don't try to solve the problem in the wrong place, as that just causes more trouble. The system doesn't know in advance which user from which timezone is going to look at the data! You keep the data in UTC, and convert it for display according to the timezone information of the user.


## Security

Note that when we use the term "malicous actor", this can be an external intruder, or a trusted insider.


### Event Logging

For event logging to be secure, storing them only locally on a system makes no sense. It may not even be relevant to store them locally at all (that can save some management hassles, but not an issue for this doc).
So events need to be sent "elsewhere", that is absolutely fundamental - otherwise an malicious actor can modify or delete them.

From this also follows that new events need to be sent out of the originating system as soon as possible.
No bundling or waiting for other reasons. Immediately.
This may not be the most efficient, but nothing else will work in terms of achieving the security objective.

Could a malicious actor send out false events? Example: similar to a CCTV camera system, they could view "normal" events for a bit, create a "loop" and then get the logging system to send those out rather than the regular ones. It's possible, but it would require quite a bit of crafting time in a system (capturing, intercepting, blocking) and guessing what kind of analysis pattern is used. In summary: it's so unlikely that the cost of us attempting to specifically mitigate for it is not worthwhile.


### Logging Relays

If event logging runs via a relay (often more practical, or necessary), we want to be sure that a compromised relay cannot mess with the events either.

Looking at the issue here, one possible solution appears to present itself: Signing. The originating system could sign its event messages before sending it out. This might be relatively "costly" (CPU cycles) on an originating system, but it's effective for enabling a downstream collector to catching modification on an intermediate (relay) system.

Cost aside, event messages that are only signed are readable to others. Thus, a compromised relay could view (more) event messages from other systems. If we were to use symmetric encryption instead, it would work out "cheaper" (in CPU terms) as well as resolving the problem of the relay reading messages. Instead, it would only handle "sealed envelopes".

Encrypting rather than signing will likely prevent a damaged message from being read at all, but realistically it doesn't make much difference. If it was maliciously altered, the original content is lost either way. The transport layer (TCP) should take care of the messsage integrity otherwise (although we do know from experience that this is not always the case. Having some form of check inside our own format remains wise).

#### Filtering/Modifications on a Relay

If we get an event off the originating system quickly, out of reach of a malicious actor there, but may not want to send through all events, filtering may be desirable.
 - If events are encrypted in full, this is obviously impossible.
 - If some aspects are unencrypted, filtering on those aspects becomes possible, at the expense of some security. If event messages are passed via TLS, only the originating system and the relay have access to the unencrypted bits, and that was already the case.

If events are sequenced, we do have to let the final destination know that an event was removed. So some overhead is still incurred. This could be made safer by the relay signing the new message, although that requires the relay to be trusted where it previously would not have to be.

If we allow events to be modified according to some ruleset(s), those modified events also need to be signed by the relay.

Why filter or modify? It is important to consider this, particularly as there appear to be substantial consequences.
Historically, two factors have been the most relevant:
 - network bandwidth or cost between DCs.
 - processing or storage capacity (or cost) at the receiving end. If we're not going to store certain log entries, why send them around first?

Pure bandwidth, and processing and storage costs are very low these days (2022), it's only licencing aspects of a product that might interfere.
If there is no licence cost constraint, it may be better to keep all the data. Destruction of data is bad in forensic terms. You can not known in advance what data may be needed.

### Log Collectors

From a log collector's perspective we still have to consider the "no news" scenario: a malicious actor can prevent events from being sent out.
How can a receiver catch this?
If we add a sequence number, a collector can catch missing entries. It won't know what they are, but it will know that there are entries missing, and how many. If we also add a timestamp, the collector work out the timerange of the missing entries.


### syslog

Yep, syslog ([RFC-5424](https://datatracker.ietf.org/doc/html/rfc5424)) has an event message, sequence number, and a (sub-second) timestamp. Excellent. We'll keep that in mind.

The above terminology of "originator", "relay", and "collector" also comes from this RFC. And we've arrived at a similar outcome to the "Security Requirements" section in [RFC-5425](https://datatracker.ietf.org/doc/html/rfc5425).
It makes sense. Cool. We don't want to be re-inventing the wheel.

Interestingly, from the above, it may be that it may NOT make sense to
 - worry about signing event messages (IETF also had https://tools.ietf.org/id/draft-ietf-syslog-sign-23.html which expired in 2008);
 - use TLS rather than plain TCP connections - because TLS only encrypts hops, it does not prevent a compromised relay. E.g., if we encrypt entire event messages, it no longer matters whether the transport layer is encrypted.

We'll consider TLS more later, and virtual network layers such as WireGuard.

