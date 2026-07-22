---

v: 3

title: Test Vectors for CBOR Encoded X.509 (C509) Certificates
abbrev: C509 Test Vectors
docname: draft-ietf-cose-c509-test-vectors-01
category: info
submissiontype: IETF

ipr: trust200902
area: Security
workgroup: COSE Working Group
keyword: Internet-Draft

stand_alone: yes
pi: [toc, sortrefs, symrefs]

coding: utf-8

author:
-
    ins: L. Liao
    name: Lijun Liao
    org: NIO
    email: lijun.liao@nio.io
-
    ins: G. Selander
    name: Göran Selander
    org: Ericsson
    email: goran.selander@ericsson.com
-
    ins: J. Preuß Mattsson
    name: John Preuß Mattsson
    org: Ericsson
    email: john.mattsson@ericsson.com

normative:
  RFC2119:
  RFC8174:
  RFC8742:
  RFC8949:
  RFC9090:

  I-D.ietf-cose-cbor-encoded-cert:

informative:

  CborMe:
    target: https://cbor.me/
    title: CBOR Playground
    author:
      -
        ins: C. Bormann
    date: May 2018

--- abstract

This document contains examples of CBOR-encoded X.509 (C509) certificates, certification requests, and certification request templates.

--- middle

# Introduction

This document contains examples of X.509 certificates, certification requests, and certification request templates encoded in CBOR {{RFC8949}} according to the C509 specification {{I-D.ietf-cose-cbor-encoded-cert}}. It complements the C509 specification by providing readable examples that illustrate the encodings of certificate and certification request fields and that can be used to test interoperability between C509 implementations.

The examples are shown in multiple encodings and formats: X.509 certificates and certification requests in PEM format, and C509 certificates and certification requests in plain hexadecimal and annotated forms.

The examples include two types of C509 certificates, distinguished by the value of the c509CertificateType field (see {{I-D.ietf-cose-cbor-encoded-cert}}):

* c509CertificateType = 03 (called type 3 in this document) is a reversible CBOR encoding of an X.509 certificate, in which the issuerSignatureValue field of the C509 certificate contains the signatureValue field of the X.509 certificate, that is, the digital signature computed over the ASN.1 DER encoding.

* c509CertificateType = 02 (called type 2 in this document) differs from type 3 only in this value and in that the issuerSignatureValue field of the C509 certificate contains the signature over the TBSCertificate of the C509 certificate, that is, the digital signature computed over the CBOR encoding.

The examples also include two types of C509 certification requests, called type 2 and type 3 in this document, distinguished by the value of the c509CertificationRequestType field.

Following {{I-D.ietf-cose-cbor-encoded-cert}}, the C509 plain hex contains the ~C509Certificate, ~C509CertificationRequest, and ~C509CertificationRequestTemplate, that is, the unwrapped CBOR Sequence {{RFC8742}}. These can readily be converted to CBOR diagnostic notation (see {{Section 8 of RFC8949}}) using the CBOR Playground {{CborMe}}. Note that CBOR Sequences require selecting the `cborseq` option in the CBOR Playground.

Private keys are also provided to enable the creation of signatures and the verification of ECDH proof of possession. The keys printed in these examples are not secret and MUST NOT be used for any purpose other than testing.

The examples are structured as follows:

* {{sec-ca}} contains a CA certificate used in later sections.

* {{sec-cert-different-keys}} contains certificates with different subject public key types, including RSA, Weierstrass EC, Edwards EC, and Montgomery EC keys.

* {{sec-cert-different-sigs}} contains certificates with different signature and proof-of-possession algorithms and points to the sections where those algorithms are encoded.

* {{sec-cert-different-attrs}} contains certificates with different RDN attributes and points to the sections where those attributes are encoded.

* {{sec-cert-different-extns}} contains certificates with different extensions and points to the sections where those extensions are encoded.

* {{sec-cert-unconvertible}} provides examples of certificates with RDN attributes or extensions for which no dedicated CBOR encoding has been defined and for which generic constructs such as CBOR OID {{RFC9090}} are used.

* {{sec-csr-different-sigs}} provides examples of certification requests with different signature or proof-of-possession algorithms.

* {{sec-csr-different-attrs}} provides examples of certification requests with different CR attributes.

* {{sec-csrt}} provides examples of certification request templates.

Editor's note: The current version does not include keys or signatures for post-quantum algorithms. These may be included in a future version or in a separate document.

## Terminology {#terminology}

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "NOT RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in BCP 14 {{RFC2119}} {{RFC8174}} when, and only when, they appear in all capitals, as shown here.
   
Readers are expected to be familiar with C509 {{I-D.ietf-cose-cbor-encoded-cert}}.

# CA Certificate {#sec-ca}

- The CA uses an Ed25519 public key because the resulting signature is compact.
- Signature algorithm: unsigned
- Key: Ed25519

## Private Key

[comment]: <> (replace-data:key/ca/prikey.pem)
~~~~~
dummy
~~~~~

## X.509 Certificate {#x509-ca}

[comment]: <> (replace-size:cert/ca/x509cert.pem)
PEM content (dummy bytes):

[comment]: <> (replace-data:cert/ca/x509cert.pem)
~~~~~
dummy
~~~~~

Textual Representation:

[comment]: <> (replace-data:cert/ca/x509cert.txt)
~~~~~
dummy
~~~~~

## C509 Type 3 Certificate

- C509 type 3 certificate converted from the X.509 certificate in {{x509-ca}}.
- Compared to the C509 type 2 certificate, the only differences are the certificate type, the signature value, and the public key identifier.

[comment]: <> (replace-size:cert/ca/c509cert-t3.hex)
Plain hex (dummy bytes):

[comment]: <> (replace-data:cert/ca/c509cert-t3.hex)
~~~~~
dummy
~~~~~

Annotated hex:

- See the annotated hex for the C509 type 2 certificate in {{c509-ca}}. The only differences are the certificate type, the signature value, and the key identifiers.

## C509 Type 2 Certificate {#c509-ca}

[comment]: <> (replace-size:cert/ca/c509cert-t2.hex)
Plain hex (dummy bytes):

[comment]: <> (replace-data:cert/ca/c509cert-t2.hex)
~~~~~
dummy
~~~~~

Annotated hex:

[comment]: <> (replace-data:cert/ca/c509cert-t2.diag)
~~~~~
dummy
~~~~~

# Certificates With Different Subject Public Keys {#sec-cert-different-keys}

## RSA Public Key With Public Exponent 65537 {#selfsign-rsa}

- Self-signed certificate
- RSA public key with public exponent 65537 (= 2^16 + 1) and a 1024-bit modulus.
- Signature algorithm: sha256WithRSAEncryption
- NotAfter: null
- Subject: a `commonName` containing an EUI-48 value
- Extensions: a single non-critical `keyUsage` extension

### Private Key {#key-selfsign-rsa}

[comment]: <> (replace-data:key/rsa/prikey.pem)
~~~~~
dummy
~~~~~

### X.509 Certificate {#x509-selfsign-rsa}

[comment]: <> (replace-size:cert/selfsign-rsa/x509cert.pem)
PEM content (dummy bytes):

[comment]: <> (replace-data:cert/selfsign-rsa/x509cert.pem)
~~~~~
dummy
~~~~~

Textual Representation:

[comment]: <> (replace-data:cert/selfsign-rsa/x509cert.txt)
~~~~~
dummy
~~~~~

### C509 Type 3 Certificate

- C509 type 3 certificate converted from the X.509 certificate in {{x509-selfsign-rsa}}.
- Compared to the C509 type 2 certificate, the only differences are the certificate type, the signature value, and the public key identifier.

[comment]: <> (replace-size:cert/selfsign-rsa/c509cert-t3.hex)
Plain hex (dummy bytes):

[comment]: <> (replace-data:cert/selfsign-rsa/c509cert-t3.hex)
~~~~~
dummy
~~~~~

Annotated hex:

- See the annotated hex for the C509 type 2 certificate in {{c509-selfsign-rsa}}. The only differences are the certificate type, the signature value, and the key identifiers.

### C509 Type 2 Certificate {#c509-selfsign-rsa}

[comment]: <> (replace-size:cert/selfsign-rsa/c509cert-t2.hex)
Plain hex (dummy bytes):

[comment]: <> (replace-data:cert/selfsign-rsa/c509cert-t2.hex)
~~~~~
dummy
~~~~~

Annotated hex:

[comment]: <> (replace-data:cert/selfsign-rsa/c509cert-t2.diag)
~~~~~
dummy
~~~~~

## RSA Public Key With Public Exponent 4294967297 {#selfsign-rsa-f5}

- Self-signed certificate
- RSA public key with public exponent 4294967297 (= 2^32 + 1) and a 1024-bit modulus
- Signature algorithm: sha384WithRSAEncryption
- Subject: a `commonName` containing an EUI-64 value
- Extensions: a single critical `keyUsage` extension

### Private Key

[comment]: <> (replace-data:key/rsa-f5/prikey.pem)
~~~~~
dummy
~~~~~

### X.509 Certificate {#x509-selfsign-rsa-f5}

[comment]: <> (replace-size:cert/selfsign-rsa-f5/x509cert.pem)
PEM content (dummy bytes):

[comment]: <> (replace-data:cert/selfsign-rsa-f5/x509cert.pem)
~~~~~
dummy
~~~~~

Textual Representation:

[comment]: <> (replace-data:cert/selfsign-rsa-f5/x509cert.txt)
~~~~~
dummy
~~~~~

### C509 Type 3 Certificate

- C509 type 3 certificate converted from the X.509 certificate in {{x509-selfsign-rsa-f5}}.

[comment]: <> (replace-size:cert/selfsign-rsa-f5/c509cert-t3.hex)
Plain hex (dummy bytes):

[comment]: <> (replace-data:cert/selfsign-rsa-f5/c509cert-t3.hex)
~~~~~
dummy
~~~~~

Annotated hex:

- See the annotated hex for the C509 type 2 certificate in {{c509-selfsign-rsa-f5}}. The only differences are the certificate type, the signature value, and the key identifiers.

