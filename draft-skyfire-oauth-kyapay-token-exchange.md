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
-
  name: Abhishek Hingnikar
  organization: Okta
  email: abhishek@hingnikar.com
-
  name: Jeffrey Hickman
  organization: Ory
  email: jhickman.it@gmail.com

contributor:
-
  name: Ammar Safdari
  organization: Skyfire Systems Inc.

# see https://github.com/cabo/kramdown-rfc/wiki/Syntax2#authors-contributors

normative:
  RFC7523:
  RFC8414:
  RFC8693:
  RFC8707:
  RFC9728:
  I-D.skyfire-oauth-kyapay-token:
  I-D.ietf-oauth-client-id-metadata-document:

informative:
  RFC6749:
  RFC6750:
  RFC6755:
  RFC7591:
  RFC9449:
  MCP:
    target: https://modelcontextprotocol.io/specification/2026-07-28
    title: Model Context Protocol Specification
    date: July 28, 2026
    author:
      - org: "Anthropic and the Model Context Protocol Contributors"

  OpenID.Core:
    target: https://openid.net/specs/openid-connect-core-1_0.html
    title: "OpenID Connect Core 1.0 incorporating errata set 2"
    date: 2023-12-15
    author:
      - name: Nat Sakimura
      - name: John Bradley
      - name: Michael B. Jones
      - name: Breno de Medeiros
      - name: Chuck Mortimore

  OpenID.Federation:
    target: https://openid.net/specs/openid-federation-1_0.html
    title: OpenID Federation 1.0
    date: February 15, 2026
    author:
      - name: Roland Hedberg
      - name: Michael B. Jones
      - name: Andreas Åkre Solberg
      - name: John Bradley
      - name: Giuseppe De Marco
      - name: Vladimir Dzhuvinov

  OpenID4VP:
    target: https://openid.net/specs/openid-4-verifiable-presentations-1_0.html
    title: OpenID for Verifiable Presentations
    date: July 9, 2025
    author:
      - name: Oliver Terbu
      - name: Torsten Lodderstedt
      - name: Kristina Yasuda
      - name: Daniel Fett
      - name: Joseph Heenan

  IANA.OAuth.Parameters:
    author:
    - org: IANA
    target: http://www.iana.org/assignments/oauth-parameters
    title: OAuth Parameters
    date: false

...


--- abstract

This specification describes how KYAPay tokens can be exchanged for
OAuth access tokens
to dynamically grant agents access to resources they need
to accomplish their mission.


--- middle


# Introduction

KYAPay tokens {{I-D.skyfire-oauth-kyapay-token}}
are used by agents to identify themselves,
the principal they are acting on behalf of,
their payment capabilities,
their authorized scope of action or mission,
and their intended audience.
Agents may need access to resources to accomplish their mission.
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

The roles defined in {{I-D.skyfire-oauth-kyapay-token}} are incorporated into this specification.


# KYAPay Token Exchange {#kyapay-token-exchange}

The KYAPay token contains identifying information for both
the principal on whose behalf the request is being made
(in the Human Identity (`hid`) claim) and
the agent that is authorized to act on behalf of the principal
(in the Agent Platform Identity (`apd`) claim and
the Agent Identity (`aid`) claim).

This specification defines two methods of exchanging KYAPay tokens for access tokens:
a direct method and
a method using a
Client ID Metadata Document (CIMD) {{I-D.ietf-oauth-client-id-metadata-document}}.
It also defines an OAuth authorization server metadata parameter
enabling authorization servers
to declare that KYAPay Token Exchange is supported.

The steps when successfully exchanging a KYAPay token for an access token
and using it with the protected resource are:

1. The client retrieves the OAuth protected resource metadata document {{RFC9728}}.
1. The client determines from the resource metadata what authorization server(s)
   can be used for the token exchange.
1. The client chooses an authorization server and retrieves its metadata {{RFC8414}}.
1. The client determines from the authorization server metadata whether
   the authorization server supports KYAPay Token Exchange, per {{as-metadata}}.
   It also may be able to determine if the authorization server
   accepts tokens from specific KYAPay token issuers.
1. The client makes a token exchange {{RFC8693}} request to the authorization server,
   passing the KYAPay token as the `subject_token` value,
   the `subject_token_type` value of `urn:ietf:params:oauth:token-type:kyapay`, and
   passing the resource identifier {{RFC8707}} as the "resource" value.
1. The authorization server returns an access token for the resource to the client
   using information from the KYAPay token to create the access token.
   Note that this may be a bearer access token {{RFC6750}}
   or a DPoP-bound access token {{RFC9449}}.
   The access token will contain or reference sufficient information
   for the protected resource to be able to provision access for the client,
   which may include dynamic account creation.
