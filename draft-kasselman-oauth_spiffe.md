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
#  group: WG
#  type: Working Group
#  mail: WG@example.com
#  arch: https://example.com/WG
  github: "PieterKas/OAuth-and-SPIFFE"
  latest: "https://PieterKas.github.io/OAuth-and-SPIFFE/draft-kasselman-oauth_spiffe.html"

author:
 -
    fullname: "PieterKas"
    organization: SPIRL
    email: "pieter@spirl.com"

normative:

informative:


--- abstract

The OAuth framework is a widely deployed authorization protocol standard that enables applications to obtain limited access to user resources. OAuth clients are applications that request access to protected resources on behalf of a user by obtaining authorization from an OAuth authorization server. OAuth clients must be registered with the OAuth authorization server. Registering and managing OAuth client IDs and clien secrets poses significant operational challenges in dynamically scaling environments. The Secure Production Identity Framework For Everyone (SPIFFE) is a graduated Cloud Native Compute Foundation project that is designed to dynamically dynamically attest and verify workload identity, assign identifiers, and issue credentials to workloads. This draft describes how workloads with SPIFFE credentials can be used with OAuth to lessen the operational burden of client registration and remove the need for additional client secrets.

--- middle

# Introduction

TODO Introduction


# Conventions and Definitions

{::boilerplate bcp14-tagged}


# Security Considerations

TODO Security


# IANA Considerations

This document has no IANA actions.


--- back

# Acknowledgments
{:numbered="false"}

TODO acknowledge.