### C509 Type 2 Certificate {#c509-selfsign-rsa-f5}

[comment]: <> (replace-size:cert/selfsign-rsa-f5/c509cert-t2.hex)
Plain hex (dummy bytes):

[comment]: <> (replace-data:cert/selfsign-rsa-f5/c509cert-t2.hex)
~~~~~
dummy
~~~~~

Annotated hex:

[comment]: <> (replace-data:cert/selfsign-rsa-f5/c509cert-t2.diag)
~~~~~
dummy
~~~~~

## Weierstrass EC Public Key On Curve secp256r1 {#selfsign-secp256r1}

- Self-signed certificate
- EC public key on the curve secp256r1
- Signature algorithm: ecdsa-with-sha256
- Subject: a `commonName` containing an even number of lowercase hexadecimal characters
- Extensions
  - Basic Constraints: CA, without pathLenConstraint
  - Extended Key Usage: only an integer-identified usage
  - Subject Key Identifier

### Private Key {#key-selfsign-secp256r1}

[comment]: <> (replace-data:key/secp256r1/prikey.pem)
~~~~~
dummy
~~~~~

### X.509 Certificate {#x509-selfsign-secp256r1}

[comment]: <> (replace-size:cert/selfsign-secp256r1/x509cert.pem)
PEM content (dummy bytes):

[comment]: <> (replace-data:cert/selfsign-secp256r1/x509cert.pem)
~~~~~
dummy
~~~~~

Textual Representation:

[comment]: <> (replace-data:cert/selfsign-secp256r1/x509cert.txt)
~~~~~
dummy
~~~~~

### C509 Type 3 Certificate

- C509 type 3 certificate converted from the X.509 certificate in {{x509-selfsign-secp256r1}}.

[comment]: <> (replace-size:cert/selfsign-secp256r1/c509cert-t3.hex)
Plain hex (dummy bytes):

[comment]: <> (replace-data:cert/selfsign-secp256r1/c509cert-t3.hex)
~~~~~
dummy
~~~~~

Annotated hex:

- See the annotated hex for the C509 type 2 certificate in {{c509-selfsign-secp256r1}}. The only differences are the certificate type, the signature value, and the public key identifier.

### C509 Type 2 Certificate {#c509-selfsign-secp256r1}

[comment]: <> (replace-size:cert/selfsign-secp256r1/c509cert-t2.hex)
Plain hex (dummy bytes):

[comment]: <> (replace-data:cert/selfsign-secp256r1/c509cert-t2.hex)
~~~~~
dummy
~~~~~

Annotated hex:

[comment]: <> (replace-data:cert/selfsign-secp256r1/c509cert-t2.diag)
~~~~~
dummy
~~~~~

## Compressed Weierstrass EC Public Key On Curve secp256r1 {#selfsign-compress-secp256r1}

- Self-signed certificate
- EC public key on the curve secp256r1, compressed only in the C509 certificates
- Signature algorithm: ecdsa-with-sha256
- Subject: a `commonName` containing an even number of lowercase hexadecimal characters
- Extensions
  - Basic Constraints: CA, without pathLenConstraint
  - Extended Key Usage: only an integer-identified usage
  - Subject Key Identifier

### Private Key

See {{key-selfsign-secp256r1}}

### X.509 Certificate {#x509-selfsign-compress-secp256r1}

See {{x509-selfsign-secp256r1}}

### C509 Type 3 Certificate

- C509 type 3 certificate converted from the X.509 certificate in {{x509-selfsign-compress-secp256r1}}.

[comment]: <> (replace-size:cert/selfsign-compress-secp256r1/c509cert-t3.hex)
Plain hex (dummy bytes):

[comment]: <> (replace-data:cert/selfsign-compress-secp256r1/c509cert-t3.hex)
~~~~~
dummy
~~~~~

Annotated hex:

- See the annotated hex for the C509 type 2 certificate in {{c509-selfsign-compress-secp256r1}}. The only differences are the certificate type, the signature value, and the public key identifier.

### C509 Type 2 Certificate {#c509-selfsign-compress-secp256r1}

[comment]: <> (replace-size:cert/selfsign-compress-secp256r1/c509cert-t2.hex)
Plain hex (dummy bytes):

[comment]: <> (replace-data:cert/selfsign-compress-secp256r1/c509cert-t2.hex)
~~~~~
dummy
~~~~~

Annotated hex:

[comment]: <> (replace-data:cert/selfsign-compress-secp256r1/c509cert-t2.diag)
~~~~~
dummy
~~~~~

## Weierstrass EC Public Key On Curve secp384r1 {#selfsign-secp384r1}

- Self-signed certificate
- EC public key on the curve secp384r1
- Subject: a `commonName` containing free-form text
- Extensions
  - Basic Constraints: CA, with pathLenConstraint
  - Extended Key Usage: only an OID-identified usage
  - Certificate Policies
  - Inhibit anyPolicy

### Private Key {#key-selfsign-secp384r1}

[comment]: <> (replace-data:key/secp384r1/prikey.pem)
~~~~~
dummy
~~~~~

### X.509 Certificate {#x509-selfsign-secp384r1}

[comment]: <> (replace-size:cert/selfsign-secp384r1/x509cert.pem)
PEM content (dummy bytes):

[comment]: <> (replace-data:cert/selfsign-secp384r1/x509cert.pem)
~~~~~
dummy
~~~~~

Textual Representation:

[comment]: <> (replace-data:cert/selfsign-secp384r1/x509cert.txt)
~~~~~
dummy
~~~~~

### C509 Type 3 Certificate

- C509 type 3 certificate converted from the X.509 certificate in {{x509-selfsign-secp384r1}}.

[comment]: <> (replace-size:cert/selfsign-secp384r1/c509cert-t3.hex)
Plain hex (dummy bytes):

[comment]: <> (replace-data:cert/selfsign-secp384r1/c509cert-t3.hex)
~~~~~
dummy
~~~~~

Annotated hex:

- See the annotated hex for the C509 type 2 certificate in {{c509-selfsign-secp384r1}}. The only differences are the certificate type, the signature value, and the key identifiers.

### C509 Type 2 Certificate {#c509-selfsign-secp384r1}

[comment]: <> (replace-size:cert/selfsign-secp384r1/c509cert-t2.hex)
Plain hex (dummy bytes):

[comment]: <> (replace-data:cert/selfsign-secp384r1/c509cert-t2.hex)
~~~~~
dummy
~~~~~

Annotated hex:

[comment]: <> (replace-data:cert/selfsign-secp384r1/c509cert-t2.diag)
~~~~~
dummy
~~~~~

## Weierstrass EC Public Key On Curve secp521r1 {#selfsign-secp521r1}

- Self-signed certificate
- EC public key on the curve secp521r1
- Signature algorithm: ecdsa-with-sha512
- Subject: empty
- Extensions
  - Basic Constraints: non-CA
  - Extended Key Usage: integer-identified and OID-identified usages
  - Subject Alternative Name

### Private Key {#key-selfsign-secp521r1}

[comment]: <> (replace-data:key/secp521r1/prikey.pem)
~~~~~
dummy
~~~~~

### X.509 Certificate {#x509-selfsign-secp521r1}

[comment]: <> (replace-size:cert/selfsign-secp521r1/x509cert.pem)
PEM content (dummy bytes):

[comment]: <> (replace-data:cert/selfsign-secp521r1/x509cert.pem)
~~~~~
dummy
~~~~~

Textual Representation:

[comment]: <> (replace-data:cert/selfsign-secp521r1/x509cert.txt)
~~~~~
dummy
~~~~~

### C509 Type 3 Certificate

- C509 type 3 certificate converted from the X.509 certificate in {{x509-selfsign-secp521r1}}.

[comment]: <> (replace-size:cert/selfsign-secp521r1/c509cert-t3.hex)
Plain hex (dummy bytes):

[comment]: <> (replace-data:cert/selfsign-secp521r1/c509cert-t3.hex)
~~~~~
dummy
~~~~~

Annotated hex:

- See the annotated hex for the C509 type 2 certificate in {{c509-selfsign-secp521r1}}. The only differences are the certificate type, the signature value, and the key identifiers.

### C509 Type 2 Certificate {#c509-selfsign-secp521r1}

[comment]: <> (replace-size:cert/selfsign-secp521r1/c509cert-t2.hex)
Plain hex (dummy bytes):

[comment]: <> (replace-data:cert/selfsign-secp521r1/c509cert-t2.hex)
~~~~~
dummy
~~~~~

Annotated hex:

[comment]: <> (replace-data:cert/selfsign-secp521r1/c509cert-t2.diag)
~~~~~
dummy
~~~~~

## Weierstrass EC Public Key On Curve sm2p256v1 {#selfsign-sm2p256v1}

- Self-signed certificate
- EC public key on the curve sm2p256v1
- Signature algorithm: sm2-with-sm3
- Subject:
  - serialNumber
  - organization
  - organizationalUnit
  - organizationIdentifier
- Extensions:
  - Policy Constraints containing only `inhibitPolicyMapping`
  - Name Constraints containing only `excludedSubTrees`

### Private Key

[comment]: <> (replace-data:key/sm2p256v1/prikey.pem)
~~~~~
dummy
~~~~~

### X.509 Certificate {#x509-selfsign-sm2p256v1}

[comment]: <> (replace-size:cert/selfsign-sm2p256v1/x509cert.pem)
PEM content (dummy bytes):

[comment]: <> (replace-data:cert/selfsign-sm2p256v1/x509cert.pem)
~~~~~
dummy
~~~~~

Textual Representation:

[comment]: <> (replace-data:cert/selfsign-sm2p256v1/x509cert.txt)
~~~~~
dummy
~~~~~

### C509 Type 3 Certificate

- C509 type 3 certificate converted from the X.509 certificate in {{x509-selfsign-sm2p256v1}}.

[comment]: <> (replace-size:cert/selfsign-sm2p256v1/c509cert-t3.hex)
Plain hex (dummy bytes):

