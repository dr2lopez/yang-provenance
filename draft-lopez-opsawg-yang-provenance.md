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
 RFC8785:
 RFC8949:
 RFC9052:
 XMLSig:
  title: XML Signature Syntax and Processing Version 2.0
  target: https://www.w3.org/TR/xmldsig-core2/

informative:
 YANGmanifest: I-D.ietf-opsawg-collected-data-manifest

--- abstract

This document defines a mechanism based on COSE signatures to provide and verify the provenance of YANG data, so it is possible to verify the orign and integrity of a dataset, even when those data are going to be processed and/or applied in workflows that are not able to use a crypto-enhanced data transport in a direct access to original data stream. As the application of evidence-based OAM and the use of tools such as AI/ML grows, provenance validation becomes more relevant in all scenarios. The use of compact signatures facilitates the inclusion of provenance strings in any YANG schema requiring them.

--- middle

# Introduction

OAM automation, generally based on  closed-loop principles, requires at least two datasets to be used. Using the common terms in Autmatics, we need those from the plant (the network device or segment under control) and those to be used as reference (the desired values of the relevant data). The usual automation behavior compares these values and takes a decision, by wahatever the method (algortihmic, rule-based, an AI model tuned by ML...) to decide on a control action according to this comparison. Assurance on the origin and integrity of these datasets, what we refer in this document as "provenance", becomes essential to guarantee a proper behavior of closed-loop automation.

When datasets are made available as an online data flow, provenance can be assessed by properties of the data transport protocol, as long as some kind of crypto-enhanced protocol is used, with TLS, SSH and IPsec as the main examples. But when these datasets are stored, go through some pre-processing stage, or even crypto-enhanced data transport is not available, provenance must be assessed by other means.

The original use case for this provenance mechanism is associated with {{YANGmanifest}}, in order to provide a proof of the origin and integrity of the provided metadata. The examples in this document use the modules described there. An analysis of other potential use cases motivated to deal with the provenance mechanism described here in an independent mechanims. Provenance verification by signatures incorporated in the YANG data elements can be applied to any usage of such data not relying on a online flow, with the use of data stores (such as data lakes or time-series databases) and the application of recorded data for ML training or validation as the most relevant examples.

This document provides a mechanism for including digital signatures within YANG data. It applies COSE {{RFC9052}}, to make the signature compact and easy to calculate. This mechanism is potentially applicable to any serialization of the YANG data supporting a clear method for canoicalization (as discussed below), but this document consider three essential ones: CBOR, JSON and XML.

# Conventions and Definitions

{::boilerplate bcp14-tagged}

The term "data provenance" refers to a documented trail accounting for the origin of a piece of data and where it has moved from to where it is presently. The signature mechanism provided here can be recursively applied to allow this accounting for YANG data.

# Provenance Elements

This must be ellaborated from the example for the data manifest. Leave them as example.

The proposal implies including new optional leaves containing signatures in platform-manifest, for the platform, and in data-collection-manifest, for the collector, as follows (the new leaves are marked with an asterisk):

~~~
module: ietf-platform-manifest
  +--ro platforms
     +--ro platform* [id]
        +--ro id                      string
       +--ro platform-provenance?    string       *
        +--ro name?                   string
        +--ro vendor?                 string
        +--ro vendor-pen?             uint32
        +--ro software-version?       string
        +--ro software-flavor?        string
        +--ro os-version?             string
        +--ro os-type?                string
        +--ro yang-push-streams
        |  +--ro stream* [name]
        |     +--ro name
        |     +--ro description?
        +--ro yang-library
        + . . .
        .
        .
        .
~~~

~~~
module: ietf-data-collection-manifest
  +--ro data-collections
     +--ro data-collection* [platform-id]
        +--ro platform-id
       +--collector-provenance?    string            *
        |       -> /p-mf:platforms/platform/id
        +--ro yang-push-subscriptions
           +--ro subscription* [id]
              +--ro id
              |       sn:subscription-id
~~~

# Provenance Signature Strings

The signature strings are COSE single signature messages with \[nil\] payload and the following structure (as defined by RFC 9052):

~~~
COSE_Sign1 = [
protected /algorithm-identifier, kid, serialization-method/
unprotected /algorithm-parameters/
signature /using as EAAD the content of the (meta-)data without the signature leaf/
]
~~~

Where:

* The COSE_Sign1 procedure yields a string when building the signature and expect a string for checking it, hence the proposed type for signature leaves.
Signature algorithm and parameters will follow COSE conventions and registries.

* The kid (Key ID) has to be locally interpreted by the element evaluating the signature. URIs and RFC822-style identifiers can be considered typical kids to be used.

## Canonicalization

Signature generation and verification require a canonicalization method to be applied, that depends on the serialization used. We can consider three types of serialization:

* CBOR, what would imply the use of the length-first core deterministic encoding, as defined by RFC 8949

* JSON, what would imply the use of the JSON Canonicalization Scheme (JCS), as defined by RFC 8785

* XML, what would imply the use of the Exclusive XML Canonicalization 1.0, as defined by W3C XML signature processing.

* EAAD refers to the COSE feature of allowing the use of external application authenticated data to be combined with the signature payload. To keep a concise signature and avoid the need for wrapping YANG constructs in COSE envelopes, we propose to use the whole YANG (meta-)data being signed as EAAD, keeping a nil payload.


# Security Considerations

The provenance assessment mechanism described in this document relies in COSE {{RFC9052}} and the deterministic encoding or canonicalization described by {{RFC8949}}, {{RFC8785}} and {{XMLSig}}. The security considerations made in these references are fully applicable here.

The verification step depends on the association of the Key ID with the proper public key. This is a local matter for the verifier and its specification is out of the scope of this document. The use of certificates, PKI mechanisms, or any other secure distribution of id-public key mapping is advised.


# IANA Considerations

This document has no IANA actions.


--- back

# Acknowledgments
{:numbered="false"}
This document is based on work partially funded by the EU H2020 project SPIRS (grant 952622), and the EU Horizon Europe projects PRIVATEER (grant 101096110), HORSE (grant 101096342) and ACROSS (grant 101097122).
