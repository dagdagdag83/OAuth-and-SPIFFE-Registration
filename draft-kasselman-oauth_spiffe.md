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
  SPIFFE-OAUTH-CLIENT-AUTH:
    title: OAuth SPIFFE Client Authentication
    target: foo

informative:


--- abstract

The OAuth framework is a widely deployed authorization protocol standard that enables applications to obtain limited access to user resources. OAuth clients are applications that request access to protected resources on behalf of a user by obtaining authorization from an OAuth authorization server. OAuth clients must be registered with the OAuth authorization server. Registering and managing OAuth client IDs and client secrets poses significant operational challenges in dynamically scaling environments. The Secure Production Identity Framework For Everyone ({{SPIFFE}}) is a graduated Cloud Native Compute Foundation project that is designed to dynamically attest and verify workload identity, assign identifiers, and issue credentials to workloads. This draft describes how workloads with SPIFFE credentials can be used with OAuth to lessen the operational burden of client registration and remove the need for additional client secrets by adopting a "register on first use" principle.

--- middle

# Introduction
The OAuth framework {{RFC6749}} is popular and broadly deployed. It defines seperate roles for the authorization server, resource server, resource owner and the client. In OAuth, the client requests access to resources controlled by the resource owner and hosted by the resource server, and is issued a different set of credentials than those of the resource owner {{RFC6749}}. The OAuth client is identified with a client_id and supports multiple authentication methods, including a client_secret, which is commonly used. Managing client_ids and client_secrets rerpesents operational challenges, including manual registrations, lifecycle management and the increasing burden of managing client_secrets used for authenticating OAuth client. Modern cloud-native architectural patterns like micro-services and other short lived, ephemeral compute concepts, allows for on-demand scaling of OAuth clients, which in turn increases the burden on managing the lifecycle of the client_id and client_secret.

The Secure Production Identity Production Framework (SPIFFE) is a graduated project in the Cloud Native Compute Foundation (CNCF) that defines a standard for managing the identity lifecycle of workloads in modern cloud-native compute environments. It is designed to dynamically attest and verify a workloads, assign identifiers, issue credentials and manage the lifecycle of those credentials in dynamically scaling environments without manual intervention. The identifier is an URI, while the credentials includes profiles of JWT and X.509 certificates, known as JWT-SVID and X.509-SVIDs. These credentials are used by workloads to authenticate to each other or to services that it needs to access. SPIFFE is commonly used to secure workload identities in large scale production systems that needs to scale dynamically.

Workloads provisioned with SPIFFE identiffiers and credentials often need to access OAuth protected resources. When they interact with OAuth protected resources, they assume the role of an OAuth client and needs to be registered with the OAuth authorization server. This requries an additional manual step, or an additional registration flow (e.g. Dynamic Client Registration {{?RFC7591}} ). The registration step results in the provisioning of an addditional identifier (the client_id), and frequently an additional secret (the client_secret) which is used for client authentication and must be secured. The additional secrets adds to the overall challenge of secrets sprawl in large scale environments that degrades the risk profile for an environment.

OAuth deployments in which SPIFFE is already deployed can leverage the SPIFFE identifiers and credentials already issued to workloads by establishing a trust relationship with the SPIFFE issuer. When a workload acts as an OAuth client, it presents a SPIFFE credential to authenticate itself to the OAuth server. The OAuth authorization server verifies the JWT or X.509 certificate, ensuring it was issued by a trusted issuer. The authorization server registers the SPIFFE ID as a client ID, along with additional metadata, if needed, before issueing an Access Token.

This "register-on-first-use" pattern removes the need for manual pre-registration or additional roundtrips and credential registration. It leverages existing credentials that were issued dynamically and reduces the dependence on static secrets which are at higher risk of compromise than short lived dynamic credentials.

# Conventions and Definitions

{::boilerplate bcp14-tagged}

# Client Registration On First Use
The SPIFFE deployment is responsible for bootstrapping the identifiers and credentials used by workloads in an environment. The SPIFFE deployment dynamically attests workloads, issues identifiers, provision short lived credentials, and rotate those credentials. The attestation techniques used by a SPIFFE issuer is deplopyment specific. SPIFFE supports multiple ccredential formats, including JWT-SVID {{SPIFFE_JWT}} and JWT-X.509 {{SPIFFE_X509}}. Workloads can use these credentials with any protocol that is compatible with these credential types to authenticate themselves. The use of SPIFFE credentials to authenticate to an OAuth authorizations server is described in {{SPIFFE-OAUTH-CLIENT-AUTH}}.

