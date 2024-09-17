---
title: "Maximum Acknowledgment Delay and QUIC Loss Recovery"
abbrev: "Max. Ack Delay & QUIC Loss Recovery"
category: std
docname: draft-eggert-quic-9002-mad-latest
updates: 9002
submissiontype: IETF
v: 3
area: "Web and Internet Transport"
workgroup: "QUIC"
keyword:
 - maximum acknowledgment delay
 - loss delay
 - loss recovery
 - QUIC
venue:
  group: "QUIC"
  mail: "quic@ietf.org"
  github: "larseggert/quic-9002-mad"
  latest: "https://larseggert.github.io/quic-9002-mad/draft-eggert-quic-9002-mad.html"

author:
 -
  name: Lars Eggert
  org: Mozilla
  street: Stenbergintie 12 B
  city: Kauniainen
  code: "02700"
  country: FI
  email: lars@eggert.org
  uri: https://eggert.org/

--- abstract

This document updates {{!RFC9002}} by specifying how the `max_ack_delay` is
incorporated into the computation of the `loss_delay` used for time-based loss
recovery.

--- middle

# Introduction

The maximum acknowledgement delay (`max_ack_delay`) is the maximum amount of
time by which the receiver intends to delay acknowledgments for packets in the
Application Data packet number space, as defined by the eponymous transport
parameter ({{Section 18.2 of ?RFC9000}}).

This document updates {{!RFC9002}} by specifying how the `max_ack_delay` is
incorporated into the computation of the `loss_delay` used for time-based loss
recovery.

# Conventions and Definitions

{::boilerplate bcp14-tagged}

# Updates to {{!RFC9002}}

## Detecting Lost Packets

{{Appendix A.10 of !RFC9002}} contains pseudo code for detecting lost packets in
a packet number space. That code includes a computation of `loss_delay`, i.e., a
time in the past before which all sent packets are deemed lost.

```
loss_delay = kTimeThreshold * max(latest_rtt, smoothed_rtt)
```

This computation of `loss_delay` does not take `max_ack_delay` into account;
`kTimeThreshold` (which is `9/8`) is the only stretch factor that adds some
affordance for delayed ACK delivery.

When the peer ACK delay approaches or (especially) when it exceeds `1/8 * RTT`,
this can cause the sender to declare sent packets as lost, causing unneeded
early retransmissions.

This document updates this calculation of `loss_delay` to take `max_ack_delay`
into account:

```
loss_delay = kTimeThreshold * max(latest_rtt, smoothed_rtt) + max_ack_delay
````

As in {{Section 6.2.1 of !RFC9002}}, when `loss_delay` is calculated for Initial
or Handshake packet number spaces, the `max_ack_delay` in the `loss_delay`
computation is set to 0, since the peer is expected to not delay ACKs in these
packet number spaces intentionally; see {{Section 13.2.1 of !RFC9000}}.

# Security Considerations

No security considerations are introduced by this document.

# IANA Considerations

This document has no IANA actions.


--- back

# Acknowledgments
{:numbered="false"}

TODO acknowledge.