[comment]: <> (replace-data:cert/selfsign-sm2p256v1/c509cert-t3.hex)
~~~~~
dummy
~~~~~

Annotated hex:

- See the annotated hex for the C509 type 2 certificate in {{c509-selfsign-sm2p256v1}}. The only differences are the certificate type, the signature value, and the key identifiers.

### C509 Type 2 Certificate {#c509-selfsign-sm2p256v1}

[comment]: <> (replace-size:cert/selfsign-sm2p256v1/c509cert-t2.hex)
Plain hex (dummy bytes):

[comment]: <> (replace-data:cert/selfsign-sm2p256v1/c509cert-t2.hex)
~~~~~
dummy
~~~~~

Annotated hex:

[comment]: <> (replace-data:cert/selfsign-sm2p256v1/c509cert-t2.diag)
~~~~~
dummy
~~~~~

## Weierstrass EC Public Key On Curve brainpoolP256r1 {#selfsign-brainpoolp256r1}

- Self-signed certificate
- EC public key on the curve brainpoolP256r1
- Signature algorithm: ecdsa-with-shake128
- Subject:
  - country
  - state
  - locality
  - postalCode
  - street
- Extensions:
  - IPAddrBlocks with `SAFI` = `null` and IP Address Choice = `null`
  - IPAddrBlocks V2 with `SAFI` = `null` and IP Address Choice = `null`
  - ASIdentifiers
  - ASIdentifiers V2

### Private Key

[comment]: <> (replace-data:key/brainpoolp256r1/prikey.pem)
~~~~~
dummy
~~~~~

### X.509 Certificate {#x509-selfsign-brainpoolp256r1}

[comment]: <> (replace-size:cert/selfsign-brainpoolp256r1/x509cert.pem)
PEM content (dummy bytes):

[comment]: <> (replace-data:cert/selfsign-brainpoolp256r1/x509cert.pem)
~~~~~
dummy
~~~~~

Textual Representation:

[comment]: <> (replace-data:cert/selfsign-brainpoolp256r1/x509cert.txt)
~~~~~
dummy
~~~~~

### C509 Type 3 Certificate

- C509 type 3 certificate converted from the X.509 certificate in {{x509-selfsign-brainpoolp256r1}}.

[comment]: <> (replace-size:cert/selfsign-brainpoolp256r1/c509cert-t3.hex)
Plain hex (dummy bytes):

[comment]: <> (replace-data:cert/selfsign-brainpoolp256r1/c509cert-t3.hex)
~~~~~
dummy
~~~~~

Annotated hex:

- See the annotated hex for the C509 type 2 certificate in {{c509-selfsign-brainpoolp256r1}}. The only differences are the certificate type, the signature value, and the key identifiers.

### C509 Type 2 Certificate {#c509-selfsign-brainpoolp256r1}

[comment]: <> (replace-size:cert/selfsign-brainpoolp256r1/c509cert-t2.hex)
Plain hex (dummy bytes):

[comment]: <> (replace-data:cert/selfsign-brainpoolp256r1/c509cert-t2.hex)
~~~~~
dummy
~~~~~

Annotated hex:

[comment]: <> (replace-data:cert/selfsign-brainpoolp256r1/c509cert-t2.diag)
~~~~~
dummy
~~~~~

## Weierstrass EC Public Key On Curve brainpoolP384r1 {#selfsign-brainpoolp384r1}

- Self-signed certificate
- EC public key on the curve brainpoolP384r1
- Signature algorithm: ecdsa-with-sha384
- Subject:
  - surname
  - givenName
  - title
  - name