1. The client uses the access token to make an HTTP resource request to the resource.
1. The resource responds to the resource request providing the requested information.

Note that the ability to use information in the KYAPay token
to dynamically provision access to the resource by the agent is critical
to enable seamless agentic access to resources for the user.
This obviates the need to pre-provision an account for the user at every resource
that their agent might access -- a process that would not scale.


# Direct KYAPay Token Exchange Method

The direct KYAPay Token Exchange method uses a Client Identifier that
is constructed directly from information in the KYA Token.
Its value contains
the token issuer,
the token originator,
the agent platform identifier, and
the agent identity identifier.
Specifically, it is the string concatenation of
- the string literal "kyapay:",
- the `iss` claim value,
- the string literal "|",
- the `ori` claim value,
- the string literal "|",
- the `apd` claim's `id` value,
- the string literal "|", and
- the `aid` claim's `id` value.

If the KYAPay token has no `ori` claim, or no `apd` claim,
the corresponding part is the empty string,
so the Client ID always has four parts separated by "|".

An example such Client ID value is:

    kyapay:https://app.skyfire.xyz|https://app.skyfire.xyz|a283ced5-95be-4abe-81aa-51552e8876ad|7d889702-7b6a-46cb-9bdd-10e83d17b18d

This requires the authorization server to recognize and accept these Client IDs
without client pre-registration (as described in {{Section 2 of RFC6749}})
or dynamic client registration {{RFC7591}}.
There is precedent for authorization servers using non-pre-registered clients
based on the syntax of their Client Identifiers;
the Client ID Metadata Document (CIMD) {{I-D.ietf-oauth-client-id-metadata-document}}
specification uses unregistered Client IDs,
the OpenID Federation {{OpenID.Federation}}
specification uses unregistered Client IDs,
and the OpenID for Verifiable Presentations {{OpenID4VP}}
specification uses unregistered Client IDs.
The OpenID4VP specification also uses Client ID prefixes,
as this specification does.

The token exchange uses {{RFC8693}} and is performed as follows:

* The "none" client authentication method {{RFC7591}} is used.
* An HTTP POST is made to the authorization server's token endpoint.
* The `grant_type` value is `urn:ietf:params:oauth:grant-type:token-exchange`.
* The `client_id` value is the Client ID constructed as described above.
* The `subject_token` value is the KYAPay token being exchanged.
* The `subject_token_type` value is `urn:ietf:params:oauth:token-type:kyapay`.
* The `resource` value is the URL of the protected resource
  that the resulting token is intended for.
* The `requested_token_type` parameter MAY be included;
  if included, its value is `urn:ietf:params:oauth:token-type:access_token`.
* No `actor_token` or `actor_token_type` parameters are included.

The following is a non-normative example of a POST request
to perform a direct KYAPay token exchange for an access token:

    POST /token HTTP/1.1
    Host: as.example.com
    Content-Type: application/x-www-form-urlencoded

    grant_type=urn:ietf:params:oauth:grant-type:token-exchange
    &client_id=kyapay:https://app.skyfire.xyz|https://app.skyfire.xyz|a283ced5-95be-4abe-81aa-51552e8876ad|7d889702-7b6a-46cb-9bdd-10e83d17b18d
    &subject_token=*KYAPay Token*
    &subject_token_type=urn:ietf:params:oauth:token-type:kyapay
    &resource=https://mcp.acme.example/
    &requested_token_type=urn:ietf:params:oauth:token-type:access_token


# KYAPay Token Exchange Method using Client ID Metadata Document (CIMD)

This section describes a KYAPay token exchange method using a
Client ID Metadata Document (CIMD) {{I-D.ietf-oauth-client-id-metadata-document}}.
This method uses the CIMD URL as the Client ID.

As described in {{Section 8.2 of I-D.ietf-oauth-client-id-metadata-document}}
(Client Authentication),
clients that are capable of maintaining private key material and
performing client authentication SHOULD do so with an acceptable
method, such as the private_key_jwt {{OpenID.Core}} client authentication method.
Otherwise, the "none" client authentication method {{RFC7591}} is used,
and the `client_id` parameter is the CIMD URL.

The token exchange uses {{RFC8693}} and is performed as follows:

* An HTTP POST is made to the authorization server's token endpoint.
* The `grant_type` value is `urn:ietf:params:oauth:grant-type:token-exchange`.
* The `subject_token` value is the KYAPay token being exchanged.
* The `subject_token_type` value is `urn:ietf:params:oauth:token-type:kyapay`.
* The `resource` value is the URL of the protected resource
  that the resulting token is intended for.