An OAuth deployment leverages the SPIFFE identifiers and credentials already issued to workloads by establishing a trust relationship with the SPIFFE issuer. As a result, the OAuth authorization server is able to verify any credential issued for a specific SPIFFE trust domain when it is presented to the OAuth authorization server. This assures the OAuth authorization server that the workload was attested and verified in acccordance with the SPIFFE issuer's policies. In addition, it is assured that the identifier was correctly assigned to the workload, and that the lifecycle of the credentials and the underlying keys are managed in accordance with the SPIFFE issuer's policies.

The OAuth authorization server avoids operational overheads of registering client_id and client_secret values by accepting SPIFFE credentials from workload acting as an OAuth client participating in an OAuth flow. The credentials must be signed by a trusted issuer. If this is the first time the authorization server verifies a credential wth a specific SPIFFE ID, it registers a new client_id, removing the need for an out-of-bound or seperate registration flow. The OAuth authorization server may derive a unique client_id from the SPIFFE ID, or it may use the SPIFFE ID as the client_id. Since the workload is already in possession of a credential that is cryptographically bound to its identifier, no addditional client_secret is needed, removing the operational overhead and securtiy risks of managing long lived secrets in large scale deployments that already have SPIFFE credentials. The OAuth authorization server may register additional metadata if needed. How the additional metadata is obtained is an implementation choice and deployment specific. Metadata may be preconfigured, automatically created or retrieved from a configuration management system.

The client registration on first use implementation varies based on whether the OAuth flows includes redirection or not. In non-redirecting flows (such as Client Credentials or Resource Owner Password Credentials), the Oauth client authenticates and request tokens directly from the authorization server without the need for user redirection. Flows with redirection (like Authorization Code Flow) temporarily send users to an external identity provider before returning them to the application with tokens or authorization codes, which is then used by the Oauth client. These redirect-based flows rely on the user agent (typically a web browser) to maintain the authentication context and handle the navigation between the application and the identity provider. These fundamental differences require distinct implementation considerations when implementing client registration on first use. Redirecting flows need state management to reconnect returning users with their original session after authentication, while non-redirecting flows can create a client_id inline within the same request context. Properly handling both OAuth patterns ensures a smooth registration experience regardless of the authentication method selected.

## Client Registration On First Use for Non-Redirecting OAuth Flows
In OAuth flows where there is no redirection, the client initiates interaction with the authorization server and access the token endpoint directly without any preceding redirects. Examples include the Client Credentials (see Section 4.4 of {{RFC6749}}) and Resource Owner Password Credentials (see Section 4.3 of {{RFC6749}}).

The diagram below shows the process for client registration on first use in the Client Credential flow.

~~~~
+-------------+ (A) Authorization Server Establish
|   SPIFFE    |     Trust with SPIFFE Issuer
|   Issuer    |<-------------------------------+
+-------------+                                |
       ^                                       |
       | (B) SPIFFE Issuer Attest Workload     |
       |     and Provisions Credentials        |
       V                                       V
+-------------+                          +-------------+
|  Workload   |------------------------->|             |
|   acting    | (C) Authorization Request|Authorization|
|     as      |                          |   Server    |
|OAuth Client |<-------------------------|             |
+-------------+ (D) Authorization        +-------------+
       |             Response
       |
       |                                 +-------------+
       |                                 |   Resource  |
       +-------------------------------->|    Server   |
      (E) Access Resource                |             |
                                         +-------------+
~~~~
{: title='Client Registration on First Use: Client Credentials Grant'}