- Extensions:
  - IPAddrBlocks with non-null `SAFI` and IntIPAddressChoice`
  - IPAddrBlocks V2 with non-null `SAFI`, `IntIPAddressChoice` and `IPAddressChoice`

### Private Key

[comment]: <> (replace-data:key/brainpoolp384r1/prikey.pem)
~~~~~
dummy
~~~~~

### X.509 Certificate {#x509-selfsign-brainpoolp384r1}

[comment]: <> (replace-size:cert/selfsign-brainpoolp384r1/x509cert.pem)
PEM content (dummy bytes):

[comment]: <> (replace-data:cert/selfsign-brainpoolp384r1/x509cert.pem)
~~~~~
dummy
~~~~~

Textual Representation:

[comment]: <> (replace-data:cert/selfsign-brainpoolp384r1/x509cert.txt)
~~~~~
dummy
~~~~~

### C509 Type 3 Certificate

- C509 type 3 certificate converted from the X.509 certificate in {{x509-selfsign-brainpoolp384r1}}.

[comment]: <> (replace-size:cert/selfsign-brainpoolp384r1/c509cert-t3.hex)
Plain hex (dummy bytes):

[comment]: <> (replace-data:cert/selfsign-brainpoolp384r1/c509cert-t3.hex)
~~~~~
dummy
~~~~~

Annotated hex:

- See the annotated hex for the C509 type 2 certificate in {{c509-selfsign-brainpoolp384r1}}. The only differences are the certificate type, the signature value, and the key identifiers.

### C509 Type 2 Certificate {#c509-selfsign-brainpoolp384r1}

[comment]: <> (replace-size:cert/selfsign-brainpoolp384r1/c509cert-t2.hex)
Plain hex (dummy bytes):

[comment]: <> (replace-data:cert/selfsign-brainpoolp384r1/c509cert-t2.hex)
~~~~~
dummy
~~~~~

Annotated hex:

[comment]: <> (replace-data:cert/selfsign-brainpoolp384r1/c509cert-t2.diag)
~~~~~
dummy
~~~~~

## Weierstrass EC Public Key On Curve brainpoolP512r1 {#selfsign-brainpoolp512r1}

- Self-signed certificate
- EC public key on the curve brainpoolP512r1
- Signature algorithm: ecdsa-with-shake256
- Subject:
  - jurisdictionCountryName
  - jurisdictionStateOrProvinceName
  - jurisdictionLocalityName
- Extensions:
  - Subject Directory Attributes
  - Subject Information Access
  - Policy Mappings

### Private Key

[comment]: <> (replace-data:key/brainpoolp512r1/prikey.pem)
~~~~~
dummy
~~~~~

### X.509 Certificate {#x509-selfsign-brainpoolp512r1}

[comment]: <> (replace-size:cert/selfsign-brainpoolp512r1/x509cert.pem)
PEM content (dummy bytes):

[comment]: <> (replace-data:cert/selfsign-brainpoolp512r1/x509cert.pem)
~~~~~
dummy
~~~~~

Textual Representation:

[comment]: <> (replace-data:cert/selfsign-brainpoolp512r1/x509cert.txt)
~~~~~
dummy
~~~~~

### C509 Type 3 Certificate

- C509 type 3 certificate converted from the X.509 certificate in {{x509-selfsign-brainpoolp512r1}}.

[comment]: <> (replace-size:cert/selfsign-brainpoolp512r1/c509cert-t3.hex)
Plain hex (dummy bytes):

[comment]: <> (replace-data:cert/selfsign-brainpoolp512r1/c509cert-t3.hex)
~~~~~
dummy
~~~~~

Annotated hex:

- See the annotated hex for the C509 type 2 certificate in {{c509-selfsign-brainpoolp512r1}}. The only differences are the certificate type, the signature value, and the key identifiers.

### C509 Type 2 Certificate {#c509-selfsign-brainpoolp512r1}

[comment]: <> (replace-size:cert/selfsign-brainpoolp512r1/c509cert-t2.hex)
Plain hex (dummy bytes):

[comment]: <> (replace-data:cert/selfsign-brainpoolp512r1/c509cert-t2.hex)
~~~~~
dummy
~~~~~

Annotated hex:

[comment]: <> (replace-data:cert/selfsign-brainpoolp512r1/c509cert-t2.diag)
~~~~~
dummy
~~~~~

## Weierstrass EC Public Key On Curve frp256v1 {#selfsign-frp256v1}

- Self-signed certificate
- EC public key on the curve frp256v1
- Signature algorithm: ecdsa-with-sha1
- Subject:
  - emailAddress
  - telephoneNumber
  - businessCategory
- Extensions:
  - Policy Constraints containing only `requireExplicitPolicy`
  - Name Constraints containing only `permittedSubTrees`

### Private Key

[comment]: <> (replace-data:key/frp256v1/prikey.pem)
~~~~~
dummy
~~~~~

### X.509 Certificate {#x509-selfsign-frp256v1}

[comment]: <> (replace-size:cert/selfsign-frp256v1/x509cert.pem)
PEM content (dummy bytes):

[comment]: <> (replace-data:cert/selfsign-frp256v1/x509cert.pem)
~~~~~
dummy
~~~~~

Textual Representation:

[comment]: <> (replace-data:cert/selfsign-frp256v1/x509cert.txt)
~~~~~
dummy
~~~~~
### C509 Type 3 Certificate

- C509 type 3 certificate converted from the X.509 certificate in {{x509-selfsign-frp256v1}}.

[comment]: <> (replace-size:cert/selfsign-frp256v1/c509cert-t3.hex)
Plain hex (dummy bytes):

[comment]: <> (replace-data:cert/selfsign-frp256v1/c509cert-t3.hex)
~~~~~
dummy
~~~~~

Annotated hex:

- See the annotated hex for the C509 type 2 certificate in {{c509-selfsign-frp256v1}}. The only differences are the certificate type, the signature value, and the key identifiers.

### C509 Type 2 Certificate {#c509-selfsign-frp256v1}

[comment]: <> (replace-size:cert/selfsign-frp256v1/c509cert-t2.hex)
Plain hex (dummy bytes):

[comment]: <> (replace-data:cert/selfsign-frp256v1/c509cert-t2.hex)
~~~~~
dummy
~~~~~

Annotated hex:

[comment]: <> (replace-data:cert/selfsign-frp256v1/c509cert-t2.diag)
~~~~~
dummy
~~~~~

## Montgomery EC Public Key On Curve X25519 {#ee-x25519}

- X25519 public key
- Extensions
  - authorityKeyIdentifier containing only the `keyIdentifier` component
  - authorityInfoAccess
  - issuerAltName

### Private Key

[comment]: <> (replace-data:key/x25519/prikey.pem)
~~~~~
dummy
~~~~~

### X.509 Certificate {#x509-ee-x25519}

- Issued by the CA in {{x509-ca}}.

[comment]: <> (replace-size:cert/ee-x25519/x509cert.pem)
PEM content (dummy bytes):

[comment]: <> (replace-data:cert/ee-x25519/x509cert.pem)
~~~~~
dummy
~~~~~

Textual Representation:

[comment]: <> (replace-data:cert/ee-x25519/x509cert.txt)
~~~~~
dummy
~~~~~

### C509 Type 3 Certificate

- C509 type 3 certificate converted from the X.509 certificate in {{x509-ee-x25519}}.

[comment]: <> (replace-size:cert/ee-x25519/c509cert-t3.hex)
Plain hex (dummy bytes):

[comment]: <> (replace-data:cert/ee-x25519/c509cert-t3.hex)
~~~~~
dummy
~~~~~

Annotated hex:

- See the annotated hex for the C509 type 2 certificate in {{c509-ee-x25519}}. The only differences are the certificate type, the signature value, and the key identifiers.

### C509 Type 2 Certificate {#c509-ee-x25519}

- Issued by the CA in {{c509-ca}}.

[comment]: <> (replace-size:cert/ee-x25519/c509cert-t2.hex)
Plain hex (dummy bytes):

[comment]: <> (replace-data:cert/ee-x25519/c509cert-t2.hex)
~~~~~
dummy
~~~~~

Annotated hex:

[comment]: <> (replace-data:cert/ee-x25519/c509cert-t2.diag)
~~~~~
dummy
~~~~~

## Montgomery EC Public Key On Curve X448 {#ee-x448}

- X448 public key
- Extensions:
  - authorityKeyIdentifier containing all fields
  - crlDistributionPoints
  - freshestCRL

### Private Key

[comment]: <> (replace-data:key/x448/prikey.pem)
~~~~~
dummy
~~~~~

### X.509 Certificate {#x509-ee-x448}

- Issued by the CA in {{x509-ca}}.

[comment]: <> (replace-size:cert/ee-x448/x509cert.pem)
PEM content (dummy bytes):

[comment]: <> (replace-data:cert/ee-x448/x509cert.pem)
~~~~~
dummy
~~~~~

Textual Representation:

[comment]: <> (replace-data:cert/ee-x448/x509cert.txt)
~~~~~
dummy
~~~~~

### C509 Type 3 Certificate

- C509 type 3 certificate converted from the X.509 certificate in {{x509-ee-x448}}.

[comment]: <> (replace-size:cert/ee-x448/c509cert-t3.hex)
Plain hex (dummy bytes):

[comment]: <> (replace-data:cert/ee-x448/c509cert-t3.hex)
~~~~~
dummy
~~~~~

Annotated hex:

- See the annotated hex for the C509 type 2 certificate in {{c509-ee-x448}}. The only differences are the certificate type, the signature value, and the key identifiers.

### C509 Type 2 Certificate {#c509-ee-x448}

- Issued by the CA in {{c509-ca}}.

[comment]: <> (replace-size:cert/ee-x448/c509cert-t2.hex)
Plain hex (dummy bytes):

[comment]: <> (replace-data:cert/ee-x448/c509cert-t2.hex)
~~~~~
dummy
~~~~~

Annotated hex:

[comment]: <> (replace-data:cert/ee-x448/c509cert-t2.diag)
~~~~~
dummy
~~~~~

## Edwards EC Public Key On Curve ED25519 {#selfsign-ed25519}

- Self-signed certificate
- Edwards public key Ed25519
- Signature algorithm: ed25519
- Subject:
  - domainComponent
  - dnQualifier
  - dmdName
  - unstructuredName
  - unstructuredAddress
  - generationQualifier
- Extensions:
  - Policy Constraints containing both `requireExplicitPolicy` and `inhibitPolicyMapping`
  - Name Constraints containing both `permittedSubTrees` and `excludedSubTrees`

### Private Key

[comment]: <> (replace-data:key/ed25519/prikey.pem)
~~~~~
dummy
~~~~~

### X.509 Certificate {#x509-selfsign-ed25519}

[comment]: <> (replace-size:cert/selfsign-ed25519/x509cert.pem)
PEM content (dummy bytes):

[comment]: <> (replace-data:cert/selfsign-ed25519/x509cert.pem)
~~~~~
dummy
~~~~~

Textual Representation:

[comment]: <> (replace-data:cert/selfsign-ed25519/x509cert.txt)
~~~~~
dummy
~~~~~

### C509 Type 3 Certificate

- C509 type 3 certificate converted from the X.509 certificate in {{x509-selfsign-ed25519}}.

[comment]: <> (replace-size:cert/selfsign-ed25519/c509cert-t3.hex)
Plain hex (dummy bytes):

[comment]: <> (replace-data:cert/selfsign-ed25519/c509cert-t3.hex)
~~~~~
dummy
~~~~~

Annotated hex:

- See the annotated hex for the C509 type 2 certificate in {{c509-selfsign-ed25519}}. The only differences are the certificate type, the signature value, and the key identifiers.

### C509 Type 2 Certificate {#c509-selfsign-ed25519}

[comment]: <> (replace-size:cert/selfsign-ed25519/c509cert-t2.hex)
Plain hex (dummy bytes):

[comment]: <> (replace-data:cert/selfsign-ed25519/c509cert-t2.hex)
~~~~~
dummy
~~~~~

Annotated hex:

[comment]: <> (replace-data:cert/selfsign-ed25519/c509cert-t2.diag)
~~~~~
dummy
~~~~~

## Edwards EC Public Key On Curve ED448 {#selfsign-ed448}

- Self-signed certificate
- Edwards public key Ed448
- Signature algorithm: ed448
- Subject:
  - initials
  - pseudonym
  - userId
- Extensions:
  - OCSP No Check
  - TLS Features

### Private Key

[comment]: <> (replace-data:key/ed448/prikey.pem)
~~~~~
dummy
~~~~~

### X.509 Certificate {#x509-selfsign-ed448}

[comment]: <> (replace-size:cert/selfsign-ed448/x509cert.pem)
PEM content (dummy bytes):

[comment]: <> (replace-data:cert/selfsign-ed448/x509cert.pem)
~~~~~
dummy
~~~~~

Textual Representation:

[comment]: <> (replace-data:cert/selfsign-ed448/x509cert.txt)
~~~~~
dummy
~~~~~

### C509 Type 3 Certificate

- C509 type 3 certificate converted from the X.509 certificate in {{x509-selfsign-ed448}}.

[comment]: <> (replace-size:cert/selfsign-ed448/c509cert-t3.hex)
Plain hex (dummy bytes):

[comment]: <> (replace-data:cert/selfsign-ed448/c509cert-t3.hex)
~~~~~
dummy
~~~~~

Annotated hex:

- See the annotated hex for the C509 type 2 certificate in {{c509-selfsign-ed448}}. The only differences are the certificate type, the signature value, and the key identifiers.

### C509 Type 2 Certificate {#c509-selfsign-ed448}

[comment]: <> (replace-size:cert/selfsign-ed448/c509cert-t2.hex)
Plain hex (dummy bytes):

[comment]: <> (replace-data:cert/selfsign-ed448/c509cert-t2.hex)
~~~~~
dummy
~~~~~

Annotated hex:

[comment]: <> (replace-data:cert/selfsign-ed448/c509cert-t2.diag)
~~~~~
dummy
~~~~~

# Certificates With Different Signature Algorithms {#sec-cert-different-sigs}

## RSASSA-PKCS1-v1_5 With SHA-1 {#selfsign-rsa-with-sha1}

- Self-signed certificate
- Signature algorithm: sha1WithRSAEncryption

### Private Key

See {{key-selfsign-rsa}}.

### X.509 Certificate {#x509-selfsign-rsa-with-sha1}

[comment]: <> (replace-size:cert/selfsign-rsa-with-sha1/x509cert.pem)
PEM content (dummy bytes):

[comment]: <> (replace-data:cert/selfsign-rsa-with-sha1/x509cert.pem)
~~~~~
dummy
~~~~~

Textual Representation:

[comment]: <> (replace-data:cert/selfsign-rsa-with-sha1/x509cert.txt)
~~~~~
dummy
~~~~~

### C509 Type 3 Certificate

- C509 type 3 certificate converted from the X.509 certificate in {{x509-selfsign-rsa-with-sha1}}.

[comment]: <> (replace-size:cert/selfsign-rsa-with-sha1/c509cert-t3.hex)
Plain hex (dummy bytes):

[comment]: <> (replace-data:cert/selfsign-rsa-with-sha1/c509cert-t3.hex)
~~~~~
dummy
~~~~~

Annotated hex:

- See the annotated hex for the C509 type 2 certificate in {{c509-selfsign-rsa-with-sha1}}. The only differences are the certificate type, the signature value, and the key identifiers.

### C509 Type 2 Certificate {#c509-selfsign-rsa-with-sha1}

[comment]: <> (replace-size:cert/selfsign-rsa-with-sha1/c509cert-t2.hex)
Plain hex (dummy bytes):

[comment]: <> (replace-data:cert/selfsign-rsa-with-sha1/c509cert-t2.hex)
~~~~~
dummy
~~~~~

Annotated hex:

[comment]: <> (replace-data:cert/selfsign-rsa-with-sha1/c509cert-t2.diag)
~~~~~
dummy
~~~~~

## ECDSA With SHA1 {#selfsign-ecdsa-with-sha1}

- Signature algorithm: ecdsa-with-sha1

See {{selfsign-frp256v1}}.

## ECDSA With SHA256

- Signature algorithm: ecdsa-with-sha256

See {{selfsign-secp256r1}}.

## ECDSA With SHA384

- Signature algorithm: ecdsa-with-sha384

See {{selfsign-secp384r1}}.

## ECDSA With SHA512

- Signature algorithm: ecdsa-with-sha512

See {{selfsign-secp521r1}}.

## ECDSA With SHAKE128

- Signature algorithm: ecdsa-with-shake128

See {{selfsign-brainpoolp256r1}}.

## ECDSA With SHAKE256

- Signature algorithm: ecdsa-with-shake256

See {{selfsign-brainpoolp512r1}}.

## Unsigned

- Signature algorithm: unsigned

See {{sec-ca}}.

## SM2 With SM3

- Signature algorithm: sm2-with-sm3

See {{selfsign-sm2p256v1}}.

## Ed25519

- Signature algorithm: ed25519

See {{selfsign-ed25519}}.

## Ed448

- Signature algorithm: ed448

See {{selfsign-ed448}}.

## ECDH PoP With SHA-256 And HMAC-SHA256

- Signature algorithm: sa-ecdhPop-sha256-hmac-sha256

See {{csr-dhsig-sha256}}.

## ECDH PoP With SHA-384 And HMAC-SHA384

- Signature algorithm: sa-ecdhPop-sha384-hmac-sha384

See {{csr-dhsig-sha384}}.

## ECDH PoP With SHA-512 And HMAC-SHA512

- Signature algorithm: sa-ecdhPop-sha512-hmac-sha512

See {{csr-dhsig-sha512}}.

## RSASSA-PKCS1-v1_5 With SHA-256

- Signature algorithm: sha256WithRSAEncryption

See {{selfsign-rsa}}.

## RSASSA-PKCS1-v1_5 With SHA-384

- Signature algorithm: sha384WithRSAEncryption

See {{selfsign-rsa-f5}}.

## RSASSA-PKCS1-v1_5 With SHA-512

- Self-signed certificate
- Signature algorithm: sha512WithRSAEncryption

### Private Key

See {{key-selfsign-rsa}}.

### X.509 Certificate {#x509-selfsign-rsa-with-sha512}

[comment]: <> (replace-size:cert/selfsign-rsa-with-sha512/x509cert.pem)
PEM content (dummy bytes):

[comment]: <> (replace-data:cert/selfsign-rsa-with-sha512/x509cert.pem)
~~~~~
dummy
~~~~~

Textual Representation:

[comment]: <> (replace-data:cert/selfsign-rsa-with-sha512/x509cert.txt)
~~~~~
dummy
~~~~~

### C509 Type 3 Certificate

- C509 type 3 certificate converted from the X.509 certificate in {{x509-selfsign-rsa-with-sha512}}.

[comment]: <> (replace-size:cert/selfsign-rsa-with-sha512/c509cert-t3.hex)
Plain hex (dummy bytes):

[comment]: <> (replace-data:cert/selfsign-rsa-with-sha512/c509cert-t3.hex)
~~~~~
dummy
~~~~~

Annotated hex:

- See the annotated hex for the C509 type 2 certificate in {{c509-selfsign-rsa-with-sha512}}. The only differences are the certificate type, the signature value, and the key identifiers.

### C509 Type 2 Certificate {#c509-selfsign-rsa-with-sha512}

[comment]: <> (replace-size:cert/selfsign-rsa-with-sha512/c509cert-t2.hex)
Plain hex (dummy bytes):

[comment]: <> (replace-data:cert/selfsign-rsa-with-sha512/c509cert-t2.hex)
~~~~~
dummy
~~~~~

Annotated hex:

[comment]: <> (replace-data:cert/selfsign-rsa-with-sha512/c509cert-t2.diag)
~~~~~
dummy
~~~~~

## RSASSA-PSS With SHA-256

- Self-signed certificate
- Signature algorithm: rsassa-pss-with-sha256

### Private Key

See {{key-selfsign-rsa}}.

### X.509 Certificate {#x509-selfsign-rsassa-pss-sha256}

[comment]: <> (replace-size:cert/selfsign-rsassa-pss-sha256/x509cert.pem)
PEM content (dummy bytes):

[comment]: <> (replace-data:cert/selfsign-rsassa-pss-sha256/x509cert.pem)
~~~~~
dummy
~~~~~

Textual Representation:

[comment]: <> (replace-data:cert/selfsign-rsassa-pss-sha256/x509cert.txt)
~~~~~
dummy
~~~~~

### C509 Type 3 Certificate

- C509 type 3 certificate converted from the X.509 certificate in {{x509-selfsign-rsassa-pss-sha256}}.

[comment]: <> (replace-size:cert/selfsign-rsassa-pss-sha256/c509cert-t3.hex)
Plain hex (dummy bytes):

[comment]: <> (replace-data:cert/selfsign-rsassa-pss-sha256/c509cert-t3.hex)
~~~~~
dummy
~~~~~

Annotated hex:

- See the annotated hex for the C509 type 2 certificate in {{c509-selfsign-rsassa-pss-sha256}}. The only differences are the certificate type, the signature value, and the key identifiers.

### C509 Type 2 Certificate {#c509-selfsign-rsassa-pss-sha256}

[comment]: <> (replace-size:cert/selfsign-rsassa-pss-sha256/c509cert-t2.hex)
Plain hex (dummy bytes):

[comment]: <> (replace-data:cert/selfsign-rsassa-pss-sha256/c509cert-t2.hex)
~~~~~
dummy
~~~~~

Annotated hex:

[comment]: <> (replace-data:cert/selfsign-rsassa-pss-sha256/c509cert-t2.diag)
~~~~~
dummy
~~~~~

## RSASSA-PSS With SHA-384

- Self-signed certificate
- Signature algorithm: rsassa-pss-with-sha384

### Private Key

See {{key-selfsign-rsa}}.

### X.509 Certificate {#x509-selfsign-rsassa-pss-sha384}

[comment]: <> (replace-size:cert/selfsign-rsassa-pss-sha384/x509cert.pem)
PEM content (dummy bytes):

[comment]: <> (replace-data:cert/selfsign-rsassa-pss-sha384/x509cert.pem)
~~~~~
dummy
~~~~~

Textual Representation:

[comment]: <> (replace-data:cert/selfsign-rsassa-pss-sha384/x509cert.txt)
~~~~~
dummy
~~~~~

### C509 Type 3 Certificate

- C509 type 3 certificate converted from the X.509 certificate in {{x509-selfsign-rsassa-pss-sha384}}.

[comment]: <> (replace-size:cert/selfsign-rsassa-pss-sha384/c509cert-t3.hex)
Plain hex (dummy bytes):

[comment]: <> (replace-data:cert/selfsign-rsassa-pss-sha384/c509cert-t3.hex)
~~~~~
dummy
~~~~~

Annotated hex:

- See the annotated hex for the C509 type 2 certificate in {{c509-selfsign-rsassa-pss-sha384}}. The only differences are the certificate type, the signature value, and the key identifiers.

### C509 Type 2 Certificate {#c509-selfsign-rsassa-pss-sha384}

[comment]: <> (replace-size:cert/selfsign-rsassa-pss-sha384/c509cert-t2.hex)
Plain hex (dummy bytes):

[comment]: <> (replace-data:cert/selfsign-rsassa-pss-sha384/c509cert-t2.hex)
~~~~~
dummy
~~~~~

Annotated hex:

[comment]: <> (replace-data:cert/selfsign-rsassa-pss-sha384/c509cert-t2.diag)
~~~~~
dummy
~~~~~

## RSASSA-PSS With SHA-512

- Self-signed certificate
- RSA public key with 1536-bit modulus
- Signature algorithm: rsassa-pss-with-sha512

### Private Key {#key-selfsign-rsassa-pss-sha512}

[comment]: <> (replace-data:key/rsa-long/prikey.pem)
~~~~~
dummy
~~~~~

### X.509 Certificate {#x509-selfsign-rsassa-pss-sha512}

[comment]: <> (replace-size:cert/selfsign-rsassa-pss-sha512/x509cert.pem)
PEM content (dummy bytes):

[comment]: <> (replace-data:cert/selfsign-rsassa-pss-sha512/x509cert.pem)
~~~~~
dummy
~~~~~

Textual Representation:

[comment]: <> (replace-data:cert/selfsign-rsassa-pss-sha512/x509cert.txt)
~~~~~
dummy
~~~~~

### C509 Type 3 Certificate

- C509 type 3 certificate converted from the X.509 certificate in {{x509-selfsign-rsassa-pss-sha512}}.

[comment]: <> (replace-size:cert/selfsign-rsassa-pss-sha512/c509cert-t3.hex)
Plain hex (dummy bytes):

[comment]: <> (replace-data:cert/selfsign-rsassa-pss-sha512/c509cert-t3.hex)
~~~~~
dummy
~~~~~

Annotated hex:

- See the annotated hex for the C509 type 2 certificate in {{c509-selfsign-rsassa-pss-sha512}}. The only differences are the certificate type, the signature value, and the key identifiers.

### C509 Type 2 Certificate {#c509-selfsign-rsassa-pss-sha512}

[comment]: <> (replace-size:cert/selfsign-rsassa-pss-sha512/c509cert-t2.hex)
Plain hex (dummy bytes):

[comment]: <> (replace-data:cert/selfsign-rsassa-pss-sha512/c509cert-t2.hex)
~~~~~
dummy
~~~~~

Annotated hex:

[comment]: <> (replace-data:cert/selfsign-rsassa-pss-sha512/c509cert-t2.diag)
~~~~~
dummy
~~~~~

## RSASSA-PSS With SHAKE128

- Self-signed certificate
- Signature algorithm: rsassa-pss-with-shake128

### Private Key

See {{key-selfsign-rsa}}.

### X.509 Certificate {#x509-selfsign-rsassa-pss-shake128}

[comment]: <> (replace-size:cert/selfsign-rsassa-pss-shake128/x509cert.pem)
PEM content (dummy bytes):

[comment]: <> (replace-data:cert/selfsign-rsassa-pss-shake128/x509cert.pem)
~~~~~
dummy
~~~~~

Textual Representation:

[comment]: <> (replace-data:cert/selfsign-rsassa-pss-shake128/x509cert.txt)
~~~~~
dummy
~~~~~

### C509 Type 3 Certificate

- C509 type 3 certificate converted from the X.509 certificate in {{x509-selfsign-rsassa-pss-shake128}}.

[comment]: <> (replace-size:cert/selfsign-rsassa-pss-shake128/c509cert-t3.hex)
Plain hex (dummy bytes):

[comment]: <> (replace-data:cert/selfsign-rsassa-pss-shake128/c509cert-t3.hex)
~~~~~
dummy
~~~~~

Annotated hex:

- See the annotated hex for the C509 type 2 certificate in {{c509-selfsign-rsassa-pss-shake128}}. The only differences are the certificate type, the signature value, and the key identifiers.

### C509 Type 2 Certificate {#c509-selfsign-rsassa-pss-shake128}

[comment]: <> (replace-size:cert/selfsign-rsassa-pss-shake128/c509cert-t2.hex)
Plain hex (dummy bytes):

[comment]: <> (replace-data:cert/selfsign-rsassa-pss-shake128/c509cert-t2.hex)
~~~~~
dummy
~~~~~

Annotated hex:

[comment]: <> (replace-data:cert/selfsign-rsassa-pss-shake128/c509cert-t2.diag)
~~~~~
dummy
~~~~~

## RSASSA-PSS With SHAKE256

- Self-signed certificate
- Signature algorithm: rsassa-pss-with-shake256

### Private Key

See {{key-selfsign-rsassa-pss-sha512}}.

### X.509 Certificate {#x509-selfsign-rsassa-pss-shake256}

[comment]: <> (replace-size:cert/selfsign-rsassa-pss-shake256/x509cert.pem)
PEM content (dummy bytes):

[comment]: <> (replace-data:cert/selfsign-rsassa-pss-shake256/x509cert.pem)
~~~~~
dummy
~~~~~

Textual Representation:

[comment]: <> (replace-data:cert/selfsign-rsassa-pss-shake256/x509cert.txt)
~~~~~
dummy
~~~~~

### C509 Type 3 Certificate

- C509 type 3 certificate converted from the X.509 certificate in {{x509-selfsign-rsassa-pss-shake256}}.

[comment]: <> (replace-size:cert/selfsign-rsassa-pss-shake256/c509cert-t3.hex)
Plain hex (dummy bytes):

[comment]: <> (replace-data:cert/selfsign-rsassa-pss-shake256/c509cert-t3.hex)
~~~~~
dummy
~~~~~

Annotated hex:

- See the annotated hex for the C509 type 2 certificate in {{c509-selfsign-rsassa-pss-shake256}}. The only differences are the certificate type, the signature value, and the key identifiers.

### C509 Type 2 Certificate {#c509-selfsign-rsassa-pss-shake256}

[comment]: <> (replace-size:cert/selfsign-rsassa-pss-shake256/c509cert-t2.hex)
Plain hex (dummy bytes):

[comment]: <> (replace-data:cert/selfsign-rsassa-pss-shake256/c509cert-t2.hex)
~~~~~
dummy
~~~~~

Annotated hex:

[comment]: <> (replace-data:cert/selfsign-rsassa-pss-shake256/c509cert-t2.diag)
~~~~~
dummy
~~~~~

# Certificates With Different RDN Attributes {#sec-cert-different-attrs}

## One RDN Attribute CommonName With EUI-48

- Subject: a single RDN attribute, `commonName`, containing an EUI-48 value.

See {{selfsign-rsa}}.

## One RDN Attribute CommonName With EUI-64

- Subject: a single RDN attribute, `commonName`, containing an EUI-64 value.

See {{selfsign-rsa-f5}}.

## One RDN Attribute CommonName With Even Number Of Lowercase Hex Letters

- Subject: a single RDN attribute, `commonName`, containing an even number of lowercase hexadecimal characters.

See {{selfsign-secp256r1}}.

## One RDN Attribute CommonName With Other Text

- Subject: a single RDN attribute, `commonName`, containing text other than an EUI-48 value, an EUI-64 value, or an even number of lowercase hexadecimal characters.

See {{selfsign-secp384r1}}.

## Empty Subject

- Subject: empty.

See {{selfsign-secp521r1}}.

## Subject With RDN Attribute Business Category

- Subject: includes the RDN attribute `businessCategory`.

See {{selfsign-frp256v1}}.

## Subject With RDN Attribute Country

- Subject: includes the RDN attribute `countryName`.

See {{selfsign-brainpoolp256r1}}.

## Subject With RDN Attribute Directory Management Domain Name

- Subject: includes the RDN attribute `directoryManagementDomainName`.

See {{selfsign-ed25519}}.

## Subject With RDN Attribute DN Qualifier

- Subject: includes the RDN attribute `dnQualifier`.

See {{selfsign-ed25519}}.

## Subject With RDN Attribute Domain Component

- Subject: includes the RDN attribute `domainComponent`.

See {{selfsign-ed25519}}.

## Subject With RDN Attribute Email Address

- Subject: includes the RDN attribute `emailAddress`.

See {{selfsign-frp256v1}}.

## Subject With RDN Attribute Generation Qualifier

- Subject: includes the RDN attribute `generationQualifier`.

See {{selfsign-ed25519}}.

## Subject With RDN Attribute Given Name

- Subject: includes the RDN attribute `givenName`.

See {{selfsign-brainpoolp384r1}}.

## Subject With RDN Attribute Initials

- Subject: includes the RDN attribute `initials`.

See {{selfsign-ed448}}.

## Subject With RDN Attribute Jurisdiction Country

- Subject: includes the RDN attribute `jurisdictionCountryName`.

See {{selfsign-brainpoolp512r1}}.

## Subject With RDN Attribute Jurisdiction Locality

- Subject: includes the RDN attribute `jurisdictionLocalityName`.

See {{selfsign-brainpoolp512r1}}.

## Subject With RDN Attribute Jurisdiction State Or Province

- Subject: includes the RDN attribute `jurisdictionStateOrProvinceName`.

See {{selfsign-brainpoolp512r1}}.

## Subject With RDN Attribute Locality

- Subject: includes the RDN attribute `localityName`.

See {{selfsign-brainpoolp256r1}}.

## Subject With RDN Attribute Name

- Subject: includes the RDN attribute `name`.

See {{selfsign-brainpoolp384r1}}.

## Subject With RDN Attribute Organization

- Subject: includes the RDN attribute `organizationName`.

See {{selfsign-sm2p256v1}}.

## Subject With RDN Attribute Organizational Unit

- Subject: includes the RDN attribute `organizationalUnitName`.

See {{selfsign-sm2p256v1}}.

## Subject With RDN Attribute Organization Identifier

- Subject: includes the RDN attribute `organizationIdentifier`.

See {{selfsign-sm2p256v1}}.

## Subject With RDN Attribute Postal Code

- Subject: includes the RDN attribute `postalCode`.

See {{selfsign-brainpoolp256r1}}.

## Subject With RDN Attribute Pseudonym

- Subject: includes the RDN attribute `pseudonym`.

See {{selfsign-ed448}}.

## Subject With RDN Attribute Serial Number

- Subject: includes the RDN attribute `serialNumber`.

See {{selfsign-sm2p256v1}}.

## Subject With RDN Attribute State

- Subject: includes the RDN attribute `stateOrProvinceName`.

See {{selfsign-brainpoolp256r1}}.

## Subject With RDN Attribute Street

- Subject: includes the RDN attribute `street`.

See {{selfsign-brainpoolp256r1}}.

## Subject With RDN Attribute Surname

- Subject: includes the RDN attribute `surname`.

See {{selfsign-brainpoolp384r1}}.

## Subject With RDN Attribute Telephone Number

- Subject: includes the RDN attribute `telephoneNumber`.

See {{selfsign-frp256v1}}.

## Subject With RDN Attribute Title

- Subject: includes the RDN attribute `title`.

See {{selfsign-brainpoolp384r1}}.

## Subject With RDN Attribute Unstructured Address

- Subject: includes the RDN attribute `unstructuredAddress`.

See {{selfsign-ed25519}}.

## Subject With RDN Attribute Unstructured Name

- Subject: includes the RDN attribute `unstructuredName`.

See {{selfsign-ed25519}}.

## Subject With RDN Attribute User Id

- Subject: includes the RDN attribute `userId`.

See {{selfsign-ed448}}.

# Certificates With Different Extensions {#sec-cert-different-extns}

## Empty Extensions

- Extensions: none

Not applicable to certificates; see {{csr-dhsig-sha512}} for examples in certification requests.

## One Extension: Non-Critical keyUsage

- Extensions: a single non-critical `keyUsage` extension

See {{selfsign-rsa}}.

## One Extension: Critical keyUsage

- Extensions: a single critical `keyUsage` extension

See {{selfsign-rsa-f5}}.

## Authority Information Access

See {{ee-x25519}}.

## Authority Key Identifier

- With only the `keyIdentifier` field present

  See {{ee-x25519}}.

- With all fields present

  See {{ee-x448}}.

## ASIdentifiers And ASIdentifiers V2

- `ASIdentifiers` set to `null`
- A non-empty array of `ASIdOrRange`

See {{selfsign-brainpoolp256r1}}.

## Basic Constraints

  - `CA = true` and `pathLenConstraint` absent

    See {{selfsign-secp256r1}}.

  - `CA = true` and `pathLenConstraint` present

    See {{selfsign-secp384r1}}.

  - `CA = false`

    See {{selfsign-secp521r1}}.

## Certificate Policies

See {{selfsign-secp384r1}}.

## CRL Distribution Points and Freshest CRL

See {{ee-x448}}.

## Extended Key Usage

- Integer-identified usages

  See {{selfsign-secp256r1}}.

- `~oid`-identified usages

  See {{selfsign-secp384r1}}.

- Integer-identified and `~oid`-identified usages

  See {{selfsign-secp521r1}}.

## Inhibit anyPolicy

See {{selfsign-secp384r1}}.

## Issuer Alternative Name

See {{ee-x25519}}.

## IPAddrBlocks and IPAddrBlocks V2

- With `SAFI` = `null` and IP Address Choice = `null`
- With `SAFI` = `null` and IP Address Choice = `null`

  See {{selfsign-brainpoolp256r1}}.

- IPAddrBlocks with non-null `SAFI` and IntIPAddressChoice`
- IPAddrBlocks V2 with non-null `SAFI`, `IntIPAddressChoice` and `IPAddressChoice`

  See {{selfsign-brainpoolp384r1}}.

