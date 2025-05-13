---
title: "OAuth and SPIFFE"
category: info

docname: draft-kasselman-oauth_spiffe-latest
submissiontype: IETF  # also: "independent", "editorial", "IAB", or "IRTF"
number:
date:
consensus: true
v: 3
# area: Sec
# workgroup: OAuth
keyword:
 - next generation
 - unicorn
 - sparkling distributed ledger
venue:
#  group: OAuth
#  type: Working Group
#  mail: WG@example.com
#  arch: https://example.com/WG
  github: "PieterKas/OAuth-and-SPIFFE"
  latest: "https://PieterKas.github.io/OAuth-and-SPIFFE/draft-kasselman-oauth_spiffe.html"

author:
 -
    fullname: "Pieter Kasselman"
    organization: SPIRL
    email: "pieter@spirl.com"

normative:

  RFC6749:
  RFC6755:
  RFC7517: JSON Web Keys
  RFC7521:
  RFC7523:
  RFC8705:
  SPIFFE:
    title: SPIFFE
    target: https://github.com/spiffe/spiffe/blob/main/standards/SPIFFE.md
  SPIFFE_ID:
    title: SPIFFE-ID
    target: https://github.com/spiffe/spiffe/blob/main/standards/SPIFFE-ID.md
  SPIFFE_X509:
    title: X509-SVID
    target: https://github.com/spiffe/spiffe/blob/main/standards/X509-SVID.md
  SPIFFE_JWT:
    title: JWT-SVID
    target: https://github.com/spiffe/spiffe/blob/main/standards/JWT-SVID.md
  SPIFFE_BUNDLE:
    title: SPIFFE Bundle
    target: https://github.com/spiffe/spiffe/blob/main/standards/SPIFFE_Trust_Domain_and_Bundle.md#4-spiffe-bundle-format
  SPIFFE_FEDERATION:
    title: SPIFFE Federation
    target: https://github.com/spiffe/spiffe/blob/main/standards/SPIFFE_Federation.md
  Headless_JWT:
    title: Headless-JWT
    target: foo

informative:


--- abstract

The OAuth framework is a widely deployed authorization protocol standard that enables applications to obtain limited access to user resources. OAuth clients are applications that request access to protected resources on behalf of a user by obtaining authorization from an OAuth authorization server. OAuth clients must be registered with the OAuth authorization server. Registering and managing OAuth client IDs and clien secrets poses significant operational challenges in dynamically scaling environments. The Secure Production Identity Framework For Everyone ({{SPIFFE}}) is a graduated Cloud Native Compute Foundation project that is designed to dynamically attest and verify workload identity, assign identifiers, and issue credentials to workloads. This draft describes how workloads with SPIFFE credentials can be used with OAuth to lessen the operational burden of client registration, remove the need for additional client secrets and be used in OAuth flows that require client authentication.

--- middle

# Introduction
The OAuth framework {{RFC6749}} is popular and broadly deployed. It defines seperate roles for the authorization server, resource server, resource owner and the client. In OAuth, the client requests access to resources controlled by the resource owner and hosted by the resource server, and is issued a different set of credentials than those of the resource owner {{RFC6749}}. The OAuth client is identified with a client_id and supports multiple authentication methods, including a client_secret, which is commonly used. Managing client_ids and client_secrets rerpesents operational challenges, including manual registrations, lifecycle management and the increasing burden of managing client_secrets used for authenticating OAuth client. Modern cloud-native architectural patterns like micro-services and other short lived, ephemeral compute concepts, allows for on-demand scaling of OAuth clients, which in turn increases the burden on managing the lifecycle of the client_id and client_secret.

The Secure Production Identity Production Framework (SPIFFE) is a graduated project in the Cloud Native Compute Foundation (CNCF) that defines a standard for managing the identity lifecycle of workloads in modern cloud-native compute environments. It is designed to dynamically attest and verify a workloads, assign identifiers, issue credentials and manage the lifecycle of those credentials in dynamically scaling environments without manual intervention. The identifier is in the form of a URI, while the credentials includes profiles of JWT and X.509 certificates, known as JWT-SVID and X.509-SVIDs. These credetnials are used by workloads to authenticate to eacch other or to services that it needs to access. SPIFFE is commonly used to secure workload identities in large scale production systems that needs to scale dynamically.

Workloads provisioned with SPIFFE identiffiers and credentials often need to access OAuth protected resources. When they interact with OAuth protected resources, they assume the role of an OAuth client and needs to be registered with the OAuth authorization server. This requries an additional manual step, or an additional registration flow (e.g. Dynamic Client Registration {{?RFC7591}} ). The registration step results in the provisioning of an addditional identifier (the client_id), and frequently an additional secret (the client_secret) that must be secured. The additional secrets adds to the overall challenge of secrets sprawl in large scale environments that degrades the risk profile for an environment.

OAuth deployments in which SPIFFE is already deployed can leverage the SPIFFE identifiers and credentials already issued to workloads by establishing a trust relationship with the SPIFFE issuer. When a workload presents a SPIFFE credential as part of authorization request to the OAuth server, the OAuth authorization server verifies the JWT or X.509 certificate, ensuring it was issued by a trusted issuer. The authorization server registers the SPIFFE ID as a client ID, along with additional metadata, if needed, before completing the authorization request and issueing an Access Token. This "register-on-first-use" pattern removes the need for manual pre-registration or additional roundtrips and credential registration. It leverages existing credentials that were issued dynamically and reduces the dependence on static secrets which are at higher risk of compromise than short lived dynamic credentials.

# Conventions and Definitions

{::boilerplate bcp14-tagged}

# Register On First Use

## Client Authentication

## Authorization Server Processing

# Additional metadata

## The redirect URI



# Security Considerations

TODO Security


# IANA Considerations

This document has no IANA actions.


--- back

# Acknowledgments
{:numbered="false"}

TODO acknowledge.