* (A) The OAuth authorization server establish a trust relationship with the SPIFFE Issuer. Once trust is established, the OAuth authorization server accepts credentials issued by the SPIFFE Issuer.
* (B) The workload is attested and credentialed by the SPIFFE Issuer {{SPIFFE}}. The SPIFFE Issuer assigns a SPIFFE ID {{SPIFFE_ID}} as an identifier for the workload, along with SPIFFE credentials (e.g. JWT-SVID {{SPIFFE_JWT}} and X.509-SVID {{SPIFFE_X509}}).
* (C) The workload, acting as an OAuth client, makes a request to the authorization server's token endpoint as specified in Section 4.4 of {{RFC6749}}. It authenticates itself using either a JWT-SVID or X.509-SVID as defined in {{SPIFFE-OAUTH-CLIENT-AUTH}}
* (D) The OAuth authorization server authenticates the workload acting as an OAuth client by verifying the credentials it presented (see {{SPIFFE-OAUTH-CLIENT-AUTH}}). If the authentication is succesfull, the authorization server accepts the SPIFFE ID as a valid client ID and checks if it has been registered previously. If it has not been registered, it registers the new SPIFFE ID as a valid client. The authorization server adds additional metadata if needed, before completing the request and returning an access token.
* (E) The Oauth client use the access token it retrieved in the previous step and use it to acccess the resource server.

## Client Registration On First Use for Redirecting OAuth Flows
In OAuth flows that rely on redirection, the initial interaction with the authorization server is not performed by the client, but is instead performed by another component, such as the user agent, after which the flow is redirected to the client. The client then interacts with the authorization server token endpoint to complete the flow and obtain tokens. Examples include the Authorization Grant Flow (see Section 4.1 of {{RFC6749}}).

~~~~
     +----------+
     | Resource |
     |   Owner  |
     |          |
     +----------+
          ^
          |
         (D)
     +----|-----+          Client Identifier      +---------------+
     |         -+----(C)-- & Redirection URI ---->|               |
     |  User-   |                                 | Authorization |
     |  Agent  -+----(D)-- User authenticates --->|     Server    |
     |          |                                 |               |
     |         -+----(E)-- Authorization Code ---<|               |
     +-|----|---+                                 +---------------+
       |    |                                         ^   v   ^
      (C)  (D)                                        |   |   |
       |    |                                         |   |   |
       ^    v                                         |   |   |
     +---------+                                      |   |   |
     |         |>---(E)-- Authorization Code ---------'   |   |
     |  Client |          & Redirection URI               |   |
     |         |                                          |   |
     |         |<---(F)----- Access Token ----------------'   |
     +---------+       (w/ Optional Refresh Token)            |
       ^                                                      |
       | (B) SPIFFE Issuer Attest Workload                    |
       |     and Provisions Credentials                       |
       V                                                      |
   +-------------+ (A) Authorization Server Establish         |
   |   SPIFFE    |     Trust with SPIFFE Issuer               |
   |   Issuer    |<-------------------------------------------'
   +-------------+                                
~~~~
{: title='Client Registration on First Use: Authorization Code Grant'}

   (C)  The client initiates the flow by directing the resource owner's
        user-agent to the authorization endpoint.  The client includes
        its client identifier, requested scope, local state, and a
        redirection URI to which the authorization server will send the
        user-agent back once access is granted (or denied).

   (D)  The authorization server authenticates the resource owner (via
        the user-agent) and establishes whether the resource owner
        grants or denies the client's access request.

   (E)  Assuming the resource owner grants access, the authorization
        server redirects the user-agent back to the client using the
        redirection URI provided earlier (in the request or during
        client registration).  The redirection URI includes an
        authorization code and any local state provided by the client
        earlier.

   (F)  The client requests an access token from the authorization
        server's token endpoint by including the authorization code
        received in the previous step.  When making the request, the
        client authenticates with the authorization server.  The client
        includes the redirection URI used to obtain the authorization
        code for verification.

   (G)  The authorization server authenticates the client, validates the
        authorization code, and ensures that the redirection URI
        received matches the URI used to redirect the client in
        step (E).  If valid, the authorization server responds back with
        an access token and, optionally, a refresh token.

# SPIFFE and OAuth Trust Relationship
SPIFFE makes provision for multiple Trust Domains, which are represented in the workload identifier. Trust Domains offers additional segmentation withing a SPIFFE deployment and each Trust Domain has its own keys for signing credentials.

## Client Authentication
Use any protocol compatible with the SPIFFE Credentials

## Authorization Server Processing
Rules for verifying and registering the client_id

# Additional metadata

## The redirect URI
TODO - all the ways addditional metadata may be obtained - including addding it as a request parameter perhaps? PAR allows for this.

## Scopes

# Client ID Lifecycle Management
delete client IDs every so often.

Velocity controls

# Security Considerations

TODO Security


# IANA Considerations

This document has no IANA actions.


--- back

# Acknowledgments
{:numbered="false"}

TODO acknowledge.