## Name Constraints

- Only `PermittedSubTree`

  See {{selfsign-frp256v1}}.

- Only `ExcludedSubTree`

  See {{selfsign-sm2p256v1}}.

- Both `PermittedSubTree` and `ExcludedSubTree`

  See {{selfsign-ed25519}}.

## OCSP No Check

See {{selfsign-ed448}}.

## Policy Constraints

- Only `RequireExplicitPolicy`

  See {{selfsign-frp256v1}}.

- Only `InhibitPolicyMapping`

  See {{selfsign-sm2p256v1}}.

- Both `RequireExplicitPolicy` and `InhibitPolicyMapping`

  See {{selfsign-ed25519}}.

## Policy Mappings

See {{selfsign-brainpoolp512r1}}.

## Subject Alternative Name

See {{selfsign-secp521r1}}.

## Subject Directory Attributes

See {{selfsign-brainpoolp512r1}}.

## Subject Information Access

See {{selfsign-brainpoolp512r1}}.

## Subject Key Identifier

See {{selfsign-secp256r1}}.

## TLS Features

See {{selfsign-ed448}}.

# X.509 Certificate With Unconvertible RDN Attributes And Extensions {#sec-cert-unconvertible}

- Common Name: text encoded in a string type other than PrintableString or UTF8String.
- Subject Public Key Algorithm: an EC public key with an unknown curve OID in the `parameters` field.
- ASIdentifiers: includes the `rdi` field.
- Name Constraints: includes a DirectoryName option encoded as IA5String.

