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
area: Security
workgroup: Web Authorization Protocol
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

contributor:
-
  name: Ammar Safdari
  organization: Skyfire Systems Inc.

# see https://github.com/cabo/kramdown-rfc/wiki/Syntax2#authors-contributors

normative:
  RFC7523:
  RFC8707:

informative:
  RFC8693:
  MCP:
    target: https://modelcontextprotocol.io/specification/2025-11-25
    title: Model Context Protocol Specification
    date: November 25, 2025

...

--- abstract

This specification describes how KYAPay tokens can be exchanged for
OAuth access tokens
to dynamically grant agents access to resources they need
to accomplish their mission.

--- middle

# Introduction

KYAPay tokens {{?I-D.skyfire-oauth-kyapay-token}}
are used by agents to identify themselves,
the principal they are acting on behalf of,
their payment capabilities,
their authorized scope of action or mission,
and their intended audience.
Agents may need access to resources in order to accomplish their mission.
This specification describes how an agent can present a KYAPay token
to obtain an OAuth access token granting access to a resource
that it needs to accomplish its mission.

## Use Cases for KYAPay Token Exchange

Enabling agents to create accounts and/or log in to accounts
on behalf of their human principals is a design goal.
To achieve this, systems can utilize a token exchange workflow.
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

The roles defined in {{?I-D.skyfire-oauth-kyapay-token}} are incorporated into this specification.


# KYAPay Token Exchange

The KYAPay token contains identifying information for both
the principal on whose behalf the request is being made
(in the Human Identity (`hid`) claim) and
the agent that is authorized to act on behalf on the principal
(in the Agent Platform Identity (`apd`) claim and/or
in the Agent Identity (`aid`) claim).
The token exchange is performed using the
grant type `urn:ietf:params:oauth:grant-type:jwt-bearer` {{RFC7523}}
at the authorization server's token endpoint.
The KYAPay token is supplied as the `assertion` value.
The protected resource that access is being requested for
is supplied as the `resource` value {{RFC8707}}.

Note that OAuth Token Exchange {{RFC8693}},
which has a separate `subject_token` and `actor_token` values,
is not used, since the KYAPay token has all the information needed
to describe both parties for delegated authorization.

## KYAPay Token Exchange Example

The following is a non-normative example of a POST request
to perform a KYAPay token exchange for an access token:

    POST /token HTTP/1.1
    Host: as.example.com
    Content-Type: application/x-www-form-urlencoded

    grant_type=urn:ietf:params:oauth:grant-type:jwt-bearer
    &assertion=*KYAPay Token*
    &resource=https://mcp.acme.example/

An example decoded set of JWT Header Parameters for the KYAPay token is:

    {
      "alg": "ES256",
      "kid": "BEDD-0",
      "typ": "kya+jwt"
    }

An example decoded JWT Claims Set for the KYAPay token is:

    {
      "env": "production",
      "ori": "https://app.skyfire.xyz",
      "itg": "cursor-agent",
      "tsi": "886eef79-4d90-4b8d-81f0-ce5c6c7dac13",
      "tdm": "auth101.dev",
      "hid": {
	"email": "example@skyfire.xyz",
	"verifier": "https://app.skyfire.xyz",
	"verified": true
      },
      "aid": {
	"name": "Buyer 1",
	"creation_ip": "12.50.206.98"
      },
      "apd": {
	"id": "a283ced5-95be-4abe-81aa-51552e8876ad",
	"name": "E2E Test Org 1",
	"email": "example@skyfire.xyz",
	"verifier": "https://app.skyfire.xyz",
	"verified": true
      },
      "scope": "",
      "iat": 1782245616,
      "iss": "https://app.skyfire.xyz",
      "jti": "019ef61d-eb8f-759b-9302-d998b294fcc9",
      "aud": "886eef79-4d90-4b8d-81f0-ce5c6c7dac13",
      "sub": "8fa1438f-8dd0-4e91-abb8-888cdbeb6d5d",
      "exp": 1782245916
    }

An example decoded set of JWT Header Parameters for the resulting access token is:

    {
      "alg": "ES256",
      "kid": "BEDD-0",
      "typ": "kya+jwt"
    }

An example decoded JWT Claims Set for the resulting access token is:

    {
      "iss": "http://127.0.0.1:8788",
      "aud": "http://127.0.0.1:8799/mcp",
      "sub": "example@skyfire.xyz",
      "scope": "openid profile email mcp",
      "iat": 1782256825,
      "exp": 1782260425,
      "jti": "656ab7da-9c28-4dd4-a572-474af7ab47fe",
      "client_metadata": {
	"aid": "Buyer 1",
	"ori": "https://app-qa.skyfire.xyz"
      }
    }

# Security Considerations

The security considerations defined in {{?I-D.skyfire-oauth-kyapay-token}}
and {{RFC7523}} apply to this specification.


# Privacy Considerations

The privacy considerations defined in {{?I-D.skyfire-oauth-kyapay-token}}
and {{RFC7523}} apply to this specification.


# IANA Considerations

This specification requires no actions by IANA.

--- back

# Document History
{: numbered="false"}

[[ to be removed by the RFC Editor before publication as an RFC ]]

-00

* Initial draft.
