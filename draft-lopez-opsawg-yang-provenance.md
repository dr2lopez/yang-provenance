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
 RFC3986:
 RFC5322:
 RFC7950:
 RFC7951:
 RFC8785:
 RFC8949:
 RFC9052:
 RFC9254:
 XMLSig:
  title: XML Signature Syntax and Processing Version 2.0
  target: https://www.w3.org/TR/xmldsig-core2/

informative:
 YANGmanifest: I-D.ietf-opsawg-collected-data-manifest

--- abstract

This document defines a mechanism based on COSE signatures to provide and verify the provenance of YANG data, so it is possible to verify the orign and integrity of a dataset, even when those data are going to be processed and/or applied in workflows where a crypto-enabled data transport directly from the original data stream is not available. As the application of evidence-based OAM and the use of tools such as AI/ML grow, provenance validation becomes more relevant in all scenarios. The use of compact signatures facilitates the inclusion of provenance strings in any YANG schema requiring them.

--- middle

# Introduction

OAM automation, generally based on  closed-loop principles, requires at least two datasets to be used. Using the common terms in Control Theory, we need those from the plant (the network device or segment under control) and those to be used as reference (the desired values of the relevant data). The usual automation behavior compares these values and takes a decision, by whatever the method (algorithmic, rule-based, an AI model tuned by ML...) to decide on a control action according to this comparison. Assurance of the origin and integrity of these datasets, what we refer in this document as "provenance", becomes essential to guarantee a proper behavior of closed-loop automation.

When datasets are made available as an online data flow, provenance can be assessed by properties of the data transport protocol, as long as some kind of cryptographic protocol is used, with TLS, SSH and IPsec as the main examples. But when these datasets are stored, go through some pre-processing or aggregation stages, or even cryptographic data transport is not available, provenance must be assessed by other means.

The original use case for this provenance mechanism is associated with {{YANGmanifest}}, in order to provide a proof of the origin and integrity of the provided metadata. The examples in this document use the modules described there. An analysis of other potential use cases motivated to deal with the provenance mechanism described here suggested the interest of defininig an independent, generally applicable mechanim. Provenance verification by signatures incorporated in the YANG data elements can be applied to any usage of such data not relying on an online flow, with the use of data stores (such as data lakes or time-series databases) and the application of recorded data for ML training or validation as the most relevant examples.

This document provides a mechanism for including digital signatures within YANG data. It applies COSE {{RFC9052}} to make the signature compact and reduce the resources required for calculating it. This mechanism is potentially applicable to any serialization of the YANG data supporting a clear method for canonicalization, but this document considers three base ones: CBOR, JSON and XML.

# Conventions and Definitions

{::boilerplate bcp14-tagged}

The term "data provenance" refers to a documented trail accounting for the origin of a piece of data and where it has moved from to where it is presently. The signature mechanism provided here can be recursively applied to allow this accounting for YANG data.

# Provenance Elements

To provide provenance for a YANG element, a new leaf element MUST be included, containing the COSE signature bitstring built according to the procedure defined in the following section. The provenance leaf MUST be of type provenance-signature, defined as follows:

~~~
typedef provenance-signature {
     type binary;
     description
      "The provenance-signature type represents a digital signature
       associated to the enclosing element. The signature is based
       on COSE and generated using a cannonicalized version of the
       enclosing element.";
     reference
      "draft-lopez-opsawg-yang-provenance";
}
~~~

When using it, a provenance-signature leaf MAY appear at any position in the enclosing element, but only one such leaf MUST be defined for the enclosing element. If the enclosing element contains other non-leaf elements, they MAY provide their own provenance-signature leaf, according to the same rule.

As example, let us consider the two modules proposed in {{YANGmanifest}}. For the platform-manifest module, the provenance for a platforn would be provided by the optional platform-provenance leaf shown below:

~~~
module: ietf-platform-manifest
  +--ro platforms
     +--ro platform* [id]
       +--ro platform-provenance?    provenance-signature
       +--ro id                      string
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

For data collections, the provenance of each one would be provided by the optional collector-provenance leaf, as shown below:

~~~
module: ietf-data-collection-manifest
  +--ro data-collections
     +--ro data-collection* [platform-id]
     +--ro platform-id
     |       -> /p-mf:platforms/platform/id
     +--ro collector-provenance?   provenance-signature
     +--ro yang-push-subscriptions
       +--ro subscription* [id]
         +--ro id
         |      sn:subscription-id
         +
         .
         .
         .
     + . . .
     |
     .
     .
     .
~~~

In both cases, and for the sake of brevity, the provenance element appears at the top of the enclosing element, but it is worth remarking they may appear anywhere within it.

# Provenance Signature Strings

Signature strings are COSE single signature messages with \[nil\] payload, according to COSE conventions and registries, and with the following structure (as defined by {{RFC9052, Section 4.2}}):

~~~
COSE_Sign1 = [
protected /algorithm-identifier, kid, serialization-method/
unprotected /algorithm-parameters/
signature /using as external data the content
           of the (meta-)data without the signature leaf/
]
~~~

The COSE_Sign1 procedure yields a bitstring when building the signature and expects a bitstring for checking it, hence the proposed type for signature leaves. The structure of the COSE_Sign1 consists of:

* The algorithm-identifier, which MUST follow COSE conventions and regisitries.

* The kid (Key ID), to be locally agreed, used and interpreted by the signer and the signature validator. URIs {{RFC3986}} and RFC822-style {{RFC5322}} identifiers are typical values to be used as kid.

* The serialization-method, a string identifying the YANG serialization in use. It MUST be one of the three possible values "xml" (for XML serialization {{RFC7950}}), "json" (for JSON serialization {{RFC7951}}) or "cbor" (for CBOR serialization {{RFC9254}}).

* The value algorithm-parameters, which MUST follow the COSE conventions for providing relevant parameters to the signing algorithm.

* The signature for the enclosing element, to be produced and verified according to the procedure described below.

## Signature and Verification Procedures

To keep a concise signature and avoid the need for wrapping YANG constructs in COSE envelopes, the whole signature MUST be built and verified by means of externally supplied data, as defined in {{RFC9052, Section 4.3}}, with a \[nil\] payload.

The byte strings to be used as input to the signature and verification procedures MUST be built by:

* Taking the whole element enclosing the signature leaf.

* Eliminating the signature leaf element.

* Applying the corresponding canonicalization method as described in the following section.

## Canonicalization

Signature generation and verification require a canonicalization method to be applied, that depends on the serialization used. According to the three types of serialization defined, the following canonicalization methods MUST be applied:

* For CBOR, length-first core deterministic encoding, as defined by {{RFC8949}}.

* For JSON, JSON Canonicalization Scheme (JCS), as defined by {{RFC8785}}.

* For XML, Exclusive XML Canonicalization 1.0, as defined by {{XMLSig}}.


# Security Considerations

The provenance assessment mechanism described in this document relies on COSE {{RFC9052}} and the deterministic encoding or canonicalization procedures described by {{RFC8949}}, {{RFC8785}} and {{XMLSig}}. The security considerations made in these references are fully applicable here.

The verification step depends on the association of the kid (Key ID) with the proper public key. This is a local matter for the verifier and its specification is out of the scope of this document. The use of certificates, PKI mechanisms, or any other secure distribution of id-public key mappings is RECOMMENDED.


# IANA Considerations

The provenance-signature type might need registration.

Others?


--- back

# Acknowledgments
{:numbered="false"}
This document is based on work partially funded by the EU H2020 project SPIRS (grant 952622), and the EU Horizon Europe projects PRIVATEER (grant 101096110), HORSE (grant 101096342) and ACROSS (grant 101097122).