## Private Key

See {{key-selfsign-secp256r1}}.

## X.509 Certificate {#x509-unconvertible}

[comment]: <> (replace-size:cert/unconvertible-secp256r1/x509cert.pem)
PEM content (dummy bytes)

[comment]: <> (replace-data:cert/unconvertible-secp256r1/x509cert.pem)
~~~~~
dummy
~~~~~

Textual Representation:

[comment]: <> (replace-data:cert/unconvertible-secp256r1/x509cert.txt)
~~~~~
dummy
~~~~~

Text representation:

[comment]: <> (replace-data:cert/unconvertible-secp256r1/x509cert.txt)
~~~~~
dummy
~~~~~

## C509 Type 3 Certificate

- C509 type 3 certificate converted from the X.509 certificate in {{x509-unconvertible}}.

[comment]: <> (replace-size:cert/unconvertible-secp256r1/c509cert-t3.hex)
Plain hex (dummy bytes):

[comment]: <> (replace-data:cert/unconvertible-secp256r1/c509cert-t3.hex)
~~~~~
dummy
~~~~~

Annotated hex:

[comment]: <> (replace-data:cert/unconvertible-secp256r1/c509cert-t3.diag)
~~~~~
dummy
~~~~~

# Certification Requests With Different Signature Algorithms {#sec-csr-different-sigs}

