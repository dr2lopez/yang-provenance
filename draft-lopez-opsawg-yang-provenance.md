---
title: "Applying COSE Signatures for YANG Data Provenance"
abbrev: "yang-data-provenance"
category: info

docname: draft-lopez-opsawg-yang-provenance-latest
submissiontype: IETF  # also: "independent", "IAB", or "IRTF"
number: 00
date:
consensus: true
v: 3
area: "Operations and Management"
workgroup: "Operations and Management Area Working Group"
keyword:
 - provenance
 - signature
 - COSE
 - metadata
venue:
  group: "Operations and Management Area Working Group"
  type: "Working Group"
  mail: "opsawg@ietf.org"
  arch: "https://mailarchive.ietf.org/arch/browse/opsawg/"
  github: "dr2lopez/yang-provenance"
  latest: "https://dr2lopez.github.io/yang-provenance/draft-lopez-opsawg-yang-provenance.html"

author:
 -
    fullname: Diego Lopez
    organization: Telefonica
    email: "diego.r.lopez@telefonica.com"

normative:

informative:
- draft-ietf-opsawg-collected-data-manifest

--- abstract

This document defines a mechanism based on COSE signature to provide and verify the provenance of YANG data. Data provenance allows to verify the orign and integrity of a dataset whenever those data are going to be processed and/or applied in workflows that are not directly attached to the original data stream, so online methods for assessing their origin and integrity (typically related to the application of crytpo-enabled data transport). As the application of evidence-based OAM and the use of tools such as AI/ML grows, provenance validation becomes more relevant. The use of compact signatures facilitates the inclusion of provenance strings in any YANG schema requiring them.

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