* No `actor_token` or `actor_token_type` parameters are included.

The following is a non-normative example of a POST request
by a CIMD client using the private_key_jwt client authentication method
to perform a KYAPay token exchange for an access token:

    POST /token HTTP/1.1
    Host: as.example.com
    Content-Type: application/x-www-form-urlencoded

    grant_type=urn:ietf:params:oauth:grant-type:token-exchange
    &subject_token=*KYAPay Token*
    &subject_token_type=urn:ietf:params:oauth:token-type:kyapay
    &resource=https://mcp.acme.example/
    &requested_token_type=urn:ietf:params:oauth:token-type:access_token
    &client_assertion_type=urn:ietf:params:oauth:client-assertion-type:jwt-bearer
    &client_assertion=*JWT whose iss and sub are the CIMD URL*


# Authorization Server Metadata {#as-metadata}

An authorization server declares support for KYAPay token exchange by including a
`kyapay_issuers_supported` authorization server metadata parameter {{RFC8414}}.
The definition of this parameter is:

{:vspace}
`kyapay_issuers_supported`:
: OPTIONAL.
  JSON array containing a list of issuer identifiers of KYAPay token issuers,
  as specified in {{I-D.skyfire-oauth-kyapay-token}},
  that can be used with this authorization server.
  Authorization servers MAY choose not to advertise some supported token issuers,
  even when this parameter is used.
  In some use cases, the set of token issuers will not be enumerable.
  The array MAY be empty.

An authorization server that includes this parameter MUST also include
`urn:ietf:params:oauth:grant-type:token-exchange` in `grant_types_supported`.


# KYAPay Token Example

An example decoded set of JWT Header Parameters for a KYAPay token is:

    {
      "alg": "ES256",
      "kid": "BEDD-0",
      "typ": "kya+jwt"
    }

An example decoded JWT Claims Set for a KYAPay token is:

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
        "id": "7d889702-7b6a-46cb-9bdd-10e83d17b18d",
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
      "scope": "xyz pdq",
      "iat": 1782245616,
      "iss": "https://app.skyfire.xyz",
      "jti": "019ef61d-eb8f-759b-9302-d998b294fcc9",
      "aud": "886eef79-4d90-4b8d-81f0-ce5c6c7dac13",
      "sub": "8fa1438f-8dd0-4e91-abb8-888cdbeb6d5d",
      "exp": 1782245916
    }

The KYAPay token is used as the `subject_token` value for the token exchange.


# Security Considerations

The security considerations defined in {{I-D.skyfire-oauth-kyapay-token}}
and {{RFC7523}} apply to this specification.


# Privacy Considerations

The privacy considerations defined in {{I-D.skyfire-oauth-kyapay-token}}
and {{RFC7523}} apply to this specification.

The `kyapay_issuers_supported` value may disclose a set of
KYAPay token issuers that the authorization server trusts.
Clients may need to know, before making a request,
whether a KYAPay token they hold or can obtain will be accepted.
Deployments that consider their issuer relationships sensitive
should take this into account before publishing the list.


# IANA Considerations

## OAuth URI Registration

This specification registers the following value in the
IANA "OAuth URI" registry {{IANA.OAuth.Parameters}}
established by {{RFC6755}}.

### urn:ietf:params:oauth:token-type:kyapay

* URN: urn:ietf:params:oauth:token-type:kyapay
* Common Name: KYAPay Token Type
* Change controller: IETF
* Specification Document: {{kyapay-token-exchange}} of this specification


## OAuth Authorization Server Metadata Registration

This specification registers the following parameter in the
IANA "OAuth Authorization Server Metadata" registry {{IANA.OAuth.Parameters}}
established by {{RFC8414}}.

### kyapay_issuers_supported

* Metadata Name: kyapay_issuers_supported
* Metadata Description: JSON array containing a list of issuer identifiers of
  KYAPay token issuers that can be used with this authorization server
* Change Controller: IETF
* Specification Document(s): {{as-metadata}} of this specification


--- back


# Document History
{: numbered="false"}

[[ to be removed by the RFC Editor before publication as an RFC ]]

-02

* Reworked to use OAuth 2.0 Token Exchange {{RFC8693}}.
  Both direct exchange of a KYAPay token for an access token
  and token exchange for a client using CIMD are defined.

-01

* Added Abhishek Hingnikar and Jeffrey Hickman as authors.

-00

* Initial draft.
