---
title: "KYAPay Token Exchange"
#abbrev: ""
category: std

docname: draft-skyfire-oauth-kyapay-token-exchange-latest
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
  github: "skyfire-xyz/draft-skyfire-oauth-kyapay-token-exchange"
#  group: WG
#  type: Working Group
#  mail: WG@example.com
#  arch: https://example.com/WG
  latest: "https://skyfire-xyz.github.io/draft-skyfire-oauth-kyapay-token-exchange/draft-skyfire-oauth-kyapay-token-exchange.html"

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
  MCP:
    target: https://modelcontextprotocol.io/specification/2025-11-25
    title: Model Context Protocol Specification
    date: November 25, 2025

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

One example use case is to exchange a KYAPay token for an OAuth access token
for a Model Context Protocol {{MCP}} service
when the agent needs to use the MCP service to accomplish its goals.

Early production deployments of KYAPay tokens are described at https://kyapay.org.

# Conventions and Definitions

{::boilerplate bcp14-tagged}

## Roles

The roles defined in {{?I-D.skyfire-kyapayprofile}} are incorporated into this specification.


# KYAPay Token Exchange

This specification defines the following token type identifier URN.

`urn:ietf:params:oauth:token-type:kyapay`:
: Indicates that the token is a KYAPay token, as defined in {{?I-D.skyfire-kyapayprofile}}.

This identfier is used as the value of the `subject_token_type`
Token Exchange {{RFC8693}} request parameter when the `subject_token` value
is a KYAPay token.

When when exchanging a KYAPay token for another token,
the `actor_token` and `actor_token_type` request parameters are not used
because the KYAPay token contains identifying information for both
the principal on whose behalf the request is being made
(in the Human Identity (`hid`) claim) and
the agent that is authorized to act on behalf on the principal
(in the Agent Platform Identity (`apd`) claim and/or
in the Agent Identity (`aid`) claim).


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