## ECDSA With SHA256 {#csr-ecdsa-p256}

- Signature algorithm: ecdsa-with-sha256
- CR attributes: one `extensionRequest` attribute.

### Private Key

See {{key-selfsign-secp256r1}}.

### X.509 Certification Request {#x509csr-ecdsa-p256}

[comment]: <> (replace-size:csr/ecdsa-p256/x509csr.pem)
PEM content (dummy bytes):

[comment]: <> (replace-data:csr/ecdsa-p256/x509csr.pem)
~~~~~
dummy
~~~~~

Text representation:

[comment]: <> (replace-data:csr/ecdsa-p256/x509csr.txt)
~~~~~
dummy
~~~~~

### C509 Type 3 Certification Request

- C509 type 3 certification request converted from the X.509 certification request in {{x509csr-ecdsa-p256}}.

[comment]: <> (replace-size:csr/ecdsa-p256/c509csr-t3.hex)
Plain hex (dummy bytes):

[comment]: <> (replace-data:csr/ecdsa-p256/c509csr-t3.hex)
~~~~~
dummy
~~~~~

Annotated hex:

- See the annotated hex for the C509 type 2 certification request in {{c509csr-ecdsa-p256}}. The only differences are the certification request type and the signature value.

### C509 Type 2 Certification Request {#c509csr-ecdsa-p256}

[comment]: <> (replace-size:csr/ecdsa-p256/c509csr-t2.hex)
Plain hex (dummy bytes):

[comment]: <> (replace-data:csr/ecdsa-p256/c509csr-t2.hex)
~~~~~
dummy
~~~~~

Annotated hex:

[comment]: <> (replace-data:csr/ecdsa-p256/c509csr-t2.diag)
~~~~~
dummy
~~~~~

## ECDSA With SHA384 {#csr-ecdsa-p384}

- Signature algorithm: ecdsa-with-sha384
- CR attributes: one `challengePassword` attribute with a PrintableString value.

### Private Key

See {{key-selfsign-secp384r1}}.

### X.509 Certification Request {#x509csr-ecdsa-p384}

[comment]: <> (replace-size:csr/ecdsa-p384/x509csr.pem)
PEM content (dummy bytes):

[comment]: <> (replace-data:csr/ecdsa-p384/x509csr.pem)
~~~~~
dummy
~~~~~

Text representation:

[comment]: <> (replace-data:csr/ecdsa-p384/x509csr.txt)
~~~~~
dummy
~~~~~

### C509 Type 3 Certification Request

- C509 type 3 certification request converted from the X.509 certification request in {{x509csr-ecdsa-p384}}.

[comment]: <> (replace-size:csr/ecdsa-p384/c509csr-t3.hex)
Plain hex (dummy bytes):

[comment]: <> (replace-data:csr/ecdsa-p384/c509csr-t3.hex)
~~~~~
dummy
~~~~~

Annotated hex:

[comment]: <> (replace-data:csr/ecdsa-p384/c509csr-t2.diag)
~~~~~
dummy
~~~~~

### C509 Type 2 Certification Request

A PrintableString `challengePassword` value is not supported in a type 2 certification request.

## ECDH PoP With SHA-256 And HMAC-SHA256 {#csr-dhsig-sha256}

- Signature algorithm: sa-ecdhPop-sha256-hmac-sha256
- Signature value: only the `hashValue` field is present.
- CR attributes: one `challengePassword` attribute with a UTF8String value.

### Private Key

[comment]: <> (replace-data:key/dhsig-secp256r1/prikey.pem)
~~~~~
dummy
~~~~~

### X.509 Certification Request {#x509csr-dhsig-sha256}

- The peer private key and certificate are given in {{key-selfsign-secp256r1}} and {{x509-selfsign-secp256r1}}.

[comment]: <> (replace-size:csr/dhsig-sha256/x509csr.pem)
PEM content (dummy bytes):

[comment]: <> (replace-data:csr/dhsig-sha256/x509csr.pem)
~~~~~
dummy
~~~~~

Text representation:

[comment]: <> (replace-data:csr/dhsig-sha256/x509csr.txt)
~~~~~
dummy
~~~~~

### C509 Type 3 Certification Request

- C509 type 3 certification request converted from the X.509 certification request in {{x509csr-dhsig-sha256}}.

[comment]: <> (replace-size:csr/dhsig-sha256/c509csr-t3.hex)
Plain hex (dummy bytes):

[comment]: <> (replace-data:csr/dhsig-sha256/c509csr-t3.hex)
~~~~~
dummy
~~~~~

Annotated hex:

- See the annotated hex for the C509 type 2 certification request in {{c509csr-dhsig-sha256}}. The only differences are the certification request type and the signature value.

### C509 Type 2 Certification Request {#c509csr-dhsig-sha256}

- The peer private key and certificate are given in {{key-selfsign-secp256r1}} and {{c509-selfsign-secp256r1}}.

[comment]: <> (replace-size:csr/dhsig-sha256/c509csr-t2.hex)
Plain hex (dummy bytes):

[comment]: <> (replace-data:csr/dhsig-sha256/c509csr-t2.hex)
~~~~~
dummy
~~~~~

Annotated hex:

[comment]: <> (replace-data:csr/dhsig-sha256/c509csr-t2.diag)
~~~~~
dummy
~~~~~

## ECDH PoP With SHA-384 And HMAC-SHA384 {#csr-dhsig-sha384}

- Signature algorithm: sa-ecdhPop-sha384-hmac-sha384
- Signature value: all fields are present.
- CR attributes: none.

### Private Key

[comment]: <> (replace-data:key/dhsig-secp384r1/prikey.pem)
~~~~~
dummy
~~~~~

### X.509 Certification Request {#x509csr-dhsig-sha384}

- The peer private key and certificate are given in {{key-selfsign-secp384r1}} and {{x509-selfsign-secp384r1}}.

[comment]: <> (replace-size:csr/dhsig-sha384/x509csr.pem)
PEM content (dummy bytes):

[comment]: <> (replace-data:csr/dhsig-sha384/x509csr.pem)
~~~~~
dummy
~~~~~

Text representation:

[comment]: <> (replace-data:csr/dhsig-sha384/x509csr.txt)
~~~~~
dummy
~~~~~

### C509 Type 3 Certification Request

- C509 type 3 certification request converted from the X.509 certification request in {{x509csr-dhsig-sha384}}.

[comment]: <> (replace-size:csr/dhsig-sha384/c509csr-t3.hex)
Plain hex (dummy bytes):

[comment]: <> (replace-data:csr/dhsig-sha384/c509csr-t3.hex)
~~~~~
dummy
~~~~~

Annotated hex:

- See the annotated hex for the C509 type 2 certification request in {{c509csr-dhsig-sha384}}. The only differences are the certification request type and the signature value.

