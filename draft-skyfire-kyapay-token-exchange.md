---
title: "KYAPay Token Exchange"
#abbrev: ""
category: std

docname: draft-skyfire-kyapay-token-exchange-latest
submissiontype: IETF  # also: "independent", "editorial", "IAB", or "IRTF"
#number:
date:
consensus: true
v: 3
# area: AREA
# workgroup: TODO
keyword:
 - agent
 - identity
 - agentic
 - payment
 - commerce
venue:
  github: "skyfire-xyz/draft-skyfire-kyapay-token-exchange"
#  group: WG
#  type: Working Group
#  mail: WG@example.com
#  arch: https://example.com/WG
  latest: "https://skyfire-xyz.github.io/draft-skyfire-kyapay-token-exchange/draft-skyfire-kyapay-token-exchange.html"

author:
-
  name: Ankit Agarwal
  organization: Skyfire Systems Inc.
  email: ankit_agarwal@yahoo.com
  uri: https://skyfire.xyz
-
  ins: M. Jones
  name: Michael B. Jones
  organization: Self-Issued Consulting
  email: michael_b_jones@hotmail.com
  uri: https://self-issued.info/

# see https://github.com/cabo/kramdown-rfc/wiki/Syntax2#authors-contributors

normative:
  RFC8693:

informative:

...

--- abstract

This specification describes how KYAPay tokens can be exchanged for
OAuth access tokens using OAuth Token Exchange
to dynamically grant agents access to resources they need
to accomplish their mission.

--- middle

# Introduction

KYAPay tokens {{?I-D.skyfire-kyapayprofile}}
are used by agents to identify themselves,
the principal they are acting on behalf of,
their payment capabilities,
their authorized scope of action or mission,
and their intended audience.
Agents may need access to resources in order to accomplish their mission.
This specification describes how an agent can use OAuth Token Exchange {{RFC8693}}
to obtain OAuth access tokens granting access to resources
that it needs to accomplish its mission.

## Use Cases for the KYAPay Token Exchange

Enabling agents to create accounts and/or log in to accounts
on behalf of their human principals is a related design goal.
To achieve this, systems can utilize a token exchange workflow {{RFC8693}}.
In this process, a Security Token Service (STS), Identity Provider (IdP),
or OAuth Authorization Server verifies incoming KYA tokens
and extracts claims associated with the human principal, such as email addresses.
The authorization server then performs a token exchange,
swapping the KYA token for a standard OAuth Access Token,
which the agent subsequently uses to interact with the target service.
Crucially, this architecture allows the service to know
that the agent is acting on behalf of the user,
making it possible to differentiate between
direct, human-present sessions and human-initiated, agentic sessions
for authorization, auditing, and security purposes.

Early production deployments of KYAPay tokens are described at https://kyapay.org.

# Conventions and Definitions

{::boilerplate bcp14-tagged}

## Roles

The roles defined in {{?I-D.skyfire-kyapayprofile}} are incorporated into this specification.


# KYAPay Token Exchange

KEY QUESTIONS

## Are we performing RFC 8693 Impersonation or Delegation?

If Impersonation, I assume that the KYAPay token is the `subject_token` value.

If Delegation, there are both `subject_token` and `actor_token` values.
In this case, which token is the KYAPay token?
Would the party for whom the token is being requested the agent identity?
Would the KYAPay token be the Subject Token and contain a `may_act` claim?
(All of this makes me think we're not using the Delegation Token Exchange flow,
but if we are, these questions all need to be answered.)

## What token_type are we using for the KYAPay tokens?

Are we using token_type urn:ietf:params:oauth:token-type:jwt?
Or are we defining a new token_type value such as urn:ietf:params:oauth:token-type:kyapay?
I assume the former is sufficient - especially since KYAPay tokens are already
explicitly typed using the "typ" header parameter.


# Security Considerations

The security considerations defined in {{?I-D.skyfire-kyapayprofile}}
and OAuth Token Exchange {{RFC8693}} apply to this specification.


# Privacy Considerations

The privacy considerations defined in {{?I-D.skyfire-kyapayprofile}}
and OAuth Token Exchange {{RFC8693}} apply to this specification.


# IANA Considerations

This specification requires no actions by IANA.

--- back

# Document History
{: numbered="false"}

[[ to be removed by the RFC Editor before publication as an RFC ]]

-00

* Initial draft.
