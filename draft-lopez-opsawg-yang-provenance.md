---
title: "Applying COSE Signatures for YANG Data Provenance"
abbrev: "yang-data-provenance"
category: info

docname: draft-lopez-opsawg-yang-provenance-latest
submissiontype: IETF  # also: "independent", "IAB", or "IRTF"
number:
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
 RFC9052:

informative:
 DATA-MANIFEST: {{?draft-ietf-opsawg-collected-data-manifest}}

--- abstract

This document defines a mechanism based on COSE signature to provide and verify the provenance of YANG data, so it is possible to verify the orign and integrity of a dataset, even when those data are going to be processed and/or applied in workflows that are not able to use a crypto-enhanced data transport in a direct access to original data stream. As the application of evidence-based OAM and the use of tools such as AI/ML grows, provenance validation becomes more relevant in all scenarios. The use of compact signatures facilitates the inclusion of provenance strings in any YANG schema requiring them.

--- middle

# Introduction

OAM automation, generally based on  closed-loop principles, requires at least two datasets to be used. Using the common terms in Autmatics, we need those from the plant (the network device or segment under control) and those to be used as reference (the desired values of the relevant data). The usual automation behavior compares these values and takes a decision, by wahatever the method (algortihmic, rule-based, an AI model tuned by ML...) to decide on a control action according to this comparison. Assurance on the origin and integrity of these datasets, what we refer in this document as "provenance", becomes essential to guarantee a proper behavior of closed-loop automation.

When datasets are made available as an online data flow, provenance can be assessed by properties of the data transport protocol, as long as some kind of crypto-enhanced protocol is used, with TLS, SSH and IPsec as the main examples. But when these datasets are stored, go through some pre-processing stage, or even crypto-enhanced data transport is not available, provenance must be assessed by other means.

This document provides a mechanism for including digital signatures within YANG data. It applies COSE {{RC9052}}, to make the signature compact and easy to calculate. This mechanism is potentially applicable to any serialization of the YANG data supporting a clear method for canoicalization (as discussed below), but this document consider three essential ones: CBOR, JSON and XML.

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