### C509 Type 2 Certification Request {#c509csr-dhsig-sha384}

- The peer private key and certificate are given in {{key-selfsign-secp384r1}} and {{c509-selfsign-secp384r1}}.

[comment]: <> (replace-size:csr/dhsig-sha384/c509csr-t2.hex)
Plain hex (dummy bytes):

[comment]: <> (replace-data:csr/dhsig-sha384/c509csr-t2.hex)
~~~~~
dummy
~~~~~

Annotated hex:

[comment]: <> (replace-data:csr/dhsig-sha384/c509csr-t2.diag)
~~~~~
dummy
~~~~~

## ECDH PoP With SHA-512 And HMAC-SHA512 {#csr-dhsig-sha512}

- Signature algorithm: sa-ecdhPop-sha512-hmac-sha512
- Signature value: only the `hashValue` field is present.
- CR attributes: none.

### Private Key

[comment]: <> (replace-data:key/dhsig-secp521r1/prikey.pem)
~~~~~
dummy
~~~~~

### X.509 Certification Request {#x509csr-dhsig-sha512}

- The peer private key and certificate are given in {{key-selfsign-secp521r1}} and {{x509-selfsign-secp521r1}}.

[comment]: <> (replace-size:csr/dhsig-sha512/x509csr.pem)
PEM content (dummy bytes):

[comment]: <> (replace-data:csr/dhsig-sha512/x509csr.pem)
~~~~~
dummy
~~~~~

Text representation:

[comment]: <> (replace-data:csr/dhsig-sha512/x509csr.txt)
~~~~~
dummy
~~~~~

### C509 Type 3 Certification Request

- C509 type 3 certification request converted from the X.509 certification request in {{x509csr-dhsig-sha512}}.

[comment]: <> (replace-size:csr/dhsig-sha512/c509csr-t3.hex)
Plain hex (dummy bytes):

[comment]: <> (replace-data:csr/dhsig-sha512/c509csr-t3.hex)
~~~~~
dummy
~~~~~

Annotated hex:

- See the annotated hex for the C509 type 2 certification request in {{c509csr-dhsig-sha512}}. The only differences are the certification request type and the signature value.

### C509 Type 2 Certification Request {#c509csr-dhsig-sha512}

- The peer private key and certificate are given in {{key-selfsign-secp521r1}} and {{c509-selfsign-secp521r1}}.

[comment]: <> (replace-size:csr/dhsig-sha512/c509csr-t2.hex)
Plain hex (dummy bytes):

[comment]: <> (replace-data:csr/dhsig-sha512/c509csr-t2.hex)
~~~~~
dummy
~~~~~

Annotated hex:

[comment]: <> (replace-data:csr/dhsig-sha512/c509csr-t2.diag)
~~~~~
dummy
~~~~~

## Unsigned PoP With X25519 Key {#csr-x25519}

- Signature algorithm: unsigned
- CR attributes: a `privateKeyPossessionStatement` attribute without the `cert` field.

### Private Key

[comment]: <> (replace-data:key/x25519/prikey.pem)
~~~~~
dummy
~~~~~

### X.509 Certification Request {#x509csr-x25519}

[comment]: <> (replace-size:csr/x25519/x509csr.pem)
PEM content (dummy bytes):

[comment]: <> (replace-data:csr/x25519/x509csr.pem)
~~~~~
dummy
~~~~~

Text representation:

[comment]: <> (replace-data:csr/x25519/x509csr.txt)
~~~~~
dummy
~~~~~

### C509 Type 3 Certification Request

- C509 type 3 certification request converted from the X.509 certification request in {{x509csr-x25519}}.

[comment]: <> (replace-size:csr/x25519/c509csr-t3.hex)
Plain hex (dummy bytes):

[comment]: <> (replace-data:csr/x25519/c509csr-t3.hex)
~~~~~
dummy
~~~~~

Annotated hex:

- See the annotated hex for the C509 type 2 certification request in {{c509csr-x25519}}. The only differences are the certification request type and the signature value.

### C509 Type 2 Certification Request {#c509csr-x25519}

[comment]: <> (replace-size:csr/x25519/c509csr-t2.hex)
Plain hex (dummy bytes):

[comment]: <> (replace-data:csr/x25519/c509csr-t2.hex)
~~~~~
dummy
~~~~~

Annotated hex:

[comment]: <> (replace-data:csr/x25519/c509csr-t2.diag)
~~~~~
dummy
~~~~~

## Unsigned PoP With X25519 Key And Cert {#csr-x25519-withcert}

- Signature algorithm: unsigned
- CR attributes: a `privateKeyPossessionStatement` attribute with the `cert` field.

### Private Key

[comment]: <> (replace-data:key/x25519/prikey.pem)
~~~~~
dummy
~~~~~

### X.509 Certification Request {#x509csr-x25519-withcert}

[comment]: <> (replace-size:csr/x25519-withcert/x509csr.pem)
PEM content (dummy bytes):

[comment]: <> (replace-data:csr/x25519-withcert/x509csr.pem)
~~~~~
dummy
~~~~~

Text representation:

[comment]: <> (replace-data:csr/x25519-withcert/x509csr.txt)
~~~~~
dummy
~~~~~

### C509 Type 3 Certification Request

- C509 type 3 certification request converted from the X.509 certification request in {{x509csr-x25519-withcert}}.

[comment]: <> (replace-size:csr/x25519-withcert/c509csr-t3.hex)
Plain hex (dummy bytes):

[comment]: <> (replace-data:csr/x25519-withcert/c509csr-t3.hex)
~~~~~
dummy
~~~~~

Annotated hex:

- See the annotated hex for the C509 type 2 certification request in {{c509csr-x25519-withcert}}. The only differences are the certification request type and the signature value.

### C509 Type 2 Certification Request {#c509csr-x25519-withcert}

[comment]: <> (replace-size:csr/x25519-withcert/c509csr-t2.hex)
Plain hex (dummy bytes):

[comment]: <> (replace-data:csr/x25519-withcert/c509csr-t2.hex)
~~~~~
dummy
~~~~~

Annotated hex:

[comment]: <> (replace-data:csr/x25519-withcert/c509csr-t2.diag)
~~~~~
dummy
~~~~~

# Certification Requests With Different CR Attributes {#sec-csr-different-attrs}

## With Empty CR Attributes

- CR attributes: none.

See {{csr-dhsig-sha384}} and {{csr-dhsig-sha512}}.

## With challengePassword Attribute

- CR attributes: one `challengePassword` attribute of type UTF8String.

See {{csr-dhsig-sha256}}.

- CR attributes: one `challengePassword` attribute of type PrintableString.

See {{csr-ecdsa-p384}}.

## With extensionRequest Attribute

- CR attributes: one `extensionRequest` attribute.

See {{csr-ecdsa-p256}}.

## With privateKeyPossessionStatement Attribute

- CR attributes: one `privateKeyPossessionStatement` attribute without the `cert` field.

See {{csr-x25519}}.

- CR attributes: one `privateKeyPossessionStatement` attribute with the `cert` field.

See {{csr-x25519-withcert}}.

# Certification Request Templates {#sec-csrt}

## All Fields Set to "undefined" Where Possible {#undefined-csrt}

- c509CertificationRequestType: undefined
- subjectSignatureAlgorithm: undefined
- subject: undefined
- subjectPublicKeyAlgorithm: undefined
- subjectPublicKey: undefined
- extensionsRequest: undefined

[comment]: <> (replace-size:csrt/undefined-csrt/c509csrt.hex)
Plain hex (dummy bytes):

[comment]: <> (replace-data:csrt/undefined-csrt/c509csrt.hex)
~~~~~
dummy
~~~~~

Annotated hex:

[comment]: <> (replace-data:csrt/undefined-csrt/c509csrt.diag)
~~~~~
dummy
~~~~~

## With One Element in Each Field {#oneelement-csrt}

- c509CertificationRequestType: one element
- subjectSignatureAlgorithm: one element
- subject: one element
- subjectPublicKeyAlgorithm: one element
- extensionsRequest: one element

[comment]: <> (replace-size:csrt/oneelement-csrt/c509csrt.hex)
Plain hex (dummy bytes):

[comment]: <> (replace-data:csrt/oneelement-csrt/c509csrt.hex)
~~~~~
dummy
~~~~~

Annotated hex:

[comment]: <> (replace-data:csrt/oneelement-csrt/c509csrt.diag)
~~~~
dummy
~~~~

## Complex Template {#complex-csrt}

- c509CertificationRequestType: multiple values
- subjectSignatureAlgorithm: all choices
- subjectPublicKeyAlgorithm: all choices
- subject
  - choice (int, Defined)
  - choice (int, undefined)
  - choice (~oid, Defined)
  - choice (~oid, undefined)
- extensions
  - choice (int, Defined)
  - choice (int, undefined)
  - choice (~oid, Defined)
  - choice (~oid, undefined)

[comment]: <> (replace-size:csrt/complex-csrt/c509csrt.hex)
Plain hex (dummy bytes):

[comment]: <> (replace-data:csrt/complex-csrt/c509csrt.hex)
~~~~~
dummy
~~~~~

Annotated hex:

[comment]: <> (replace-data:csrt/complex-csrt/c509csrt.diag)
~~~~~
dummy
~~~~~

# Security Considerations

The private keys shown in this document are for example purposes only. They are not secret and MUST NOT be used in deployments.

The examples use 1024-bit or 1536-bit RSA keys and reuse key pairs to keep the examples compact. In deployments, key pairs are expected to be generated uniquely and not reused. The examples also use RSA PKCS#1 v1.5 signatures and SHA-1 to cover all signature algorithms defined in [I-D.ietf-cose-cbor-encoded-cert]. These choices do not reflect current state-of-the-art security recommendations; at the time of writing, RSA keys of at least 3072 bits, stronger hash functions, and RSA-PSS are required for adequate security.

# Privacy Considerations

There are no privacy considerations.

# IANA Considerations

There are no IANA considerations.

--- back

# Acknowledgments {#acknowledgment}
{: numbered="no"}
