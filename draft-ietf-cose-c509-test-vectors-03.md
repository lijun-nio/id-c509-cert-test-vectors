---

v: 3

title: Test Vectors for CBOR-Encoded X.509 (C509) Certificates
abbrev: C509 Test Vectors
docname: draft-ietf-cose-c509-test-vectors-03
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
    org: NIO Inc.
    email: lijun.liao@nio.io
-
    ins: G. Selander
    name: Göran Selander
    org: Ericsson AB
    email: goran.selander@ericsson.com
-
    ins: J. Preuß Mattsson
    name: John Preuß Mattsson
    org: Ericsson AB
    email: john.mattsson@ericsson.com

normative:
  RFC2119:
  RFC8174:
  RFC8949:
  RFC9090:

  I-D.ietf-cose-cbor-encoded-cert:

informative:

--- abstract

This document contains examples of CBOR-encoded X.509 (C509) certificates, certification requests, and certification request templates. 

--- middle

# Introduction

This document contains examples of CBOR-encoded X.509 (C509) certificates, certification requests, and certification request templates encoded in CBOR {{RFC8949}} according to the C509 specification {{I-D.ietf-cose-cbor-encoded-cert}}. It complements the C509 specification by providing an extensive set of readable examples that illustrate the encodings of certificate and certification request fields, and that can be used to test interoperability between C509 implementations.

The examples are shown in multiple encodings and formats: X.509 certificates and certification requests in PEM format, and C509 certificates and certification requests in plain hexadecimal and annotated forms.

The examples include two types of C509 certificates, distinguished by the value of the c509CertificateType field (see {{Section 4.2 of I-D.ietf-cose-cbor-encoded-cert}}):

* c509CertificateType = 03 (called type 3 in this document) is a reversible CBOR encoding of an X.509 certificate, in which the issuerSignatureValue field of the C509 certificate contains the signatureValue field of the X.509 certificate, that is, the digital signature computed over the ASN.1 DER encoding.

* c509CertificateType = 02 (called type 2 in this document) differs from type 3 only in this value and in that the issuerSignatureValue field of the C509 certificate contains the signature over the TBSCertificate of the C509 certificate, that is, the digital signature computed over the CBOR encoding.

The examples also include two analogous types of C509 certification requests, also called type 2 and type 3 in this document, distinguished by the value of the c509CertificationRequestType field.

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

## Terminology {#terminology}

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "NOT RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in BCP 14 {{RFC2119}} {{RFC8174}} when, and only when, they appear in all capitals, as shown here.
   
Readers are expected to be familiar with C509 certificates {{I-D.ietf-cose-cbor-encoded-cert}}.

## Target version of draft-ietf-cose-cbor-encoded-cert

> **Note to RFC Editor:** Please remove this section prior to publication of the final version of this document.

The test vectors in this document align with draft-ietf-cose-cbor-encoded-cert-20.

# Overview

{{tab-cert-examples-overview}} shows the size in byte and the size reduction of C509 certificates compared to X.509 certificates.

| Section       | Description           | size(X.509) | size(C509) | Size Reduction |
|:--------------|:----------------------|:-----------|:-----------|:----------------------|
| {{sec-ca}} | CA Certificate | 249 | 101 | 59% |
| {{selfsign-rsa}} | RSA Public Key With Public Exponent 65537 | 464 | 284 | 39% |
| {{selfsign-rsa-f5}} | RSA Public Key With Public Exponent 4294967297 | 467 | 298 | 36% |
| {{selfsign-secp256r1}} | Weierstrass EC Public Key On Curve secp256r1 | 383 | 190 | 50% |
| {{selfsign-compress-secp256r1}} | Compressed Weierstrass EC Public Key On Curve secp256r1 | 383 | 158 | 59% |
| {{selfsign-secp384r1}} | Weierstrass EC Public Key On Curve secp384r1 | 539 | 309 | 43% |
| {{selfsign-secp521r1}} | Weierstrass EC Public Key On Curve secp521r1 | 811 | 531 | 35% |
| {{selfsign-sm2p256v1}} | Weierstrass EC Public Key On Curve sm2p256v1 | 644 | 326 | 49% |
| {{selfsign-brainpoolp256r1}} | Weierstrass EC Public Key On Curve brainpoolP256r1  | 646 | 264 | 63% |
| {{selfsign-brainpoolp384r1}} | Weierstrass EC Public Key On Curve brainpoolP384r1  | 717 | 406 | 43% |
| {{selfsign-brainpoolp512r1}} | Weierstrass EC Public Key On Curve brainpoolP512r1  | 809 | 432 | 47% |
| {{selfsign-frp256v1}} | Weierstrass EC Public Key On Curve frp256v1 | 584 | 303 | 48% |
| {{ee-x25519}} | Montgomery EC Public Key On Curve X25519 | 681 | 459 | 33% |
| {{ee-x448}} | Montgomery EC Public Key On Curve X448 | 572 | 367 | 36% |
| {{selfsign-ed25519}} | Edwards EC Public Key On Curve ED25519 | 784 | 384 | 51% |
| {{selfsign-ed448}} | Edwards EC Public Key On Curve ED448 | 503 | 261 | 48% |
| {{selfsign-rsa-with-sha1}} | Signature Algorithm RSASSA-PKCS1-v1_5 With SHA-1 | 463 | 303 | 35% |
| {{selfsign-rsa-with-sha512}} | Signature Algorithm RSASSA-PKCS1-v1_5 With SHA-512 | 467 | 306 | 34% |
| {{selfsign-rsassa-pss-sha256}} | Signature Algorithm RSASSA-PSS With SHA-256 | 575 | 308 | 46% |
| {{selfsign-rsassa-pss-sha384}} | Signature Algorithm RSASSA-PSS With SHA-384 | 575 | 308 | 46% |
| {{selfsign-rsassa-pss-sha512}} | Signature Algorithm RSASSA-PSS With SHA-512 | 703 | 436 | 38% |
| {{selfsign-rsassa-pss-shake128}} | Signature Algorithm RSASSA-PSS With SHAKE128 | 469 | 310 | 38% |
| {{selfsign-rsassa-pss-shake256}} | Signature Algorithm RSASSA-PSS With SHAKE256 | 597 | 438 | 27% |
| {{sec-cert-unconvertible}} | X.509 Certificate With Unconvertible RDN Attributes And Extensions | 387 | 257 | 34% |
{: #tab-cert-examples-overview title="Size comparison of certificates in examples (TODO: update the percent data)"}

{{tab-csr-examples-overview}} shows the size in byte and the size reduction of C509 certification requests compared to X.509 certification requests.

| Section       | Description           | size(X.509) | size(C509) | Size Reduction |
|:--------------|:----------------------|:-----------|:-----------|:----------------------|
| {{csr-ecdsa-p256}} | SignatureAlgorithm ECDSA With SHA256 | 253 | 164 | 35% |
| {{csr-ecdsa-p384}} | SignatureAlgorithm ECDSA With SHA384 | 300 | 228 | 24% |
| {{csr-dhsig-sha256}} | SignatureAlgorithm ECDH PoP With SHA-256 And HMAC-SHA256 | 206 | 132 | 36% |
| {{csr-dhsig-sha384}} | SignatureAlgorithm ECDH PoP With SHA-384 And HMAC-SHA384 | 261 | 190 | 27% |
| {{csr-dhsig-sha512}} | SignatureAlgorithm ECDH PoP With SHA-512 And HMAC-SHA512 | 278 | 219 | 21% |
| {{csr-x25519}} | SignatureAlgorithm Unsigned PoP With X25519 Key | 135 | 64 | 53% |
| {{csr-x25519-withcert}} | SignatureAlgorithm Unsigned PoP With X25519 Key And Cert | 433 | 230 | 47% |
{: #tab-csr-examples-overview title="Size comparison of certification requests in examples (TODO: update the percent data)"}

# CA Certificate {#sec-ca}

- The CA uses an Ed25519 public key because the resulting signature is compact and deterministic.
- Signature algorithm: unsigned
- Key: Ed25519

## Private Key

[comment]: <> (replace-data:key/ca/prikey.pem)
~~~~~
-----BEGIN PRIVATE KEY-----
MC4CAQAwBQYDK2VwBCIEILRAHe59PSDnJqhejd8oytYWP0L6SU8kMSRdxzbDybzG
-----END PRIVATE KEY-----
~~~~~

## X.509 Certificate {#x509-ca}

[comment]: <> (replace-size:cert/ca/x509cert.pem)
PEM content (249 bytes):

[comment]: <> (replace-data:cert/ca/x509cert.pem)
~~~~~
-----BEGIN CERTIFICATE-----
MIH2MIHkoAMCAQICAQEwCgYIKwYBBQUHBiQwEjEQMA4GA1UEAwwHdGVzdCBjYTAe
Fw0yNTAxMDEwMDAwMDBaFw0yNjEyMzEyMzU5NTlaMBIxEDAOBgNVBAMMB3Rlc3Qg
Y2EwKjAFBgMrZXADIQBalBSsVtG2rwyWb8U7lHa1yV0O6q73ZNnv6G23Mgw24aNa
MFgwHQYDVR0OBBYEFH/NuC0ElS4aNrkK83o88WbRXvkhMA4GA1UdDwEB/wQEAwIB
BjAWBgNVHREEDzANggtleGFtcGxlLmNvbTAPBgNVHRMBAf8EBTADAQH/MAoGCCsG
AQUFBwYkAwEA
-----END CERTIFICATE-----
~~~~~

Textual Representation:

[comment]: <> (replace-data:cert/ca/x509cert.txt)
~~~~~
Certificate:
  Version: v3 (2)
  Serial Number:
    01
  Issuer: CN=test ca
  Validity:
    Not Before: Wed Jan 01 01:00:00 CET 2025
    Not After : Fri Jan 01 00:59:59 CET 2027
  Subject: CN=test ca
  Subject Public Key Info:
    Public Key Algorithm: ED25519
    Pub:
      5a:94:14:ac:56:d1:b6:af:0c:96:6f:c5:3b:94:76:b5:c9:5d:
      0e:ea:ae:f7:64:d9:ef:e8:6d:b7:32:0c:36:e1
  X509v3 extensions:
    X509v3 subjectKeyIdentifier:
      7f:cd:b8:2d:04:95:2e:1a:36:b9:0a:f3:7a:3c:f1:66:d1:5e:f9:21
    X509v3 keyUsage: critical
      keyCertSign, cRLSign
    X509v3 subjectAlternativeName:
      DNS: example.com
    X509v3 basicConstraints: critical
      CA: true, pathlen: null
  Signature Algorithm: unsigned
  Signature Value: <empty>
~~~~~

## C509 Type 3 Certificate

- C509 type 3 certificate converted from the X.509 certificate in {{x509-ca}}.
- Compared to the C509 type 2 certificate, the only differences are the certificate type, the signature value, and the public key identifier.

[comment]: <> (replace-size:cert/ca/c509cert-t3.hex)
Plain hex (101 bytes):

[comment]: <> (replace-data:cert/ca/c509cert-t3.hex)
~~~~~
8B03410105F61A677485801A6B36EC7F67746573742063610C58205A9414AC56D1B6
AF0C966FC53B9476B5C95D0EEAAEF764D9EFE86DB7320C36E18801547FCDB82D0495
2E1A36B90AF37A3CF166D15EF921211860036B6578616D706C652E636F6D232040
~~~~~

Annotated hex:

- See the annotated hex for the C509 type 2 certificate in {{c509-ca}}. The only differences are the certificate type, the signature value, and the key identifiers.

## C509 Type 2 Certificate {#c509-ca}

[comment]: <> (replace-size:cert/ca/c509cert-t2.hex)
Plain hex (101 bytes):

[comment]: <> (replace-data:cert/ca/c509cert-t2.hex)
~~~~~
8B02410105F61A677485801A6B36EC7F67746573742063610C58205A9414AC56D1B6
AF0C966FC53B9476B5C95D0EEAAEF764D9EFE86DB7320C36E18801540369D71F96FE
1258A746AC2B208E756E6D1D3ED9211860036B6578616D706C652E636F6D232040
~~~~~

Annotated hex:

[comment]: <> (replace-data:cert/ca/c509cert-t2.diag)
~~~~~
  0: 8B             # C509Certificate=array[11]
  1:   02             # [0]. certificate type=2
  2:   41             # [1]. certificateSerialNumber=byte[1]
  3:     01
  4:   05             # [2]. signature alg=unsigned (5)
  5:   F6             # [3]. issuer=<null>
  6:   1A 67748580    # [4]. notBefore=1735689600:
                      #      2025-01-01T00:00:00Z
 11:   1A 6B36EC7F    # [5]. notAfter=1798761599:
                      #      2026-12-31T23:59:59Z
 16:   67             # [6]. subject=char[7]
 17:     74657374206361  # "test ca"
 24:   0C             # [7]. subjectPublicKeyAlg=Ed25519 (12)
 25:   58 20          # [8]. subject public key=EC point=byte[32]
 27:     5A9414AC56D1B6AF0C966FC53B9476B5C95D0EEAAEF764D9EFE86DB732
 56:     0C36E1
 59:   88             # [9]. extensions=array[8]
                        #---extension[0]---
 60:     01             # [0]. type=SubjectKeyIdentifier (1)
 61:     54             # [1]. value=byte[20]
 62:       0369D71F96FE1258A746AC2B208E756E6D1D3ED9
                        #---extension[1]---
 82:     21             # [2]. type=KeyUsage, critical (-2)
 83:     18 60          # [3]. value=96: [keyCertSign, cRLSign]
                        #---extension[2]---
 85:     03             # [4]. type=SubjectAlternativeName (3)
 86:     6B             # [5]. value: DNS, value=char[11]
 87:       6578616D706C652E636F6D # "example.com"
                        #---extension[3]---
 98:     23             # [6]. type=BasicConstraints, critical (-4)
 99:     20             # [7]. value=-1: CA: true,
                        #      pathLenConstraint: unlimited
100:   40             # [10]. signature value=byte[0]
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
-----BEGIN PRIVATE KEY-----
MIICdgIBADANBgkqhkiG9w0BAQEFAASCAmAwggJcAgEAAoGBALgJL28EcmqSHPqy
0xOunS8Bx85GX6t9pix6XHP6zl/7ovHdgKKa3EM5nPyiInm4miZIEOW5JrteDT9y
enY+FgE/ifj+rFnQ+91eiwxSgn5UkPE7hMNjTonG0XMa5fGmD4jtEY0IDhqyyqUy
0GwvfSoIdN7k5rblcoP2R42vQlPbAgMBAAECgYAfuNGYyMLyFLJzEhzpERmd7ygi
KaY2+KcKlqLWCP7Gs6jAGQahwKDD46voLghUQ9oqTBTBjDsdY9ZTv+dU91myXRnR
81QZxVmzFfZRZ1yNBZE7NctjH1UM1uejX08dp8ypFEZ8YUXydgwnVThyma6nwqeD
W2hAnP7S7LcisHs9/QJBAPJNe3l0Mqeq8Fwp4DL6opcnehT4qMe0d+/4nSIVodQc
iOJE752KYXs1GZ0/+ETWNoBn8L7ZFO9gjcd/xtlSAEUCQQDCcHyiGcuNRFx6vTGo
8Su460/PF8ViALOUAZwPiFO+Fxpx2kyWKk5DtKbCOXgeEPE2gcCFT1ztb/nusPJv
PJWfAkBM1rN0ZZv2veexYno90GulQZiec+iaRCnQyOeTuoZyTIZAMxpFvG+Dceho
jLO/qKrp94xKCW9xJg5wGJ5HppB9AkAVaK1Qa+att0k3en1tTKCzy7UN6GAjWlBr
i4HTc/9Efua6gn7MSfw0GEAEQq+nH9ZvWfoIs6RXwGyUi4cmGFWhAkEAjf207bsV
VUEhtJ3LEsgOaTGg+8VfHIZuXxNNwxxyIm2YacTXDXpvfslFWRuNxomp44dtPmmb
bqJRmhxWlrumnw==
-----END PRIVATE KEY-----
~~~~~

### X.509 Certificate {#x509-selfsign-rsa}

[comment]: <> (replace-size:cert/selfsign-rsa/x509cert.pem)
PEM content (464 bytes):

[comment]: <> (replace-data:cert/selfsign-rsa/x509cert.pem)
~~~~~
-----BEGIN CERTIFICATE-----
MIIBzDCCATWgAwIBAgICEjQwDQYJKoZIhvcNAQELBQAwIjEgMB4GA1UEAwwXMTIt
MzQtNTYtRkYtRkUtNzgtOTAtQUIwIBcNMjUwMTAyMDAwMDAwWhgPOTk5OTEyMzEy
MzU5NTlaMCIxIDAeBgNVBAMMFzEyLTM0LTU2LUZGLUZFLTc4LTkwLUFCMIGfMA0G
CSqGSIb3DQEBAQUAA4GNADCBiQKBgQC4CS9vBHJqkhz6stMTrp0vAcfORl+rfaYs
elxz+s5f+6Lx3YCimtxDOZz8oiJ5uJomSBDluSa7Xg0/cnp2PhYBP4n4/qxZ0Pvd
XosMUoJ+VJDxO4TDY06JxtFzGuXxpg+I7RGNCA4assqlMtBsL30qCHTe5Oa25XKD
9keNr0JT2wIDAQABow8wDTALBgNVHQ8EBAMCB4AwDQYJKoZIhvcNAQELBQADgYEA
GBLBJZ3E5ChTAXQA4d/ZO6GbIyvogslssZnHA80bDbg03D8/6USiLw/Y3ETDc+CS
dWtBTRsVWLv9DTEMWu54D2UQnp9jgoiuouE+mCvAhC00DjCAizYJ5yDk5xN93Vik
7jHXYl9hrTw2ey826BOcyJuxlfUfN9GMGf7LHRYs6Ok=
-----END CERTIFICATE-----
~~~~~

Textual Representation:

[comment]: <> (replace-data:cert/selfsign-rsa/x509cert.txt)
~~~~~
Certificate:
  Version: v3 (2)
  Serial Number:
    12:34
  Issuer: CN=12-34-56-FF-FE-78-90-AB
  Validity:
    Not Before: Thu Jan 02 01:00:00 CET 2025
    Not After : Sat Jan 01 00:59:59 CET 10000
  Subject: CN=12-34-56-FF-FE-78-90-AB
  Subject Public Key Info:
    Public Key Algorithm: 1.2.840.113549.1.1.1
    Pub:
      30:81:89:02:81:81:00:b8:09:2f:6f:04:72:6a:92:1c:fa:b2:
      d3:13:ae:9d:2f:01:c7:ce:46:5f:ab:7d:a6:2c:7a:5c:73:fa:
      ce:5f:fb:a2:f1:dd:80:a2:9a:dc:43:39:9c:fc:a2:22:79:b8:
      9a:26:48:10:e5:b9:26:bb:5e:0d:3f:72:7a:76:3e:16:01:3f:
      89:f8:fe:ac:59:d0:fb:dd:5e:8b:0c:52:82:7e:54:90:f1:3b:
      84:c3:63:4e:89:c6:d1:73:1a:e5:f1:a6:0f:88:ed:11:8d:08:
      0e:1a:b2:ca:a5:32:d0:6c:2f:7d:2a:08:74:de:e4:e6:b6:e5:
      72:83:f6:47:8d:af:42:53:db:02:03:01:00:01
  X509v3 extensions:
    X509v3 keyUsage:
      digitalSignature
  Signature Algorithm: SHA256WITHRSA
  Signature Value:
    18:12:c1:25:9d:c4:e4:28:53:01:74:00:e1:df:d9:3b:a1:9b:
    23:2b:e8:82:c9:6c:b1:99:c7:03:cd:1b:0d:b8:34:dc:3f:3f:
    e9:44:a2:2f:0f:d8:dc:44:c3:73:e0:92:75:6b:41:4d:1b:15:
    58:bb:fd:0d:31:0c:5a:ee:78:0f:65:10:9e:9f:63:82:88:ae:
    a2:e1:3e:98:2b:c0:84:2d:34:0e:30:80:8b:36:09:e7:20:e4:
    e7:13:7d:dd:58:a4:ee:31:d7:62:5f:61:ad:3c:36:7b:2f:36:
    e8:13:9c:c8:9b:b1:95:f5:1f:37:d1:8c:19:fe:cb:1d:16:2c:
    e8:e9
~~~~~

### C509 Type 3 Certificate

- C509 type 3 certificate converted from the X.509 certificate in {{x509-selfsign-rsa}}.
- Compared to the C509 type 2 certificate, the only differences are the certificate type, the signature value, and the public key identifier.

[comment]: <> (replace-size:cert/selfsign-rsa/c509cert-t3.hex)
Plain hex (284 bytes):

[comment]: <> (replace-data:cert/selfsign-rsa/c509cert-t3.hex)
~~~~~
8B0342123417F61A6775D700F6D830461234567890AB005880B8092F6F04726A921C
FAB2D313AE9D2F01C7CE465FAB7DA62C7A5C73FACE5FFBA2F1DD80A29ADC43399CFC
A22279B89A264810E5B926BB5E0D3F727A763E16013F89F8FEAC59D0FBDD5E8B0C52
827E5490F13B84C3634E89C6D1731AE5F1A60F88ED118D080E1AB2CAA532D06C2F7D
2A0874DEE4E6B6E57283F6478DAF4253DB0158801812C1259DC4E42853017400E1DF
D93BA19B232BE882C96CB199C703CD1B0DB834DC3F3FE944A22F0FD8DC44C373E092
756B414D1B1558BBFD0D310C5AEE780F65109E9F638288AEA2E13E982BC0842D340E
30808B3609E720E4E7137DDD58A4EE31D7625F61AD3C367B2F36E8139CC89BB195F5
1F37D18C19FECB1D162CE8E9
~~~~~

Annotated hex:

- See the annotated hex for the C509 type 2 certificate in {{c509-selfsign-rsa}}. The only differences are the certificate type, the signature value, and the key identifiers.

### C509 Type 2 Certificate {#c509-selfsign-rsa}

[comment]: <> (replace-size:cert/selfsign-rsa/c509cert-t2.hex)
Plain hex (284 bytes):

[comment]: <> (replace-data:cert/selfsign-rsa/c509cert-t2.hex)
~~~~~
8B0242123417F61A6775D700F6D830461234567890AB005880B8092F6F04726A921C
FAB2D313AE9D2F01C7CE465FAB7DA62C7A5C73FACE5FFBA2F1DD80A29ADC43399CFC
A22279B89A264810E5B926BB5E0D3F727A763E16013F89F8FEAC59D0FBDD5E8B0C52
827E5490F13B84C3634E89C6D1731AE5F1A60F88ED118D080E1AB2CAA532D06C2F7D
2A0874DEE4E6B6E57283F6478DAF4253DB015880371A7322CDD9DECB1F3B4851A18A
47B461A479C29DCE7397290C79DB95643A5C7FE2B1F02DB6AF5F0BCA9602D837F7EB
3D4AA28738CBCAD385043304E648022A1E9FE0FD19687839AC3EC7C7B6F6E5F85B44
16BA085D5C9E367A0B892829F2F3E4A31D3FDA0E58EA701A72CB3F1B4A06E3DF44F4
492FCFBD5C5F71F03340D7CA
~~~~~

Annotated hex:

[comment]: <> (replace-data:cert/selfsign-rsa/c509cert-t2.diag)
~~~~~
  0: 8B             # C509Certificate=array[11]
  1:   02             # [0]. certificate type=2
  2:   42             # [1]. certificateSerialNumber=byte[2]
  3:     1234
  5:   17             # [2]. signature alg=sha256WithRSAEncryption
                      #      (23)
  6:   F6             # [3]. issuer=<null>
  7:   1A 6775D700    # [4]. notBefore=1735776000:
                      #      2025-01-02T00:00:00Z
 12:   F6             # [5]. notAfter=<null>
 13:   D8 30          # [6]. subject=tag(48)
 15:     46             # byte[6]
 16:       1234567890AB
 22:   00             # [7]. subjectPublicKeyAlg=RSA (0)
 23:   58 80          # [8]. subject public key=modulus=byte[128]
 25:     B8092F6F04726A921CFAB2D313AE9D2F01C7CE465FAB7DA62C7A5C73FA
 54:     CE5FFBA2F1DD80A29ADC43399CFCA22279B89A264810E5B926BB5E0D3F
 83:     727A763E16013F89F8FEAC59D0FBDD5E8B0C52827E5490F13B84C3634E
112:     89C6D1731AE5F1A60F88ED118D080E1AB2CAA532D06C2F7D2A0874DEE4
141:     E6B6E57283F6478DAF4253DB
153:   01             # [9]. extensions=1, KeyUsage:
                      #      [digitalSignature]
154:   58 80          # [10]. signature value=byte[128]
156:     371A7322CDD9DECB1F3B4851A18A47B461A479C29DCE7397290C79DB95
185:     643A5C7FE2B1F02DB6AF5F0BCA9602D837F7EB3D4AA28738CBCAD38504
214:     3304E648022A1E9FE0FD19687839AC3EC7C7B6F6E5F85B4416BA085D5C
243:     9E367A0B892829F2F3E4A31D3FDA0E58EA701A72CB3F1B4A06E3DF44F4
272:     492FCFBD5C5F71F03340D7CA
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
-----BEGIN PRIVATE KEY-----
MIICeQIBADANBgkqhkiG9w0BAQEFAASCAmMwggJfAgEAAoGBALgJL28EcmqSHPqy
0xOunS8Bx85GX6t9pix6XHP6zl/7ovHdgKKa3EM5nPyiInm4miZIEOW5JrteDT9y
enY+FgE/ifj+rFnQ+91eiwxSgn5UkPE7hMNjTonG0XMa5fGmD4jtEY0IDhqyyqUy
0GwvfSoIdN7k5rblcoP2R42vQlPbAgUBAAAAAQKBgE3mIbMMJlO/XyHBNfBtKWLe
6Sf+tlOecafT3HGmDHbZc+Z5ENXuCJw7troW2qozNNk23fNayknBx1qJjAgL0bD3
zwWk0SYJwFXQDUu/9D8RUas14eQfC5WUQGBAGuJVXDMyK7LoNulWvCD+KQIndBLN
u6wzYRMWmwq3BWtEFsLVAkEA8k17eXQyp6rwXCngMvqilyd6FPiox7R37/idIhWh
1ByI4kTvnYphezUZnT/4RNY2gGfwvtkU72CNx3/G2VIARQJBAMJwfKIZy41EXHq9
MajxK7jrT88XxWIAs5QBnA+IU74XGnHaTJYqTkO0psI5eB4Q8TaBwIVPXO1v+e6w
8m88lZ8CQBsJKNy9t67rKHgi3j6OP6OnQKEawjrTJ6sxRPbHvKQ//Eqihnuv8M7Q
tKCYIIjm31yHvyi7WJzb81KtdzRQACkCQQCJ8eE96J22NQsKhaH2GYQaoyaJPTg0
hXX1JPXV3a7otceXpoWJ1dH7cYBf4vKpHlJlVn59TdmRXAvUH2P544ZzAkEAjf20
7bsVVUEhtJ3LEsgOaTGg+8VfHIZuXxNNwxxyIm2YacTXDXpvfslFWRuNxomp44dt
PmmbbqJRmhxWlrumnw==
-----END PRIVATE KEY-----
~~~~~

### X.509 Certificate {#x509-selfsign-rsa-f5}

[comment]: <> (replace-size:cert/selfsign-rsa-f5/x509cert.pem)
PEM content (467 bytes):

[comment]: <> (replace-data:cert/selfsign-rsa-f5/x509cert.pem)
~~~~~
-----BEGIN CERTIFICATE-----
MIIBzzCCATigAwIBAgICEjQwDQYJKoZIhvcNAQEMBQAwIjEgMB4GA1UEAwwXMTIt
MzQtNTYtNzgtOTAtQUItQ0QtRUYwHhcNMjUwMTAyMDAwMDAwWhcNMjYwMTAyMDAw
MDAwWjAiMSAwHgYDVQQDDBcxMi0zNC01Ni03OC05MC1BQi1DRC1FRjCBoTANBgkq
hkiG9w0BAQEFAAOBjwAwgYsCgYEAuAkvbwRyapIc+rLTE66dLwHHzkZfq32mLHpc
c/rOX/ui8d2AoprcQzmc/KIiebiaJkgQ5bkmu14NP3J6dj4WAT+J+P6sWdD73V6L
DFKCflSQ8TuEw2NOicbRcxrl8aYPiO0RjQgOGrLKpTLQbC99Kgh03uTmtuVyg/ZH
ja9CU9sCBQEAAAABoxIwEDAOBgNVHQ8BAf8EBAMCB4AwDQYJKoZIhvcNAQEMBQAD
gYEAds76W8xddqZgQ+2qwuS/ulE85K4HB5VkFEvyB/RphTFhZwUjlKCD97af01v7
7QQ62LJigojdL44ZyGwgx97itpr6CRuC76vbxau9+RyEWdFP+M+/bRD2jqWvrnR0
mIIQ2CmvG8Z+AaXf6kMKqgckKohtQwcBTt9sGkLgLQuJNSE=
-----END CERTIFICATE-----
~~~~~

Textual Representation:

[comment]: <> (replace-data:cert/selfsign-rsa-f5/x509cert.txt)
~~~~~
Certificate:
  Version: v3 (2)
  Serial Number:
    12:34
  Issuer: CN=12-34-56-78-90-AB-CD-EF
  Validity:
    Not Before: Thu Jan 02 01:00:00 CET 2025
    Not After : Fri Jan 02 01:00:00 CET 2026
  Subject: CN=12-34-56-78-90-AB-CD-EF
  Subject Public Key Info:
    Public Key Algorithm: 1.2.840.113549.1.1.1
    Pub:
      30:81:8b:02:81:81:00:b8:09:2f:6f:04:72:6a:92:1c:fa:b2:
      d3:13:ae:9d:2f:01:c7:ce:46:5f:ab:7d:a6:2c:7a:5c:73:fa:
      ce:5f:fb:a2:f1:dd:80:a2:9a:dc:43:39:9c:fc:a2:22:79:b8:
      9a:26:48:10:e5:b9:26:bb:5e:0d:3f:72:7a:76:3e:16:01:3f:
      89:f8:fe:ac:59:d0:fb:dd:5e:8b:0c:52:82:7e:54:90:f1:3b:
      84:c3:63:4e:89:c6:d1:73:1a:e5:f1:a6:0f:88:ed:11:8d:08:
      0e:1a:b2:ca:a5:32:d0:6c:2f:7d:2a:08:74:de:e4:e6:b6:e5:
      72:83:f6:47:8d:af:42:53:db:02:05:01:00:00:00:01
  X509v3 extensions:
    X509v3 keyUsage: critical
      digitalSignature
  Signature Algorithm: SHA384WITHRSA
  Signature Value:
    76:ce:fa:5b:cc:5d:76:a6:60:43:ed:aa:c2:e4:bf:ba:51:3c:
    e4:ae:07:07:95:64:14:4b:f2:07:f4:69:85:31:61:67:05:23:
    94:a0:83:f7:b6:9f:d3:5b:fb:ed:04:3a:d8:b2:62:82:88:dd:
    2f:8e:19:c8:6c:20:c7:de:e2:b6:9a:fa:09:1b:82:ef:ab:db:
    c5:ab:bd:f9:1c:84:59:d1:4f:f8:cf:bf:6d:10:f6:8e:a5:af:
    ae:74:74:98:82:10:d8:29:af:1b:c6:7e:01:a5:df:ea:43:0a:
    aa:07:24:2a:88:6d:43:07:01:4e:df:6c:1a:42:e0:2d:0b:89:
    35:21
~~~~~

### C509 Type 3 Certificate

- C509 type 3 certificate converted from the X.509 certificate in {{x509-selfsign-rsa-f5}}.

[comment]: <> (replace-size:cert/selfsign-rsa-f5/c509cert-t3.hex)
Plain hex (298 bytes):

[comment]: <> (replace-data:cert/selfsign-rsa-f5/c509cert-t3.hex)
~~~~~
8B034212341818F61A6775D7001A69570A80D830481234567890ABCDEF00825880B8
092F6F04726A921CFAB2D313AE9D2F01C7CE465FAB7DA62C7A5C73FACE5FFBA2F1DD
80A29ADC43399CFCA22279B89A264810E5B926BB5E0D3F727A763E16013F89F8FEAC
59D0FBDD5E8B0C52827E5490F13B84C3634E89C6D1731AE5F1A60F88ED118D080E1A
B2CAA532D06C2F7D2A0874DEE4E6B6E57283F6478DAF4253DB450100000001205880
76CEFA5BCC5D76A66043EDAAC2E4BFBA513CE4AE07079564144BF207F46985316167
052394A083F7B69FD35BFBED043AD8B2628288DD2F8E19C86C20C7DEE2B69AFA091B
82EFABDBC5ABBDF91C8459D14FF8CFBF6D10F68EA5AFAE7474988210D829AF1BC67E
01A5DFEA430AAA07242A886D4307014EDF6C1A42E02D0B893521
~~~~~

Annotated hex:

- See the annotated hex for the C509 type 2 certificate in {{c509-selfsign-rsa-f5}}. The only differences are the certificate type, the signature value, and the key identifiers.

### C509 Type 2 Certificate {#c509-selfsign-rsa-f5}

[comment]: <> (replace-size:cert/selfsign-rsa-f5/c509cert-t2.hex)
Plain hex (298 bytes):

[comment]: <> (replace-data:cert/selfsign-rsa-f5/c509cert-t2.hex)
~~~~~
8B024212341818F61A6775D7001A69570A80D830481234567890ABCDEF00825880B8
092F6F04726A921CFAB2D313AE9D2F01C7CE465FAB7DA62C7A5C73FACE5FFBA2F1DD
80A29ADC43399CFCA22279B89A264810E5B926BB5E0D3F727A763E16013F89F8FEAC
59D0FBDD5E8B0C52827E5490F13B84C3634E89C6D1731AE5F1A60F88ED118D080E1A
B2CAA532D06C2F7D2A0874DEE4E6B6E57283F6478DAF4253DB450100000001205880
507EE326549994969B3FAD4309A2704F424E435A24C505603F51104F1E8DDB153D78
4A9E34E88BC74F67143970B0FCBD2119AC89E87ABF9C2818FFBF5C6993F87F7A5B64
0BB7E0FDBCE5F2136377F70C279D76866D725E5868F4FE56F031E80ACBEDE58A6E1A
D1CD65A0ADB303C555F408651D5E7F752805EA4B79EF36105344
~~~~~

Annotated hex:

[comment]: <> (replace-data:cert/selfsign-rsa-f5/c509cert-t2.diag)
~~~~~
  0: 8B             # C509Certificate=array[11]
  1:   02             # [0]. certificate type=2
  2:   42             # [1]. certificateSerialNumber=byte[2]
  3:     1234
  5:   18 18          # [2]. signature alg=sha384WithRSAEncryption
                      #      (24)
  7:   F6             # [3]. issuer=<null>
  8:   1A 6775D700    # [4]. notBefore=1735776000:
                      #      2025-01-02T00:00:00Z
 13:   1A 69570A80    # [5]. notAfter=1767312000:
                      #      2026-01-02T00:00:00Z
 18:   D8 30          # [6]. subject=tag(48)
 20:     48             # byte[8]
 21:       1234567890ABCDEF
 29:   00             # [7]. subjectPublicKeyAlg=RSA (0)
 30:   82             # [8]. subject public key=array[2]
 31:     58 80          # [0]. modulus=byte[128]
 33:       B8092F6F04726A921CFAB2D313AE9D2F01C7CE465FAB7DA62C7A5C73
 61:       FACE5FFBA2F1DD80A29ADC43399CFCA22279B89A264810E5B926BB5E
 89:       0D3F727A763E16013F89F8FEAC59D0FBDD5E8B0C52827E5490F13B84
117:       C3634E89C6D1731AE5F1A60F88ED118D080E1AB2CAA532D06C2F7D2A
145:       0874DEE4E6B6E57283F6478DAF4253DB
161:     45             # [1]. exponent=byte[5]
162:       0100000001
167:   20             # [9]. extensions=-1, KeyUsage, critical:
                      #      [digitalSignature]
168:   58 80          # [10]. signature value=byte[128]
170:     507EE326549994969B3FAD4309A2704F424E435A24C505603F51104F1E
199:     8DDB153D784A9E34E88BC74F67143970B0FCBD2119AC89E87ABF9C2818
228:     FFBF5C6993F87F7A5B640BB7E0FDBCE5F2136377F70C279D76866D725E
257:     5868F4FE56F031E80ACBEDE58A6E1AD1CD65A0ADB303C555F408651D5E
286:     7F752805EA4B79EF36105344
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
-----BEGIN PRIVATE KEY-----
MEECAQAwEwYHKoZIzj0CAQYIKoZIzj0DAQcEJzAlAgEBBCAMGYOct/eLyZgLXgeP
Q3jT81GUERXqvBvkzaxRD1pG7A==
-----END PRIVATE KEY-----
~~~~~

### X.509 Certificate {#x509-selfsign-secp256r1}

[comment]: <> (replace-size:cert/selfsign-secp256r1/x509cert.pem)
PEM content (383 bytes):

[comment]: <> (replace-data:cert/selfsign-secp256r1/x509cert.pem)
~~~~~
-----BEGIN CERTIFICATE-----
MIIBezCCASCgAwIBAgICEjQwCgYIKoZIzj0EAwIwGzEZMBcGA1UEAwwQMTIzNDU2
Nzg5MGFiY2RlZjAeFw0yNTAxMDIwMDAwMDBaFw0yNjAxMDIwMDAwMDBaMBsxGTAX
BgNVBAMMEDEyMzQ1Njc4OTBhYmNkZWYwWTATBgcqhkjOPQIBBggqhkjOPQMBBwNC
AAT0E1lqhxJZlbTg2Le++8TW7bEfYa8IqzJAjU/5+QeN26s2Na/UltVlaiLv3D1Z
xEgqmYNrs1j79Mp405MENshXo1QwUjAdBgNVHQ4EFgQUB+EsTKzpXCKF7EpbBaSi
uw7IenowCwYDVR0PBAQDAgEGMA8GA1UdEwEB/wQFMAMBAf8wEwYDVR0lBAwwCgYI
KwYBBQUHAwEwCgYIKoZIzj0EAwIDSQAwRgIhAIol6Kq7pLGbjg0VlqR2wsQvUGj1
80V2BoBuLyhKIubnAiEAp5m4CfQ2QkbnoJOzzBDOKLXtmsX+4pVCSDuF5iQ7wT8=
-----END CERTIFICATE-----
~~~~~

Textual Representation:

[comment]: <> (replace-data:cert/selfsign-secp256r1/x509cert.txt)
~~~~~
Certificate:
  Version: v3 (2)
  Serial Number:
    12:34
  Issuer: CN=1234567890abcdef
  Validity:
    Not Before: Thu Jan 02 01:00:00 CET 2025
    Not After : Fri Jan 02 01:00:00 CET 2026
  Subject: CN=1234567890abcdef
  Subject Public Key Info:
    Public Key Algorithm: EC/P256
    Pub:
      04:f4:13:59:6a:87:12:59:95:b4:e0:d8:b7:be:fb:c4:d6:ed:
      b1:1f:61:af:08:ab:32:40:8d:4f:f9:f9:07:8d:db:ab:36:35:
      af:d4:96:d5:65:6a:22:ef:dc:3d:59:c4:48:2a:99:83:6b:b3:
      58:fb:f4:ca:78:d3:93:04:36:c8:57
  X509v3 extensions:
    X509v3 subjectKeyIdentifier:
      07:e1:2c:4c:ac:e9:5c:22:85:ec:4a:5b:05:a4:a2:bb:0e:c8:7a:7a
    X509v3 keyUsage:
      keyCertSign, cRLSign
    X509v3 basicConstraints: critical
      CA: true, pathlen: null
    X509v3 extendedKeyUsage:
      kp-serverAuth
  Signature Algorithm: SHA256WITHECDSA
  Signature Value:
    30:46:02:21:00:8a:25:e8:aa:bb:a4:b1:9b:8e:0d:15:96:a4:
    76:c2:c4:2f:50:68:f5:f3:45:76:06:80:6e:2f:28:4a:22:e6:
    e7:02:21:00:a7:99:b8:09:f4:36:42:46:e7:a0:93:b3:cc:10:
    ce:28:b5:ed:9a:c5:fe:e2:95:42:48:3b:85:e6:24:3b:c1:3f
~~~~~

### C509 Type 3 Certificate

- C509 type 3 certificate converted from the X.509 certificate in {{x509-selfsign-secp256r1}}.

[comment]: <> (replace-size:cert/selfsign-secp256r1/c509cert-t3.hex)
Plain hex (190 bytes):

[comment]: <> (replace-data:cert/selfsign-secp256r1/c509cert-t3.hex)
~~~~~
8B0342123400F61A6775D7001A69570A80481234567890ABCDEF01584104F413596A
87125995B4E0D8B7BEFBC4D6EDB11F61AF08AB32408D4FF9F9078DDBAB3635AFD496
D5656A22EFDC3D59C4482A99836BB358FBF4CA78D3930436C85788015407E12C4CAC
E95C2285EC4A5B05A4A2BB0EC87A7A0218602320080158408A25E8AABBA4B19B8E0D
1596A476C2C42F5068F5F3457606806E2F284A22E6E7A799B809F4364246E7A093B3
CC10CE28B5ED9AC5FEE29542483B85E6243BC13F
~~~~~

Annotated hex:

- See the annotated hex for the C509 type 2 certificate in {{c509-selfsign-secp256r1}}. The only differences are the certificate type, the signature value, and the public key identifier.

### C509 Type 2 Certificate {#c509-selfsign-secp256r1}

[comment]: <> (replace-size:cert/selfsign-secp256r1/c509cert-t2.hex)
Plain hex (190 bytes):

[comment]: <> (replace-data:cert/selfsign-secp256r1/c509cert-t2.hex)
~~~~~
8B0242123400F61A6775D7001A69570A80481234567890ABCDEF01584104F413596A
87125995B4E0D8B7BEFBC4D6EDB11F61AF08AB32408D4FF9F9078DDBAB3635AFD496
D5656A22EFDC3D59C4482A99836BB358FBF4CA78D3930436C8578801541F3BC19DE1
94830066C6EAE7CB9D211339EDD9420218602320080158408A25E8AABBA4B19B8E0D
1596A476C2C42F5068F5F3457606806E2F284A22E6E71EF91E63F35636A4D497E1FB
4D3C393ADCDB09D92E02E0194D703ECD98EB2D79
~~~~~

Annotated hex:

[comment]: <> (replace-data:cert/selfsign-secp256r1/c509cert-t2.diag)
~~~~~
  0: 8B             # C509Certificate=array[11]
  1:   02             # [0]. certificate type=2
  2:   42             # [1]. certificateSerialNumber=byte[2]
  3:     1234
  5:   00             # [2]. signature alg=ecdsa-with-sha256 (0)
  6:   F6             # [3]. issuer=<null>
  7:   1A 6775D700    # [4]. notBefore=1735776000:
                      #      2025-01-02T00:00:00Z
 12:   1A 69570A80    # [5]. notAfter=1767312000:
                      #      2026-01-02T00:00:00Z
 17:   48             # [6]. subject=byte[8]
 18:     1234567890ABCDEF
 26:   01             # [7]. subjectPublicKeyAlg=EC public key on
                      #      curve secp256r1 (1)
 27:   58 41          # [8]. subject public key=EC point=byte[65]
 29:     04F413596A87125995B4E0D8B7BEFBC4D6EDB11F61AF08AB32408D4FF9
 58:     F9078DDBAB3635AFD496D5656A22EFDC3D59C4482A99836BB358FBF4CA
 87:     78D3930436C857
 94:   88             # [9]. extensions=array[8]
                        #---extension[0]---
 95:     01             # [0]. type=SubjectKeyIdentifier (1)
 96:     54             # [1]. value=byte[20]
 97:       1F3BC19DE194830066C6EAE7CB9D211339EDD942
                        #---extension[1]---
117:     02             # [2]. type=KeyUsage (2)
118:     18 60          # [3]. value=96: [keyCertSign, cRLSign]
                        #---extension[2]---
120:     23             # [4]. type=BasicConstraints, critical (-4)
121:     20             # [5]. value=-1: CA: true,
                        #      pathLenConstraint: unlimited
                        #---extension[3]---
122:     08             # [6]. type=ExtendedKeyUsage (8)
123:     01             # [7]. value=serverAuth (1)
124:   58 40          # [10]. signature value=byte[64]
126:     8A25E8AABBA4B19B8E0D1596A476C2C42F5068F5F3457606806E2F284A
155:     22E6E71EF91E63F35636A4D497E1FB4D3C393ADCDB09D92E02E0194D70
184:     3ECD98EB2D79
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
Plain hex (158 bytes):

[comment]: <> (replace-data:cert/selfsign-compress-secp256r1/c509cert-t3.hex)
~~~~~
8B0342123400F61A6775D7001A69570A80481234567890ABCDEF015821FDF413596A
87125995B4E0D8B7BEFBC4D6EDB11F61AF08AB32408D4FF9F9078DDB88015407E12C
4CACE95C2285EC4A5B05A4A2BB0EC87A7A0218602320080158408A25E8AABBA4B19B
8E0D1596A476C2C42F5068F5F3457606806E2F284A22E6E7A799B809F4364246E7A0
93B3CC10CE28B5ED9AC5FEE29542483B85E6243BC13F
~~~~~

Annotated hex:

- See the annotated hex for the C509 type 2 certificate in {{c509-selfsign-compress-secp256r1}}. The only differences are the certificate type, the signature value, and the public key identifier.

### C509 Type 2 Certificate {#c509-selfsign-compress-secp256r1}

[comment]: <> (replace-size:cert/selfsign-compress-secp256r1/c509cert-t2.hex)
Plain hex (158 bytes):

[comment]: <> (replace-data:cert/selfsign-compress-secp256r1/c509cert-t2.hex)
~~~~~
8B0242123400F61A6775D7001A69570A80481234567890ABCDEF015821FDF413596A
87125995B4E0D8B7BEFBC4D6EDB11F61AF08AB32408D4FF9F9078DDB88015426CD54
0B3E3D99A64AAB0B62ECA277B4359EAD040218602320080158408A25E8AABBA4B19B
8E0D1596A476C2C42F5068F5F3457606806E2F284A22E6E7017CEE1E3B865C5EEA2A
7946503C5BA1C39424F71ACDC6AFA5FB55F7E397B83C
~~~~~

Annotated hex:

[comment]: <> (replace-data:cert/selfsign-compress-secp256r1/c509cert-t2.diag)
~~~~~
  0: 8B             # C509Certificate=array[11]
  1:   02             # [0]. certificate type=2
  2:   42             # [1]. certificateSerialNumber=byte[2]
  3:     1234
  5:   00             # [2]. signature alg=ecdsa-with-sha256 (0)
  6:   F6             # [3]. issuer=<null>
  7:   1A 6775D700    # [4]. notBefore=1735776000:
                      #      2025-01-02T00:00:00Z
 12:   1A 69570A80    # [5]. notAfter=1767312000:
                      #      2026-01-02T00:00:00Z
 17:   48             # [6]. subject=byte[8]
 18:     1234567890ABCDEF
 26:   01             # [7]. subjectPublicKeyAlg=EC public key on
                      #      curve secp256r1 (1)
 27:   58 21          # [8]. subject public key=EC point=byte[33]
 29:     FDF413596A87125995B4E0D8B7BEFBC4D6EDB11F61AF08AB32408D4FF9
 58:     F9078DDB
 62:   88             # [9]. extensions=array[8]
                        #---extension[0]---
 63:     01             # [0]. type=SubjectKeyIdentifier (1)
 64:     54             # [1]. value=byte[20]
 65:       26CD540B3E3D99A64AAB0B62ECA277B4359EAD04
                        #---extension[1]---
 85:     02             # [2]. type=KeyUsage (2)
 86:     18 60          # [3]. value=96: [keyCertSign, cRLSign]
                        #---extension[2]---
 88:     23             # [4]. type=BasicConstraints, critical (-4)
 89:     20             # [5]. value=-1: CA: true,
                        #      pathLenConstraint: unlimited
                        #---extension[3]---
 90:     08             # [6]. type=ExtendedKeyUsage (8)
 91:     01             # [7]. value=serverAuth (1)
 92:   58 40          # [10]. signature value=byte[64]
 94:     8A25E8AABBA4B19B8E0D1596A476C2C42F5068F5F3457606806E2F284A
123:     22E6E7017CEE1E3B865C5EEA2A7946503C5BA1C39424F71ACDC6AFA5FB
152:     55F7E397B83C
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
-----BEGIN PRIVATE KEY-----
ME4CAQAwEAYHKoZIzj0CAQYFK4EEACIENzA1AgEBBDA21kT4P50ZNXthip3vROKI
MkRC62+05D/N4Hr8iGcZ6gaS2DwnwNLOUYwYlY1pT3I=
-----END PRIVATE KEY-----
~~~~~

### X.509 Certificate {#x509-selfsign-secp384r1}

[comment]: <> (replace-size:cert/selfsign-secp384r1/x509cert.pem)
PEM content (539 bytes):

[comment]: <> (replace-data:cert/selfsign-secp384r1/x509cert.pem)
~~~~~
-----BEGIN CERTIFICATE-----
MIICFzCCAZ6gAwIBAgICEjQwCgYIKoZIzj0EAwMwHTEbMBkGA1UEAwwSc2VsZnNp
Z24tc2VjcDM4NHIxMB4XDTI1MDEwMjAwMDAwMFoXDTI2MDEwMjAwMDAwMFowHTEb
MBkGA1UEAwwSc2VsZnNpZ24tc2VjcDM4NHIxMHYwEAYHKoZIzj0CAQYFK4EEACID
YgAE3Wdi8DWJlFE3Ky/ptSqDFK0Q4sQ2PFpYSeKW/lGqub/QOrA40zQYoLzYMoCr
oL2RBAFxZcBItTRrVBCeRJ/MiJ5O6HC1+KLGPa5BTeGXVb4nihdeBA1KTBoO5G6S
Slwpo4GwMIGtMAsGA1UdDwQEAwIBBjASBgNVHRMBAf8ECDAGAQH/AgEBMGgGA1Ud
IARhMF8wCAYGZ4EMAQIDMFMGBFUdIAAwSzAiBggrBgEFBQcCARYWaHR0cDovL2Nw
cy5leGFtcGxlLmNvbTAlBggrBgEFBQcCAjAZDBd0aGlzIGlzIHRoZSB1c2VyIG5v
dGljZTAUBgNVHSUEDTALBgkrBgEEAYH9WQQwCgYDVR02BAMCAQAwCgYIKoZIzj0E
AwMDZwAwZAIwNH617Unh9lNuKj87XfHBLZuu9EDfvEK9RdMfSz/avfqdaFqEWC2Y
sbJHQhDxx4XaAjA0gnN1wF0oDGkz6hPat63ExIwGXALM0hnZAIyqqLHtv6b3oa56
KppqhQZ6RL8aIe8=
-----END CERTIFICATE-----
~~~~~

Textual Representation:

[comment]: <> (replace-data:cert/selfsign-secp384r1/x509cert.txt)
~~~~~
Certificate:
  Version: v3 (2)
  Serial Number:
    12:34
  Issuer: CN=selfsign-secp384r1
  Validity:
    Not Before: Thu Jan 02 01:00:00 CET 2025
    Not After : Fri Jan 02 01:00:00 CET 2026
  Subject: CN=selfsign-secp384r1
  Subject Public Key Info:
    Public Key Algorithm: EC/P384
    Pub:
      04:dd:67:62:f0:35:89:94:51:37:2b:2f:e9:b5:2a:83:14:ad:
      10:e2:c4:36:3c:5a:58:49:e2:96:fe:51:aa:b9:bf:d0:3a:b0:
      38:d3:34:18:a0:bc:d8:32:80:ab:a0:bd:91:04:01:71:65:c0:
      48:b5:34:6b:54:10:9e:44:9f:cc:88:9e:4e:e8:70:b5:f8:a2:
      c6:3d:ae:41:4d:e1:97:55:be:27:8a:17:5e:04:0d:4a:4c:1a:
      0e:e4:6e:92:4a:5c:29
  X509v3 extensions:
    X509v3 keyUsage:
      keyCertSign, cRLSign
    X509v3 basicConstraints: critical
      CA: true, pathlen: 1
    X509v3 certificatePolicies:
      Policy: Individual Validation (IV)
      Policy: Any Policy
        CPS: http://cps.example.com
        User Notice: [this is the user notice]
    X509v3 extendedKeyUsage:
      1.3.6.1.4.1.32473.4
    X509v3 inhibitAnyPolicy:
      02:01:00
  Signature Algorithm: SHA384WITHECDSA
  Signature Value:
    30:64:02:30:34:7e:b5:ed:49:e1:f6:53:6e:2a:3f:3b:5d:f1:
    c1:2d:9b:ae:f4:40:df:bc:42:bd:45:d3:1f:4b:3f:da:bd:fa:
    9d:68:5a:84:58:2d:98:b1:b2:47:42:10:f1:c7:85:da:02:30:
    34:82:73:75:c0:5d:28:0c:69:33:ea:13:da:b7:ad:c4:c4:8c:
    06:5c:02:cc:d2:19:d9:00:8c:aa:a8:b1:ed:bf:a6:f7:a1:ae:
    7a:2a:9a:6a:85:06:7a:44:bf:1a:21:ef
~~~~~

### C509 Type 3 Certificate

- C509 type 3 certificate converted from the X.509 certificate in {{x509-selfsign-secp384r1}}.

[comment]: <> (replace-size:cert/selfsign-secp384r1/c509cert-t3.hex)
Plain hex (309 bytes):

[comment]: <> (replace-data:cert/selfsign-secp384r1/c509cert-t3.hex)
~~~~~
8B0342123401F61A6775D7001A69570A807273656C667369676E2D73656370333834
723102586104DD6762F035899451372B2FE9B52A8314AD10E2C4363C5A5849E296FE
51AAB9BFD03AB038D33418A0BCD83280ABA0BD9104017165C048B5346B54109E449F
CC889E4EE870B5F8A2C63DAE414DE19755BE278A175E040D4A4C1A0EE46E924A5C29
8A02186023010684038000840176687474703A2F2F6370732E6578616D706C652E63
6F6D027774686973206973207468652075736572206E6F7469636508492B06010401
81FD5904181E005860347EB5ED49E1F6536E2A3F3B5DF1C12D9BAEF440DFBC42BD45
D31F4B3FDABDFA9D685A84582D98B1B2474210F1C785DA34827375C05D280C6933EA
13DAB7ADC4C48C065C02CCD219D9008CAAA8B1EDBFA6F7A1AE7A2A9A6A85067A44BF
1A21EF
~~~~~

Annotated hex:

- See the annotated hex for the C509 type 2 certificate in {{c509-selfsign-secp384r1}}. The only differences are the certificate type, the signature value, and the key identifiers.

### C509 Type 2 Certificate {#c509-selfsign-secp384r1}

[comment]: <> (replace-size:cert/selfsign-secp384r1/c509cert-t2.hex)
Plain hex (309 bytes):

[comment]: <> (replace-data:cert/selfsign-secp384r1/c509cert-t2.hex)
~~~~~
8B0242123401F61A6775D7001A69570A807273656C667369676E2D73656370333834
723102586104DD6762F035899451372B2FE9B52A8314AD10E2C4363C5A5849E296FE
51AAB9BFD03AB038D33418A0BCD83280ABA0BD9104017165C048B5346B54109E449F
CC889E4EE870B5F8A2C63DAE414DE19755BE278A175E040D4A4C1A0EE46E924A5C29
8A02186023010684038000840176687474703A2F2F6370732E6578616D706C652E63
6F6D027774686973206973207468652075736572206E6F7469636508492B06010401
81FD5904181E005860347EB5ED49E1F6536E2A3F3B5DF1C12D9BAEF440DFBC42BD45
D31F4B3FDABDFA9D685A84582D98B1B2474210F1C785DA055BE26787AB4DD58FD330
E3435AC84EBE49A4382FB964591C3EACC9A04F3814FC2964C1B4B201C013AE7CCB37
27CDF7
~~~~~

Annotated hex:

[comment]: <> (replace-data:cert/selfsign-secp384r1/c509cert-t2.diag)
~~~~~
  0: 8B             # C509Certificate=array[11]
  1:   02             # [0]. certificate type=2
  2:   42             # [1]. certificateSerialNumber=byte[2]
  3:     1234
  5:   01             # [2]. signature alg=ecdsa-with-sha384 (1)
  6:   F6             # [3]. issuer=<null>
  7:   1A 6775D700    # [4]. notBefore=1735776000:
                      #      2025-01-02T00:00:00Z
 12:   1A 69570A80    # [5]. notAfter=1767312000:
                      #      2026-01-02T00:00:00Z
 17:   72             # [6]. subject=char[18]
 18:     73656C667369676E2D73656370333834 # "selfsign-secp384"
 34:     7231                             # "r1"
 36:   02             # [7]. subjectPublicKeyAlg=EC public key on
                      #      curve secp384r1 (2)
 37:   58 61          # [8]. subject public key=EC point=byte[97]
 39:     04DD6762F035899451372B2FE9B52A8314AD10E2C4363C5A5849E296FE
 68:     51AAB9BFD03AB038D33418A0BCD83280ABA0BD9104017165C048B5346B
 97:     54109E449FCC889E4EE870B5F8A2C63DAE414DE19755BE278A175E040D
126:     4A4C1A0EE46E924A5C29
136:   8A             # [9]. extensions=array[10]
                        #---extension[0]---
137:     02             # [0]. type=KeyUsage (2)
138:     18 60          # [1]. value=96: [keyCertSign, cRLSign]
                        #---extension[1]---
140:     23             # [2]. type=BasicConstraints, critical (-4)
141:     01             # [3]. value=1: CA: true, pathLenConstraint:
                        #      1
                        #---extension[2]---
142:     06             # [4]. type=CertificatePolicies (6)
143:     84             # [5]. value=array[4]
                          #---CertificatePolicy[0]---
144:       03             # [0]. PolicyIdentifier=individual-validat
                          #      ed (3)
145:       80             # [1]. PolicyQualifierInfos=array[0]
                          #---CertificatePolicy[1]---
146:       00             # [2]. PolicyIdentifier=anyPolicy (0)
147:       84             # [3]. PolicyQualifierInfos=array[4]
                            #---PolicyQualifierInfo[0]---
148:         01             # [0]. policyQualifierId=domain-validate
                            #      d (1)
149:         76             # [1]. qualifier=char[22]
150:           687474703A2F2F6370732E6578616D # "http://cps.exam"
165:           706C652E636F6D                 # "ple.com"
                            #---PolicyQualifierInfo[1]---
172:         02             # [2]. policyQualifierId=organization-va
                            #      lidated (2)
173:         77             # [3]. qualifier=char[23]
174:           746869732069732074686520757365 # "this is the use"
189:           72206E6F74696365               # "r notice"
                        #---extension[3]---
197:     08             # [6]. type=ExtendedKeyUsage (8)
198:     49             # [7]. value=byte[9]: 
199:       2B0601040181FD5904 # oid: 1.3.6.1.4.1.32473.4
                        #---extension[4]---
208:     18 1E          # [8]. type=InhibitAnyPolicy (30)
210:     00             # [9]. value=simple-uint(0)
211:   58 60          # [10]. signature value=byte[96]
213:     347EB5ED49E1F6536E2A3F3B5DF1C12D9BAEF440DFBC42BD45D31F4B3F
242:     DABDFA9D685A84582D98B1B2474210F1C785DA055BE26787AB4DD58FD3
271:     30E3435AC84EBE49A4382FB964591C3EACC9A04F3814FC2964C1B4B201
300:     C013AE7CCB3727CDF7
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
-----BEGIN PRIVATE KEY-----
MGACAQAwEAYHKoZIzj0CAQYFK4EEACMESTBHAgEBBEIBTyH+UdnGf8eNYO8U2b7w
Z4A21kT4P50ZOUtjpDPxlkovVX2d6SpSsMn1njLAikYP2YOZwZZwd86lpB1+7sdS
a/Y=
-----END PRIVATE KEY-----
~~~~~

### X.509 Certificate {#x509-selfsign-secp521r1}

[comment]: <> (replace-size:cert/selfsign-secp521r1/x509cert.pem)
PEM content (811 bytes):

[comment]: <> (replace-data:cert/selfsign-secp521r1/x509cert.pem)
~~~~~
-----BEGIN CERTIFICATE-----
MIIDJzCCAoigAwIBAgICEjQwCgYIKoZIzj0EAwQwHTEbMBkGA1UEAwwSc2VsZnNp
Z24tc2VjcDUyMXIxMB4XDTI1MDEwMjAwMDAwMFoXDTI2MDEwMjAwMDAwMFowHTEb
MBkGA1UEAwwSc2VsZnNpZ24tc2VjcDUyMXIxMIGbMBAGByqGSM49AgEGBSuBBAAj
A4GGAAQAVTjOj3zeIpM1yFlYqs0Cnd9lz8KnKnUFXmO4/lnQf7S98Q23uLp9V8XG
keuW6rl0EWFaakMOUXhwMXGc68MF5pIAcFzIKLl1X4/VNFK3d6QMtnklVOVxi6uR
6j8DCGpAcqRwR87sJJPDhARfzG7Y5PdIpSI68SkB6y4ZpsKIlRyTmwWjggFzMIIB
bzALBgNVHQ8EBAMCB4AwggExBgNVHREEggEoMIIBJKQhMB8xCzAJBgNVBAYMAkRF
MRAwDgYDVQQDDAdleGFtcGxlgg9hYmMuZXhhbXBsZS5jb22BD2FiY0BleGFtcGxl
LmNvbaAiBggrBgEFBQcICaAWDBRzbXRwdXRmOEBleGFtcGxlLmNvbYYcaHR0cDov
L215dXJpLmV4YW1wbGUuY29tL2FiY4gJKwYBBAGB/VkIhwQRERERoBkGCSsGAQQB
gf1ZAaAMBAoiIiIiIiIiIiIioBQGCCsGAQUFBwgMoAgEBiIiIiIiIqAqBggrBgEF
BQcICaAeDBxzbXRwLnV0ZjhtYWlsYm94QGV4YW1wbGUuY29toC0GCCsGAQUFBwgE
oCEwHwYJKwYBBAGB/VkCBBIEEDMzMzMzMzMzMzMzMzMzMzMwDAYDVR0TAQH/BAIw
ADAdBgNVHSUEFjAUBggrBgEFBQcDAgYIKwYBBQUHAwEwCgYIKoZIzj0EAwQDgYwA
MIGIAkIBL4NY9kYhymj322RK7+N5WAQaaGQ2r87d5wrOunN0TXAUd4SkGtZ2c6Hu
WzubGoNRVTop6HgVq/zKXR3hCZ8oS0wCQgEzC3SPWC0K7TtbBbfhYp/pm0dzgtHv
PTwXZ/Qp44S7AyONrm4X6kZH0KhubJ3K6PzKVw3BHdO2XZmlFtocAvDl1g==
-----END CERTIFICATE-----
~~~~~

Textual Representation:

[comment]: <> (replace-data:cert/selfsign-secp521r1/x509cert.txt)
~~~~~
Certificate:
  Version: v3 (2)
  Serial Number:
    12:34
  Issuer: CN=selfsign-secp521r1
  Validity:
    Not Before: Thu Jan 02 01:00:00 CET 2025
    Not After : Fri Jan 02 01:00:00 CET 2026
  Subject: CN=selfsign-secp521r1
  Subject Public Key Info:
    Public Key Algorithm: EC/P521
    Pub:
      04:00:55:38:ce:8f:7c:de:22:93:35:c8:59:58:aa:cd:02:9d:
      df:65:cf:c2:a7:2a:75:05:5e:63:b8:fe:59:d0:7f:b4:bd:f1:
      0d:b7:b8:ba:7d:57:c5:c6:91:eb:96:ea:b9:74:11:61:5a:6a:
      43:0e:51:78:70:31:71:9c:eb:c3:05:e6:92:00:70:5c:c8:28:
      b9:75:5f:8f:d5:34:52:b7:77:a4:0c:b6:79:25:54:e5:71:8b:
      ab:91:ea:3f:03:08:6a:40:72:a4:70:47:ce:ec:24:93:c3:84:
      04:5f:cc:6e:d8:e4:f7:48:a5:22:3a:f1:29:01:eb:2e:19:a6:
      c2:88:95:1c:93:9b:05
  X509v3 extensions:
    X509v3 keyUsage:
      digitalSignature
    X509v3 subjectAlternativeName:
      Directory Name: C=DE,CN=example
      DNS: abc.example.com
      RFC822: abc@example.com
      OtherName: SmtpUTF8Mailbox:smtputf8@example.com
      URI: http://myuri.example.com/abc
      RegisteredID: 1.3.6.1.4.1.32473.8
      IP: 17.17.17.17
      OtherName: 1.3.6.1.4.1.32473.1 = h'22222222222222222222'
      OtherName: MACAddress:22-22-22-22-22-22
      OtherName: SmtpUTF8Mailbox:smtp.utf8mailbox@example.com
      OtherName: hardwareModuleName:1.3.6.1.4.1.32473.2 = 
        h'041033333333333333333333333333333333'
    X509v3 basicConstraints: critical
      CA: false
    X509v3 extendedKeyUsage:
      kp-clientAuth
      kp-serverAuth
  Signature Algorithm: SHA512WITHECDSA
  Signature Value:
    30:81:88:02:42:01:2f:83:58:f6:46:21:ca:68:f7:db:64:4a:
    ef:e3:79:58:04:1a:68:64:36:af:ce:dd:e7:0a:ce:ba:73:74:
    4d:70:14:77:84:a4:1a:d6:76:73:a1:ee:5b:3b:9b:1a:83:51:
    55:3a:29:e8:78:15:ab:fc:ca:5d:1d:e1:09:9f:28:4b:4c:02:
    42:01:33:0b:74:8f:58:2d:0a:ed:3b:5b:05:b7:e1:62:9f:e9:
    9b:47:73:82:d1:ef:3d:3c:17:67:f4:29:e3:84:bb:03:23:8d:
    ae:6e:17:ea:46:47:d0:a8:6e:6c:9d:ca:e8:fc:ca:57:0d:c1:
    1d:d3:b6:5d:99:a5:16:da:1c:02:f0:e5:d6
~~~~~

### C509 Type 3 Certificate

- C509 type 3 certificate converted from the X.509 certificate in {{x509-selfsign-secp521r1}}.

[comment]: <> (replace-size:cert/selfsign-secp521r1/c509cert-t3.hex)
Plain hex (531 bytes):

[comment]: <> (replace-data:cert/selfsign-secp521r1/c509cert-t3.hex)
~~~~~
8B0342123402F61A6775D7001A69570A807273656C667369676E2D73656370353231
723103588504005538CE8F7CDE229335C85958AACD029DDF65CFC2A72A75055E63B8
FE59D07FB4BDF10DB7B8BA7D57C5C691EB96EAB97411615A6A430E51787031719CEB
C305E69200705CC828B9755F8FD53452B777A40CB6792554E5718BAB91EA3F03086A
4072A47047CEEC2493C384045FCC6ED8E4F748A5223AF12901EB2E19A6C288951C93
9B05880201039604840462444501676578616D706C65026F6162632E6578616D706C
652E636F6D016F616263406578616D706C652E636F6D2174736D7470757466384065
78616D706C652E636F6D06781C687474703A2F2F6D797572692E6578616D706C652E
636F6D2F61626308492B0601040181FD59080744111111110082492B0601040181FD
59014C040A22222222222222222222224622222222222221781C736D74702E757466
386D61696C626F78406578616D706C652E636F6D2082492B0601040181FD59025204
10333333333333333333333333333333332321088202015884012F8358F64621CA68
F7DB644AEFE37958041A686436AFCEDDE70ACEBA73744D70147784A41AD67673A1EE
5B3B9B1A8351553A29E87815ABFCCA5D1DE1099F284B4C01330B748F582D0AED3B5B
05B7E1629FE99B477382D1EF3D3C1767F429E384BB03238DAE6E17EA4647D0A86E6C
9DCAE8FCCA570DC11DD3B65D99A516DA1C02F0E5D6
~~~~~

Annotated hex:

- See the annotated hex for the C509 type 2 certificate in {{c509-selfsign-secp521r1}}. The only differences are the certificate type, the signature value, and the key identifiers.

### C509 Type 2 Certificate {#c509-selfsign-secp521r1}

[comment]: <> (replace-size:cert/selfsign-secp521r1/c509cert-t2.hex)
Plain hex (531 bytes):

[comment]: <> (replace-data:cert/selfsign-secp521r1/c509cert-t2.hex)
~~~~~
8B0242123402F61A6775D7001A69570A807273656C667369676E2D73656370353231
723103588504005538CE8F7CDE229335C85958AACD029DDF65CFC2A72A75055E63B8
FE59D07FB4BDF10DB7B8BA7D57C5C691EB96EAB97411615A6A430E51787031719CEB
C305E69200705CC828B9755F8FD53452B777A40CB6792554E5718BAB91EA3F03086A
4072A47047CEEC2493C384045FCC6ED8E4F748A5223AF12901EB2E19A6C288951C93
9B05880201039604840462444501676578616D706C65026F6162632E6578616D706C
652E636F6D016F616263406578616D706C652E636F6D2174736D7470757466384065
78616D706C652E636F6D06781C687474703A2F2F6D797572692E6578616D706C652E
636F6D2F61626308492B0601040181FD59080744111111110082492B0601040181FD
59014C040A22222222222222222222224622222222222221781C736D74702E757466
386D61696C626F78406578616D706C652E636F6D2082492B0601040181FD59025204
10333333333333333333333333333333332321088202015884012F8358F64621CA68
F7DB644AEFE37958041A686436AFCEDDE70ACEBA73744D70147784A41AD67673A1EE
5B3B9B1A8351553A29E87815ABFCCA5D1DE1099F284B4C013D3594A3444D9D9E9048
B5505F17E183FD49EC74C0372823602D057B6A9C86C8238C29135D4EC6C10ABCE9CE
22144AF8570C6D4ED42D4576F2CF6A38C975F5EA48
~~~~~

Annotated hex:

[comment]: <> (replace-data:cert/selfsign-secp521r1/c509cert-t2.diag)
~~~~~
  0: 8B             # C509Certificate=array[11]
  1:   02             # [0]. certificate type=2
  2:   42             # [1]. certificateSerialNumber=byte[2]
  3:     1234
  5:   02             # [2]. signature alg=ecdsa-with-sha512 (2)
  6:   F6             # [3]. issuer=<null>
  7:   1A 6775D700    # [4]. notBefore=1735776000:
                      #      2025-01-02T00:00:00Z
 12:   1A 69570A80    # [5]. notAfter=1767312000:
                      #      2026-01-02T00:00:00Z
 17:   72             # [6]. subject=char[18]
 18:     73656C667369676E2D73656370353231 # "selfsign-secp521"
 34:     7231                             # "r1"
 36:   03             # [7]. subjectPublicKeyAlg=EC public key on
                      #      curve secp521r1 (3)
 37:   58 85          # [8]. subject public key=EC point=byte[133]
 39:     04005538CE8F7CDE229335C85958AACD029DDF65CFC2A72A75055E63B8
 68:     FE59D07FB4BDF10DB7B8BA7D57C5C691EB96EAB97411615A6A430E5178
 97:     7031719CEBC305E69200705CC828B9755F8FD53452B777A40CB6792554
126:     E5718BAB91EA3F03086A4072A47047CEEC2493C384045FCC6ED8E4F748
155:     A5223AF12901EB2E19A6C288951C939B05
172:   88             # [9]. extensions=array[8]
                        #---extension[0]---
173:     02             # [0]. type=KeyUsage (2)
174:     01             # [1]. value=1: [digitalSignature]
                        #---extension[1]---
175:     03             # [2]. type=SubjectAlternativeName (3)
176:     96             # [3]. value=array[22]
                          #---GeneralName[0]---
177:       04             # [0]. GeneralNameType=4: directoryName
178:       84             # [1]. GeneralNameValue=array[4], 2
                          #      attributes
                            #---attribute[0]---
179:         04             # [0]. type=4: country
180:         62             # [1]. value=char[2]
181:           4445           # "DE"
                            #---attribute[1]---
183:         01             # [2]. type=1: commonName
184:         67             # [3]. value=char[7]
185:           6578616D706C65 # "example"
                          #---GeneralName[1]---
192:       02             # [2]. GeneralNameType=2: dNSName
193:       6F             # [3]. GeneralNameValue=char[15]
194:         6162632E6578616D706C652E636F6D # "abc.example.com"
                          #---GeneralName[2]---
209:       01             # [4]. GeneralNameType=1: rfc822Name
210:       6F             # [5]. GeneralNameValue=char[15]
211:         616263406578616D706C652E636F6D # "abc@example.com"
                          #---GeneralName[3]---
226:       21             # [6]. GeneralNameType=-2:
                          #      on-SmtpUTF8Mailbox
227:       74             # [7]. GeneralNameValue=char[20]
228:         736D747075746638406578616D706C65 # "smtputf8@example"
244:         2E636F6D                         # ".com"
                          #---GeneralName[4]---
248:       06             # [8]. GeneralNameType=6: uri
249:       78 1C          # [9]. GeneralNameValue=char[28]
251:         687474703A2F2F6D797572692E657861 # "http://myuri.exa"
267:         6D706C652E636F6D2F616263         # "mple.com/abc"
                          #---GeneralName[5]---
279:       08             # [10]. GeneralNameType=8: registeredID
280:       49             # [11]. GeneralNameValue=byte[9]: 
281:         2B0601040181FD5908 # oid: 1.3.6.1.4.1.32473.8
                          #---GeneralName[6]---
290:       07             # [12]. GeneralNameType=7: iPAddress
291:       44             # [13]. GeneralNameValue=byte[4]
292:         11111111
                          #---GeneralName[7]---
296:       00             # [14]. GeneralNameType=0: otherName
297:       82             # [15]. GeneralNameValue=array[2]
298:         49             # [0]. id=byte[9]: 
299:           2B0601040181FD5901 # oid: 1.3.6.1.4.1.32473.1
308:         4C             # [1]. value=byte[12]
309:           040A22222222222222222222
                          #---GeneralName[8]---
321:       22             # [16]. GeneralNameType=-3: on-MACAddress
322:       46             # [17]. GeneralNameValue=byte[6]
323:         222222222222
                          #---GeneralName[9]---
329:       21             # [18]. GeneralNameType=-2:
                          #       on-SmtpUTF8Mailbox
330:       78 1C          # [19]. GeneralNameValue=char[28]
332:         736D74702E757466386D61696C626F78 # "smtp.utf8mailbox"
348:         406578616D706C652E636F6D         # "@example.com"
                          #---GeneralName[10]---
360:       20             # [20]. GeneralNameType=-1:
                          #       on-hardwareModuleName
361:       82             # [21]. GeneralNameValue=array[2]
362:         49             # [0]. id=byte[9]: 
363:           2B0601040181FD5902 # oid: 1.3.6.1.4.1.32473.2
372:         52             # [1]. value=byte[18]
373:           041033333333333333333333333333333333
                        #---extension[2]---
391:     23             # [4]. type=BasicConstraints, critical (-4)
392:     21             # [5]. value=-2: CA: false
                        #---extension[3]---
393:     08             # [6]. type=ExtendedKeyUsage (8)
394:     82             # [7]. value=array[2]
395:       02             # clientAuth (2)
396:       01             # serverAuth (1)
397:   58 84          # [10]. signature value=byte[132]
399:     012F8358F64621CA68F7DB644AEFE37958041A686436AFCEDDE70ACEBA
428:     73744D70147784A41AD67673A1EE5B3B9B1A8351553A29E87815ABFCCA
457:     5D1DE1099F284B4C013D3594A3444D9D9E9048B5505F17E183FD49EC74
486:     C0372823602D057B6A9C86C8238C29135D4EC6C10ABCE9CE22144AF857
515:     0C6D4ED42D4576F2CF6A38C975F5EA48
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
-----BEGIN PRIVATE KEY-----
MEECAQAwEwYHKoZIzj0CAQYIKoEcz1UBgi0EJzAlAgEBBCDyTXt5dDKnqvBcKeAy
+qKXJ3oU+KjHtHfv+J0iFaHUHA==
-----END PRIVATE KEY-----
~~~~~

### X.509 Certificate {#x509-selfsign-sm2p256v1}

[comment]: <> (replace-size:cert/selfsign-sm2p256v1/x509cert.pem)
PEM content (644 bytes):

[comment]: <> (replace-data:cert/selfsign-sm2p256v1/x509cert.pem)
~~~~~
-----BEGIN CERTIFICATE-----
MIICgDCCAiagAwIBAgICEjQwCgYIKoEcz1UBg3UwgZUxGzAZBgNVBAMMEnNlbGZz
aWduLXNtMnAyNTZ2MTEYMBYGA1UEBQwPbXkgc2VyaWFsTnVtYmVyMRgwFgYDVQQK
DA9teSBvcmdhbml6YXRpb24xHjAcBgNVBAsMFW15IG9yZ2FuaXphdGlvbmFsVW5p
dDEiMCAGA1UEYQwZbXkgb3JnYW5pemF0aW9uSWRlbnRpZmllcjAeFw0yNTAxMDIw
MDAwMDBaFw0yNjAxMDIwMDAwMDBaMIGVMRswGQYDVQQDDBJzZWxmc2lnbi1zbTJw
MjU2djExGDAWBgNVBAUMD215IHNlcmlhbE51bWJlcjEYMBYGA1UECgwPbXkgb3Jn
YW5pemF0aW9uMR4wHAYDVQQLDBVteSBvcmdhbml6YXRpb25hbFVuaXQxIjAgBgNV
BGEMGW15IG9yZ2FuaXphdGlvbklkZW50aWZpZXIwWTATBgcqhkjOPQIBBggqgRzP
VQGCLQNCAASV//S+hhHIFJyBrewUEl2synRqLz/jjNLqtxHoyZ8QH7tEhCPxZvn/
2Y8OMhWXuzlINab7JAM3o4kSkHoiw/BKo2QwYjALBgNVHQ8EBAMCB4AwRQYDVR0e
BD4wPKE6MBuCGWV4Y2x1ZGVkLmRuczEuZXhhbXBsZS5jb20wG4IZZXhjbHVkZWQu
ZG5zMi5leGFtcGxlLmNvbTAMBgNVHSQEBTADgQECMAoGCCqBHM9VAYN1A0gAMEUC
IFqV641WbBysLPl+0sfFD+L8eG4Zb56Ry3DVPlO3VGiQAiEAjCVf7QY1cbj6AOqX
T+yb/N3maosc9h6BcL00XD/QZ7Y=
-----END CERTIFICATE-----
~~~~~

Textual Representation:

[comment]: <> (replace-data:cert/selfsign-sm2p256v1/x509cert.txt)
~~~~~
Certificate:
  Version: v3 (2)
  Serial Number:
    12:34
  Issuer: CN=selfsign-sm2p256v1,SERIALNUMBER=my serialNumber,O=my org
          anization,OU=my organizationalUnit,organizationIdentifier=m
          y organizationIdentifier
  Validity:
    Not Before: Thu Jan 02 01:00:00 CET 2025
    Not After : Fri Jan 02 01:00:00 CET 2026
  Subject: CN=selfsign-sm2p256v1,SERIALNUMBER=my serialNumber,O=my or
           ganization,OU=my organizationalUnit,organizationIdentifier
           =my organizationIdentifier
  Subject Public Key Info:
    Public Key Algorithm: EC/SM2
    Pub:
      04:95:ff:f4:be:86:11:c8:14:9c:81:ad:ec:14:12:5d:ac:ca:
      74:6a:2f:3f:e3:8c:d2:ea:b7:11:e8:c9:9f:10:1f:bb:44:84:
      23:f1:66:f9:ff:d9:8f:0e:32:15:97:bb:39:48:35:a6:fb:24:
      03:37:a3:89:12:90:7a:22:c3:f0:4a
  X509v3 extensions:
    X509v3 keyUsage:
      digitalSignature
    X509v3 nameConstraints:
      Excluded
        DNS: excluded.dns1.example.com
        DNS: excluded.dns2.example.com
    X509v3 policyConstraints:
      Require Explicit Policy:null, Inhibit Explicit Policy:2
  Signature Algorithm: SM3WITHSM2
  Signature Value:
    30:45:02:20:5a:95:eb:8d:56:6c:1c:ac:2c:f9:7e:d2:c7:c5:
    0f:e2:fc:78:6e:19:6f:9e:91:cb:70:d5:3e:53:b7:54:68:90:
    02:21:00:8c:25:5f:ed:06:35:71:b8:fa:00:ea:97:4f:ec:9b:
    fc:dd:e6:6a:8b:1c:f6:1e:81:70:bd:34:5c:3f:d0:67:b6
~~~~~

### C509 Type 3 Certificate

- C509 type 3 certificate converted from the X.509 certificate in {{x509-selfsign-sm2p256v1}}.

[comment]: <> (replace-size:cert/selfsign-sm2p256v1/c509cert-t3.hex)
Plain hex (326 bytes):

[comment]: <> (replace-data:cert/selfsign-sm2p256v1/c509cert-t3.hex)
~~~~~
8B0342123408F61A6775D7001A69570A808A017273656C667369676E2D736D327032
35367631036F6D792073657269616C4E756D626572086F6D79206F7267616E697A61
74696F6E09756D79206F7267616E697A6174696F6E616C556E69741278196D79206F
7267616E697A6174696F6E4964656E7469666965720658410495FFF4BE8611C8149C
81ADEC14125DACCA746A2F3FE38CD2EAB711E8C99F101FBB448423F166F9FFD98F0E
321597BB394835A6FB240337A38912907A22C3F04A860201181A82F6840278196578
636C756465642E646E73312E6578616D706C652E636F6D0278196578636C75646564
2E646E73322E6578616D706C652E636F6D181C82F60258405A95EB8D566C1CAC2CF9
7ED2C7C50FE2FC786E196F9E91CB70D53E53B75468908C255FED063571B8FA00EA97
4FEC9BFCDDE66A8B1CF61E8170BD345C3FD067B6
~~~~~

Annotated hex:

- See the annotated hex for the C509 type 2 certificate in {{c509-selfsign-sm2p256v1}}. The only differences are the certificate type, the signature value, and the key identifiers.

### C509 Type 2 Certificate {#c509-selfsign-sm2p256v1}

[comment]: <> (replace-size:cert/selfsign-sm2p256v1/c509cert-t2.hex)
Plain hex (326 bytes):

[comment]: <> (replace-data:cert/selfsign-sm2p256v1/c509cert-t2.hex)
~~~~~
8B0242123408F61A6775D7001A69570A808A017273656C667369676E2D736D327032
35367631036F6D792073657269616C4E756D626572086F6D79206F7267616E697A61
74696F6E09756D79206F7267616E697A6174696F6E616C556E69741278196D79206F
7267616E697A6174696F6E4964656E7469666965720658410495FFF4BE8611C8149C
81ADEC14125DACCA746A2F3FE38CD2EAB711E8C99F101FBB448423F166F9FFD98F0E
321597BB394835A6FB240337A38912907A22C3F04A860201181A82F6840278196578
636C756465642E646E73312E6578616D706C652E636F6D0278196578636C75646564
2E646E73322E6578616D706C652E636F6D181C82F602584059F40C77AE8AC0BD0638
E0B822001FD47EEF15667C7034436A95C97E94CEEE5FEFA1441C1F6537A76692605B
ED70A1168D2AFE6B03B4E9F925024D1B76729555
~~~~~

Annotated hex:

[comment]: <> (replace-data:cert/selfsign-sm2p256v1/c509cert-t2.diag)
~~~~~
  0: 8B             # C509Certificate=array[11]
  1:   02             # [0]. certificate type=2
  2:   42             # [1]. certificateSerialNumber=byte[2]
  3:     1234
  5:   08             # [2]. signature alg=sm2-with-sm3 (8)
  6:   F6             # [3]. issuer=<null>
  7:   1A 6775D700    # [4]. notBefore=1735776000:
                      #      2025-01-02T00:00:00Z
 12:   1A 69570A80    # [5]. notAfter=1767312000:
                      #      2026-01-02T00:00:00Z
 17:   8A             # [6]. subject=array[10], 5 attributes
                        #---attribute[0]---
 18:     01             # [0]. type=1: commonName
 19:     72             # [1]. value=char[18]
 20:       73656C667369676E2D736D3270323536 # "selfsign-sm2p256"
 36:       7631                             # "v1"
                        #---attribute[1]---
 38:     03             # [2]. type=3: serialNumber
 39:     6F             # [3]. value=char[15]
 40:       6D792073657269616C4E756D626572 # "my serialNumber"
                        #---attribute[2]---
 55:     08             # [4]. type=8: organization
 56:     6F             # [5]. value=char[15]
 57:       6D79206F7267616E697A6174696F6E # "my organization"
                        #---attribute[3]---
 72:     09             # [6]. type=9: organizationalUnit
 73:     75             # [7]. value=char[21]
 74:       6D79206F7267616E697A6174696F6E61 # "my organizationa"
 90:       6C556E6974                       # "lUnit"
                        #---attribute[4]---
 95:     12             # [8]. type=18: organizationIdentifier
 96:     78 19          # [9]. value=char[25]
 98:       6D79206F7267616E697A6174696F6E49 # "my organizationI"
114:       64656E746966696572               # "dentifier"
123:   06             # [7]. subjectPublicKeyAlg=EC public key on
                      #      curve sm2p256v1 (6)
124:   58 41          # [8]. subject public key=EC point=byte[65]
126:     0495FFF4BE8611C8149C81ADEC14125DACCA746A2F3FE38CD2EAB711E8
155:     C99F101FBB448423F166F9FFD98F0E321597BB394835A6FB240337A389
184:     12907A22C3F04A
191:   86             # [9]. extensions=array[6]
                        #---extension[0]---
192:     02             # [0]. type=KeyUsage (2)
193:     01             # [1]. value=1: [digitalSignature]
                        #---extension[1]---
194:     18 1A          # [2]. type=NameConstraints (26)
196:     82             # [3]. value=array[2]
197:       F6             # [0]. permittedSubtrees=<null>
198:       84             # [1]. excludedSubtrees=array[4]
                            #---GeneralName[0]---
199:         02             # [0]. GeneralNameType=2: dNSName
200:         78 19          # [1]. GeneralNameValue=char[25]
202:           6578636C756465642E646E73312E65 # "excluded.dns1.e"
217:           78616D706C652E636F6D           # "xample.com"
                            #---GeneralName[1]---
227:         02             # [2]. GeneralNameType=2: dNSName
228:         78 19          # [3]. GeneralNameValue=char[25]
230:           6578636C756465642E646E73322E65 # "excluded.dns2.e"
245:           78616D706C652E636F6D           # "xample.com"
                        #---extension[2]---
255:     18 1C          # [4]. type=PolicyConstraints (28)
257:     82             # [5]. value=array[2]
258:       F6             # [0]. requireExplicitPolicy=<null>
259:       02             # [1]. inhibitPolicyMapping=2
260:   58 40          # [10]. signature value=byte[64]
262:     59F40C77AE8AC0BD0638E0B822001FD47EEF15667C7034436A95C97E94
291:     CEEE5FEFA1441C1F6537A76692605BED70A1168D2AFE6B03B4E9F92502
320:     4D1B76729555
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
-----BEGIN PRIVATE KEY-----
MEICAQAwFAYHKoZIzj0CAQYJKyQDAwIIAQEHBCcwJQIBAQQgiOJE752KYXs1GZ0/
+ETWNoBn8L7ZFO9gjcd/xtlR/iE=
-----END PRIVATE KEY-----
~~~~~

### X.509 Certificate {#x509-selfsign-brainpoolp256r1}

[comment]: <> (replace-size:cert/selfsign-brainpoolp256r1/x509cert.pem)
PEM content (646 bytes):

[comment]: <> (replace-data:cert/selfsign-brainpoolp256r1/x509cert.pem)
~~~~~
-----BEGIN CERTIFICATE-----
MIICgjCCAimgAwIBAgICEjQwCgYIKwYBBQUHBiAwgYUxITAfBgNVBAMMGHNlbGZz
aWduLWJyYWlucG9vbHAyNTZyMTELMAkGA1UEBgwCREUxFDASBgNVBAcMC215IGxv
Y2FsaXR5MREwDwYDVQQIDAhteSBzdGF0ZTESMBAGA1UECQwJbXkgc3RyZWV0MRYw
FAYDVQQRDA1teSBwb3N0YWxDb2RlMB4XDTI1MDEwMjAwMDAwMFoXDTI2MDEwMjAw
MDAwMFowgYUxITAfBgNVBAMMGHNlbGZzaWduLWJyYWlucG9vbHAyNTZyMTELMAkG
A1UEBgwCREUxFDASBgNVBAcMC215IGxvY2FsaXR5MREwDwYDVQQIDAhteSBzdGF0
ZTESMBAGA1UECQwJbXkgc3RyZWV0MRYwFAYDVQQRDA1teSBwb3N0YWxDb2RlMFow
FAYHKoZIzj0CAQYJKyQDAwIIAQEHA0IABHewd0Eu6YlQd57Ygv/LFkjgFCcjVEaW
JPW84vFPMkKtSsxWhqhlCNWftyn9wpgRGI2L8BbOSlFRBU2viIxI1e2jgYUwgYIw
CwYDVR0PBAQDAgeAMB4GCCsGAQUFBwEHBBIwEDAGBAIAAQUAMAYEAgACBQAwHQYI
KwYBBQUHAQgEETAPoA0wCwIBAjAGAgEDAgEGMCAGCCsGAQUFBwEcBBQwEjAHBAMA
AQEFADAHBAMAAgEFADASBggrBgEFBQcBHQQGMASgAgUAMAoGCCsGAQUFBwYgA0cA
MEQCIHggH5kJkQjKOqxZN3kK/9+Bzvb10b5iKf4mgvfT08NGAiBii7Sl1hSG8Cbc
2q19KPBwN4m/jhwMIHGfXzJC/d7/bQ==
-----END CERTIFICATE-----
~~~~~

Textual Representation:

[comment]: <> (replace-data:cert/selfsign-brainpoolp256r1/x509cert.txt)
~~~~~
Certificate:
  Version: v3 (2)
  Serial Number:
    12:34
  Issuer: CN=selfsign-brainpoolp256r1,C=DE,L=my locality,ST=my state,
          STREET=my street,PostalCode=my postalCode
  Validity:
    Not Before: Thu Jan 02 01:00:00 CET 2025
    Not After : Fri Jan 02 01:00:00 CET 2026
  Subject: CN=selfsign-brainpoolp256r1,C=DE,L=my locality,ST=my state
           ,STREET=my street,PostalCode=my postalCode
  Subject Public Key Info:
    Public Key Algorithm: EC/BRAINPOOLP256R1
    Pub:
      04:77:b0:77:41:2e:e9:89:50:77:9e:d8:82:ff:cb:16:48:e0:
      14:27:23:54:46:96:24:f5:bc:e2:f1:4f:32:42:ad:4a:cc:56:
      86:a8:65:08:d5:9f:b7:29:fd:c2:98:11:18:8d:8b:f0:16:ce:
      4a:51:51:05:4d:af:88:8c:48:d5:ed
  X509v3 extensions:
    X509v3 keyUsage:
      digitalSignature
    X509v3 sbgp-ipAddrBlock:
      IPv4: inherit
      IPv6: inherit
    X509v3 sbgp-autonomousSysNum:
      Autonomous System Numbers:
        2
        3-6
    X509v3 sbgp-ipAddrBlockV2:
      IPv4 unicast: inherit
      IPv6 unicast: inherit
    X509v3 sbgp-autonomousSysNumV2:
      Autonomous System Numbers: inherit
  Signature Algorithm: SHAKE128WITHECDSA
  Signature Value:
    30:44:02:20:78:20:1f:99:09:91:08:ca:3a:ac:59:37:79:0a:
    ff:df:81:ce:f6:f5:d1:be:62:29:fe:26:82:f7:d3:d3:c3:46:
    02:20:62:8b:b4:a5:d6:14:86:f0:26:dc:da:ad:7d:28:f0:70:
    37:89:bf:8e:1c:0c:20:71:9f:5f:32:42:fd:de:ff:6d
~~~~~

### C509 Type 3 Certificate

- C509 type 3 certificate converted from the X.509 certificate in {{x509-selfsign-brainpoolp256r1}}.

[comment]: <> (replace-size:cert/selfsign-brainpoolp256r1/c509cert-t3.hex)
Plain hex (264 bytes):

[comment]: <> (replace-data:cert/selfsign-brainpoolp256r1/c509cert-t3.hex)
~~~~~
8B0342123403F61A6775D7001A69570A808C01781873656C667369676E2D62726169
6E706F6F6C70323536723104624445056B6D79206C6F63616C69747906686D792073
7461746507696D79207374726565740C6D6D7920706F7374616C436F646518185841
0477B077412EE98950779ED882FFCB1648E014272354469624F5BCE2F14F3242AD4A
CC5686A86508D59FB729FDC29811188D8BF016CE4A5151054DAF888C48D5ED8A0201
18208601F6F602F6F6182182028201031822860101F60201F61823F6584078201F99
099108CA3AAC5937790AFFDF81CEF6F5D1BE6229FE2682F7D3D3C346628BB4A5D614
86F026DCDAAD7D28F0703789BF8E1C0C20719F5F3242FDDEFF6D
~~~~~

Annotated hex:

- See the annotated hex for the C509 type 2 certificate in {{c509-selfsign-brainpoolp256r1}}. The only differences are the certificate type, the signature value, and the key identifiers.

### C509 Type 2 Certificate {#c509-selfsign-brainpoolp256r1}

[comment]: <> (replace-size:cert/selfsign-brainpoolp256r1/c509cert-t2.hex)
Plain hex (264 bytes):

[comment]: <> (replace-data:cert/selfsign-brainpoolp256r1/c509cert-t2.hex)
~~~~~
8B0242123403F61A6775D7001A69570A808C01781873656C667369676E2D62726169
6E706F6F6C70323536723104624445056B6D79206C6F63616C69747906686D792073
7461746507696D79207374726565740C6D6D7920706F7374616C436F646518185841
0477B077412EE98950779ED882FFCB1648E014272354469624F5BCE2F14F3242AD4A
CC5686A86508D59FB729FDC29811188D8BF016CE4A5151054DAF888C48D5ED8A0201
18208601F6F602F6F6182182028201031822860101F60201F61823F658404BF673AB
08781A572889717416F5B662A188B3218506F1780EB1D17C8B0207FF9F318BB7DBFD
17F6C829E93C0CDCB657E795436FA8FC4240A71F223DC0366059
~~~~~

Annotated hex:

[comment]: <> (replace-data:cert/selfsign-brainpoolp256r1/c509cert-t2.diag)
~~~~~
  0: 8B             # C509Certificate=array[11]
  1:   02             # [0]. certificate type=2
  2:   42             # [1]. certificateSerialNumber=byte[2]
  3:     1234
  5:   03             # [2]. signature alg=ecdsa-with-shake128 (3)
  6:   F6             # [3]. issuer=<null>
  7:   1A 6775D700    # [4]. notBefore=1735776000:
                      #      2025-01-02T00:00:00Z
 12:   1A 69570A80    # [5]. notAfter=1767312000:
                      #      2026-01-02T00:00:00Z
 17:   8C             # [6]. subject=array[12], 6 attributes
                        #---attribute[0]---
 18:     01             # [0]. type=1: commonName
 19:     78 18          # [1]. value=char[24]
 21:       73656C667369676E2D627261696E706F # "selfsign-brainpo"
 37:       6F6C703235367231                 # "olp256r1"
                        #---attribute[1]---
 45:     04             # [2]. type=4: country
 46:     62             # [3]. value=char[2]
 47:       4445            # "DE"
                        #---attribute[2]---
 49:     05             # [4]. type=5: locality
 50:     6B             # [5]. value=char[11]
 51:       6D79206C6F63616C697479 # "my locality"
                        #---attribute[3]---
 62:     06             # [6]. type=6: state
 63:     68             # [7]. value=char[8]
 64:       6D79207374617465 # "my state"
                        #---attribute[4]---
 72:     07             # [8]. type=7: street
 73:     69             # [9]. value=char[9]
 74:       6D7920737472656574 # "my street"
                        #---attribute[5]---
 83:     0C             # [10]. type=12: postalCode
 84:     6D             # [11]. value=char[13]
 85:       6D7920706F7374616C436F6465 # "my postalCode"
 98:   18 18          # [7]. subjectPublicKeyAlg=EC public key on
                      #      curve brainpoolp256r1 (24)
100:   58 41          # [8]. subject public key=EC point=byte[65]
102:     0477B077412EE98950779ED882FFCB1648E014272354469624F5BCE2F1
131:     4F3242AD4ACC5686A86508D59FB729FDC29811188D8BF016CE4A515105
160:     4DAF888C48D5ED
167:   8A             # [9]. extensions=array[10]
                        #---extension[0]---
168:     02             # [0]. type=KeyUsage (2)
169:     01             # [1]. value=1: [digitalSignature]
                        #---extension[1]---
170:     18 20          # [2]. type=IPAddrBlocks (32)
172:     86             # [3]. value=array[6]
                          #---IPAddrBlocks[0]---
173:       01             # [0]. AFI=1: IPv4
174:       F6             # [1]. SAFI=<null>
175:       F6             # [2]. IP Address Choice=<null>
                          #---IPAddrBlocks[1]---
176:       02             # [3]. AFI=2: IPv6
177:       F6             # [4]. SAFI=<null>
178:       F6             # [5]. IP Address Choice=<null>
                        #---extension[2]---
179:     18 21          # [4]. type=ASIdentifiers (33)
181:     82             # [5]. value=array[2]
182:       02             # [0]. id=2
183:       82             # [1]. range=array[2]
184:         01             # [0]. min=1
185:         03             # [1]. max=3
                        #---extension[3]---
186:     18 22          # [6]. type=IPAddrBlocksV2 (34)
188:     86             # [7]. value=array[6]
                          #---IPAddrBlocks[0]---
189:       01             # [0]. AFI=1: IPv4
190:       01             # [1]. SAFI=1: unicast
191:       F6             # [2]. IP Address Choice=<null>
                          #---IPAddrBlocks[1]---
192:       02             # [3]. AFI=2: IPv6
193:       01             # [4]. SAFI=1: unicast
194:       F6             # [5]. IP Address Choice=<null>
                        #---extension[4]---
195:     18 23          # [8]. type=ASIdentifiersV2 (35)
197:     F6             # [9]. value=<null>
198:   58 40          # [10]. signature value=byte[64]
200:     4BF673AB08781A572889717416F5B662A188B3218506F1780EB1D17C8B
229:     0207FF9F318BB7DBFD17F6C829E93C0CDCB657E795436FA8FC4240A71F
258:     223DC0366059
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
-----BEGIN PRIVATE KEY-----
MFICAQAwFAYHKoZIzj0CAQYJKyQDAwIIAQELBDcwNQIBAQQwgGfwvtkU72CNx3/G
2VH+IU85UqnWjleSVxt87bW/XLcURC4qRMOJB9G6KmsodzwN
-----END PRIVATE KEY-----
~~~~~

### X.509 Certificate {#x509-selfsign-brainpoolp384r1}

[comment]: <> (replace-size:cert/selfsign-brainpoolp384r1/x509cert.pem)
PEM content (717 bytes):

[comment]: <> (replace-data:cert/selfsign-brainpoolp384r1/x509cert.pem)
~~~~~
-----BEGIN CERTIFICATE-----
MIICyTCCAlCgAwIBAgICEjQwCgYIKoZIzj0EAwMwdDEhMB8GA1UEAwwYc2VsZnNp
Z24tYnJhaW5wb29scDM4NHIxMRMwEQYDVQQEDApteSBzdXJuYW1lMREwDwYDVQQM
DAhteSB0aXRsZTEVMBMGA1UEKgwMbXkgZ2l2ZW5OYW1lMRAwDgYDVQQpDAdteSBu
YW1lMB4XDTI1MDEwMjAwMDAwMFoXDTI2MDEwMjAwMDAwMFowdDEhMB8GA1UEAwwY
c2VsZnNpZ24tYnJhaW5wb29scDM4NHIxMRMwEQYDVQQEDApteSBzdXJuYW1lMREw
DwYDVQQMDAhteSB0aXRsZTEVMBMGA1UEKgwMbXkgZ2l2ZW5OYW1lMRAwDgYDVQQp
DAdteSBuYW1lMHowFAYHKoZIzj0CAQYJKyQDAwIIAQELA2IABGcJyZKRm0nEj9kx
0FxJfThl5ghMkd86TH54H0GFQ7Aj1Z6L8l0TP7GglOnULI+m7TtG6Yg6NavUsKnT
Cq79m36I7TgAVl0efwYzE01lGSktSb1V7DChZxl/7A90KYIrlaOBsDCBrTALBgNV
HQ8EBAMCB4AwSAYIKwYBBQUHAQcEPDA6MBkEAgABMBMDBADAAAIDBQTGM2QAAwQA
ywBxMB0EAgACMBcDBwAgAQ24EjQwDAMEAD//BgMEAD//DzBUBggrBgEFBQcBHARI
MEYwGgQDAAEBMBMDBADAAAIDBQTGM2QAAwQAywBxMCgEAwACATAhAwcAIAENuBI0
MBYDBQA//wADAw0AP/8BIgAAIjMzRFVmMAoGCCqGSM49BAMDA2cAMGQCMGcJyZKR
m0nEj9kx0FxJfThl5ghMkd86TH54H0GFQ7Aj1Z6L8l0TP7GglOnULI+m7QIwIO2f
21owmyyHBN2l8UTxe7MWuYwpEST7pc/sbvl/JogGmubFLis84iMSjdEMKqcw
-----END CERTIFICATE-----
~~~~~

Textual Representation:

[comment]: <> (replace-data:cert/selfsign-brainpoolp384r1/x509cert.txt)
~~~~~
Certificate:
  Version: v3 (2)
  Serial Number:
    12:34
  Issuer: CN=selfsign-brainpoolp384r1,SURNAME=my surname,T=my title,G
          IVENNAME=my givenName,Name=my name
  Validity:
    Not Before: Thu Jan 02 01:00:00 CET 2025
    Not After : Fri Jan 02 01:00:00 CET 2026
  Subject: CN=selfsign-brainpoolp384r1,SURNAME=my surname,T=my title,
           GIVENNAME=my givenName,Name=my name
  Subject Public Key Info:
    Public Key Algorithm: EC/BRAINPOOLP384R1
    Pub:
      04:67:09:c9:92:91:9b:49:c4:8f:d9:31:d0:5c:49:7d:38:65:
      e6:08:4c:91:df:3a:4c:7e:78:1f:41:85:43:b0:23:d5:9e:8b:
      f2:5d:13:3f:b1:a0:94:e9:d4:2c:8f:a6:ed:3b:46:e9:88:3a:
      35:ab:d4:b0:a9:d3:0a:ae:fd:9b:7e:88:ed:38:00:56:5d:1e:
      7f:06:33:13:4d:65:19:29:2d:49:bd:55:ec:30:a1:67:19:7f:
      ec:0f:74:29:82:2b:95
  X509v3 extensions:
    X509v3 keyUsage:
      digitalSignature
    X509v3 sbgp-ipAddrBlock:
      IPv4:
        192.0.2.0/24
        198.51.100.0/28
        203.0.113.0/24
      IPv6:
        2001:db8:1234::/48
        3fff:600:: - 3fff:fff:ffff:ffff:ffff:ffff:ffff:ffff
    X509v3 sbgp-ipAddrBlockV2:
      IPv4 unicast:
        192.0.2.0/24
        198.51.100.0/28
        203.0.113.0/24
      IPv6 unicast:
        2001:db8:1234::/48
        3fff:3:: - 3fff:122:0:2233:3344:5566:ffff:ffff
  Signature Algorithm: SHA384WITHECDSA
  Signature Value:
    30:64:02:30:67:09:c9:92:91:9b:49:c4:8f:d9:31:d0:5c:49:
    7d:38:65:e6:08:4c:91:df:3a:4c:7e:78:1f:41:85:43:b0:23:
    d5:9e:8b:f2:5d:13:3f:b1:a0:94:e9:d4:2c:8f:a6:ed:02:30:
    20:ed:9f:db:5a:30:9b:2c:87:04:dd:a5:f1:44:f1:7b:b3:16:
    b9:8c:29:11:24:fb:a5:cf:ec:6e:f9:7f:26:88:06:9a:e6:c5:
    2e:2b:3c:e2:23:12:8d:d1:0c:2a:a7:30
~~~~~

### C509 Type 3 Certificate

- C509 type 3 certificate converted from the X.509 certificate in {{x509-selfsign-brainpoolp384r1}}.

[comment]: <> (replace-size:cert/selfsign-brainpoolp384r1/c509cert-t3.hex)
Plain hex (406 bytes):

[comment]: <> (replace-data:cert/selfsign-brainpoolp384r1/c509cert-t3.hex)
~~~~~
8B0342123401F61A6775D7001A69570A808A01781873656C667369676E2D62726169
6E706F6F6C703338347231026A6D79207375726E616D650A686D79207469746C650D
6C6D7920676976656E4E616D651819676D79206E616D6518195861046709C992919B
49C48FD931D05C497D3865E6084C91DF3A4C7E781F418543B023D59E8BF25D133FB1
A094E9D42C8FA6ED3B46E9883A35ABD4B0A9D30AAEFD9B7E88ED3800565D1E7F0633
134D6519292D49BD55EC30A167197FEC0F7429822B9586020118208601F6831A01C0
00021B00000005C47363FE3B00000005C468638E02F6821B000120010DB81234823B
000120010C78132D091822860101831A01C000021B00000005C47363FE3B00000005
C468638E020182470020010DB812348245003FFF00034D003FFF0122000022333344
556658606709C992919B49C48FD931D05C497D3865E6084C91DF3A4C7E781F418543
B023D59E8BF25D133FB1A094E9D42C8FA6ED20ED9FDB5A309B2C8704DDA5F144F17B
B316B98C291124FBA5CFEC6EF97F2688069AE6C52E2B3CE223128DD10C2AA730
~~~~~

Annotated hex:

- See the annotated hex for the C509 type 2 certificate in {{c509-selfsign-brainpoolp384r1}}. The only differences are the certificate type, the signature value, and the key identifiers.

### C509 Type 2 Certificate {#c509-selfsign-brainpoolp384r1}

[comment]: <> (replace-size:cert/selfsign-brainpoolp384r1/c509cert-t2.hex)
Plain hex (406 bytes):

[comment]: <> (replace-data:cert/selfsign-brainpoolp384r1/c509cert-t2.hex)
~~~~~
8B0242123401F61A6775D7001A69570A808A01781873656C667369676E2D62726169
6E706F6F6C703338347231026A6D79207375726E616D650A686D79207469746C650D
6C6D7920676976656E4E616D651819676D79206E616D6518195861046709C992919B
49C48FD931D05C497D3865E6084C91DF3A4C7E781F418543B023D59E8BF25D133FB1
A094E9D42C8FA6ED3B46E9883A35ABD4B0A9D30AAEFD9B7E88ED3800565D1E7F0633
134D6519292D49BD55EC30A167197FEC0F7429822B9586020118208601F6831A01C0
00021B00000005C47363FE3B00000005C468638E02F6821B000120010DB81234823B
000120010C78132D091822860101831A01C000021B00000005C47363FE3B00000005
C468638E020182470020010DB812348245003FFF00034D003FFF0122000022333344
556658606709C992919B49C48FD931D05C497D3865E6084C91DF3A4C7E781F418543
B023D59E8BF25D133FB1A094E9D42C8FA6ED01853890E65008EADEEAB4914203C9AE
8EC9DA8908B30BFA61E9A1618C2047C3ECF3BAB63E2008D66C9DE3B4F3AE0193
~~~~~

Annotated hex:

[comment]: <> (replace-data:cert/selfsign-brainpoolp384r1/c509cert-t2.diag)
~~~~~
  0: 8B             # C509Certificate=array[11]
  1:   02             # [0]. certificate type=2
  2:   42             # [1]. certificateSerialNumber=byte[2]
  3:     1234
  5:   01             # [2]. signature alg=ecdsa-with-sha384 (1)
  6:   F6             # [3]. issuer=<null>
  7:   1A 6775D700    # [4]. notBefore=1735776000:
                      #      2025-01-02T00:00:00Z
 12:   1A 69570A80    # [5]. notAfter=1767312000:
                      #      2026-01-02T00:00:00Z
 17:   8A             # [6]. subject=array[10], 5 attributes
                        #---attribute[0]---
 18:     01             # [0]. type=1: commonName
 19:     78 18          # [1]. value=char[24]
 21:       73656C667369676E2D627261696E706F # "selfsign-brainpo"
 37:       6F6C703338347231                 # "olp384r1"
                        #---attribute[1]---
 45:     02             # [2]. type=2: surname
 46:     6A             # [3]. value=char[10]
 47:       6D79207375726E616D65 # "my surname"
                        #---attribute[2]---
 57:     0A             # [4]. type=10: title
 58:     68             # [5]. value=char[8]
 59:       6D79207469746C65 # "my title"
                        #---attribute[3]---
 67:     0D             # [6]. type=13: givenName
 68:     6C             # [7]. value=char[12]
 69:       6D7920676976656E4E616D65 # "my givenName"
                        #---attribute[4]---
 81:     18 19          # [8]. type=25: name
 83:     67             # [9]. value=char[7]
 84:       6D79206E616D65  # "my name"
 91:   18 19          # [7]. subjectPublicKeyAlg=EC public key on
                      #      curve brainpoolp384r1 (25)
 93:   58 61          # [8]. subject public key=EC point=byte[97]
 95:     046709C992919B49C48FD931D05C497D3865E6084C91DF3A4C7E781F41
124:     8543B023D59E8BF25D133FB1A094E9D42C8FA6ED3B46E9883A35ABD4B0
153:     A9D30AAEFD9B7E88ED3800565D1E7F0633134D6519292D49BD55EC30A1
182:     67197FEC0F7429822B95
192:   86             # [9]. extensions=array[6]
                        #---extension[0]---
193:     02             # [0]. type=KeyUsage (2)
194:     01             # [1]. value=1: [digitalSignature]
                        #---extension[1]---
195:     18 20          # [2]. type=IPAddrBlocks (32)
197:     86             # [3]. value=array[6]
                          #---IPAddrBlocks[0]---
198:       01             # [0]. AFI=1: IPv4
199:       F6             # [1]. SAFI=<null>
200:       83             # [2]. IntIPAddressChoice=array[3]
201:         1A 01C00002    # [0]. AddressPrefix=29360130
206:         1B 00000005C47363FE # [1]. AddressPrefix=24770733054
215:         3B 00000005C468638E # [2]. AddressPrefix=-24770012047
                          #---IPAddrBlocks[1]---
224:       02             # [3]. AFI=2: IPv6
225:       F6             # [4]. SAFI=<null>
226:       82             # [5]. IntIPAddressChoice=array[2]
227:         1B 000120010DB81234 # [0]. AddressPrefix=31666387393387
                                 #      6
236:         82             # [1]. AddressRange=array[2]
237:           3B 000120010C78132D # [0]. min=-316663852962606
246:           09             # [1]. max=9
                        #---extension[2]---
247:     18 22          # [4]. type=IPAddrBlocksV2 (34)
249:     86             # [5]. value=array[6]
                          #---IPAddrBlocks[0]---
250:       01             # [0]. AFI=1: IPv4
251:       01             # [1]. SAFI=1: unicast
252:       83             # [2]. IntIPAddressChoice=array[3]
253:         1A 01C00002    # [0]. AddressPrefix=29360130
258:         1B 00000005C47363FE # [1]. AddressPrefix=24770733054
267:         3B 00000005C468638E # [2]. AddressPrefix=-24770012047
                          #---IPAddrBlocks[1]---
276:       02             # [3]. AFI=2: IPv6
277:       01             # [4]. SAFI=1: unicast
278:       82             # [5]. IPAddressChoice=array[2]
279:         47             # [0]. AddressPrefix=byte[7]
280:           0020010DB81234
287:         82             # [1]. AddressRange=array[2]
288:           45             # [0]. min=byte[5]
289:             003FFF0003
294:           4D             # [1]. max=byte[13]
295:             003FFF01220000223333445566
308:   58 60          # [10]. signature value=byte[96]
310:     6709C992919B49C48FD931D05C497D3865E6084C91DF3A4C7E781F4185
339:     43B023D59E8BF25D133FB1A094E9D42C8FA6ED01853890E65008EADEEA
368:     B4914203C9AE8EC9DA8908B30BFA61E9A1618C2047C3ECF3BAB63E2008
397:     D66C9DE3B4F3AE0193
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
-----BEGIN PRIVATE KEY-----
MGICAQAwFAYHKoZIzj0CAQYJKyQDAwIIAQENBEcwRQIBAQRATzlSqdaOV5JXG3zt
tb9ctxRELipEw4kH0boqayh3PA02UW4leQ6yGuqx7iyWQKOzZYT5tnXlUqGh7jh/
+l3K8g==
-----END PRIVATE KEY-----
~~~~~

### X.509 Certificate {#x509-selfsign-brainpoolp512r1}

[comment]: <> (replace-size:cert/selfsign-brainpoolp512r1/x509cert.pem)
PEM content (809 bytes):

[comment]: <> (replace-data:cert/selfsign-brainpoolp512r1/x509cert.pem)
~~~~~
-----BEGIN CERTIFICATE-----
MIIDJTCCAomgAwIBAgICEjQwCgYIKwYBBQUHBiEwgYkxITAfBgNVBAMMGHNlbGZz
aWduLWJyYWlucG9vbHA1MTJyMTEoMCYGCysGAQQBgjc8AgEBDBdteSBqdXJpc2Rp
Y3Rpb25Mb2NhbGl0eTElMCMGCysGAQQBgjc8AgECDBRteSBqdXJpc2RpY3Rpb25T
dGF0ZTETMBEGCysGAQQBgjc8AgEDDAJTRTAeFw0yNTAxMDIwMDAwMDBaFw0yNjAx
MDIwMDAwMDBaMIGJMSEwHwYDVQQDDBhzZWxmc2lnbi1icmFpbnBvb2xwNTEycjEx
KDAmBgsrBgEEAYI3PAIBAQwXbXkganVyaXNkaWN0aW9uTG9jYWxpdHkxJTAjBgsr
BgEEAYI3PAIBAgwUbXkganVyaXNkaWN0aW9uU3RhdGUxEzARBgsrBgEEAYI3PAIB
AwwCU0UwgZswFAYHKoZIzj0CAQYJKyQDAwIIAQENA4GCAARtMnBn0zTOU/opMXqi
B7hcojdiPxmhDFlL8CT8P/62T6tYhNDUSKJxVS4C589E2L8QTaGCzsHeiVyEGLhS
nZuLLEuApzbdxWRx1qUsbOQU5p1XNWsV/Aika2H6W3Ifq8dpMvODba3k9w9vDKy4
0zUafrVO/wd8QCNoxJu3FbU0WKOBmzCBmDALBgNVHQ8EBAMCB4AwGgYDVR0JBBMw
ETAPBgNVBAYxCAwCREUMAlNFMDMGA1UdIQQsMCowEAYGZ4EMAQICBgZngQwBAgEw
FgYJKwYBBAGB/VkGBgkrBgEEAYH9WQcwOAYIKwYBBQUHAQsELDAqMCgGCCsGAQUF
BzAChhxodHRwOi8vY2Fpc3N1ZXJzLmV4YW1wbGUuY29tMAoGCCsGAQUFBwYhA4GJ
ADCBhQJBAKjwK7PveSjw+rp+JZFbF+xJviSYgDW/nZyWlMzp5exXNGrRInHSDwy0
CKQVXpuac9bZv627p4Z+DOUxx6/4Cv4CQBt+5b77HaFS0XasMdcu+WbiwPD/tNsZ
Fx6YPBtAcnGbXbrq1LaRuOGjIKX0nvkiqhhVEaPv25rABP8o5V+l3Ls=
-----END CERTIFICATE-----
~~~~~

Textual Representation:

[comment]: <> (replace-data:cert/selfsign-brainpoolp512r1/x509cert.txt)
~~~~~
Certificate:
  Version: v3 (2)
  Serial Number:
    12:34
  Issuer: CN=selfsign-brainpoolp512r1,jurisdictionLocality=my jurisdi
          ctionLocality,jurisdictionState=my jurisdictionState,jurisd
          ictionCountry=SE
  Validity:
    Not Before: Thu Jan 02 01:00:00 CET 2025
    Not After : Fri Jan 02 01:00:00 CET 2026
  Subject: CN=selfsign-brainpoolp512r1,jurisdictionLocality=my jurisd
           ictionLocality,jurisdictionState=my jurisdictionState,juri
           sdictionCountry=SE
  Subject Public Key Info:
    Public Key Algorithm: EC/BRAINPOOLP512R1
    Pub:
      04:6d:32:70:67:d3:34:ce:53:fa:29:31:7a:a2:07:b8:5c:a2:
      37:62:3f:19:a1:0c:59:4b:f0:24:fc:3f:fe:b6:4f:ab:58:84:
      d0:d4:48:a2:71:55:2e:02:e7:cf:44:d8:bf:10:4d:a1:82:ce:
      c1:de:89:5c:84:18:b8:52:9d:9b:8b:2c:4b:80:a7:36:dd:c5:
      64:71:d6:a5:2c:6c:e4:14:e6:9d:57:35:6b:15:fc:08:a4:6b:
      61:fa:5b:72:1f:ab:c7:69:32:f3:83:6d:ad:e4:f7:0f:6f:0c:
      ac:b8:d3:35:1a:7e:b5:4e:ff:07:7c:40:23:68:c4:9b:b7:15:
      b5:34:58
  X509v3 extensions:
    X509v3 keyUsage:
      digitalSignature
    X509v3 subjectDirectoryAttributes:
      at-country
        DE
        SE
    X509v3 policyMappings:
      2.23.140.1.2.2 : 2.23.140.1.2.1
      1.3.6.1.4.1.32473.6 : 1.3.6.1.4.1.32473.7
    X509v3 subjectInfoAccess:
      CA Issuers: URI: http://caissuers.example.com
  Signature Algorithm: SHAKE256WITHECDSA
  Signature Value:
    30:81:85:02:41:00:a8:f0:2b:b3:ef:79:28:f0:fa:ba:7e:25:
    91:5b:17:ec:49:be:24:98:80:35:bf:9d:9c:96:94:cc:e9:e5:
    ec:57:34:6a:d1:22:71:d2:0f:0c:b4:08:a4:15:5e:9b:9a:73:
    d6:d9:bf:ad:bb:a7:86:7e:0c:e5:31:c7:af:f8:0a:fe:02:40:
    1b:7e:e5:be:fb:1d:a1:52:d1:76:ac:31:d7:2e:f9:66:e2:c0:
    f0:ff:b4:db:19:17:1e:98:3c:1b:40:72:71:9b:5d:ba:ea:d4:
    b6:91:b8:e1:a3:20:a5:f4:9e:f9:22:aa:18:55:11:a3:ef:db:
    9a:c0:04:ff:28:e5:5f:a5:dc:bb
~~~~~

### C509 Type 3 Certificate

- C509 type 3 certificate converted from the X.509 certificate in {{x509-selfsign-brainpoolp512r1}}.

[comment]: <> (replace-size:cert/selfsign-brainpoolp512r1/c509cert-t3.hex)
Plain hex (432 bytes):

[comment]: <> (replace-data:cert/selfsign-brainpoolp512r1/c509cert-t3.hex)
~~~~~
8B0342123404F61A6775D7001A69570A808801781873656C667369676E2D62726169
6E706F6F6C70353132723113776D79206A7572697364696374696F6E4C6F63616C69
747914746D79206A7572697364696374696F6E537461746515625345181A5881046D
327067D334CE53FA29317AA207B85CA237623F19A10C594BF024FC3FFEB64FAB5884
D0D448A271552E02E7CF44D8BF104DA182CEC1DE895C8418B8529D9B8B2C4B80A736
DDC56471D6A52C6CE414E69D57356B15FC08A46B61FA5B721FABC76932F3836DADE4
F70F6F0CACB8D3351A7EB54EFF077C402368C49BB715B53458880201181882048262
4445625345181B840201492B0601040181FD5906492B0601040181FD5907181F8202
781C687474703A2F2F6361697373756572732E6578616D706C652E636F6D5880A8F0
2BB3EF7928F0FABA7E25915B17EC49BE24988035BF9D9C9694CCE9E5EC57346AD122
71D20F0CB408A4155E9B9A73D6D9BFADBBA7867E0CE531C7AFF80AFE1B7EE5BEFB1D
A152D176AC31D72EF966E2C0F0FFB4DB19171E983C1B4072719B5DBAEAD4B691B8E1
A320A5F49EF922AA185511A3EFDB9AC004FF28E55FA5DCBB
~~~~~

Annotated hex:

- See the annotated hex for the C509 type 2 certificate in {{c509-selfsign-brainpoolp512r1}}. The only differences are the certificate type, the signature value, and the key identifiers.

### C509 Type 2 Certificate {#c509-selfsign-brainpoolp512r1}

[comment]: <> (replace-size:cert/selfsign-brainpoolp512r1/c509cert-t2.hex)
Plain hex (432 bytes):

[comment]: <> (replace-data:cert/selfsign-brainpoolp512r1/c509cert-t2.hex)
~~~~~
8B0242123404F61A6775D7001A69570A808801781873656C667369676E2D62726169
6E706F6F6C70353132723113776D79206A7572697364696374696F6E4C6F63616C69
747914746D79206A7572697364696374696F6E537461746515625345181A5881046D
327067D334CE53FA29317AA207B85CA237623F19A10C594BF024FC3FFEB64FAB5884
D0D448A271552E02E7CF44D8BF104DA182CEC1DE895C8418B8529D9B8B2C4B80A736
DDC56471D6A52C6CE414E69D57356B15FC08A46B61FA5B721FABC76932F3836DADE4
F70F6F0CACB8D3351A7EB54EFF077C402368C49BB715B53458880201181882048262
4445625345181B840201492B0601040181FD5906492B0601040181FD5907181F8202
781C687474703A2F2F6361697373756572732E6578616D706C652E636F6D5880A0B9
ED538672D0B80E48F3D7C4E902503BAC0BDC88B45DAC784DDCFA551AF188B6E2E51F
6E695D7CFC91396BEB17FD91CF9C82D1FB819FEA09C9C4AC9BDCEF1B9AE37BD98556
C3917E9D2FA7327C4FDE6A6CFC99320B3CA097766A9C1A41227A5227CE4F29079B59
D533DEAFDE51B599B052C91178BE8FA29F86F0FADBC412F9
~~~~~

Annotated hex:

[comment]: <> (replace-data:cert/selfsign-brainpoolp512r1/c509cert-t2.diag)
~~~~~
  0: 8B             # C509Certificate=array[11]
  1:   02             # [0]. certificate type=2
  2:   42             # [1]. certificateSerialNumber=byte[2]
  3:     1234
  5:   04             # [2]. signature alg=ecdsa-with-shake256 (4)
  6:   F6             # [3]. issuer=<null>
  7:   1A 6775D700    # [4]. notBefore=1735776000:
                      #      2025-01-02T00:00:00Z
 12:   1A 69570A80    # [5]. notAfter=1767312000:
                      #      2026-01-02T00:00:00Z
 17:   88             # [6]. subject=array[8], 4 attributes
                        #---attribute[0]---
 18:     01             # [0]. type=1: commonName
 19:     78 18          # [1]. value=char[24]
 21:       73656C667369676E2D627261696E706F # "selfsign-brainpo"
 37:       6F6C703531327231                 # "olp512r1"
                        #---attribute[1]---
 45:     13             # [2]. type=19: jurisdictionLocalityName
 46:     77             # [3]. value=char[23]
 47:       6D79206A7572697364696374696F6E4C # "my jurisdictionL"
 63:       6F63616C697479                   # "ocality"
                        #---attribute[2]---
 70:     14             # [4]. type=20:
                        #      jurisdictionStateOrProvinceName
 71:     74             # [5]. value=char[20]
 72:       6D79206A7572697364696374696F6E53 # "my jurisdictionS"
 88:       74617465                         # "tate"
                        #---attribute[3]---
 92:     15             # [6]. type=21: jurisdictionCountryName
 93:     62             # [7]. value=char[2]
 94:       5345            # "SE"
 96:   18 1A          # [7]. subjectPublicKeyAlg=EC public key on
                      #      curve brainpoolp512r1 (26)
 98:   58 81          # [8]. subject public key=EC point=byte[129]
100:     046D327067D334CE53FA29317AA207B85CA237623F19A10C594BF024FC
129:     3FFEB64FAB5884D0D448A271552E02E7CF44D8BF104DA182CEC1DE895C
158:     8418B8529D9B8B2C4B80A736DDC56471D6A52C6CE414E69D57356B15FC
187:     08A46B61FA5B721FABC76932F3836DADE4F70F6F0CACB8D3351A7EB54E
216:     FF077C402368C49BB715B53458
229:   88             # [9]. extensions=array[8]
                        #---extension[0]---
230:     02             # [0]. type=KeyUsage (2)
231:     01             # [1]. value=1: [digitalSignature]
                        #---extension[1]---
232:     18 18          # [2]. type=SubjectDirectoryAttributes (24)
234:     82             # [3]. value=array[2], 1 attribute
                          #---attribute[0]---
235:       04             # [0]. attributeType=country (4)
236:       82             # [1]. attributeValue=array[2]
237:         62             # [0]=char[2]
238:           4445           # "DE"
240:         62             # [1]=char[2]
241:           5345           # "SE"
                        #---extension[2]---
243:     18 1B          # [4]. type=PolicyMappings (27)
245:     84             # [5]. value=array[4]
                          #---policyMapping[0]---
246:       02             # [0]. issuerDomainPolicy=organization-val
                          #      idated (2)
247:       01             # [1]. subjectDomainPolicy=domain-validate
                          #      d (1)
                          #---policyMapping[1]---
248:       49             # [2].  issuerDomainPolicy=byte[9]: 
249:         2B0601040181FD5906 # oid: 1.3.6.1.4.1.32473.6
258:       49             # [3]. subjectDomainPolicy=byte[9]: 
259:         2B0601040181FD5907 # oid: 1.3.6.1.4.1.32473.7
                        #---extension[3]---
268:     18 1F          # [6]. type=SubjectInfoAccess (31)
270:     82             # [7]. value=array[2]
                          #---AccessDescription[0]---
271:       02             # [0]. accessMethod=caIssuers (2)
272:       78 1C          # [1]. uri=char[28]
274:         687474703A2F2F636169737375657273 # "http://caissuers"
290:         2E6578616D706C652E636F6D         # ".example.com"
302:   58 80          # [10]. signature value=byte[128]
304:     A0B9ED538672D0B80E48F3D7C4E902503BAC0BDC88B45DAC784DDCFA55
333:     1AF188B6E2E51F6E695D7CFC91396BEB17FD91CF9C82D1FB819FEA09C9
362:     C4AC9BDCEF1B9AE37BD98556C3917E9D2FA7327C4FDE6A6CFC99320B3C
391:     A097766A9C1A41227A5227CE4F29079B59D533DEAFDE51B599B052C911
420:     78BE8FA29F86F0FADBC412F9
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
-----BEGIN PRIVATE KEY-----
MEMCAQAwFQYHKoZIzj0CAQYKKoF6AYFfZYIAAQQnMCUCAQEEIIjiRO+dimF7NRmd
P/hE1jaAZ/C+2RTvYI3Hf8bZUf4h
-----END PRIVATE KEY-----
~~~~~

### X.509 Certificate {#x509-selfsign-frp256v1}

[comment]: <> (replace-size:cert/selfsign-frp256v1/x509cert.pem)
PEM content (584 bytes):

[comment]: <> (replace-data:cert/selfsign-frp256v1/x509cert.pem)
~~~~~
-----BEGIN CERTIFICATE-----
MIICRDCCAeugAwIBAgICEjQwCQYHKoZIzj0EATB3MR4wHAYJKoZIhvcNAQkBFg9h
YmNAZXhhbXBsZS5vcmcxGjAYBgNVBAMMEXNlbGZzaWduLWZycDI1NnYxMRwwGgYD
VQQPDBNteSBidXNpbmVzc0NhdGVnb3J5MRswGQYDVQQUDBJteSB0ZWxlcGhvbmVO
dW1iZXIwHhcNMjUwMTAyMDAwMDAwWhcNMjYwMTAyMDAwMDAwWjB3MR4wHAYJKoZI
hvcNAQkBFg9hYmNAZXhhbXBsZS5vcmcxGjAYBgNVBAMMEXNlbGZzaWduLWZycDI1
NnYxMRwwGgYDVQQPDBNteSBidXNpbmVzc0NhdGVnb3J5MRswGQYDVQQUDBJteSB0
ZWxlcGhvbmVOdW1iZXIwWzAVBgcqhkjOPQIBBgoqgXoBgV9lggABA0IABDeNLSih
9lRxJPLbakL2ORW/ovZTeuIM8EF9Z1++ZgPagKTPPx5DYzNDqzvoAVDsBOlkndYn
BbwFW9zaeR6yUGKjZjBkMAsGA1UdDwQEAwIHgDBHBgNVHR4EQDA+oDwwHIIacGVy
bWl0dGVkLmRuczEuZXhhbXBsZS5jb20wHIIacGVybWl0dGVkLmRuczIuZXhhbXBs
ZS5jb20wDAYDVR0kBAUwA4ABATAJBgcqhkjOPQQBA0gAMEUCIDeNLSih9lRxJPLb
akL2ORW/ovZTeuIM8EF9Z1++ZgPaAiEA05r5YOT77AWy1Iik7Z8Bj0ZgxVC8dqcW
8WcF/AjYW9s=
-----END CERTIFICATE-----
~~~~~

Textual Representation:

[comment]: <> (replace-data:cert/selfsign-frp256v1/x509cert.txt)
~~~~~
Certificate:
  Version: v3 (2)
  Serial Number:
    12:34
  Issuer: E=abc@example.org,CN=selfsign-frp256v1,BusinessCategory=my 
          businessCategory,TelephoneNumber=my telephoneNumber
  Validity:
    Not Before: Thu Jan 02 01:00:00 CET 2025
    Not After : Fri Jan 02 01:00:00 CET 2026
  Subject: E=abc@example.org,CN=selfsign-frp256v1,BusinessCategory=my
            businessCategory,TelephoneNumber=my telephoneNumber
  Subject Public Key Info:
    Public Key Algorithm: EC/FRP256V1
    Pub:
      04:37:8d:2d:28:a1:f6:54:71:24:f2:db:6a:42:f6:39:15:bf:
      a2:f6:53:7a:e2:0c:f0:41:7d:67:5f:be:66:03:da:80:a4:cf:
      3f:1e:43:63:33:43:ab:3b:e8:01:50:ec:04:e9:64:9d:d6:27:
      05:bc:05:5b:dc:da:79:1e:b2:50:62
  X509v3 extensions:
    X509v3 keyUsage:
      digitalSignature
    X509v3 nameConstraints:
      Permitted
        DNS: permitted.dns1.example.com
        DNS: permitted.dns2.example.com
    X509v3 policyConstraints:
      Require Explicit Policy:1, Inhibit Explicit Policy:null
  Signature Algorithm: SHA1WITHECDSA
  Signature Value:
    30:45:02:20:37:8d:2d:28:a1:f6:54:71:24:f2:db:6a:42:f6:
    39:15:bf:a2:f6:53:7a:e2:0c:f0:41:7d:67:5f:be:66:03:da:
    02:21:00:d3:9a:f9:60:e4:fb:ec:05:b2:d4:88:a4:ed:9f:01:
    8f:46:60:c5:50:bc:76:a7:16:f1:67:05:fc:08:d8:5b:db
~~~~~
### C509 Type 3 Certificate

- C509 type 3 certificate converted from the X.509 certificate in {{x509-selfsign-frp256v1}}.

[comment]: <> (replace-size:cert/selfsign-frp256v1/c509cert-t3.hex)
Plain hex (303 bytes):

[comment]: <> (replace-data:cert/selfsign-frp256v1/c509cert-t3.hex)
~~~~~
8B0342123438FEF61A6775D7001A69570A8088006F616263406578616D706C652E6F
7267017173656C667369676E2D66727032353676310B736D7920627573696E657373
43617465676F7279181A726D792074656C6570686F6E654E756D626572181B584104
378D2D28A1F6547124F2DB6A42F63915BFA2F6537AE20CF0417D675FBE6603DA80A4
CF3F1E43633343AB3BE80150EC04E9649DD62705BC055BDCDA791EB2506286020118
1A828402781A7065726D69747465642E646E73312E6578616D706C652E636F6D0278
1A7065726D69747465642E646E73322E6578616D706C652E636F6DF6181C8201F658
40378D2D28A1F6547124F2DB6A42F63915BFA2F6537AE20CF0417D675FBE6603DAD3
9AF960E4FBEC05B2D488A4ED9F018F4660C550BC76A716F16705FC08D85BDB
~~~~~

Annotated hex:

- See the annotated hex for the C509 type 2 certificate in {{c509-selfsign-frp256v1}}. The only differences are the certificate type, the signature value, and the key identifiers.

### C509 Type 2 Certificate {#c509-selfsign-frp256v1}

[comment]: <> (replace-size:cert/selfsign-frp256v1/c509cert-t2.hex)
Plain hex (303 bytes):

[comment]: <> (replace-data:cert/selfsign-frp256v1/c509cert-t2.hex)
~~~~~
8B0242123438FEF61A6775D7001A69570A8088006F616263406578616D706C652E6F
7267017173656C667369676E2D66727032353676310B736D7920627573696E657373
43617465676F7279181A726D792074656C6570686F6E654E756D626572181B584104
378D2D28A1F6547124F2DB6A42F63915BFA2F6537AE20CF0417D675FBE6603DA80A4
CF3F1E43633343AB3BE80150EC04E9649DD62705BC055BDCDA791EB2506286020118
1A828402781A7065726D69747465642E646E73312E6578616D706C652E636F6D0278
1A7065726D69747465642E646E73322E6578616D706C652E636F6DF6181C8201F658
40378D2D28A1F6547124F2DB6A42F63915BFA2F6537AE20CF0417D675FBE6603DA6F
2D4CE3787CBACE549599BC5F3BAACCA2B7E67352E4A6BA1F4496CEAD53D8E6
~~~~~

Annotated hex:

[comment]: <> (replace-data:cert/selfsign-frp256v1/c509cert-t2.diag)
~~~~~
  0: 8B             # C509Certificate=array[11]
  1:   02             # [0]. certificate type=2
  2:   42             # [1]. certificateSerialNumber=byte[2]
  3:     1234
  5:   38 FE          # [2]. signature alg=ecdsa-with-sha1 (-255)
  7:   F6             # [3]. issuer=<null>
  8:   1A 6775D700    # [4]. notBefore=1735776000:
                      #      2025-01-02T00:00:00Z
 13:   1A 69570A80    # [5]. notAfter=1767312000:
                      #      2026-01-02T00:00:00Z
 18:   88             # [6]. subject=array[8], 4 attributes
                        #---attribute[0]---
 19:     00             # [0]. type=0: emailAddress
 20:     6F             # [1]. value=char[15]
 21:       616263406578616D706C652E6F7267 # "abc@example.org"
                        #---attribute[1]---
 36:     01             # [2]. type=1: commonName
 37:     71             # [3]. value=char[17]
 38:       73656C667369676E2D66727032353676 # "selfsign-frp256v"
 54:       31                               # "1"
                        #---attribute[2]---
 55:     0B             # [4]. type=11: businessCategory
 56:     73             # [5]. value=char[19]
 57:       6D7920627573696E6573734361746567 # "my businessCateg"
 73:       6F7279                           # "ory"
                        #---attribute[3]---
 76:     18 1A          # [6]. type=26: telephoneNumber
 78:     72             # [7]. value=char[18]
 79:       6D792074656C6570686F6E654E756D62 # "my telephoneNumb"
 95:       6572                             # "er"
 97:   18 1B          # [7]. subjectPublicKeyAlg=EC public key on
                      #      curve frp256v1 (27)
 99:   58 41          # [8]. subject public key=EC point=byte[65]
101:     04378D2D28A1F6547124F2DB6A42F63915BFA2F6537AE20CF0417D675F
130:     BE6603DA80A4CF3F1E43633343AB3BE80150EC04E9649DD62705BC055B
159:     DCDA791EB25062
166:   86             # [9]. extensions=array[6]
                        #---extension[0]---
167:     02             # [0]. type=KeyUsage (2)
168:     01             # [1]. value=1: [digitalSignature]
                        #---extension[1]---
169:     18 1A          # [2]. type=NameConstraints (26)
171:     82             # [3]. value=array[2]
172:       84             # [0]. permittedSubtrees=array[4]
                            #---GeneralName[0]---
173:         02             # [0]. GeneralNameType=2: dNSName
174:         78 1A          # [1]. GeneralNameValue=char[26]
176:           7065726D69747465642E646E73312E # "permitted.dns1."
191:           6578616D706C652E636F6D         # "example.com"
                            #---GeneralName[1]---
202:         02             # [2]. GeneralNameType=2: dNSName
203:         78 1A          # [3]. GeneralNameValue=char[26]
205:           7065726D69747465642E646E73322E # "permitted.dns2."
220:           6578616D706C652E636F6D         # "example.com"
231:       F6             # [1]. excludedSubtrees=<null>
                        #---extension[2]---
232:     18 1C          # [4]. type=PolicyConstraints (28)
234:     82             # [5]. value=array[2]
235:       01             # [0]. requireExplicitPolicy=1
236:       F6             # [1]. inhibitPolicyMapping=<null>
237:   58 40          # [10]. signature value=byte[64]
239:     378D2D28A1F6547124F2DB6A42F63915BFA2F6537AE20CF0417D675FBE
268:     6603DA6F2D4CE3787CBACE549599BC5F3BAACCA2B7E67352E4A6BA1F44
297:     96CEAD53D8E6
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
-----BEGIN PRIVATE KEY-----
MC4CAQAwBQYDK2VuBCIEIPJNe3l0Mqeq8Fwp4DL6opcnehT4qMe0d+/4nSIVodQc
-----END PRIVATE KEY-----
~~~~~

### X.509 Certificate {#x509-ee-x25519}

- Issued by the CA in {{x509-ca}}.

[comment]: <> (replace-size:cert/ee-x25519/x509cert.pem)
PEM content (681 bytes):

[comment]: <> (replace-data:cert/ee-x25519/x509cert.pem)
~~~~~
-----BEGIN CERTIFICATE-----
MIICpTCCAlegAwIBAgICEjQwBQYDK2VwMBIxEDAOBgNVBAMMB3Rlc3QgY2EwHhcN
MjUwMTAyMDAwMDAwWhcNMjYwMTAyMDAwMDAwWjAAMCowBQYDK2VuAyEAiv9Rb6xx
JEFQ5w+Sd/St9/sp9Bp6SogovUdnIvwbfwijggHhMIIB3TALBgNVHQ8EBAMCAygw
HwYDVR0jBBgwFoAUf824LQSVLho2uQrzejzxZtFe+SEwggGTBggrBgEFBQcBAQSC
AYUwggGBMCMGCCsGAQUFBzABhhdodHRwOi8vb2NzcC5leGFtcGxlLmNvbTAoBggr
BgEFBQcwAoYcaHR0cDovL2NhaXNzdWVycy5leGFtcGxlLmNvbTArBggrBgEFBQcw
BYYfaHR0cDovL2NhcmVwb3NpdG9yeS5leGFtcGxlLmNvbTArBggrBgEFBQcwA4Yf
aHR0cDovL3RpbWVzdGFtcGluZy5leGFtcGxlLmNvbTArBggrBgEFBQcwBYYfaHR0
cDovL2NhcmVwb3NpdG9yeS5leGFtcGxlLmNvbTArBggrBgEFBQcwCoYfaHR0cDov
L3Jwa2ltYW5pZmVzdC5leGFtcGxlLmNvbTArBggrBgEFBQcwC4YfaHR0cDovL3Np
Z25lZG9iamVjdC5leGFtcGxlLmNvbTApBggrBgEFBQcwDYYdaHR0cDovL3Jwa2lu
b3RpZnkuZXhhbXBsZS5jb20wJAYJKwYBBAGB/VkDhhdodHRwOi8vMTIzNC5leGFt
cGxlLmNvbTAWBgNVHRIEDzANggtleGFtcGxlLmNvbTAFBgMrZXADQQB+UNyW/sP4
qzz/BhSU9BmsKGdsNV9GkHgu2lYIeY3ubFSF91YUjmr4kGSCbXnS/N0rPDIG3Ezh
X5XygKMaD4sJ
-----END CERTIFICATE-----
~~~~~

Textual Representation:

[comment]: <> (replace-data:cert/ee-x25519/x509cert.txt)
~~~~~
Certificate:
  Version: v3 (2)
  Serial Number:
    12:34
  Issuer: CN=test ca
  Validity:
    Not Before: Thu Jan 02 01:00:00 CET 2025
    Not After : Fri Jan 02 01:00:00 CET 2026
  Subject: <empty>
  Subject Public Key Info:
    Public Key Algorithm: X25519
    Pub:
      8a:ff:51:6f:ac:71:24:41:50:e7:0f:92:77:f4:ad:f7:fb:29:
      f4:1a:7a:4a:88:28:bd:47:67:22:fc:1b:7f:08
  X509v3 extensions:
    X509v3 keyUsage:
      keyEncipherment, keyEncipherment
    X509v3 authorityKeyIdentifier:
      7f:cd:b8:2d:04:95:2e:1a:36:b9:0a:f3:7a:3c:f1:66:d1:5e:f9:21
    X509v3 authorityInfoAccess:
      OCSP: URI: http://ocsp.example.com
      CA Issuers: URI: http://caissuers.example.com
      ad-caRepository: URI: http://carepository.example.com
      ad-timeStamping: URI: http://timestamping.example.com
      ad-caRepository: URI: http://carepository.example.com
      RPKI Manifest: URI: http://rpkimanifest.example.com
      Signed Object: URI: http://signedobject.example.com
      RPKI Notify: URI: http://rpkinotify.example.com
      1.3.6.1.4.1.32473.3: URI: http://1234.example.com
    X509v3 issuerAlternativeName:
      30:0d:82:0b:65:78:61:6d:70:6c:65:2e:63:6f:6d
  Signature Algorithm: ED25519
  Signature Value:
    7e:50:dc:96:fe:c3:f8:ab:3c:ff:06:14:94:f4:19:ac:28:67:
    6c:35:5f:46:90:78:2e:da:56:08:79:8d:ee:6c:54:85:f7:56:
    14:8e:6a:f8:90:64:82:6d:79:d2:fc:dd:2b:3c:32:06:dc:4c:
    e1:5f:95:f2:80:a3:1a:0f:8b:09
~~~~~

### C509 Type 3 Certificate

- C509 type 3 certificate converted from the X.509 certificate in {{x509-ee-x25519}}.

[comment]: <> (replace-size:cert/ee-x25519/c509cert-t3.hex)
Plain hex (459 bytes):

[comment]: <> (replace-data:cert/ee-x25519/c509cert-t3.hex)
~~~~~
8B034212340C67746573742063611A6775D7001A69570A80800858208AFF516FAC71
244150E70F9277F4ADF7FB29F41A7A4A8828BD476722FC1B7F0888021407547FCDB8
2D04952E1A36B90AF37A3CF166D15EF92109920177687474703A2F2F6F6373702E65
78616D706C652E636F6D02781C687474703A2F2F6361697373756572732E6578616D
706C652E636F6D05781F687474703A2F2F63617265706F7369746F72792E6578616D
706C652E636F6D03781F687474703A2F2F74696D657374616D70696E672E6578616D
706C652E636F6D05781F687474703A2F2F63617265706F7369746F72792E6578616D
706C652E636F6D0A781F687474703A2F2F72706B696D616E69666573742E6578616D
706C652E636F6D0B781F687474703A2F2F7369676E65646F626A6563742E6578616D
706C652E636F6D0D781D687474703A2F2F72706B696E6F746966792E6578616D706C
652E636F6D492B0601040181FD590377687474703A2F2F313233342E6578616D706C
652E636F6D18196B6578616D706C652E636F6D58407E50DC96FEC3F8AB3CFF061494
F419AC28676C355F4690782EDA5608798DEE6C5485F756148E6AF89064826D79D2FC
DD2B3C3206DC4CE15F95F280A31A0F8B09
~~~~~

Annotated hex:

- See the annotated hex for the C509 type 2 certificate in {{c509-ee-x25519}}. The only differences are the certificate type, the signature value, and the key identifiers.

### C509 Type 2 Certificate {#c509-ee-x25519}

- Issued by the CA in {{c509-ca}}.

[comment]: <> (replace-size:cert/ee-x25519/c509cert-t2.hex)
Plain hex (459 bytes):

[comment]: <> (replace-data:cert/ee-x25519/c509cert-t2.hex)
~~~~~
8B024212340C67746573742063611A6775D7001A69570A80800858208AFF516FAC71
244150E70F9277F4ADF7FB29F41A7A4A8828BD476722FC1B7F0888021407540369D7
1F96FE1258A746AC2B208E756E6D1D3ED909920177687474703A2F2F6F6373702E65
78616D706C652E636F6D02781C687474703A2F2F6361697373756572732E6578616D
706C652E636F6D05781F687474703A2F2F63617265706F7369746F72792E6578616D
706C652E636F6D03781F687474703A2F2F74696D657374616D70696E672E6578616D
706C652E636F6D05781F687474703A2F2F63617265706F7369746F72792E6578616D
706C652E636F6D0A781F687474703A2F2F72706B696D616E69666573742E6578616D
706C652E636F6D0B781F687474703A2F2F7369676E65646F626A6563742E6578616D
706C652E636F6D0D781D687474703A2F2F72706B696E6F746966792E6578616D706C
652E636F6D492B0601040181FD590377687474703A2F2F313233342E6578616D706C
652E636F6D18196B6578616D706C652E636F6D5840C85D9310C572C7BC037B217C06
022234D9866069B1881D203EB3B6A0A4FAC8B4DF319465E9A2CDF6108F347C7983BA
662313C35480434F890B7F6D5819883505
~~~~~

Annotated hex:

[comment]: <> (replace-data:cert/ee-x25519/c509cert-t2.diag)
~~~~~
  0: 8B             # C509Certificate=array[11]
  1:   02             # [0]. certificate type=2
  2:   42             # [1]. certificateSerialNumber=byte[2]
  3:     1234
  5:   0C             # [2]. signature alg=Ed25519 (12)
  6:   67             # [3]. issuer=char[7]
  7:     74657374206361  # "test ca"
 14:   1A 6775D700    # [4]. notBefore=1735776000:
                      #      2025-01-02T00:00:00Z
 19:   1A 69570A80    # [5]. notAfter=1767312000:
                      #      2026-01-02T00:00:00Z
 24:   80             # [6]. subject=array[0], 0 attribute
 25:   08             # [7]. subjectPublicKeyAlg=X25519 (8)
 26:   58 20          # [8]. subject public key=EC point=byte[32]
 28:     8AFF516FAC71244150E70F9277F4ADF7FB29F41A7A4A8828BD476722FC
 57:     1B7F08
 60:   88             # [9]. extensions=array[8]
                        #---extension[0]---
 61:     02             # [0]. type=KeyUsage (2)
 62:     14             # [1]. value=20: [keyEncipherment,
                        #      keyAgreement]
                        #---extension[1]---
 63:     07             # [2]. type=AuthorityKeyIdentifier (7)
 64:     54             # [3]. value: keyIdentifier=byte[20]
 65:       0369D71F96FE1258A746AC2B208E756E6D1D3ED9
                        #---extension[2]---
 85:     09             # [4]. type=AuthorityInfoAccess (9)
 86:     92             # [5]. value=array[18]
                          #---AccessDescription[0]---
 87:       01             # [0]. accessMethod=ocsp (1)
 88:       77             # [1]. uri=char[23]
 89:         687474703A2F2F6F6373702E6578616D # "http://ocsp.exam"
105:         706C652E636F6D                   # "ple.com"
                          #---AccessDescription[1]---
112:       02             # [2]. accessMethod=caIssuers (2)
113:       78 1C          # [3]. uri=char[28]
115:         687474703A2F2F636169737375657273 # "http://caissuers"
131:         2E6578616D706C652E636F6D         # ".example.com"
                          #---AccessDescription[2]---
143:       05             # [4]. accessMethod=caRepository (5)
144:       78 1F          # [5]. uri=char[31]
146:         687474703A2F2F63617265706F736974 # "http://careposit"
162:         6F72792E6578616D706C652E636F6D   # "ory.example.com"
                          #---AccessDescription[3]---
177:       03             # [6]. accessMethod=timeStamping (3)
178:       78 1F          # [7]. uri=char[31]
180:         687474703A2F2F74696D657374616D70 # "http://timestamp"
196:         696E672E6578616D706C652E636F6D   # "ing.example.com"
                          #---AccessDescription[4]---
211:       05             # [8]. accessMethod=caRepository (5)
212:       78 1F          # [9]. uri=char[31]
214:         687474703A2F2F63617265706F736974 # "http://careposit"
230:         6F72792E6578616D706C652E636F6D   # "ory.example.com"
                          #---AccessDescription[5]---
245:       0A             # [10]. accessMethod=rpkiManifest (10)
246:       78 1F          # [11]. uri=char[31]
248:         687474703A2F2F72706B696D616E6966 # "http://rpkimanif"
264:         6573742E6578616D706C652E636F6D   # "est.example.com"
                          #---AccessDescription[6]---
279:       0B             # [12]. accessMethod=signedObject (11)
280:       78 1F          # [13]. uri=char[31]
282:         687474703A2F2F7369676E65646F626A # "http://signedobj"
298:         6563742E6578616D706C652E636F6D   # "ect.example.com"
                          #---AccessDescription[7]---
313:       0D             # [14]. accessMethod=rpkiNotify (13)
314:       78 1D          # [15]. uri=char[29]
316:         687474703A2F2F72706B696E6F746966 # "http://rpkinotif"
332:         792E6578616D706C652E636F6D       # "y.example.com"
                          #---AccessDescription[8]---
345:       49             # [16]. accessMethod=byte[9]: 
346:         2B0601040181FD5903 # oid: 1.3.6.1.4.1.32473.3
355:       77             # [17]. uri=char[23]
356:         687474703A2F2F313233342E6578616D # "http://1234.exam"
372:         706C652E636F6D                   # "ple.com"
                        #---extension[3]---
379:     18 19          # [6]. type=IssuerAlternativeName (25)
381:     6B             # [7]. value: DNS, value=char[11]
382:       6578616D706C652E636F6D # "example.com"
393:   58 40          # [10]. signature value=byte[64]
395:     C85D9310C572C7BC037B217C06022234D9866069B1881D203EB3B6A0A4
424:     FAC8B4DF319465E9A2CDF6108F347C7983BA662313C35480434F890B7F
453:     6D5819883505
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
-----BEGIN PRIVATE KEY-----
MEYCAQAwBQYDK2VvBDoEOPJNe3l0Mqeq8Fwp4DL6opcnehT4qMe0d+/4nSIVodQc
iOJE752KYXs1GZ0/+ETWNoBn8L7ZFO9g
-----END PRIVATE KEY-----
~~~~~

### X.509 Certificate {#x509-ee-x448}

- Issued by the CA in {{x509-ca}}.

[comment]: <> (replace-size:cert/ee-x448/x509cert.pem)
PEM content (572 bytes):

[comment]: <> (replace-data:cert/ee-x448/x509cert.pem)
~~~~~
-----BEGIN CERTIFICATE-----
MIICODCCAeqgAwIBAgICEjQwBQYDK2VwMBIxEDAOBgNVBAMMB3Rlc3QgY2EwHhcN
MjUwMTAyMDAwMDAwWhcNMjYwMTAyMDAwMDAwWjASMRAwDgYDVQQDDAdlZS14NDQ4
MEIwBQYDK2VvAzkAHDfvq+qShHs28mSJCu/byWQDnFxhoxEcCQvk0KQpeaZmfitk
J1dg/2/U/wP8WWa1Dj2aqstI9DujggFKMIIBRjALBgNVHQ8EBAMCAygwJwYDVR0f
BCAwHjAcoBqgGIYWaHR0cDovL2NybC5leGFtcGxlLmNvbTA6BgNVHSMEMzAxgBR/
zbgtBJUuGja5CvN6PPFm0V75IaEWpBQwEjEQMA4GA1UEAwwHdGVzdCBjYYIBATCB
0QYDVR0uBIHJMIHGMFagI6Ahhh9odHRwOi8vZnJlc2hlc3RjcmwxLmV4YW1wbGUu
Y29tgQIGwKIrpCkwJzELMAkGA1UEBgwCREUxGDAWBgNVBAMMD015IENSTCBpc3N1
ZXIgMTBsoEagRIYgaHR0cDovL2ZyZXNoZXN0Y3JsMjEuZXhhbXBsZS5jb22GIGh0
dHA6Ly9mcmVzaGVzdGNybDIyLmV4YW1wbGUuY29tgQIBAqIepBwwGjEYMBYGA1UE
AwwPTXkgQ1JMIGlzc3VlciAyMAUGAytlcANBANPIPA29PT7nPbnrdkLEjfiT5i+Z
wPRwCmb6CfG1E3U0HnRsLMvmmJI/AxdayWaDsjDHKTIuGX470J36lw2vWwc=
-----END CERTIFICATE-----
~~~~~

Textual Representation:

[comment]: <> (replace-data:cert/ee-x448/x509cert.txt)
~~~~~
Certificate:
  Version: v3 (2)
  Serial Number:
    12:34
  Issuer: CN=test ca
  Validity:
    Not Before: Thu Jan 02 01:00:00 CET 2025
    Not After : Fri Jan 02 01:00:00 CET 2026
  Subject: CN=ee-x448
  Subject Public Key Info:
    Public Key Algorithm: X448
    Pub:
      1c:37:ef:ab:ea:92:84:7b:36:f2:64:89:0a:ef:db:c9:64:03:
      9c:5c:61:a3:11:1c:09:0b:e4:d0:a4:29:79:a6:66:7e:2b:64:
      27:57:60:ff:6f:d4:ff:03:fc:59:66:b5:0e:3d:9a:aa:cb:48:
      f4:3b
  X509v3 extensions:
    X509v3 keyUsage:
      keyEncipherment, keyEncipherment
    X509v3 cRLDistributionPoints:
      Full Name:
        URI: http://crl.example.com
    X509v3 authorityKeyIdentifier:
      7f:cd:b8:2d:04:95:2e:1a:36:b9:0a:f3:7a:3c:f1:66:d1:5e:f9:21
      Issuer: Directory Name: CN=test ca
      Serial Number:
        01
    X509v3 freshestCRL:
      CRL Issuer:
        Directory Name: C=DE,CN=My CRL issuer 1
      Reasons: [unused, keyCompromise]
      Full Name:
        URI: http://freshestcrl1.example.com
      CRL Issuer:
        Directory Name: CN=My CRL issuer 2
      Reasons: [certificateHold]
      Full Name:
        URI: http://freshestcrl21.example.com
        URI: http://freshestcrl22.example.com
  Signature Algorithm: ED25519
  Signature Value:
    d3:c8:3c:0d:bd:3d:3e:e7:3d:b9:eb:76:42:c4:8d:f8:93:e6:
    2f:99:c0:f4:70:0a:66:fa:09:f1:b5:13:75:34:1e:74:6c:2c:
    cb:e6:98:92:3f:03:17:5a:c9:66:83:b2:30:c7:29:32:2e:19:
    7e:3b:d0:9d:fa:97:0d:af:5b:07
~~~~~

### C509 Type 3 Certificate

- C509 type 3 certificate converted from the X.509 certificate in {{x509-ee-x448}}.

[comment]: <> (replace-size:cert/ee-x448/c509cert-t3.hex)
Plain hex (367 bytes):

[comment]: <> (replace-data:cert/ee-x448/c509cert-t3.hex)
~~~~~
8B034212340C67746573742063611A6775D7001A69570A806765652D783434380958
381C37EFABEA92847B36F264890AEFDBC964039C5C61A3111C090BE4D0A42979A666
7E2B64275760FF6FD4FF03FC5966B50E3D9AAACB48F43B8802140576687474703A2F
2F63726C2E6578616D706C652E636F6D0783547FCDB82D04952E1A36B90AF37A3CF1
66D15EF921820467746573742063614101181D8283781F687474703A2F2F66726573
6865737463726C312E6578616D706C652E636F6D038404624445016F4D792043524C
20697373756572203183827820687474703A2F2F667265736865737463726C32312E
6578616D706C652E636F6D7820687474703A2F2F667265736865737463726C32322E
6578616D706C652E636F6D18406F4D792043524C2069737375657220325840D3C83C
0DBD3D3EE73DB9EB7642C48DF893E62F99C0F4700A66FA09F1B51375341E746C2CCB
E698923F03175AC96683B230C729322E197E3BD09DFA970DAF5B07
~~~~~

Annotated hex:

- See the annotated hex for the C509 type 2 certificate in {{c509-ee-x448}}. The only differences are the certificate type, the signature value, and the key identifiers.

### C509 Type 2 Certificate {#c509-ee-x448}

- Issued by the CA in {{c509-ca}}.

[comment]: <> (replace-size:cert/ee-x448/c509cert-t2.hex)
Plain hex (367 bytes):

[comment]: <> (replace-data:cert/ee-x448/c509cert-t2.hex)
~~~~~
8B024212340C67746573742063611A6775D7001A69570A806765652D783434380958
381C37EFABEA92847B36F264890AEFDBC964039C5C61A3111C090BE4D0A42979A666
7E2B64275760FF6FD4FF03FC5966B50E3D9AAACB48F43B8802140576687474703A2F
2F63726C2E6578616D706C652E636F6D0783540369D71F96FE1258A746AC2B208E75
6E6D1D3ED9820467746573742063614101181D8283781F687474703A2F2F66726573
6865737463726C312E6578616D706C652E636F6D038404624445016F4D792043524C
20697373756572203183827820687474703A2F2F667265736865737463726C32312E
6578616D706C652E636F6D7820687474703A2F2F667265736865737463726C32322E
6578616D706C652E636F6D18406F4D792043524C20697373756572203258409D8153
5FF622AE3C60E29391DBF16C22A411ED3F0FFBCF90E985F12D6F26160F8744DD7BC7
DB893F2855EF433539F6E501A7843EDE246C0C9E97615B7191120D
~~~~~

Annotated hex:

[comment]: <> (replace-data:cert/ee-x448/c509cert-t2.diag)
~~~~~
  0: 8B             # C509Certificate=array[11]
  1:   02             # [0]. certificate type=2
  2:   42             # [1]. certificateSerialNumber=byte[2]
  3:     1234
  5:   0C             # [2]. signature alg=Ed25519 (12)
  6:   67             # [3]. issuer=char[7]
  7:     74657374206361  # "test ca"
 14:   1A 6775D700    # [4]. notBefore=1735776000:
                      #      2025-01-02T00:00:00Z
 19:   1A 69570A80    # [5]. notAfter=1767312000:
                      #      2026-01-02T00:00:00Z
 24:   67             # [6]. subject=char[7]
 25:     65652D78343438  # "ee-x448"
 32:   09             # [7]. subjectPublicKeyAlg=X448 (9)
 33:   58 38          # [8]. subject public key=EC point=byte[56]
 35:     1C37EFABEA92847B36F264890AEFDBC964039C5C61A3111C090BE4D0A4
 64:     2979A6667E2B64275760FF6FD4FF03FC5966B50E3D9AAACB48F43B
 91:   88             # [9]. extensions=array[8]
                        #---extension[0]---
 92:     02             # [0]. type=KeyUsage (2)
 93:     14             # [1]. value=20: [keyEncipherment,
                        #      keyAgreement]
                        #---extension[1]---
 94:     05             # [2]. type=CRLDistributionPoints (5)
 95:     76             # [3]. value=fullName=char[22]
 96:       687474703A2F2F63726C2E6578616D70 # "http://crl.examp"
112:       6C652E636F6D                     # "le.com"
                        #---extension[2]---
118:     07             # [4]. type=AuthorityKeyIdentifier (7)
119:     83             # [5]. value=array[3]
120:       54             # [0]. keyIdentifier=byte[20]
121:         0369D71F96FE1258A746AC2B208E756E6D1D3ED9
141:       82             # [1]. authorityCertIssuer=array[2]
                            #---GeneralName[0]---
142:         04             # [0]. GeneralNameType=4: directoryName
143:         67             # [1]. GeneralNameValue=char[7]
144:           74657374206361 # "test ca"
151:       41             # [2]. authorityCertSerialNumber=byte[1]
152:         01
                        #---extension[3]---
153:     18 1D          # [6]. type=FreshestCRL (29)
155:     82             # [7]. value=array[2]
156:       83             # DistributionPoint[0]=array[3]
157:         78 1F          # [0]. fullName=char[31]
159:           687474703A2F2F6672657368657374 # "http://freshest"
174:           63726C312E6578616D706C652E636F # "crl1.example.co"
189:           6D                             # "m"
190:         03             # [1]. reasons=3: [unused,
                            #      keyCompromise]
191:         84             # [2]. cRLIssuer=array[4], 2 attributes
                              #---attribute[0]---
192:           04             # [0]. type=4: country
193:           62             # [1]. value=char[2]
194:             4445           # "DE"
                              #---attribute[1]---
196:           01             # [2]. type=1: commonName
197:           6F             # [3]. value=char[15]
198:             4D792043524C206973737565722031 # "My CRL issuer 1"
213:       83             # DistributionPoint[1]=array[3]
214:         82             # [0]. fullName=array[2]
215:           78 20          # [0]=char[32]
217:             687474703A2F2F6672657368657374 # "http://freshest"
232:             63726C32312E6578616D706C652E63 # "crl21.example.c"
247:             6F6D                           # "om"
249:           78 20          # [1]=char[32]
251:             687474703A2F2F6672657368657374 # "http://freshest"
266:             63726C32322E6578616D706C652E63 # "crl22.example.c"
281:             6F6D                           # "om"
283:         18 40          # [1]. reasons=64: [certificateHold]
285:         6F             # [2]. cRLIssuer=char[15]
286:           4D792043524C206973737565722032 # "My CRL issuer 2"
301:   58 40          # [10]. signature value=byte[64]
303:     9D81535FF622AE3C60E29391DBF16C22A411ED3F0FFBCF90E985F12D6F
332:     26160F8744DD7BC7DB893F2855EF433539F6E501A7843EDE246C0C9E97
361:     615B7191120D
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
-----BEGIN PRIVATE KEY-----
MC4CAQAwBQYDK2VwBCIEIPJNe3l0Mqeq8Fwp4DL6opcnehT4qMe0d+/4nSIVodQc
-----END PRIVATE KEY-----
~~~~~

### X.509 Certificate {#x509-selfsign-ed25519}

[comment]: <> (replace-size:cert/selfsign-ed25519/x509cert.pem)
PEM content (784 bytes):

[comment]: <> (replace-data:cert/selfsign-ed25519/x509cert.pem)
~~~~~
-----BEGIN CERTIFICATE-----
MIIDDDCCAr6gAwIBAgICEjQwBQYDK2VwMIHZMRkwFwYDVQQDDBBzZWxmc2lnbi1l
ZDI1NTE5MR8wHQYDVQQsDBZteSBnZW5lcmF0aW9uUXVhbGlmaWVyMRcwFQYDVQQu
DA5teSBkblF1YWxpZmllcjEiMCAGCgmSJomT8ixkARkWEm15IGRvbWFpbkNvbXBv
bmVudDETMBEGA1UENgwKbXkgZG1kTmFtZTEiMCAGCSqGSIb3DQEJAgwTbXkgdW5z
dHJ1Y3R1cmVkTmFtZTElMCMGCSqGSIb3DQEJCAwWbXkgdW5zdHJ1Y3R1cmVkQWRk
cmVzczAeFw0yNTAxMDIwMDAwMDBaFw0yNjAxMDIwMDAwMDBaMIHZMRkwFwYDVQQD
DBBzZWxmc2lnbi1lZDI1NTE5MR8wHQYDVQQsDBZteSBnZW5lcmF0aW9uUXVhbGlm
aWVyMRcwFQYDVQQuDA5teSBkblF1YWxpZmllcjEiMCAGCgmSJomT8ixkARkWEm15
IGRvbWFpbkNvbXBvbmVudDETMBEGA1UENgwKbXkgZG1kTmFtZTEiMCAGCSqGSIb3
DQEJAgwTbXkgdW5zdHJ1Y3R1cmVkTmFtZTElMCMGCSqGSIb3DQEJCAwWbXkgdW5z
dHJ1Y3R1cmVkQWRkcmVzczAqMAUGAytlcAMhAEYnCuwPMoN+Eod50wsknFMdbULB
rCnkAjKO3Hn6wr6Vo4GnMIGkMAsGA1UdDwQEAwIHgDCBgwYDVR0eBHwweqA8MByC
GnBlcm1pdHRlZC5kbnMxLmV4YW1wbGUuY29tMByCGnBlcm1pdHRlZC5kbnMyLmV4
YW1wbGUuY29toTowG4IZZXhjbHVkZWQuZG5zMS5leGFtcGxlLmNvbTAbghlleGNs
dWRlZC5kbnMyLmV4YW1wbGUuY29tMA8GA1UdJAQIMAaAAQGBAQIwBQYDK2VwA0EA
Ovw5mXwNx5nlnJcpmUEKuXhoSDzSIr+S8GtqLEWfLAsTT3yQHiSGLfxersyLqI21
0oDqikqXO/pN0D0OK9NoCQ==
-----END CERTIFICATE-----
~~~~~

Textual Representation:

[comment]: <> (replace-data:cert/selfsign-ed25519/x509cert.txt)
~~~~~
Certificate:
  Version: v3 (2)
  Serial Number:
    12:34
  Issuer: CN=selfsign-ed25519,GENERATION=my generationQualifier,DN=my
           dnQualifier,DC=my domainComponent,2.5.4.54=my dmdName,unst
          ructuredName=my unstructuredName,unstructuredAddress=my uns
          tructuredAddress
  Validity:
    Not Before: Thu Jan 02 01:00:00 CET 2025
    Not After : Fri Jan 02 01:00:00 CET 2026
  Subject: CN=selfsign-ed25519,GENERATION=my generationQualifier,DN=m
           y dnQualifier,DC=my domainComponent,2.5.4.54=my dmdName,un
           structuredName=my unstructuredName,unstructuredAddress=my 
           unstructuredAddress
  Subject Public Key Info:
    Public Key Algorithm: ED25519
    Pub:
      46:27:0a:ec:0f:32:83:7e:12:87:79:d3:0b:24:9c:53:1d:6d:
      42:c1:ac:29:e4:02:32:8e:dc:79:fa:c2:be:95
  X509v3 extensions:
    X509v3 keyUsage:
      digitalSignature
    X509v3 nameConstraints:
      Permitted
        DNS: permitted.dns1.example.com
        DNS: permitted.dns2.example.com
      Excluded
        DNS: excluded.dns1.example.com
        DNS: excluded.dns2.example.com
    X509v3 policyConstraints:
      Require Explicit Policy:1, Inhibit Explicit Policy:2
  Signature Algorithm: ED25519
  Signature Value:
    3a:fc:39:99:7c:0d:c7:99:e5:9c:97:29:99:41:0a:b9:78:68:
    48:3c:d2:22:bf:92:f0:6b:6a:2c:45:9f:2c:0b:13:4f:7c:90:
    1e:24:86:2d:fc:5e:ae:cc:8b:a8:8d:b5:d2:80:ea:8a:4a:97:
    3b:fa:4d:d0:3d:0e:2b:d3:68:09
~~~~~

### C509 Type 3 Certificate

- C509 type 3 certificate converted from the X.509 certificate in {{x509-selfsign-ed25519}}.

[comment]: <> (replace-size:cert/selfsign-ed25519/c509cert-t3.hex)
Plain hex (384 bytes):

[comment]: <> (replace-data:cert/selfsign-ed25519/c509cert-t3.hex)
~~~~~
8B034212340CF61A6775D7001A69570A808E017073656C667369676E2D6564323535
31390F766D792067656E65726174696F6E5175616C6966696572106E6D7920646E51
75616C696669657216726D7920646F6D61696E436F6D706F6E656E74181B6A6D7920
646D644E616D65181D736D7920756E737472756374757265644E616D65181E766D79
20756E73747275637475726564416464726573730C582046270AEC0F32837E128779
D30B249C531D6D42C1AC29E402328EDC79FAC2BE95860201181A828402781A706572
6D69747465642E646E73312E6578616D706C652E636F6D02781A7065726D69747465
642E646E73322E6578616D706C652E636F6D840278196578636C756465642E646E73
312E6578616D706C652E636F6D0278196578636C756465642E646E73322E6578616D
706C652E636F6D181C82010258403AFC39997C0DC799E59C972999410AB97868483C
D222BF92F06B6A2C459F2C0B134F7C901E24862DFC5EAECC8BA88DB5D280EA8A4A97
3BFA4DD03D0E2BD36809
~~~~~

Annotated hex:

- See the annotated hex for the C509 type 2 certificate in {{c509-selfsign-ed25519}}. The only differences are the certificate type, the signature value, and the key identifiers.

### C509 Type 2 Certificate {#c509-selfsign-ed25519}

[comment]: <> (replace-size:cert/selfsign-ed25519/c509cert-t2.hex)
Plain hex (384 bytes):

[comment]: <> (replace-data:cert/selfsign-ed25519/c509cert-t2.hex)
~~~~~
8B024212340CF61A6775D7001A69570A808E017073656C667369676E2D6564323535
31390F766D792067656E65726174696F6E5175616C6966696572106E6D7920646E51
75616C696669657216726D7920646F6D61696E436F6D706F6E656E74181B6A6D7920
646D644E616D65181D736D7920756E737472756374757265644E616D65181E766D79
20756E73747275637475726564416464726573730C582046270AEC0F32837E128779
D30B249C531D6D42C1AC29E402328EDC79FAC2BE95860201181A828402781A706572
6D69747465642E646E73312E6578616D706C652E636F6D02781A7065726D69747465
642E646E73322E6578616D706C652E636F6D840278196578636C756465642E646E73
312E6578616D706C652E636F6D0278196578636C756465642E646E73322E6578616D
706C652E636F6D181C8201025840213CF14F253BCECA58A1CDF0AAD3565E01D66124
61F86DBACC6E0140995AC3EEF507AF1341D604243751562CCB363B0C72C989E9D2F2
60C594228342AFAC7B00
~~~~~

Annotated hex:

[comment]: <> (replace-data:cert/selfsign-ed25519/c509cert-t2.diag)
~~~~~
  0: 8B             # C509Certificate=array[11]
  1:   02             # [0]. certificate type=2
  2:   42             # [1]. certificateSerialNumber=byte[2]
  3:     1234
  5:   0C             # [2]. signature alg=Ed25519 (12)
  6:   F6             # [3]. issuer=<null>
  7:   1A 6775D700    # [4]. notBefore=1735776000:
                      #      2025-01-02T00:00:00Z
 12:   1A 69570A80    # [5]. notAfter=1767312000:
                      #      2026-01-02T00:00:00Z
 17:   8E             # [6]. subject=array[14], 7 attributes
                        #---attribute[0]---
 18:     01             # [0]. type=1: commonName
 19:     70             # [1]. value=char[16]
 20:       73656C667369676E2D65643235353139 # "selfsign-ed25519"
                        #---attribute[1]---
 36:     0F             # [2]. type=15: generationQualifier
 37:     76             # [3]. value=char[22]
 38:       6D792067656E65726174696F6E517561 # "my generationQua"
 54:       6C6966696572                     # "lifier"
                        #---attribute[2]---
 60:     10             # [4]. type=16: DNQualifier
 61:     6E             # [5]. value=char[14]
 62:       6D7920646E5175616C6966696572 # "my dnQualifier"
                        #---attribute[3]---
 76:     16             # [6]. type=22: domainComponent
 77:     72             # [7]. value=char[18]
 78:       6D7920646F6D61696E436F6D706F6E65 # "my domainCompone"
 94:       6E74                             # "nt"
                        #---attribute[4]---
 96:     18 1B          # [8]. type=27: DMDName
 98:     6A             # [9]. value=char[10]
 99:       6D7920646D644E616D65 # "my dmdName"
                        #---attribute[5]---
109:     18 1D          # [10]. type=29: unstructuredName
111:     73             # [11]. value=char[19]
112:       6D7920756E737472756374757265644E # "my unstructuredN"
128:       616D65                           # "ame"
                        #---attribute[6]---
131:     18 1E          # [12]. type=30: unstructuredAddress
133:     76             # [13]. value=char[22]
134:       6D7920756E7374727563747572656441 # "my unstructuredA"
150:       646472657373                     # "ddress"
156:   0C             # [7]. subjectPublicKeyAlg=Ed25519 (12)
157:   58 20          # [8]. subject public key=EC point=byte[32]
159:     46270AEC0F32837E128779D30B249C531D6D42C1AC29E402328EDC79FA
188:     C2BE95
191:   86             # [9]. extensions=array[6]
                        #---extension[0]---
192:     02             # [0]. type=KeyUsage (2)
193:     01             # [1]. value=1: [digitalSignature]
                        #---extension[1]---
194:     18 1A          # [2]. type=NameConstraints (26)
196:     82             # [3]. value=array[2]
197:       84             # [0]. permittedSubtrees=array[4]
                            #---GeneralName[0]---
198:         02             # [0]. GeneralNameType=2: dNSName
199:         78 1A          # [1]. GeneralNameValue=char[26]
201:           7065726D69747465642E646E73312E # "permitted.dns1."
216:           6578616D706C652E636F6D         # "example.com"
                            #---GeneralName[1]---
227:         02             # [2]. GeneralNameType=2: dNSName
228:         78 1A          # [3]. GeneralNameValue=char[26]
230:           7065726D69747465642E646E73322E # "permitted.dns2."
245:           6578616D706C652E636F6D         # "example.com"
256:       84             # [1]. excludedSubtrees=array[4]
                            #---GeneralName[0]---
257:         02             # [0]. GeneralNameType=2: dNSName
258:         78 19          # [1]. GeneralNameValue=char[25]
260:           6578636C756465642E646E73312E65 # "excluded.dns1.e"
275:           78616D706C652E636F6D           # "xample.com"
                            #---GeneralName[1]---
285:         02             # [2]. GeneralNameType=2: dNSName
286:         78 19          # [3]. GeneralNameValue=char[25]
288:           6578636C756465642E646E73322E65 # "excluded.dns2.e"
303:           78616D706C652E636F6D           # "xample.com"
                        #---extension[2]---
313:     18 1C          # [4]. type=PolicyConstraints (28)
315:     82             # [5]. value=array[2]
316:       01             # [0]. requireExplicitPolicy=1
317:       02             # [1]. inhibitPolicyMapping=2
318:   58 40          # [10]. signature value=byte[64]
320:     213CF14F253BCECA58A1CDF0AAD3565E01D6612461F86DBACC6E014099
349:     5AC3EEF507AF1341D604243751562CCB363B0C72C989E9D2F260C59422
378:     8342AFAC7B00
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
-----BEGIN PRIVATE KEY-----
MEcCAQAwBQYDK2VxBDsEOfJNe3l0Mqeq8Fwp4DL6opcnehT4qMe0d+/4nSIVodQc
iOJE752KYXs1GZ0/+ETWNoBn8L7ZFO9gjQ==
-----END PRIVATE KEY-----
~~~~~

### X.509 Certificate {#x509-selfsign-ed448}

[comment]: <> (replace-size:cert/selfsign-ed448/x509cert.pem)
PEM content (503 bytes):

[comment]: <> (replace-data:cert/selfsign-ed448/x509cert.pem)
~~~~~
-----BEGIN CERTIFICATE-----
MIIB8zCCAXOgAwIBAgICEjQwBQYDK2VxMGExFzAVBgNVBAMMDnNlbGZzaWduLWVk
NDQ4MRQwEgYDVQQrDAtteSBpbml0aWFsczEVMBMGA1UEQQwMbXkgcHNldWRvbnlt
MRkwFwYKCZImiZPyLGQBAQwJbXkgdXNlcmlkMB4XDTI1MDEwMjAwMDAwMFoXDTI2
MDEwMjAwMDAwMFowYTEXMBUGA1UEAwwOc2VsZnNpZ24tZWQ0NDgxFDASBgNVBCsM
C215IGluaXRpYWxzMRUwEwYDVQRBDAxteSBwc2V1ZG9ueW0xGTAXBgoJkiaJk/Is
ZAEBDAlteSB1c2VyaWQwQzAFBgMrZXEDOgCMNeSR21hwLXuZFnwZ+Gsmgeol0h+D
rG7HgECpBBFPyx3PI59sTYRRR/fiJ5Ookan5yl0eIKc5jICjNjA0MAsGA1UdDwQE
AwIHgDAPBgkrBgEFBQcwAQUEAgUAMBQGCCsGAQUFBwEYBAgwBgIBLAIBEDAFBgMr
ZXEDcwAIyFAExQwfWAUK3hZXBgq0xcng4EH5i+nAHJ0qVXSz/uZN4Q4J39t41FZa
nofvtGpcNsrJo6R9lYA7hrTcjMhMdKYWZFUQC4/tKRa9evXD+ksKC5cQDK0NeylV
e9mC1hBrlA91HlK7VwgFTHwIqKaQMQA=
-----END CERTIFICATE-----
~~~~~

Textual Representation:

[comment]: <> (replace-data:cert/selfsign-ed448/x509cert.txt)
~~~~~
Certificate:
  Version: v3 (2)
  Serial Number:
    12:34
  Issuer: CN=selfsign-ed448,INITIALS=my initials,Pseudonym=my pseudon
          ym,UID=my userid
  Validity:
    Not Before: Thu Jan 02 01:00:00 CET 2025
    Not After : Fri Jan 02 01:00:00 CET 2026
  Subject: CN=selfsign-ed448,INITIALS=my initials,Pseudonym=my pseudo
           nym,UID=my userid
  Subject Public Key Info:
    Public Key Algorithm: ED448
    Pub:
      8c:35:e4:91:db:58:70:2d:7b:99:16:7c:19:f8:6b:26:81:ea:
      25:d2:1f:83:ac:6e:c7:80:40:a9:04:11:4f:cb:1d:cf:23:9f:
      6c:4d:84:51:47:f7:e2:27:93:a8:91:a9:f9:ca:5d:1e:20:a7:
      39:8c:80
  X509v3 extensions:
    X509v3 keyUsage:
      digitalSignature
    X509v3 pkix-ocsp-nocheck:
      NULL
    X509v3 pe-tlsfeature:
      44
      16
  Signature Algorithm: ED448
  Signature Value:
    08:c8:50:04:c5:0c:1f:58:05:0a:de:16:57:06:0a:b4:c5:c9:
    e0:e0:41:f9:8b:e9:c0:1c:9d:2a:55:74:b3:fe:e6:4d:e1:0e:
    09:df:db:78:d4:56:5a:9e:87:ef:b4:6a:5c:36:ca:c9:a3:a4:
    7d:95:80:3b:86:b4:dc:8c:c8:4c:74:a6:16:64:55:10:0b:8f:
    ed:29:16:bd:7a:f5:c3:fa:4b:0a:0b:97:10:0c:ad:0d:7b:29:
    55:7b:d9:82:d6:10:6b:94:0f:75:1e:52:bb:57:08:05:4c:7c:
    08:a8:a6:90:31:00
~~~~~

### C509 Type 3 Certificate

- C509 type 3 certificate converted from the X.509 certificate in {{x509-selfsign-ed448}}.

[comment]: <> (replace-size:cert/selfsign-ed448/c509cert-t3.hex)
Plain hex (261 bytes):

[comment]: <> (replace-data:cert/selfsign-ed448/c509cert-t3.hex)
~~~~~
8B034212340DF61A6775D7001A69570A8088016E73656C667369676E2D6564343438
0E6B6D7920696E697469616C73116C6D792070736575646F6E796D181C696D792075
73657269640D58398C35E491DB58702D7B99167C19F86B2681EA25D21F83AC6EC780
40A904114FCB1DCF239F6C4D845147F7E22793A891A9F9CA5D1E20A7398C80860201
1824F6182682182C10587208C85004C50C1F58050ADE1657060AB4C5C9E0E041F98B
E9C01C9D2A5574B3FEE64DE10E09DFDB78D4565A9E87EFB46A5C36CAC9A3A47D9580
3B86B4DC8CC84C74A6166455100B8FED2916BD7AF5C3FA4B0A0B97100CAD0D7B2955
7BD982D6106B940F751E52BB5708054C7C08A8A6903100
~~~~~

Annotated hex:

- See the annotated hex for the C509 type 2 certificate in {{c509-selfsign-ed448}}. The only differences are the certificate type, the signature value, and the key identifiers.

### C509 Type 2 Certificate {#c509-selfsign-ed448}

[comment]: <> (replace-size:cert/selfsign-ed448/c509cert-t2.hex)
Plain hex (261 bytes):

[comment]: <> (replace-data:cert/selfsign-ed448/c509cert-t2.hex)
~~~~~
8B024212340DF61A6775D7001A69570A8088016E73656C667369676E2D6564343438
0E6B6D7920696E697469616C73116C6D792070736575646F6E796D181C696D792075
73657269640D58398C35E491DB58702D7B99167C19F86B2681EA25D21F83AC6EC780
40A904114FCB1DCF239F6C4D845147F7E22793A891A9F9CA5D1E20A7398C80860201
1824F6182682182C1058725E12D7D2F577CBDB36BA15DD9EA97B9BB9B49284210308
CCFB2B1C2F9E2FF80398CC5D4F50293AFD24C5BCE3569379D344BCC4D31C6062A400
EC582489B9F8B8CDCF0F4F2C2C38482A6201B78D9B222B8E7CF75431BDBE4FA9061B
066DA656B5509F36D6005D0C2B602018B79E79C9A20A00
~~~~~

Annotated hex:

[comment]: <> (replace-data:cert/selfsign-ed448/c509cert-t2.diag)
~~~~~
  0: 8B             # C509Certificate=array[11]
  1:   02             # [0]. certificate type=2
  2:   42             # [1]. certificateSerialNumber=byte[2]
  3:     1234
  5:   0D             # [2]. signature alg=Ed448 (13)
  6:   F6             # [3]. issuer=<null>
  7:   1A 6775D700    # [4]. notBefore=1735776000:
                      #      2025-01-02T00:00:00Z
 12:   1A 69570A80    # [5]. notAfter=1767312000:
                      #      2026-01-02T00:00:00Z
 17:   88             # [6]. subject=array[8], 4 attributes
                        #---attribute[0]---
 18:     01             # [0]. type=1: commonName
 19:     6E             # [1]. value=char[14]
 20:       73656C667369676E2D6564343438 # "selfsign-ed448"
                        #---attribute[1]---
 34:     0E             # [2]. type=14: initials
 35:     6B             # [3]. value=char[11]
 36:       6D7920696E697469616C73 # "my initials"
                        #---attribute[2]---
 47:     11             # [4]. type=17: pseudonym
 48:     6C             # [5]. value=char[12]
 49:       6D792070736575646F6E796D # "my pseudonym"
                        #---attribute[3]---
 61:     18 1C          # [6]. type=28: userID
 63:     69             # [7]. value=char[9]
 64:       6D7920757365726964 # "my userid"
 73:   0D             # [7]. subjectPublicKeyAlg=Ed448 (13)
 74:   58 39          # [8]. subject public key=EC point=byte[57]
 76:     8C35E491DB58702D7B99167C19F86B2681EA25D21F83AC6EC78040A904
105:     114FCB1DCF239F6C4D845147F7E22793A891A9F9CA5D1E20A7398C80
133:   86             # [9]. extensions=array[6]
                        #---extension[0]---
134:     02             # [0]. type=KeyUsage (2)
135:     01             # [1]. value=1: [digitalSignature]
                        #---extension[1]---
136:     18 24          # [2]. type=OCSPNoCheck (36)
138:     F6             # [3]. value=<null>
                        #---extension[2]---
139:     18 26          # [4]. type=TLSFeatures (38)
141:     82             # [5]. value=array[2]
142:       18 2C          # [0]. cookie (44)
144:       10             # [1]. application layer protocol
                          #      negotiation (16)
145:   58 72          # [10]. signature value=byte[114]
147:     5E12D7D2F577CBDB36BA15DD9EA97B9BB9B49284210308CCFB2B1C2F9E
176:     2FF80398CC5D4F50293AFD24C5BCE3569379D344BCC4D31C6062A400EC
205:     582489B9F8B8CDCF0F4F2C2C38482A6201B78D9B222B8E7CF75431BDBE
234:     4FA9061B066DA656B5509F36D6005D0C2B602018B79E79C9A20A00
~~~~~

# Certificates With Different Signature Algorithms {#sec-cert-different-sigs}

## RSASSA-PKCS1-v1_5 With SHA-1 {#selfsign-rsa-with-sha1}

- Self-signed certificate
- Signature algorithm: sha1WithRSAEncryption

### Private Key

See {{key-selfsign-rsa}}.

### X.509 Certificate {#x509-selfsign-rsa-with-sha1}

[comment]: <> (replace-size:cert/selfsign-rsa-with-sha1/x509cert.pem)
PEM content (463 bytes):

[comment]: <> (replace-data:cert/selfsign-rsa-with-sha1/x509cert.pem)
~~~~~
-----BEGIN CERTIFICATE-----
MIIByzCCATSgAwIBAgICEjQwDQYJKoZIhvcNAQEFBQAwITEfMB0GA1UEAwwWc2Vs
ZnNpZ24tcnNhLXdpdGgtc2hhMTAeFw0yNTAxMDIwMDAwMDBaFw0yNjAxMDIwMDAw
MDBaMCExHzAdBgNVBAMMFnNlbGZzaWduLXJzYS13aXRoLXNoYTEwgZ8wDQYJKoZI
hvcNAQEBBQADgY0AMIGJAoGBALgJL28EcmqSHPqy0xOunS8Bx85GX6t9pix6XHP6
zl/7ovHdgKKa3EM5nPyiInm4miZIEOW5JrteDT9yenY+FgE/ifj+rFnQ+91eiwxS
gn5UkPE7hMNjTonG0XMa5fGmD4jtEY0IDhqyyqUy0GwvfSoIdN7k5rblcoP2R42v
QlPbAgMBAAGjEjAQMA4GA1UdDwEB/wQEAwIHgDANBgkqhkiG9w0BAQUFAAOBgQBy
vlKYpmd/Cnh0eiiaEvGVVbFQY6Qx95k5BpyVL+Wp2K5oB/WZ5fwP/mf/w8viB7hc
rTO02AaTvv5NJNyt3ubIwV1UBn1MX+OWC43HH6GRH+6cndHiE/2cAKRoGMBA3xKp
4YMRr/kweqpFcIWfFObSWJ4aWdmw/6eg5e3uPIhd/A==
-----END CERTIFICATE-----
~~~~~

Textual Representation:

[comment]: <> (replace-data:cert/selfsign-rsa-with-sha1/x509cert.txt)
~~~~~
Certificate:
  Version: v3 (2)
  Serial Number:
    12:34
  Issuer: CN=selfsign-rsa-with-sha1
  Validity:
    Not Before: Thu Jan 02 01:00:00 CET 2025
    Not After : Fri Jan 02 01:00:00 CET 2026
  Subject: CN=selfsign-rsa-with-sha1
  Subject Public Key Info:
    Public Key Algorithm: 1.2.840.113549.1.1.1
    Pub:
      30:81:89:02:81:81:00:b8:09:2f:6f:04:72:6a:92:1c:fa:b2:
      d3:13:ae:9d:2f:01:c7:ce:46:5f:ab:7d:a6:2c:7a:5c:73:fa:
      ce:5f:fb:a2:f1:dd:80:a2:9a:dc:43:39:9c:fc:a2:22:79:b8:
      9a:26:48:10:e5:b9:26:bb:5e:0d:3f:72:7a:76:3e:16:01:3f:
      89:f8:fe:ac:59:d0:fb:dd:5e:8b:0c:52:82:7e:54:90:f1:3b:
      84:c3:63:4e:89:c6:d1:73:1a:e5:f1:a6:0f:88:ed:11:8d:08:
      0e:1a:b2:ca:a5:32:d0:6c:2f:7d:2a:08:74:de:e4:e6:b6:e5:
      72:83:f6:47:8d:af:42:53:db:02:03:01:00:01
  X509v3 extensions:
    X509v3 keyUsage: critical
      digitalSignature
  Signature Algorithm: SHA1WITHRSA
  Signature Value:
    72:be:52:98:a6:67:7f:0a:78:74:7a:28:9a:12:f1:95:55:b1:
    50:63:a4:31:f7:99:39:06:9c:95:2f:e5:a9:d8:ae:68:07:f5:
    99:e5:fc:0f:fe:67:ff:c3:cb:e2:07:b8:5c:ad:33:b4:d8:06:
    93:be:fe:4d:24:dc:ad:de:e6:c8:c1:5d:54:06:7d:4c:5f:e3:
    96:0b:8d:c7:1f:a1:91:1f:ee:9c:9d:d1:e2:13:fd:9c:00:a4:
    68:18:c0:40:df:12:a9:e1:83:11:af:f9:30:7a:aa:45:70:85:
    9f:14:e6:d2:58:9e:1a:59:d9:b0:ff:a7:a0:e5:ed:ee:3c:88:
    5d:fc
~~~~~

### C509 Type 3 Certificate

- C509 type 3 certificate converted from the X.509 certificate in {{x509-selfsign-rsa-with-sha1}}.

[comment]: <> (replace-size:cert/selfsign-rsa-with-sha1/c509cert-t3.hex)
Plain hex (303 bytes):

[comment]: <> (replace-data:cert/selfsign-rsa-with-sha1/c509cert-t3.hex)
~~~~~
8B0342123438FFF61A6775D7001A69570A807673656C667369676E2D7273612D7769
74682D73686131005880B8092F6F04726A921CFAB2D313AE9D2F01C7CE465FAB7DA6
2C7A5C73FACE5FFBA2F1DD80A29ADC43399CFCA22279B89A264810E5B926BB5E0D3F
727A763E16013F89F8FEAC59D0FBDD5E8B0C52827E5490F13B84C3634E89C6D1731A
E5F1A60F88ED118D080E1AB2CAA532D06C2F7D2A0874DEE4E6B6E57283F6478DAF42
53DB20588072BE5298A6677F0A78747A289A12F19555B15063A431F79939069C952F
E5A9D8AE6807F599E5FC0FFE67FFC3CBE207B85CAD33B4D80693BEFE4D24DCADDEE6
C8C15D54067D4C5FE3960B8DC71FA1911FEE9C9DD1E213FD9C00A46818C040DF12A9
E18311AFF9307AAA4570859F14E6D2589E1A59D9B0FFA7A0E5EDEE3C885DFC
~~~~~

Annotated hex:

- See the annotated hex for the C509 type 2 certificate in {{c509-selfsign-rsa-with-sha1}}. The only differences are the certificate type, the signature value, and the key identifiers.

### C509 Type 2 Certificate {#c509-selfsign-rsa-with-sha1}

[comment]: <> (replace-size:cert/selfsign-rsa-with-sha1/c509cert-t2.hex)
Plain hex (303 bytes):

[comment]: <> (replace-data:cert/selfsign-rsa-with-sha1/c509cert-t2.hex)
~~~~~
8B0242123438FFF61A6775D7001A69570A807673656C667369676E2D7273612D7769
74682D73686131005880B8092F6F04726A921CFAB2D313AE9D2F01C7CE465FAB7DA6
2C7A5C73FACE5FFBA2F1DD80A29ADC43399CFCA22279B89A264810E5B926BB5E0D3F
727A763E16013F89F8FEAC59D0FBDD5E8B0C52827E5490F13B84C3634E89C6D1731A
E5F1A60F88ED118D080E1AB2CAA532D06C2F7D2A0874DEE4E6B6E57283F6478DAF42
53DB2058800150926C5642D9CB2AAA27A17B68FBBFC9F47AA42CD9E6779B4E9A7A81
C760589C53AC23BAD6A94F5A6B275BE292B79BA9CB59D045E44809353DCE73C936A0
6EC20D51AE24C559DDB02EBF4B0838F515328058F601D91F6DAE5BFF55DC78DEB809
70D2F74757FC5F96BE6F217825DC8286D9446CCA0C9AF257FCE66CD963F891
~~~~~

Annotated hex:

[comment]: <> (replace-data:cert/selfsign-rsa-with-sha1/c509cert-t2.diag)
~~~~~
  0: 8B             # C509Certificate=array[11]
  1:   02             # [0]. certificate type=2
  2:   42             # [1]. certificateSerialNumber=byte[2]
  3:     1234
  5:   38 FF          # [2]. signature alg=sha1WithRSAEncryption
                      #      (-256)
  7:   F6             # [3]. issuer=<null>
  8:   1A 6775D700    # [4]. notBefore=1735776000:
                      #      2025-01-02T00:00:00Z
 13:   1A 69570A80    # [5]. notAfter=1767312000:
                      #      2026-01-02T00:00:00Z
 18:   76             # [6]. subject=char[22]
 19:     73656C667369676E2D7273612D776974 # "selfsign-rsa-wit"
 35:     682D73686131                     # "h-sha1"
 41:   00             # [7]. subjectPublicKeyAlg=RSA (0)
 42:   58 80          # [8]. subject public key=modulus=byte[128]
 44:     B8092F6F04726A921CFAB2D313AE9D2F01C7CE465FAB7DA62C7A5C73FA
 73:     CE5FFBA2F1DD80A29ADC43399CFCA22279B89A264810E5B926BB5E0D3F
102:     727A763E16013F89F8FEAC59D0FBDD5E8B0C52827E5490F13B84C3634E
131:     89C6D1731AE5F1A60F88ED118D080E1AB2CAA532D06C2F7D2A0874DEE4
160:     E6B6E57283F6478DAF4253DB
172:   20             # [9]. extensions=-1, KeyUsage, critical:
                      #      [digitalSignature]
173:   58 80          # [10]. signature value=byte[128]
175:     0150926C5642D9CB2AAA27A17B68FBBFC9F47AA42CD9E6779B4E9A7A81
204:     C760589C53AC23BAD6A94F5A6B275BE292B79BA9CB59D045E44809353D
233:     CE73C936A06EC20D51AE24C559DDB02EBF4B0838F515328058F601D91F
262:     6DAE5BFF55DC78DEB80970D2F74757FC5F96BE6F217825DC8286D9446C
291:     CA0C9AF257FCE66CD963F891
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

## RSASSA-PKCS1-v1_5 With SHA-512 {#selfsign-rsa-with-sha512}

- Self-signed certificate
- Signature algorithm: sha512WithRSAEncryption

### Private Key

See {{key-selfsign-rsa}}.

### X.509 Certificate {#x509-selfsign-rsa-with-sha512}

[comment]: <> (replace-size:cert/selfsign-rsa-with-sha512/x509cert.pem)
PEM content (467 bytes):

[comment]: <> (replace-data:cert/selfsign-rsa-with-sha512/x509cert.pem)
~~~~~
-----BEGIN CERTIFICATE-----
MIIBzzCCATigAwIBAgICEjQwDQYJKoZIhvcNAQENBQAwIzEhMB8GA1UEAwwYc2Vs
ZnNpZ24tcnNhLXdpdGgtc2hhNTEyMB4XDTI1MDEwMjAwMDAwMFoXDTI2MDEwMjAw
MDAwMFowIzEhMB8GA1UEAwwYc2VsZnNpZ24tcnNhLXdpdGgtc2hhNTEyMIGfMA0G
CSqGSIb3DQEBAQUAA4GNADCBiQKBgQC4CS9vBHJqkhz6stMTrp0vAcfORl+rfaYs
elxz+s5f+6Lx3YCimtxDOZz8oiJ5uJomSBDluSa7Xg0/cnp2PhYBP4n4/qxZ0Pvd
XosMUoJ+VJDxO4TDY06JxtFzGuXxpg+I7RGNCA4assqlMtBsL30qCHTe5Oa25XKD
9keNr0JT2wIDAQABoxIwEDAOBgNVHQ8BAf8EBAMCB4AwDQYJKoZIhvcNAQENBQAD
gYEAJAbODIhhp4VHV8H6LZ5YI4CFgBdOg8lrlnTBlpBetrwI7NCEha/22u5epjzv
ZvdYOXLcsvKhqhS2hMGjOVS7R6ffu/9N/nUSa9341ocG/SbeZuXL8Ba12faHGMq2
6yK9i42EI91vKGuHIXw6WONk8GAXXFHdH6xgTJ/1U2krm+4=
-----END CERTIFICATE-----
~~~~~

Textual Representation:

[comment]: <> (replace-data:cert/selfsign-rsa-with-sha512/x509cert.txt)
~~~~~
Certificate:
  Version: v3 (2)
  Serial Number:
    12:34
  Issuer: CN=selfsign-rsa-with-sha512
  Validity:
    Not Before: Thu Jan 02 01:00:00 CET 2025
    Not After : Fri Jan 02 01:00:00 CET 2026
  Subject: CN=selfsign-rsa-with-sha512
  Subject Public Key Info:
    Public Key Algorithm: 1.2.840.113549.1.1.1
    Pub:
      30:81:89:02:81:81:00:b8:09:2f:6f:04:72:6a:92:1c:fa:b2:
      d3:13:ae:9d:2f:01:c7:ce:46:5f:ab:7d:a6:2c:7a:5c:73:fa:
      ce:5f:fb:a2:f1:dd:80:a2:9a:dc:43:39:9c:fc:a2:22:79:b8:
      9a:26:48:10:e5:b9:26:bb:5e:0d:3f:72:7a:76:3e:16:01:3f:
      89:f8:fe:ac:59:d0:fb:dd:5e:8b:0c:52:82:7e:54:90:f1:3b:
      84:c3:63:4e:89:c6:d1:73:1a:e5:f1:a6:0f:88:ed:11:8d:08:
      0e:1a:b2:ca:a5:32:d0:6c:2f:7d:2a:08:74:de:e4:e6:b6:e5:
      72:83:f6:47:8d:af:42:53:db:02:03:01:00:01
  X509v3 extensions:
    X509v3 keyUsage: critical
      digitalSignature
  Signature Algorithm: SHA512WITHRSA
  Signature Value:
    24:06:ce:0c:88:61:a7:85:47:57:c1:fa:2d:9e:58:23:80:85:
    80:17:4e:83:c9:6b:96:74:c1:96:90:5e:b6:bc:08:ec:d0:84:
    85:af:f6:da:ee:5e:a6:3c:ef:66:f7:58:39:72:dc:b2:f2:a1:
    aa:14:b6:84:c1:a3:39:54:bb:47:a7:df:bb:ff:4d:fe:75:12:
    6b:dd:f8:d6:87:06:fd:26:de:66:e5:cb:f0:16:b5:d9:f6:87:
    18:ca:b6:eb:22:bd:8b:8d:84:23:dd:6f:28:6b:87:21:7c:3a:
    58:e3:64:f0:60:17:5c:51:dd:1f:ac:60:4c:9f:f5:53:69:2b:
    9b:ee
~~~~~

### C509 Type 3 Certificate

- C509 type 3 certificate converted from the X.509 certificate in {{x509-selfsign-rsa-with-sha512}}.

[comment]: <> (replace-size:cert/selfsign-rsa-with-sha512/c509cert-t3.hex)
Plain hex (306 bytes):

[comment]: <> (replace-data:cert/selfsign-rsa-with-sha512/c509cert-t3.hex)
~~~~~
8B034212341819F61A6775D7001A69570A80781873656C667369676E2D7273612D77
6974682D736861353132005880B8092F6F04726A921CFAB2D313AE9D2F01C7CE465F
AB7DA62C7A5C73FACE5FFBA2F1DD80A29ADC43399CFCA22279B89A264810E5B926BB
5E0D3F727A763E16013F89F8FEAC59D0FBDD5E8B0C52827E5490F13B84C3634E89C6
D1731AE5F1A60F88ED118D080E1AB2CAA532D06C2F7D2A0874DEE4E6B6E57283F647
8DAF4253DB2058802406CE0C8861A7854757C1FA2D9E5823808580174E83C96B9674
C196905EB6BC08ECD08485AFF6DAEE5EA63CEF66F7583972DCB2F2A1AA14B684C1A3
3954BB47A7DFBBFF4DFE75126BDDF8D68706FD26DE66E5CBF016B5D9F68718CAB6EB
22BD8B8D8423DD6F286B87217C3A58E364F060175C51DD1FAC604C9FF553692B9BEE
~~~~~

Annotated hex:

- See the annotated hex for the C509 type 2 certificate in {{c509-selfsign-rsa-with-sha512}}. The only differences are the certificate type, the signature value, and the key identifiers.

### C509 Type 2 Certificate {#c509-selfsign-rsa-with-sha512}

[comment]: <> (replace-size:cert/selfsign-rsa-with-sha512/c509cert-t2.hex)
Plain hex (306 bytes):

[comment]: <> (replace-data:cert/selfsign-rsa-with-sha512/c509cert-t2.hex)
~~~~~
8B024212341819F61A6775D7001A69570A80781873656C667369676E2D7273612D77
6974682D736861353132005880B8092F6F04726A921CFAB2D313AE9D2F01C7CE465F
AB7DA62C7A5C73FACE5FFBA2F1DD80A29ADC43399CFCA22279B89A264810E5B926BB
5E0D3F727A763E16013F89F8FEAC59D0FBDD5E8B0C52827E5490F13B84C3634E89C6
D1731AE5F1A60F88ED118D080E1AB2CAA532D06C2F7D2A0874DEE4E6B6E57283F647
8DAF4253DB205880B5D4C31502957FBEE2E4DED7E45E723A8B9A86A46E9FEA1D8781
7808D1ACE802370B91718755F101FFB3B971816120BE5CC05D2EE866422D78EF7D16
AA78CE4011E4DC92AE1C7DA3C7831773A44A7B2F5BAFED5D2B8A6A4E6E49638B3335
DC68B596AE5FC48360E1C7DD50BD457CF2CFDCF56F98BE1EA3103B12DD5B6221DB21
~~~~~

Annotated hex:

[comment]: <> (replace-data:cert/selfsign-rsa-with-sha512/c509cert-t2.diag)
~~~~~
  0: 8B             # C509Certificate=array[11]
  1:   02             # [0]. certificate type=2
  2:   42             # [1]. certificateSerialNumber=byte[2]
  3:     1234
  5:   18 19          # [2]. signature alg=sha512WithRSAEncryption
                      #      (25)
  7:   F6             # [3]. issuer=<null>
  8:   1A 6775D700    # [4]. notBefore=1735776000:
                      #      2025-01-02T00:00:00Z
 13:   1A 69570A80    # [5]. notAfter=1767312000:
                      #      2026-01-02T00:00:00Z
 18:   78 18          # [6]. subject=char[24]
 20:     73656C667369676E2D7273612D776974 # "selfsign-rsa-wit"
 36:     682D736861353132                 # "h-sha512"
 44:   00             # [7]. subjectPublicKeyAlg=RSA (0)
 45:   58 80          # [8]. subject public key=modulus=byte[128]
 47:     B8092F6F04726A921CFAB2D313AE9D2F01C7CE465FAB7DA62C7A5C73FA
 76:     CE5FFBA2F1DD80A29ADC43399CFCA22279B89A264810E5B926BB5E0D3F
105:     727A763E16013F89F8FEAC59D0FBDD5E8B0C52827E5490F13B84C3634E
134:     89C6D1731AE5F1A60F88ED118D080E1AB2CAA532D06C2F7D2A0874DEE4
163:     E6B6E57283F6478DAF4253DB
175:   20             # [9]. extensions=-1, KeyUsage, critical:
                      #      [digitalSignature]
176:   58 80          # [10]. signature value=byte[128]
178:     B5D4C31502957FBEE2E4DED7E45E723A8B9A86A46E9FEA1D87817808D1
207:     ACE802370B91718755F101FFB3B971816120BE5CC05D2EE866422D78EF
236:     7D16AA78CE4011E4DC92AE1C7DA3C7831773A44A7B2F5BAFED5D2B8A6A
265:     4E6E49638B3335DC68B596AE5FC48360E1C7DD50BD457CF2CFDCF56F98
294:     BE1EA3103B12DD5B6221DB21
~~~~~

## RSASSA-PSS With SHA-256 {#selfsign-rsassa-pss-sha256}

- Self-signed certificate
- Signature algorithm: rsassa-pss-with-sha256

### Private Key

See {{key-selfsign-rsa}}.

### X.509 Certificate {#x509-selfsign-rsassa-pss-sha256}

[comment]: <> (replace-size:cert/selfsign-rsassa-pss-sha256/x509cert.pem)
PEM content (575 bytes):

[comment]: <> (replace-data:cert/selfsign-rsassa-pss-sha256/x509cert.pem)
~~~~~
-----BEGIN CERTIFICATE-----
MIICOzCCAXCgAwIBAgICEjQwQQYJKoZIhvcNAQEKMDSgDzANBglghkgBZQMEAgEF
AKEcMBoGCSqGSIb3DQEBCDANBglghkgBZQMEAgEFAKIDAgEgMCUxIzAhBgNVBAMM
GnNlbGZzaWduLXJzYXNzYS1wc3Mtc2hhMjU2MB4XDTI1MDEwMjAwMDAwMFoXDTI2
MDEwMjAwMDAwMFowJTEjMCEGA1UEAwwac2VsZnNpZ24tcnNhc3NhLXBzcy1zaGEy
NTYwgZ8wDQYJKoZIhvcNAQEBBQADgY0AMIGJAoGBALgJL28EcmqSHPqy0xOunS8B
x85GX6t9pix6XHP6zl/7ovHdgKKa3EM5nPyiInm4miZIEOW5JrteDT9yenY+FgE/
ifj+rFnQ+91eiwxSgn5UkPE7hMNjTonG0XMa5fGmD4jtEY0IDhqyyqUy0GwvfSoI
dN7k5rblcoP2R42vQlPbAgMBAAGjEjAQMA4GA1UdDwEB/wQEAwIHgDBBBgkqhkiG
9w0BAQowNKAPMA0GCWCGSAFlAwQCAQUAoRwwGgYJKoZIhvcNAQEIMA0GCWCGSAFl
AwQCAQUAogMCASADgYEAaxYseZDcSnGCKNIuIk3zWqHnUZIwyOtSrx6iG2RBexZj
Hr8Gp9Anfzk5p8iw//TcfIkbJ119gfUpdZhxvMQjqq6NRqD1Hj/gte0npsFtSojF
7zIGqFbtuwbnr3PUKXa8Y/Yu0QBRvw3bZCVNxeixHr1O71t6oJLfEHm6QSXQv+c=
-----END CERTIFICATE-----
~~~~~

Textual Representation:

[comment]: <> (replace-data:cert/selfsign-rsassa-pss-sha256/x509cert.txt)
~~~~~
Certificate:
  Version: v3 (2)
  Serial Number:
    12:34
  Issuer: CN=selfsign-rsassa-pss-sha256
  Validity:
    Not Before: Thu Jan 02 01:00:00 CET 2025
    Not After : Fri Jan 02 01:00:00 CET 2026
  Subject: CN=selfsign-rsassa-pss-sha256
  Subject Public Key Info:
    Public Key Algorithm: 1.2.840.113549.1.1.1
    Pub:
      30:81:89:02:81:81:00:b8:09:2f:6f:04:72:6a:92:1c:fa:b2:
      d3:13:ae:9d:2f:01:c7:ce:46:5f:ab:7d:a6:2c:7a:5c:73:fa:
      ce:5f:fb:a2:f1:dd:80:a2:9a:dc:43:39:9c:fc:a2:22:79:b8:
      9a:26:48:10:e5:b9:26:bb:5e:0d:3f:72:7a:76:3e:16:01:3f:
      89:f8:fe:ac:59:d0:fb:dd:5e:8b:0c:52:82:7e:54:90:f1:3b:
      84:c3:63:4e:89:c6:d1:73:1a:e5:f1:a6:0f:88:ed:11:8d:08:
      0e:1a:b2:ca:a5:32:d0:6c:2f:7d:2a:08:74:de:e4:e6:b6:e5:
      72:83:f6:47:8d:af:42:53:db:02:03:01:00:01
  X509v3 extensions:
    X509v3 keyUsage: critical
      digitalSignature
  Signature Algorithm: SHA256WITHRSAANDMGF1
  Signature Value:
    6b:16:2c:79:90:dc:4a:71:82:28:d2:2e:22:4d:f3:5a:a1:e7:
    51:92:30:c8:eb:52:af:1e:a2:1b:64:41:7b:16:63:1e:bf:06:
    a7:d0:27:7f:39:39:a7:c8:b0:ff:f4:dc:7c:89:1b:27:5d:7d:
    81:f5:29:75:98:71:bc:c4:23:aa:ae:8d:46:a0:f5:1e:3f:e0:
    b5:ed:27:a6:c1:6d:4a:88:c5:ef:32:06:a8:56:ed:bb:06:e7:
    af:73:d4:29:76:bc:63:f6:2e:d1:00:51:bf:0d:db:64:25:4d:
    c5:e8:b1:1e:bd:4e:ef:5b:7a:a0:92:df:10:79:ba:41:25:d0:
    bf:e7
~~~~~

### C509 Type 3 Certificate

- C509 type 3 certificate converted from the X.509 certificate in {{x509-selfsign-rsassa-pss-sha256}}.

[comment]: <> (replace-size:cert/selfsign-rsassa-pss-sha256/c509cert-t3.hex)
Plain hex (308 bytes):

[comment]: <> (replace-data:cert/selfsign-rsassa-pss-sha256/c509cert-t3.hex)
~~~~~
8B03421234181AF61A6775D7001A69570A80781A73656C667369676E2D7273617373
612D7073732D736861323536005880B8092F6F04726A921CFAB2D313AE9D2F01C7CE
465FAB7DA62C7A5C73FACE5FFBA2F1DD80A29ADC43399CFCA22279B89A264810E5B9
26BB5E0D3F727A763E16013F89F8FEAC59D0FBDD5E8B0C52827E5490F13B84C3634E
89C6D1731AE5F1A60F88ED118D080E1AB2CAA532D06C2F7D2A0874DEE4E6B6E57283
F6478DAF4253DB2058806B162C7990DC4A718228D22E224DF35AA1E7519230C8EB52
AF1EA21B64417B16631EBF06A7D0277F3939A7C8B0FFF4DC7C891B275D7D81F52975
9871BCC423AAAE8D46A0F51E3FE0B5ED27A6C16D4A88C5EF3206A856EDBB06E7AF73
D42976BC63F62ED10051BF0DDB64254DC5E8B11EBD4EEF5B7AA092DF1079BA4125D0
BFE7
~~~~~

Annotated hex:

- See the annotated hex for the C509 type 2 certificate in {{c509-selfsign-rsassa-pss-sha256}}. The only differences are the certificate type, the signature value, and the key identifiers.

### C509 Type 2 Certificate {#c509-selfsign-rsassa-pss-sha256}

[comment]: <> (replace-size:cert/selfsign-rsassa-pss-sha256/c509cert-t2.hex)
Plain hex (308 bytes):

[comment]: <> (replace-data:cert/selfsign-rsassa-pss-sha256/c509cert-t2.hex)
~~~~~
8B02421234181AF61A6775D7001A69570A80781A73656C667369676E2D7273617373
612D7073732D736861323536005880B8092F6F04726A921CFAB2D313AE9D2F01C7CE
465FAB7DA62C7A5C73FACE5FFBA2F1DD80A29ADC43399CFCA22279B89A264810E5B9
26BB5E0D3F727A763E16013F89F8FEAC59D0FBDD5E8B0C52827E5490F13B84C3634E
89C6D1731AE5F1A60F88ED118D080E1AB2CAA532D06C2F7D2A0874DEE4E6B6E57283
F6478DAF4253DB2058804D8FB6928B9C34EF6E53A22DE2BED6579A58FB990CED4C7C
C5B0227CBB210741B3C3DA6A72CFA764CEF937DABC9C373776FD882ABBD052936D6B
4A14A12E628AF43CA89A6CAAC11513AA9C4438C668447FFF7497F32BE445B58A4EA2
E40E30C32165558EFB66E2B17640B93B061BD8BF5812818B318415E9F20FFE5EA50C
9D39
~~~~~

Annotated hex:

[comment]: <> (replace-data:cert/selfsign-rsassa-pss-sha256/c509cert-t2.diag)
~~~~~
  0: 8B             # C509Certificate=array[11]
  1:   02             # [0]. certificate type=2
  2:   42             # [1]. certificateSerialNumber=byte[2]
  3:     1234
  5:   18 1A          # [2]. signature alg=rsassa-pss-with-sha256
                      #      (26)
  7:   F6             # [3]. issuer=<null>
  8:   1A 6775D700    # [4]. notBefore=1735776000:
                      #      2025-01-02T00:00:00Z
 13:   1A 69570A80    # [5]. notAfter=1767312000:
                      #      2026-01-02T00:00:00Z
 18:   78 1A          # [6]. subject=char[26]
 20:     73656C667369676E2D7273617373612D # "selfsign-rsassa-"
 36:     7073732D736861323536             # "pss-sha256"
 46:   00             # [7]. subjectPublicKeyAlg=RSA (0)
 47:   58 80          # [8]. subject public key=modulus=byte[128]
 49:     B8092F6F04726A921CFAB2D313AE9D2F01C7CE465FAB7DA62C7A5C73FA
 78:     CE5FFBA2F1DD80A29ADC43399CFCA22279B89A264810E5B926BB5E0D3F
107:     727A763E16013F89F8FEAC59D0FBDD5E8B0C52827E5490F13B84C3634E
136:     89C6D1731AE5F1A60F88ED118D080E1AB2CAA532D06C2F7D2A0874DEE4
165:     E6B6E57283F6478DAF4253DB
177:   20             # [9]. extensions=-1, KeyUsage, critical:
                      #      [digitalSignature]
178:   58 80          # [10]. signature value=byte[128]
180:     4D8FB6928B9C34EF6E53A22DE2BED6579A58FB990CED4C7CC5B0227CBB
209:     210741B3C3DA6A72CFA764CEF937DABC9C373776FD882ABBD052936D6B
238:     4A14A12E628AF43CA89A6CAAC11513AA9C4438C668447FFF7497F32BE4
267:     45B58A4EA2E40E30C32165558EFB66E2B17640B93B061BD8BF5812818B
296:     318415E9F20FFE5EA50C9D39
~~~~~

## RSASSA-PSS With SHA-384 {#selfsign-rsassa-pss-sha384}

- Self-signed certificate
- Signature algorithm: rsassa-pss-with-sha384

### Private Key

See {{key-selfsign-rsa}}.

### X.509 Certificate {#x509-selfsign-rsassa-pss-sha384}

[comment]: <> (replace-size:cert/selfsign-rsassa-pss-sha384/x509cert.pem)
PEM content (575 bytes):

[comment]: <> (replace-data:cert/selfsign-rsassa-pss-sha384/x509cert.pem)
~~~~~
-----BEGIN CERTIFICATE-----
MIICOzCCAXCgAwIBAgICEjQwQQYJKoZIhvcNAQEKMDSgDzANBglghkgBZQMEAgIF
AKEcMBoGCSqGSIb3DQEBCDANBglghkgBZQMEAgIFAKIDAgEwMCUxIzAhBgNVBAMM
GnNlbGZzaWduLXJzYXNzYS1wc3Mtc2hhMzg0MB4XDTI1MDEwMjAwMDAwMFoXDTI2
MDEwMjAwMDAwMFowJTEjMCEGA1UEAwwac2VsZnNpZ24tcnNhc3NhLXBzcy1zaGEz
ODQwgZ8wDQYJKoZIhvcNAQEBBQADgY0AMIGJAoGBALgJL28EcmqSHPqy0xOunS8B
x85GX6t9pix6XHP6zl/7ovHdgKKa3EM5nPyiInm4miZIEOW5JrteDT9yenY+FgE/
ifj+rFnQ+91eiwxSgn5UkPE7hMNjTonG0XMa5fGmD4jtEY0IDhqyyqUy0GwvfSoI
dN7k5rblcoP2R42vQlPbAgMBAAGjEjAQMA4GA1UdDwEB/wQEAwIHgDBBBgkqhkiG
9w0BAQowNKAPMA0GCWCGSAFlAwQCAgUAoRwwGgYJKoZIhvcNAQEIMA0GCWCGSAFl
AwQCAgUAogMCATADgYEAqVzA6vSrfxLlxmScZDh63L2urQ7spjsiVkW6EQFEZvUT
eJBArUoQh+skNokj0K/ThgT0Ivh2hFgrvWx69/t4h9JAy2OMGj3sAHGH8HlgsqG4
glCKKGyhsXJqPEtOSAilslD7s5zI1xhmToKQ5ZqZVkXSSph5+rZkwKjRO67tv64=
-----END CERTIFICATE-----
~~~~~

Textual Representation:

[comment]: <> (replace-data:cert/selfsign-rsassa-pss-sha384/x509cert.txt)
~~~~~
Certificate:
  Version: v3 (2)
  Serial Number:
    12:34
  Issuer: CN=selfsign-rsassa-pss-sha384
  Validity:
    Not Before: Thu Jan 02 01:00:00 CET 2025
    Not After : Fri Jan 02 01:00:00 CET 2026
  Subject: CN=selfsign-rsassa-pss-sha384
  Subject Public Key Info:
    Public Key Algorithm: 1.2.840.113549.1.1.1
    Pub:
      30:81:89:02:81:81:00:b8:09:2f:6f:04:72:6a:92:1c:fa:b2:
      d3:13:ae:9d:2f:01:c7:ce:46:5f:ab:7d:a6:2c:7a:5c:73:fa:
      ce:5f:fb:a2:f1:dd:80:a2:9a:dc:43:39:9c:fc:a2:22:79:b8:
      9a:26:48:10:e5:b9:26:bb:5e:0d:3f:72:7a:76:3e:16:01:3f:
      89:f8:fe:ac:59:d0:fb:dd:5e:8b:0c:52:82:7e:54:90:f1:3b:
      84:c3:63:4e:89:c6:d1:73:1a:e5:f1:a6:0f:88:ed:11:8d:08:
      0e:1a:b2:ca:a5:32:d0:6c:2f:7d:2a:08:74:de:e4:e6:b6:e5:
      72:83:f6:47:8d:af:42:53:db:02:03:01:00:01
  X509v3 extensions:
    X509v3 keyUsage: critical
      digitalSignature
  Signature Algorithm: SHA384WITHRSAANDMGF1
  Signature Value:
    a9:5c:c0:ea:f4:ab:7f:12:e5:c6:64:9c:64:38:7a:dc:bd:ae:
    ad:0e:ec:a6:3b:22:56:45:ba:11:01:44:66:f5:13:78:90:40:
    ad:4a:10:87:eb:24:36:89:23:d0:af:d3:86:04:f4:22:f8:76:
    84:58:2b:bd:6c:7a:f7:fb:78:87:d2:40:cb:63:8c:1a:3d:ec:
    00:71:87:f0:79:60:b2:a1:b8:82:50:8a:28:6c:a1:b1:72:6a:
    3c:4b:4e:48:08:a5:b2:50:fb:b3:9c:c8:d7:18:66:4e:82:90:
    e5:9a:99:56:45:d2:4a:98:79:fa:b6:64:c0:a8:d1:3b:ae:ed:
    bf:ae
~~~~~

### C509 Type 3 Certificate

- C509 type 3 certificate converted from the X.509 certificate in {{x509-selfsign-rsassa-pss-sha384}}.

[comment]: <> (replace-size:cert/selfsign-rsassa-pss-sha384/c509cert-t3.hex)
Plain hex (308 bytes):

[comment]: <> (replace-data:cert/selfsign-rsassa-pss-sha384/c509cert-t3.hex)
~~~~~
8B03421234181BF61A6775D7001A69570A80781A73656C667369676E2D7273617373
612D7073732D736861333834005880B8092F6F04726A921CFAB2D313AE9D2F01C7CE
465FAB7DA62C7A5C73FACE5FFBA2F1DD80A29ADC43399CFCA22279B89A264810E5B9
26BB5E0D3F727A763E16013F89F8FEAC59D0FBDD5E8B0C52827E5490F13B84C3634E
89C6D1731AE5F1A60F88ED118D080E1AB2CAA532D06C2F7D2A0874DEE4E6B6E57283
F6478DAF4253DB205880A95CC0EAF4AB7F12E5C6649C64387ADCBDAEAD0EECA63B22
5645BA11014466F513789040AD4A1087EB24368923D0AFD38604F422F87684582BBD
6C7AF7FB7887D240CB638C1A3DEC007187F07960B2A1B882508A286CA1B1726A3C4B
4E4808A5B250FBB39CC8D718664E8290E59A995645D24A9879FAB664C0A8D13BAEED
BFAE
~~~~~

Annotated hex:

- See the annotated hex for the C509 type 2 certificate in {{c509-selfsign-rsassa-pss-sha384}}. The only differences are the certificate type, the signature value, and the key identifiers.

### C509 Type 2 Certificate {#c509-selfsign-rsassa-pss-sha384}

[comment]: <> (replace-size:cert/selfsign-rsassa-pss-sha384/c509cert-t2.hex)
Plain hex (308 bytes):

[comment]: <> (replace-data:cert/selfsign-rsassa-pss-sha384/c509cert-t2.hex)
~~~~~
8B02421234181BF61A6775D7001A69570A80781A73656C667369676E2D7273617373
612D7073732D736861333834005880B8092F6F04726A921CFAB2D313AE9D2F01C7CE
465FAB7DA62C7A5C73FACE5FFBA2F1DD80A29ADC43399CFCA22279B89A264810E5B9
26BB5E0D3F727A763E16013F89F8FEAC59D0FBDD5E8B0C52827E5490F13B84C3634E
89C6D1731AE5F1A60F88ED118D080E1AB2CAA532D06C2F7D2A0874DEE4E6B6E57283
F6478DAF4253DB20588062E00951C6AC6536337911F5568E8FCB79440A0A7A59EA7E
EFFC20CD8A85E2111502116A040D14A209602BCD8F635D9B91689429F8B43D35FC79
A4B3AE34824D41B56D9472513673F7D13B2F77B81992B205DDFF91088CCDF03E85A7
F07471EFF6549AF07A77BBAE313D1B909DDF2EC94C67E0F20A342CC25CFFF87A820C
E9DC
~~~~~

Annotated hex:

[comment]: <> (replace-data:cert/selfsign-rsassa-pss-sha384/c509cert-t2.diag)
~~~~~
  0: 8B             # C509Certificate=array[11]
  1:   02             # [0]. certificate type=2
  2:   42             # [1]. certificateSerialNumber=byte[2]
  3:     1234
  5:   18 1B          # [2]. signature alg=rsassa-pss-with-sha384
                      #      (27)
  7:   F6             # [3]. issuer=<null>
  8:   1A 6775D700    # [4]. notBefore=1735776000:
                      #      2025-01-02T00:00:00Z
 13:   1A 69570A80    # [5]. notAfter=1767312000:
                      #      2026-01-02T00:00:00Z
 18:   78 1A          # [6]. subject=char[26]
 20:     73656C667369676E2D7273617373612D # "selfsign-rsassa-"
 36:     7073732D736861333834             # "pss-sha384"
 46:   00             # [7]. subjectPublicKeyAlg=RSA (0)
 47:   58 80          # [8]. subject public key=modulus=byte[128]
 49:     B8092F6F04726A921CFAB2D313AE9D2F01C7CE465FAB7DA62C7A5C73FA
 78:     CE5FFBA2F1DD80A29ADC43399CFCA22279B89A264810E5B926BB5E0D3F
107:     727A763E16013F89F8FEAC59D0FBDD5E8B0C52827E5490F13B84C3634E
136:     89C6D1731AE5F1A60F88ED118D080E1AB2CAA532D06C2F7D2A0874DEE4
165:     E6B6E57283F6478DAF4253DB
177:   20             # [9]. extensions=-1, KeyUsage, critical:
                      #      [digitalSignature]
178:   58 80          # [10]. signature value=byte[128]
180:     62E00951C6AC6536337911F5568E8FCB79440A0A7A59EA7EEFFC20CD8A
209:     85E2111502116A040D14A209602BCD8F635D9B91689429F8B43D35FC79
238:     A4B3AE34824D41B56D9472513673F7D13B2F77B81992B205DDFF91088C
267:     CDF03E85A7F07471EFF6549AF07A77BBAE313D1B909DDF2EC94C67E0F2
296:     0A342CC25CFFF87A820CE9DC
~~~~~

## RSASSA-PSS With SHA-512 {#selfsign-rsassa-pss-sha512}

- Self-signed certificate
- RSA public key with 1536-bit modulus
- Signature algorithm: rsassa-pss-with-sha512

### Private Key {#key-selfsign-rsassa-pss-sha512}

[comment]: <> (replace-data:key/rsa-long/prikey.pem)
~~~~~
-----BEGIN PRIVATE KEY-----
MIIDlgIBADANBgkqhkiG9w0BAQEFAASCA4AwggN8AgEAAoHBALURcpGG7cAbIzXs
fkarHPOzH7jUYTPONQWI79he0IdlaLe+AGzjUnq6ZDobD4+l7FQCIpYwRCz84wUI
BMsD+pnlvD+NSLPuqUtCDCb43se3joWAp54prqDcm9xZc/idmz236LvrC/01fOcQ
2e855Ktii4YShduQk58NL9AdjpvJcXz0xOQvsX3MUixCqfoACwX/+6niTuZLmRwe
AYJKnVr9w36hHIGvKh+oK9WJQK19TbYfjcaIxIpobEWxBlYm0wIDAQABAoHAGnwr
BXVhPmnZxyVIvT6B6sr8ggpyuLivZxgGI+COi6zThJjisrViQaMU+QOEowQ9TXZ4
fP53YnCP0RMWBw9rGcARvpFZx+lKKGtzSpoO6tNYd1eqbPH3jKkS6p1yX18cxXKv
IXDGNeaHkrMAzUrFnyAdp84N4py6ilwe4GFSXoXsY+hUFqf2ndLnrdbtutorPOSb
OoAkSha26343TA4KbWSSjzIBdFrevSdVJKPhyXTMM7kzl8rT5ZVq6A5FxkeJAmEA
8k17eXQyp6rwXCngMvqilyd6FPiox7R37/idIhWh1ByI4kTvnYphezUZnT/4RNY2
gGfwvtkU72CNx3/G2VH+IU85UqnWjleSVxt87bW/XLcURC4qRMOJB9G6Kmsodz6Z
AmEAv03NHUsQ+DtA0quYuttBDJYzatVELoR0DINO9uUl7b6kpOeVrHHDqrnKaUKK
NSw7MU2jjGxUlDYblX/CfRFbGi26jefCDHRCJNBdK7XDEobcuWiR3N5G3v5RALyP
71BLAmA1TWg5Op+eFNOVYrzoWgyEdfG8RUtTy1aLGsTuZ5XXBIP0Srfvjy6MnQzK
c/UYp4sLylHCmhyp4DKwTWKgEhJ2n2f70FLbs1YFBY4A+Pdk3P1/ViyEPgOaDcAH
btVTDhkCYQC+HmjpYs/TkreCZszbbsi/5Inm81nWTQYz5U9VqTe/hcaEqC6keU82
3XuejmViILoW7ozKImMQBJH3SkgUGWqLosSTAK9lxyhxA66EoRfj0qmLjbPgZ/rQ
5LfF3p/A+u8CYHb/ZTlM+RxAgAJ7bsxrUKJrkM4yRz7YXNcXZH4N6A+s4HzY1+Y/
aKaI5NsOmVR3Gr2OzpxQwALgD+Xo6hzyBPQ2gbozBA2VQ38zwt3M4dU6clFcc9R1
q1jQfu4/j1hEEg==
-----END PRIVATE KEY-----
~~~~~

### X.509 Certificate {#x509-selfsign-rsassa-pss-sha512}

[comment]: <> (replace-size:cert/selfsign-rsassa-pss-sha512/x509cert.pem)
PEM content (703 bytes):

[comment]: <> (replace-data:cert/selfsign-rsassa-pss-sha512/x509cert.pem)
~~~~~
-----BEGIN CERTIFICATE-----
MIICuzCCAbCgAwIBAgICEjQwQQYJKoZIhvcNAQEKMDSgDzANBglghkgBZQMEAgMF
AKEcMBoGCSqGSIb3DQEBCDANBglghkgBZQMEAgMFAKIDAgFAMCUxIzAhBgNVBAMM
GnNlbGZzaWduLXJzYXNzYS1wc3Mtc2hhNTEyMB4XDTI1MDEwMjAwMDAwMFoXDTI2
MDEwMjAwMDAwMFowJTEjMCEGA1UEAwwac2VsZnNpZ24tcnNhc3NhLXBzcy1zaGE1
MTIwgd8wDQYJKoZIhvcNAQEBBQADgc0AMIHJAoHBALURcpGG7cAbIzXsfkarHPOz
H7jUYTPONQWI79he0IdlaLe+AGzjUnq6ZDobD4+l7FQCIpYwRCz84wUIBMsD+pnl
vD+NSLPuqUtCDCb43se3joWAp54prqDcm9xZc/idmz236LvrC/01fOcQ2e855Kti
i4YShduQk58NL9AdjpvJcXz0xOQvsX3MUixCqfoACwX/+6niTuZLmRweAYJKnVr9
w36hHIGvKh+oK9WJQK19TbYfjcaIxIpobEWxBlYm0wIDAQABoxIwEDAOBgNVHQ8B
Af8EBAMCB4AwQQYJKoZIhvcNAQEKMDSgDzANBglghkgBZQMEAgMFAKEcMBoGCSqG
SIb3DQEBCDANBglghkgBZQMEAgMFAKIDAgFAA4HBAColIqgyLpIBpUstLLEaPeNe
NpA9ZibWHKT+HrHFhINsWmg6SNYDrn4/XA1j/1II9nlCvq8oq2HnHr6IUqIe6tpd
YSA5sNiAdqzSDdWRmpzpGCBzCZoxrxT3mtZ7NPqmOKYj9xRGhyrRyHB9VnEbaWXt
r2Tyr3LI6dmUsRkh+jjW31NeaLl2YAPkqsMqiXBaxiIkaYs+CwQ6UIwiy8wcdqKC
K3F9aMjdTyyfZOdSU8YqONqQbmZPJSuBAHxVgrumyQ==
-----END CERTIFICATE-----
~~~~~

Textual Representation:

[comment]: <> (replace-data:cert/selfsign-rsassa-pss-sha512/x509cert.txt)
~~~~~
Certificate:
  Version: v3 (2)
  Serial Number:
    12:34
  Issuer: CN=selfsign-rsassa-pss-sha512
  Validity:
    Not Before: Thu Jan 02 01:00:00 CET 2025
    Not After : Fri Jan 02 01:00:00 CET 2026
  Subject: CN=selfsign-rsassa-pss-sha512
  Subject Public Key Info:
    Public Key Algorithm: 1.2.840.113549.1.1.1
    Pub:
      30:81:c9:02:81:c1:00:b5:11:72:91:86:ed:c0:1b:23:35:ec:
      7e:46:ab:1c:f3:b3:1f:b8:d4:61:33:ce:35:05:88:ef:d8:5e:
      d0:87:65:68:b7:be:00:6c:e3:52:7a:ba:64:3a:1b:0f:8f:a5:
      ec:54:02:22:96:30:44:2c:fc:e3:05:08:04:cb:03:fa:99:e5:
      bc:3f:8d:48:b3:ee:a9:4b:42:0c:26:f8:de:c7:b7:8e:85:80:
      a7:9e:29:ae:a0:dc:9b:dc:59:73:f8:9d:9b:3d:b7:e8:bb:eb:
      0b:fd:35:7c:e7:10:d9:ef:39:e4:ab:62:8b:86:12:85:db:90:
      93:9f:0d:2f:d0:1d:8e:9b:c9:71:7c:f4:c4:e4:2f:b1:7d:cc:
      52:2c:42:a9:fa:00:0b:05:ff:fb:a9:e2:4e:e6:4b:99:1c:1e:
      01:82:4a:9d:5a:fd:c3:7e:a1:1c:81:af:2a:1f:a8:2b:d5:89:
      40:ad:7d:4d:b6:1f:8d:c6:88:c4:8a:68:6c:45:b1:06:56:26:
      d3:02:03:01:00:01
  X509v3 extensions:
    X509v3 keyUsage: critical
      digitalSignature
  Signature Algorithm: SHA512WITHRSAANDMGF1
  Signature Value:
    2a:25:22:a8:32:2e:92:01:a5:4b:2d:2c:b1:1a:3d:e3:5e:36:
    90:3d:66:26:d6:1c:a4:fe:1e:b1:c5:84:83:6c:5a:68:3a:48:
    d6:03:ae:7e:3f:5c:0d:63:ff:52:08:f6:79:42:be:af:28:ab:
    61:e7:1e:be:88:52:a2:1e:ea:da:5d:61:20:39:b0:d8:80:76:
    ac:d2:0d:d5:91:9a:9c:e9:18:20:73:09:9a:31:af:14:f7:9a:
    d6:7b:34:fa:a6:38:a6:23:f7:14:46:87:2a:d1:c8:70:7d:56:
    71:1b:69:65:ed:af:64:f2:af:72:c8:e9:d9:94:b1:19:21:fa:
    38:d6:df:53:5e:68:b9:76:60:03:e4:aa:c3:2a:89:70:5a:c6:
    22:24:69:8b:3e:0b:04:3a:50:8c:22:cb:cc:1c:76:a2:82:2b:
    71:7d:68:c8:dd:4f:2c:9f:64:e7:52:53:c6:2a:38:da:90:6e:
    66:4f:25:2b:81:00:7c:55:82:bb:a6:c9
~~~~~

### C509 Type 3 Certificate

- C509 type 3 certificate converted from the X.509 certificate in {{x509-selfsign-rsassa-pss-sha512}}.

[comment]: <> (replace-size:cert/selfsign-rsassa-pss-sha512/c509cert-t3.hex)
Plain hex (436 bytes):

[comment]: <> (replace-data:cert/selfsign-rsassa-pss-sha512/c509cert-t3.hex)
~~~~~
8B03421234181CF61A6775D7001A69570A80781A73656C667369676E2D7273617373
612D7073732D7368613531320058C0B511729186EDC01B2335EC7E46AB1CF3B31FB8
D46133CE350588EFD85ED0876568B7BE006CE3527ABA643A1B0F8FA5EC5402229630
442CFCE3050804CB03FA99E5BC3F8D48B3EEA94B420C26F8DEC7B78E8580A79E29AE
A0DC9BDC5973F89D9B3DB7E8BBEB0BFD357CE710D9EF39E4AB628B861285DB90939F
0D2FD01D8E9BC9717CF4C4E42FB17DCC522C42A9FA000B05FFFBA9E24EE64B991C1E
01824A9D5AFDC37EA11C81AF2A1FA82BD58940AD7D4DB61F8DC688C48A686C45B106
5626D32058C02A2522A8322E9201A54B2D2CB11A3DE35E36903D6626D61CA4FE1EB1
C584836C5A683A48D603AE7E3F5C0D63FF5208F67942BEAF28AB61E71EBE8852A21E
EADA5D612039B0D88076ACD20DD5919A9CE9182073099A31AF14F79AD67B34FAA638
A623F71446872AD1C8707D56711B6965EDAF64F2AF72C8E9D994B11921FA38D6DF53
5E68B9766003E4AAC32A89705AC62224698B3E0B043A508C22CBCC1C76A2822B717D
68C8DD4F2C9F64E75253C62A38DA906E664F252B81007C5582BBA6C9
~~~~~

Annotated hex:

- See the annotated hex for the C509 type 2 certificate in {{c509-selfsign-rsassa-pss-sha512}}. The only differences are the certificate type, the signature value, and the key identifiers.

### C509 Type 2 Certificate {#c509-selfsign-rsassa-pss-sha512}

[comment]: <> (replace-size:cert/selfsign-rsassa-pss-sha512/c509cert-t2.hex)
Plain hex (436 bytes):

[comment]: <> (replace-data:cert/selfsign-rsassa-pss-sha512/c509cert-t2.hex)
~~~~~
8B02421234181CF61A6775D7001A69570A80781A73656C667369676E2D7273617373
612D7073732D7368613531320058C0B511729186EDC01B2335EC7E46AB1CF3B31FB8
D46133CE350588EFD85ED0876568B7BE006CE3527ABA643A1B0F8FA5EC5402229630
442CFCE3050804CB03FA99E5BC3F8D48B3EEA94B420C26F8DEC7B78E8580A79E29AE
A0DC9BDC5973F89D9B3DB7E8BBEB0BFD357CE710D9EF39E4AB628B861285DB90939F
0D2FD01D8E9BC9717CF4C4E42FB17DCC522C42A9FA000B05FFFBA9E24EE64B991C1E
01824A9D5AFDC37EA11C81AF2A1FA82BD58940AD7D4DB61F8DC688C48A686C45B106
5626D32058C0A2218182F9D326F7A5164835FF9B2D24927A5277D9482AB0A729D432
1D66365D58A0DFADDABB6D6D57FF358CFB090DFDFE12EA0D1FCA209808AAFAD0DC4F
24F1ACA12B364B6922B93DD574737BA10B77B1BFF69512C4A35692C03565E19EB8F3
123A3B07063783A08F9AB93FEDCEAB7C2295F47226D4B6ED536E71BB7E671DD9D9BC
C9BF592353C9BCEFFC0B78BC1615F4C53C6B8EF403B606E6D89A3458AA16C786609F
353E40F8EB5BACDA815B1BDDA10132BC8642EBBF6FF5D9AB1A11D272
~~~~~

Annotated hex:

[comment]: <> (replace-data:cert/selfsign-rsassa-pss-sha512/c509cert-t2.diag)
~~~~~
  0: 8B             # C509Certificate=array[11]
  1:   02             # [0]. certificate type=2
  2:   42             # [1]. certificateSerialNumber=byte[2]
  3:     1234
  5:   18 1C          # [2]. signature alg=rsassa-pss-with-sha512
                      #      (28)
  7:   F6             # [3]. issuer=<null>
  8:   1A 6775D700    # [4]. notBefore=1735776000:
                      #      2025-01-02T00:00:00Z
 13:   1A 69570A80    # [5]. notAfter=1767312000:
                      #      2026-01-02T00:00:00Z
 18:   78 1A          # [6]. subject=char[26]
 20:     73656C667369676E2D7273617373612D # "selfsign-rsassa-"
 36:     7073732D736861353132             # "pss-sha512"
 46:   00             # [7]. subjectPublicKeyAlg=RSA (0)
 47:   58 C0          # [8]. subject public key=modulus=byte[192]
 49:     B511729186EDC01B2335EC7E46AB1CF3B31FB8D46133CE350588EFD85E
 78:     D0876568B7BE006CE3527ABA643A1B0F8FA5EC5402229630442CFCE305
107:     0804CB03FA99E5BC3F8D48B3EEA94B420C26F8DEC7B78E8580A79E29AE
136:     A0DC9BDC5973F89D9B3DB7E8BBEB0BFD357CE710D9EF39E4AB628B8612
165:     85DB90939F0D2FD01D8E9BC9717CF4C4E42FB17DCC522C42A9FA000B05
194:     FFFBA9E24EE64B991C1E01824A9D5AFDC37EA11C81AF2A1FA82BD58940
223:     AD7D4DB61F8DC688C48A686C45B1065626D3
241:   20             # [9]. extensions=-1, KeyUsage, critical:
                      #      [digitalSignature]
242:   58 C0          # [10]. signature value=byte[192]
244:     A2218182F9D326F7A5164835FF9B2D24927A5277D9482AB0A729D4321D
273:     66365D58A0DFADDABB6D6D57FF358CFB090DFDFE12EA0D1FCA209808AA
302:     FAD0DC4F24F1ACA12B364B6922B93DD574737BA10B77B1BFF69512C4A3
331:     5692C03565E19EB8F3123A3B07063783A08F9AB93FEDCEAB7C2295F472
360:     26D4B6ED536E71BB7E671DD9D9BCC9BF592353C9BCEFFC0B78BC1615F4
389:     C53C6B8EF403B606E6D89A3458AA16C786609F353E40F8EB5BACDA815B
418:     1BDDA10132BC8642EBBF6FF5D9AB1A11D272
~~~~~

## RSASSA-PSS With SHAKE128 {#selfsign-rsassa-pss-shake128}

- Self-signed certificate
- Signature algorithm: rsassa-pss-with-shake128

### Private Key

See {{key-selfsign-rsa}}.

### X.509 Certificate {#x509-selfsign-rsassa-pss-shake128}

[comment]: <> (replace-size:cert/selfsign-rsassa-pss-shake128/x509cert.pem)
PEM content (469 bytes):

[comment]: <> (replace-data:cert/selfsign-rsassa-pss-shake128/x509cert.pem)
~~~~~
-----BEGIN CERTIFICATE-----
MIIB0TCCAT2gAwIBAgICEjQwCgYIKwYBBQUHBh4wJzElMCMGA1UEAwwcc2VsZnNp
Z24tcnNhc3NhLXBzcy1zaGFrZTEyODAeFw0yNTAxMDIwMDAwMDBaFw0yNjAxMDIw
MDAwMDBaMCcxJTAjBgNVBAMMHHNlbGZzaWduLXJzYXNzYS1wc3Mtc2hha2UxMjgw
gZ8wDQYJKoZIhvcNAQEBBQADgY0AMIGJAoGBALgJL28EcmqSHPqy0xOunS8Bx85G
X6t9pix6XHP6zl/7ovHdgKKa3EM5nPyiInm4miZIEOW5JrteDT9yenY+FgE/ifj+
rFnQ+91eiwxSgn5UkPE7hMNjTonG0XMa5fGmD4jtEY0IDhqyyqUy0GwvfSoIdN7k
5rblcoP2R42vQlPbAgMBAAGjEjAQMA4GA1UdDwEB/wQEAwIHgDAKBggrBgEFBQcG
HgOBgQByM9xppigNp+i9m2mNiHR93BfwLhn0bn9889MUt+khw3LvDPUWnbqUVAj8
DwAFBzSHsZlDFI1gdkEH69Ruu/+fij6pDCjoMyCset+RWZwOWdyP2bHx/JWncxj1
5lAouomFNeD+ixv2eI/sJfiA0nGaVwOJmzrs5cC3wY1MzlW3GQ==
-----END CERTIFICATE-----
~~~~~

Textual Representation:

[comment]: <> (replace-data:cert/selfsign-rsassa-pss-shake128/x509cert.txt)
~~~~~
Certificate:
  Version: v3 (2)
  Serial Number:
    12:34
  Issuer: CN=selfsign-rsassa-pss-shake128
  Validity:
    Not Before: Thu Jan 02 01:00:00 CET 2025
    Not After : Fri Jan 02 01:00:00 CET 2026
  Subject: CN=selfsign-rsassa-pss-shake128
  Subject Public Key Info:
    Public Key Algorithm: 1.2.840.113549.1.1.1
    Pub:
      30:81:89:02:81:81:00:b8:09:2f:6f:04:72:6a:92:1c:fa:b2:
      d3:13:ae:9d:2f:01:c7:ce:46:5f:ab:7d:a6:2c:7a:5c:73:fa:
      ce:5f:fb:a2:f1:dd:80:a2:9a:dc:43:39:9c:fc:a2:22:79:b8:
      9a:26:48:10:e5:b9:26:bb:5e:0d:3f:72:7a:76:3e:16:01:3f:
      89:f8:fe:ac:59:d0:fb:dd:5e:8b:0c:52:82:7e:54:90:f1:3b:
      84:c3:63:4e:89:c6:d1:73:1a:e5:f1:a6:0f:88:ed:11:8d:08:
      0e:1a:b2:ca:a5:32:d0:6c:2f:7d:2a:08:74:de:e4:e6:b6:e5:
      72:83:f6:47:8d:af:42:53:db:02:03:01:00:01
  X509v3 extensions:
    X509v3 keyUsage: critical
      digitalSignature
  Signature Algorithm: SHAKE128WITHRSAPSS
  Signature Value:
    72:33:dc:69:a6:28:0d:a7:e8:bd:9b:69:8d:88:74:7d:dc:17:
    f0:2e:19:f4:6e:7f:7c:f3:d3:14:b7:e9:21:c3:72:ef:0c:f5:
    16:9d:ba:94:54:08:fc:0f:00:05:07:34:87:b1:99:43:14:8d:
    60:76:41:07:eb:d4:6e:bb:ff:9f:8a:3e:a9:0c:28:e8:33:20:
    ac:7a:df:91:59:9c:0e:59:dc:8f:d9:b1:f1:fc:95:a7:73:18:
    f5:e6:50:28:ba:89:85:35:e0:fe:8b:1b:f6:78:8f:ec:25:f8:
    80:d2:71:9a:57:03:89:9b:3a:ec:e5:c0:b7:c1:8d:4c:ce:55:
    b7:19
~~~~~

### C509 Type 3 Certificate

- C509 type 3 certificate converted from the X.509 certificate in {{x509-selfsign-rsassa-pss-shake128}}.

[comment]: <> (replace-size:cert/selfsign-rsassa-pss-shake128/c509cert-t3.hex)
Plain hex (310 bytes):

[comment]: <> (replace-data:cert/selfsign-rsassa-pss-shake128/c509cert-t3.hex)
~~~~~
8B03421234181DF61A6775D7001A69570A80781C73656C667369676E2D7273617373
612D7073732D7368616B65313238005880B8092F6F04726A921CFAB2D313AE9D2F01
C7CE465FAB7DA62C7A5C73FACE5FFBA2F1DD80A29ADC43399CFCA22279B89A264810
E5B926BB5E0D3F727A763E16013F89F8FEAC59D0FBDD5E8B0C52827E5490F13B84C3
634E89C6D1731AE5F1A60F88ED118D080E1AB2CAA532D06C2F7D2A0874DEE4E6B6E5
7283F6478DAF4253DB2058807233DC69A6280DA7E8BD9B698D88747DDC17F02E19F4
6E7F7CF3D314B7E921C372EF0CF5169DBA945408FC0F0005073487B19943148D6076
4107EBD46EBBFF9F8A3EA90C28E83320AC7ADF91599C0E59DC8FD9B1F1FC95A77318
F5E65028BA898535E0FE8B1BF6788FEC25F880D2719A5703899B3AECE5C0B7C18D4C
CE55B719
~~~~~

Annotated hex:

- See the annotated hex for the C509 type 2 certificate in {{c509-selfsign-rsassa-pss-shake128}}. The only differences are the certificate type, the signature value, and the key identifiers.

### C509 Type 2 Certificate {#c509-selfsign-rsassa-pss-shake128}

[comment]: <> (replace-size:cert/selfsign-rsassa-pss-shake128/c509cert-t2.hex)
Plain hex (310 bytes):

[comment]: <> (replace-data:cert/selfsign-rsassa-pss-shake128/c509cert-t2.hex)
~~~~~
8B02421234181DF61A6775D7001A69570A80781C73656C667369676E2D7273617373
612D7073732D7368616B65313238005880B8092F6F04726A921CFAB2D313AE9D2F01
C7CE465FAB7DA62C7A5C73FACE5FFBA2F1DD80A29ADC43399CFCA22279B89A264810
E5B926BB5E0D3F727A763E16013F89F8FEAC59D0FBDD5E8B0C52827E5490F13B84C3
634E89C6D1731AE5F1A60F88ED118D080E1AB2CAA532D06C2F7D2A0874DEE4E6B6E5
7283F6478DAF4253DB20588006B4F24DEFA5DC3C58E8C0B8E30A03C43A43A42B6EAD
06458EE0FFB5EAA443204DA030DFD19BCDCA2D5C0B4D6C848B5F9EC444C39CDF4C72
63887D922AE17D8989A5F2046E6B4D2D9F114BA960DC55DFFFF775F9481F580DAD43
A984BAE37A650297C563C9AAA24CBFC3086BBCD6CAEE405E23EDC9104DD16F653B47
C9EB6B31
~~~~~

Annotated hex:

[comment]: <> (replace-data:cert/selfsign-rsassa-pss-shake128/c509cert-t2.diag)
~~~~~
  0: 8B             # C509Certificate=array[11]
  1:   02             # [0]. certificate type=2
  2:   42             # [1]. certificateSerialNumber=byte[2]
  3:     1234
  5:   18 1D          # [2]. signature alg=rsassa-pss-with-shake128
                      #      (29)
  7:   F6             # [3]. issuer=<null>
  8:   1A 6775D700    # [4]. notBefore=1735776000:
                      #      2025-01-02T00:00:00Z
 13:   1A 69570A80    # [5]. notAfter=1767312000:
                      #      2026-01-02T00:00:00Z
 18:   78 1C          # [6]. subject=char[28]
 20:     73656C667369676E2D7273617373612D # "selfsign-rsassa-"
 36:     7073732D7368616B65313238         # "pss-shake128"
 48:   00             # [7]. subjectPublicKeyAlg=RSA (0)
 49:   58 80          # [8]. subject public key=modulus=byte[128]
 51:     B8092F6F04726A921CFAB2D313AE9D2F01C7CE465FAB7DA62C7A5C73FA
 80:     CE5FFBA2F1DD80A29ADC43399CFCA22279B89A264810E5B926BB5E0D3F
109:     727A763E16013F89F8FEAC59D0FBDD5E8B0C52827E5490F13B84C3634E
138:     89C6D1731AE5F1A60F88ED118D080E1AB2CAA532D06C2F7D2A0874DEE4
167:     E6B6E57283F6478DAF4253DB
179:   20             # [9]. extensions=-1, KeyUsage, critical:
                      #      [digitalSignature]
180:   58 80          # [10]. signature value=byte[128]
182:     06B4F24DEFA5DC3C58E8C0B8E30A03C43A43A42B6EAD06458EE0FFB5EA
211:     A443204DA030DFD19BCDCA2D5C0B4D6C848B5F9EC444C39CDF4C726388
240:     7D922AE17D8989A5F2046E6B4D2D9F114BA960DC55DFFFF775F9481F58
269:     0DAD43A984BAE37A650297C563C9AAA24CBFC3086BBCD6CAEE405E23ED
298:     C9104DD16F653B47C9EB6B31
~~~~~

## RSASSA-PSS With SHAKE256 {#selfsign-rsassa-pss-shake256}

- Self-signed certificate
- Signature algorithm: rsassa-pss-with-shake256

### Private Key

See {{key-selfsign-rsassa-pss-sha512}}.

### X.509 Certificate {#x509-selfsign-rsassa-pss-shake256}

[comment]: <> (replace-size:cert/selfsign-rsassa-pss-shake256/x509cert.pem)
PEM content (597 bytes):

[comment]: <> (replace-data:cert/selfsign-rsassa-pss-shake256/x509cert.pem)
~~~~~
-----BEGIN CERTIFICATE-----
MIICUTCCAX2gAwIBAgICEjQwCgYIKwYBBQUHBh8wJzElMCMGA1UEAwwcc2VsZnNp
Z24tcnNhc3NhLXBzcy1zaGFrZTI1NjAeFw0yNTAxMDIwMDAwMDBaFw0yNjAxMDIw
MDAwMDBaMCcxJTAjBgNVBAMMHHNlbGZzaWduLXJzYXNzYS1wc3Mtc2hha2UyNTYw
gd8wDQYJKoZIhvcNAQEBBQADgc0AMIHJAoHBALURcpGG7cAbIzXsfkarHPOzH7jU
YTPONQWI79he0IdlaLe+AGzjUnq6ZDobD4+l7FQCIpYwRCz84wUIBMsD+pnlvD+N
SLPuqUtCDCb43se3joWAp54prqDcm9xZc/idmz236LvrC/01fOcQ2e855Ktii4YS
hduQk58NL9AdjpvJcXz0xOQvsX3MUixCqfoACwX/+6niTuZLmRweAYJKnVr9w36h
HIGvKh+oK9WJQK19TbYfjcaIxIpobEWxBlYm0wIDAQABoxIwEDAOBgNVHQ8BAf8E
BAMCB4AwCgYIKwYBBQUHBh8DgcEAjurY2mbPMZ4lE99aj8wWiiQhOvUIfCaGVyTW
sYWSkNvvtn/SuszOwwD3u4GCIH7fP66LRYWthFw/3ZTpT82lqdJSfrJe1BnZ7a0r
aarrQN1eJEdJNpeF7GdJinsRXUzgDpVCYifjiBnufr3gE3Eygsso/Pr1Q/XvQ/OK
nnCj+vQQDSOXCkEuTHGZ9cgc9kczlvP3MZmg6OLZ2KgtdgFzRJCT39JeD3p3lyn7
j/mfBk9ZxL3vWY2gR5eDwgXvIdmK
-----END CERTIFICATE-----
~~~~~

Textual Representation:

[comment]: <> (replace-data:cert/selfsign-rsassa-pss-shake256/x509cert.txt)
~~~~~
Certificate:
  Version: v3 (2)
  Serial Number:
    12:34
  Issuer: CN=selfsign-rsassa-pss-shake256
  Validity:
    Not Before: Thu Jan 02 01:00:00 CET 2025
    Not After : Fri Jan 02 01:00:00 CET 2026
  Subject: CN=selfsign-rsassa-pss-shake256
  Subject Public Key Info:
    Public Key Algorithm: 1.2.840.113549.1.1.1
    Pub:
      30:81:c9:02:81:c1:00:b5:11:72:91:86:ed:c0:1b:23:35:ec:
      7e:46:ab:1c:f3:b3:1f:b8:d4:61:33:ce:35:05:88:ef:d8:5e:
      d0:87:65:68:b7:be:00:6c:e3:52:7a:ba:64:3a:1b:0f:8f:a5:
      ec:54:02:22:96:30:44:2c:fc:e3:05:08:04:cb:03:fa:99:e5:
      bc:3f:8d:48:b3:ee:a9:4b:42:0c:26:f8:de:c7:b7:8e:85:80:
      a7:9e:29:ae:a0:dc:9b:dc:59:73:f8:9d:9b:3d:b7:e8:bb:eb:
      0b:fd:35:7c:e7:10:d9:ef:39:e4:ab:62:8b:86:12:85:db:90:
      93:9f:0d:2f:d0:1d:8e:9b:c9:71:7c:f4:c4:e4:2f:b1:7d:cc:
      52:2c:42:a9:fa:00:0b:05:ff:fb:a9:e2:4e:e6:4b:99:1c:1e:
      01:82:4a:9d:5a:fd:c3:7e:a1:1c:81:af:2a:1f:a8:2b:d5:89:
      40:ad:7d:4d:b6:1f:8d:c6:88:c4:8a:68:6c:45:b1:06:56:26:
      d3:02:03:01:00:01
  X509v3 extensions:
    X509v3 keyUsage: critical
      digitalSignature
  Signature Algorithm: SHAKE256WITHRSAPSS
  Signature Value:
    8e:ea:d8:da:66:cf:31:9e:25:13:df:5a:8f:cc:16:8a:24:21:
    3a:f5:08:7c:26:86:57:24:d6:b1:85:92:90:db:ef:b6:7f:d2:
    ba:cc:ce:c3:00:f7:bb:81:82:20:7e:df:3f:ae:8b:45:85:ad:
    84:5c:3f:dd:94:e9:4f:cd:a5:a9:d2:52:7e:b2:5e:d4:19:d9:
    ed:ad:2b:69:aa:eb:40:dd:5e:24:47:49:36:97:85:ec:67:49:
    8a:7b:11:5d:4c:e0:0e:95:42:62:27:e3:88:19:ee:7e:bd:e0:
    13:71:32:82:cb:28:fc:fa:f5:43:f5:ef:43:f3:8a:9e:70:a3:
    fa:f4:10:0d:23:97:0a:41:2e:4c:71:99:f5:c8:1c:f6:47:33:
    96:f3:f7:31:99:a0:e8:e2:d9:d8:a8:2d:76:01:73:44:90:93:
    df:d2:5e:0f:7a:77:97:29:fb:8f:f9:9f:06:4f:59:c4:bd:ef:
    59:8d:a0:47:97:83:c2:05:ef:21:d9:8a
~~~~~

### C509 Type 3 Certificate

- C509 type 3 certificate converted from the X.509 certificate in {{x509-selfsign-rsassa-pss-shake256}}.

[comment]: <> (replace-size:cert/selfsign-rsassa-pss-shake256/c509cert-t3.hex)
Plain hex (438 bytes):

[comment]: <> (replace-data:cert/selfsign-rsassa-pss-shake256/c509cert-t3.hex)
~~~~~
8B03421234181EF61A6775D7001A69570A80781C73656C667369676E2D7273617373
612D7073732D7368616B653235360058C0B511729186EDC01B2335EC7E46AB1CF3B3
1FB8D46133CE350588EFD85ED0876568B7BE006CE3527ABA643A1B0F8FA5EC540222
9630442CFCE3050804CB03FA99E5BC3F8D48B3EEA94B420C26F8DEC7B78E8580A79E
29AEA0DC9BDC5973F89D9B3DB7E8BBEB0BFD357CE710D9EF39E4AB628B861285DB90
939F0D2FD01D8E9BC9717CF4C4E42FB17DCC522C42A9FA000B05FFFBA9E24EE64B99
1C1E01824A9D5AFDC37EA11C81AF2A1FA82BD58940AD7D4DB61F8DC688C48A686C45
B1065626D32058C08EEAD8DA66CF319E2513DF5A8FCC168A24213AF5087C26865724
D6B1859290DBEFB67FD2BACCCEC300F7BB8182207EDF3FAE8B4585AD845C3FDD94E9
4FCDA5A9D2527EB25ED419D9EDAD2B69AAEB40DD5E244749369785EC67498A7B115D
4CE00E95426227E38819EE7EBDE013713282CB28FCFAF543F5EF43F38A9E70A3FAF4
100D23970A412E4C7199F5C81CF6473396F3F73199A0E8E2D9D8A82D760173449093
DFD25E0F7A779729FB8FF99F064F59C4BDEF598DA0479783C205EF21D98A
~~~~~

Annotated hex:

- See the annotated hex for the C509 type 2 certificate in {{c509-selfsign-rsassa-pss-shake256}}. The only differences are the certificate type, the signature value, and the key identifiers.

### C509 Type 2 Certificate {#c509-selfsign-rsassa-pss-shake256}

[comment]: <> (replace-size:cert/selfsign-rsassa-pss-shake256/c509cert-t2.hex)
Plain hex (438 bytes):

[comment]: <> (replace-data:cert/selfsign-rsassa-pss-shake256/c509cert-t2.hex)
~~~~~
8B02421234181EF61A6775D7001A69570A80781C73656C667369676E2D7273617373
612D7073732D7368616B653235360058C0B511729186EDC01B2335EC7E46AB1CF3B3
1FB8D46133CE350588EFD85ED0876568B7BE006CE3527ABA643A1B0F8FA5EC540222
9630442CFCE3050804CB03FA99E5BC3F8D48B3EEA94B420C26F8DEC7B78E8580A79E
29AEA0DC9BDC5973F89D9B3DB7E8BBEB0BFD357CE710D9EF39E4AB628B861285DB90
939F0D2FD01D8E9BC9717CF4C4E42FB17DCC522C42A9FA000B05FFFBA9E24EE64B99
1C1E01824A9D5AFDC37EA11C81AF2A1FA82BD58940AD7D4DB61F8DC688C48A686C45
B1065626D32058C03C5A7DBA06D0918EB0397D881C60312E0668171E2644F9E30E05
DC76231AF177C8E1B460A763B31B7B869F2070602BB5749D627A7074973D4D49ADF9
A282C506101713DD246B92AD47D2A8A914891538670F8F38F32B4C39A87C5B4FF1DF
BF7F00A6353F199F885EA95172C334B61335A46D9DE493D2A1DB40B7CF7F39E6297D
951CC35D459B911A591EF16511D9470C861320B6559A138D1F4AE6B4FF8E493A3B9C
5150B123FEB2FB84B5FDE60CE4FBC5FA74E4E1B9CCDAA8F2A8D4CF574263
~~~~~

Annotated hex:

[comment]: <> (replace-data:cert/selfsign-rsassa-pss-shake256/c509cert-t2.diag)
~~~~~
  0: 8B             # C509Certificate=array[11]
  1:   02             # [0]. certificate type=2
  2:   42             # [1]. certificateSerialNumber=byte[2]
  3:     1234
  5:   18 1E          # [2]. signature alg=rsassa-pss-with-shake256
                      #      (30)
  7:   F6             # [3]. issuer=<null>
  8:   1A 6775D700    # [4]. notBefore=1735776000:
                      #      2025-01-02T00:00:00Z
 13:   1A 69570A80    # [5]. notAfter=1767312000:
                      #      2026-01-02T00:00:00Z
 18:   78 1C          # [6]. subject=char[28]
 20:     73656C667369676E2D7273617373612D # "selfsign-rsassa-"
 36:     7073732D7368616B65323536         # "pss-shake256"
 48:   00             # [7]. subjectPublicKeyAlg=RSA (0)
 49:   58 C0          # [8]. subject public key=modulus=byte[192]
 51:     B511729186EDC01B2335EC7E46AB1CF3B31FB8D46133CE350588EFD85E
 80:     D0876568B7BE006CE3527ABA643A1B0F8FA5EC5402229630442CFCE305
109:     0804CB03FA99E5BC3F8D48B3EEA94B420C26F8DEC7B78E8580A79E29AE
138:     A0DC9BDC5973F89D9B3DB7E8BBEB0BFD357CE710D9EF39E4AB628B8612
167:     85DB90939F0D2FD01D8E9BC9717CF4C4E42FB17DCC522C42A9FA000B05
196:     FFFBA9E24EE64B991C1E01824A9D5AFDC37EA11C81AF2A1FA82BD58940
225:     AD7D4DB61F8DC688C48A686C45B1065626D3
243:   20             # [9]. extensions=-1, KeyUsage, critical:
                      #      [digitalSignature]
244:   58 C0          # [10]. signature value=byte[192]
246:     3C5A7DBA06D0918EB0397D881C60312E0668171E2644F9E30E05DC7623
275:     1AF177C8E1B460A763B31B7B869F2070602BB5749D627A7074973D4D49
304:     ADF9A282C506101713DD246B92AD47D2A8A914891538670F8F38F32B4C
333:     39A87C5B4FF1DFBF7F00A6353F199F885EA95172C334B61335A46D9DE4
362:     93D2A1DB40B7CF7F39E6297D951CC35D459B911A591EF16511D9470C86
391:     1320B6559A138D1F4AE6B4FF8E493A3B9C5150B123FEB2FB84B5FDE60C
420:     E4FBC5FA74E4E1B9CCDAA8F2A8D4CF574263
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
PEM content (387 bytes)

[comment]: <> (replace-data:cert/unconvertible-secp256r1/x509cert.pem)
~~~~~
-----BEGIN CERTIFICATE-----
MIIBfzCCASWgAwIBAgIBATAKBggqhkjOPQQDAjAiMSAwHgYDVQQDFhdBbiBJQTVT
dHJpbmcgQ29tbW9uTmFtZTAeFw0yNTAxMDIwMDAwMDBaFw0yNjAxMDIwMDAwMDBa
MCIxIDAeBgNVBAMWF0FuIElBNVN0cmluZyBDb21tb25OYW1lMFowFAYHKoZIzj0C
AQYJKwYBBAGB/VkFA0IABPQTWWqHElmVtODYt777xNbtsR9hrwirMkCNT/n5B43b
qzY1r9SW1WVqIu/cPVnESCqZg2uzWPv0ynjTkwQ2yFejSzBJMBIGCCsGAQUFBwEI
BAYwBKECBQAwMwYDVR0eBCwwKqAoMCakJDAiMSAwHgYDVQQDFhdBbiBJQTVTdHJp
bmcgQ29tbW9uTmFtZTAKBggqhkjOPQQDAgNIADBFAiEAiiXoqruksZuODRWWpHbC
xC9QaPXzRXYGgG4vKEoi5ucCIE94fU4E/kti3t2ogAd9ycsqXXodjtYjCc1GfmGF
TcMK
-----END CERTIFICATE-----
~~~~~

Textual Representation:

[comment]: <> (replace-data:cert/unconvertible-secp256r1/x509cert.txt)
~~~~~
Certificate:
  Version: v3 (2)
  Serial Number:
    01
  Issuer: CN=An IA5String CommonName
  Validity:
    Not Before: Thu Jan 02 01:00:00 CET 2025
    Not After : Fri Jan 02 01:00:00 CET 2026
  Subject: CN=An IA5String CommonName
  Subject Public Key Info:
    Public Key Algorithm: 1.2.840.10045.2.1
    Pub:
      04:f4:13:59:6a:87:12:59:95:b4:e0:d8:b7:be:fb:c4:d6:ed:
      b1:1f:61:af:08:ab:32:40:8d:4f:f9:f9:07:8d:db:ab:36:35:
      af:d4:96:d5:65:6a:22:ef:dc:3d:59:c4:48:2a:99:83:6b:b3:
      58:fb:f4:ca:78:d3:93:04:36:c8:57
  X509v3 extensions:
    X509v3 sbgp-autonomousSysNum:
      Routing Domain Identifier (RDI): inherit
    X509v3 nameConstraints:
      Permitted
        Directory Name: CN=An IA5String CommonName
  Signature Algorithm: SHA256WITHECDSA
  Signature Value:
    30:45:02:21:00:8a:25:e8:aa:bb:a4:b1:9b:8e:0d:15:96:a4:
    76:c2:c4:2f:50:68:f5:f3:45:76:06:80:6e:2f:28:4a:22:e6:
    e7:02:20:4f:78:7d:4e:04:fe:4b:62:de:dd:a8:80:07:7d:c9:
    cb:2a:5d:7a:1d:8e:d6:23:09:cd:46:7e:61:85:4d:c3:0a
~~~~~

Text representation:

[comment]: <> (replace-data:cert/unconvertible-secp256r1/x509cert.txt)
~~~~~
Certificate:
  Version: v3 (2)
  Serial Number:
    01
  Issuer: CN=An IA5String CommonName
  Validity:
    Not Before: Thu Jan 02 01:00:00 CET 2025
    Not After : Fri Jan 02 01:00:00 CET 2026
  Subject: CN=An IA5String CommonName
  Subject Public Key Info:
    Public Key Algorithm: 1.2.840.10045.2.1
    Pub:
      04:f4:13:59:6a:87:12:59:95:b4:e0:d8:b7:be:fb:c4:d6:ed:
      b1:1f:61:af:08:ab:32:40:8d:4f:f9:f9:07:8d:db:ab:36:35:
      af:d4:96:d5:65:6a:22:ef:dc:3d:59:c4:48:2a:99:83:6b:b3:
      58:fb:f4:ca:78:d3:93:04:36:c8:57
  X509v3 extensions:
    X509v3 sbgp-autonomousSysNum:
      Routing Domain Identifier (RDI): inherit
    X509v3 nameConstraints:
      Permitted
        Directory Name: CN=An IA5String CommonName
  Signature Algorithm: SHA256WITHECDSA
  Signature Value:
    30:45:02:21:00:8a:25:e8:aa:bb:a4:b1:9b:8e:0d:15:96:a4:
    76:c2:c4:2f:50:68:f5:f3:45:76:06:80:6e:2f:28:4a:22:e6:
    e7:02:20:4f:78:7d:4e:04:fe:4b:62:de:dd:a8:80:07:7d:c9:
    cb:2a:5d:7a:1d:8e:d6:23:09:cd:46:7e:61:85:4d:c3:0a
~~~~~

## C509 Type 3 Certificate

- C509 type 3 certificate converted from the X.509 certificate in {{x509-unconvertible}}.

[comment]: <> (replace-size:cert/unconvertible-secp256r1/c509cert-t3.hex)
Plain hex (257 bytes):

[comment]: <> (replace-data:cert/unconvertible-secp256r1/c509cert-t3.hex)
~~~~~
8B03410100F61A6775D7001A69570A80824355040358191617416E20494135537472
696E6720436F6D6D6F6E4E616D6582472A8648CE3D02014B06092B0601040181FD59
05584104F413596A87125995B4E0D8B7BEFBC4D6EDB11F61AF08AB32408D4FF9F907
8DDBAB3635AFD496D5656A22EFDC3D59C4482A99836BB358FBF4CA78D3930436C857
84482B06010505070108463004A1020500181A828204824355040358191617416E20
494135537472696E6720436F6D6D6F6E4E616D65F658408A25E8AABBA4B19B8E0D15
96A476C2C42F5068F5F3457606806E2F284A22E6E74F787D4E04FE4B62DEDDA88007
7DC9CB2A5D7A1D8ED62309CD467E61854DC30A
~~~~~

Annotated hex:

[comment]: <> (replace-data:cert/unconvertible-secp256r1/c509cert-t3.diag)
~~~~~
  0: 8B             # C509Certificate=array[11]
  1:   03             # [0]. certificate type=3
  2:   41             # [1]. certificateSerialNumber=byte[1]
  3:     01
  4:   00             # [2]. signature alg=ecdsa-with-sha256 (0)
  5:   F6             # [3]. issuer=<null>
  6:   1A 6775D700    # [4]. notBefore=1735776000:
                      #      2025-01-02T00:00:00Z
 11:   1A 69570A80    # [5]. notAfter=1767312000:
                      #      2026-01-02T00:00:00Z
 16:   82             # [6]. subject=array[2], 1 attribute
                        #---attribute[0]---
 17:     43             # [0]. type=byte[3]: 
 18:       550403         # oid: 2.5.4.3 (commonName)
 21:     58 19          # [1]. value=byte[25]
 23:       1617416E20494135537472696E6720436F6D6D6F6E4E616D65
 48:   82             # [7]. subjectPublicKeyAlg=array[2]
 49:     47             # [0]. algorithm=byte[7]: 
 50:       2A8648CE3D0201 # oid: 1.2.840.10045.2.1
 57:     4B             # [1]. parameters=byte[11]
 58:       06092B0601040181FD5905
 69:   58 41          # [8]. subject public key=byte[65]
 71:     04F413596A87125995B4E0D8B7BEFBC4D6EDB11F61AF08AB32408D4FF9
100:     F9078DDBAB3635AFD496D5656A22EFDC3D59C4482A99836BB358FBF4CA
129:     78D3930436C857
136:   84             # [9]. extensions=array[4]
                        #---extension[0]---
137:     48             # [0]. type=byte[8]: 
138:       2B06010505070108 # oid: 1.3.6.1.5.5.7.1.8 (ASIdentifiers)
146:     46             # [1]. value=byte[6]
147:       3004A1020500
                        #---extension[1]---
153:     18 1A          # [2]. type=NameConstraints (26)
155:     82             # [3]. value=array[2]
156:       82             # [0]. permittedSubtrees=array[2]
                            #---GeneralName[0]---
157:         04             # [0]. GeneralNameType=4: directoryName
158:         82             # [1]. GeneralNameValue=array[2], 1
                            #      attribute
                              #---attribute[0]---
159:           43             # [0]. type=byte[3]: 
160:             550403         # oid: 2.5.4.3 (commonName)
163:           58 19          # [1]. value=byte[25]
165:             1617416E20494135537472696E6720436F6D6D6F6E4E616D65
190:       F6             # [1]. excludedSubtrees=<null>
191:   58 40          # [10]. signature value=byte[64]
193:     8A25E8AABBA4B19B8E0D1596A476C2C42F5068F5F3457606806E2F284A
222:     22E6E74F787D4E04FE4B62DEDDA880077DC9CB2A5D7A1D8ED62309CD46
251:     7E61854DC30A
~~~~~

# Certification Requests With Different Signature Algorithms {#sec-csr-different-sigs}

## ECDSA With SHA256 {#csr-ecdsa-p256}

- Signature algorithm: ecdsa-with-sha256
- CR attributes: one `extensionRequest` attribute.

### Private Key

See {{key-selfsign-secp256r1}}.

### X.509 Certification Request {#x509csr-ecdsa-p256}

[comment]: <> (replace-size:csr/ecdsa-p256/x509csr.pem)
PEM content (253 bytes):

[comment]: <> (replace-data:csr/ecdsa-p256/x509csr.pem)
~~~~~
-----BEGIN CERTIFICATE REQUEST-----
MIH6MIGgAgEAMBUxEzARBgNVBAMMCmVjZHNhLXAyNTYwWTATBgcqhkjOPQIBBggq
hkjOPQMBBwNCAAT0E1lqhxJZlbTg2Le++8TW7bEfYa8IqzJAjU/5+QeN26s2Na/U
ltVlaiLv3D1ZxEgqmYNrs1j79Mp405MENshXoCkwJwYJKoZIhvcNAQkOMRowGDAW
BgNVHREEDzANggtleGFtcGxlLmNvbTAKBggqhkjOPQQDAgNJADBGAiEAiiXoqruk
sZuODRWWpHbCxC9QaPXzRXYGgG4vKEoi5ucCIQDV7zUSEnMONXpslIgD2G+1pK+m
0zKj5RlqeIHj1cykkA==
-----END CERTIFICATE REQUEST-----
~~~~~

Text representation:

[comment]: <> (replace-data:csr/ecdsa-p256/x509csr.txt)
~~~~~
Certificate Request:
  Data:
    Version: v1 (0)
    Subject: CN=ecdsa-p256
    Subject Public Key Info:
      Public Key Algorithm: EC/P256
      Pub:
        04:f4:13:59:6a:87:12:59:95:b4:e0:d8:b7:be:fb:c4:d6:ed:
        b1:1f:61:af:08:ab:32:40:8d:4f:f9:f9:07:8d:db:ab:36:35:
        af:d4:96:d5:65:6a:22:ef:dc:3d:59:c4:48:2a:99:83:6b:b3:
        58:fb:f4:ca:78:d3:93:04:36:c8:57
    Attributes:
      X509v3 extensions:
        X509v3 subjectAlternativeName:
          DNS: example.com
  Signature Algorithm: SHA256WITHECDSA
  Signature Value:
    30:46:02:21:00:8a:25:e8:aa:bb:a4:b1:9b:8e:0d:15:96:a4:
    76:c2:c4:2f:50:68:f5:f3:45:76:06:80:6e:2f:28:4a:22:e6:
    e7:02:21:00:d5:ef:35:12:12:73:0e:35:7a:6c:94:88:03:d8:
    6f:b5:a4:af:a6:d3:32:a3:e5:19:6a:78:81:e3:d5:cc:a4:90
~~~~~

### C509 Type 3 Certification Request

- C509 type 3 certification request converted from the X.509 certification request in {{x509csr-ecdsa-p256}}.

[comment]: <> (replace-size:csr/ecdsa-p256/c509csr-t3.hex)
Plain hex (164 bytes):

[comment]: <> (replace-data:csr/ecdsa-p256/c509csr-t3.hex)
~~~~~
8703006A65636473612D7032353601584104F413596A87125995B4E0D8B7BEFBC4D6
EDB11F61AF08AB32408D4FF9F9078DDBAB3635AFD496D5656A22EFDC3D59C4482A99
836BB358FBF4CA78D3930436C857820082036B6578616D706C652E636F6D58408A25
E8AABBA4B19B8E0D1596A476C2C42F5068F5F3457606806E2F284A22E6E7D5EF3512
12730E357A6C948803D86FB5A4AFA6D332A3E5196A7881E3D5CCA490
~~~~~

Annotated hex:

- See the annotated hex for the C509 type 2 certification request in {{c509csr-ecdsa-p256}}. The only differences are the certification request type and the signature value.

### C509 Type 2 Certification Request {#c509csr-ecdsa-p256}

[comment]: <> (replace-size:csr/ecdsa-p256/c509csr-t2.hex)
Plain hex (164 bytes):

[comment]: <> (replace-data:csr/ecdsa-p256/c509csr-t2.hex)
~~~~~
8702006A65636473612D7032353601584104F413596A87125995B4E0D8B7BEFBC4D6
EDB11F61AF08AB32408D4FF9F9078DDBAB3635AFD496D5656A22EFDC3D59C4482A99
836BB358FBF4CA78D3930436C857820082036B6578616D706C652E636F6D58408A25
E8AABBA4B19B8E0D1596A476C2C42F5068F5F3457606806E2F284A22E6E7E355FB37
F434A895FDC41C015EF9924F0B96C6EB92FC51990D3878536B23FC8C
~~~~~

Annotated hex:

[comment]: <> (replace-data:csr/ecdsa-p256/c509csr-t2.diag)
~~~~~
  0: 87             # C509CertificationRequest=array[7]
  1:   02             # [0]. c509CertificationRequestType=2
  2:   00             # [1]. subjectSignatureAlgorithm=ecdsa-with-sh
                      #      a256 (0)
  3:   6A             # [2]. subject=char[10]
  4:     65636473612D70323536 # "ecdsa-p256"
 14:   01             # [3]. subjectPublicKeyAlg=EC public key on
                      #      curve secp256r1 (1)
 15:   58 41          # [4]. subject public key=EC point=byte[65]
 17:     04F413596A87125995B4E0D8B7BEFBC4D6EDB11F61AF08AB32408D4FF9
 46:     F9078DDBAB3635AFD496D5656A22EFDC3D59C4482A99836BB358FBF4CA
 75:     78D3930436C857
 82:   82             # [5]. attributes=array[2]
                        #---CRAttributes[0]---
 83:     00             # [0]. type=ExtensionRequest (0)
 84:     82             # [1]. value: extensions=array[2]
                          #---extension[0]---
 85:       03             # [0]. type=SubjectAlternativeName (3)
 86:       6B             # [1]. value: DNS, value=char[11]
 87:         6578616D706C652E636F6D # "example.com"
 98:   58 40          # [6]. signature value=byte[64]
100:     8A25E8AABBA4B19B8E0D1596A476C2C42F5068F5F3457606806E2F284A
129:     22E6E7E355FB37F434A895FDC41C015EF9924F0B96C6EB92FC51990D38
158:     78536B23FC8C
~~~~~

## ECDSA With SHA384 {#csr-ecdsa-p384}

- Signature algorithm: ecdsa-with-sha384
- CR attributes: one `challengePassword` attribute with a PrintableString value.

### Private Key

See {{key-selfsign-secp384r1}}.

### X.509 Certification Request {#x509csr-ecdsa-p384}

[comment]: <> (replace-size:csr/ecdsa-p384/x509csr.pem)
PEM content (300 bytes):

[comment]: <> (replace-data:csr/ecdsa-p384/x509csr.pem)
~~~~~
-----BEGIN CERTIFICATE REQUEST-----
MIIBKDCBsAIBADAVMRMwEQYDVQQDDAplY2RzYS1wMzg0MHYwEAYHKoZIzj0CAQYF
K4EEACIDYgAE3Wdi8DWJlFE3Ky/ptSqDFK0Q4sQ2PFpYSeKW/lGqub/QOrA40zQY
oLzYMoCroL2RBAFxZcBItTRrVBCeRJ/MiJ5O6HC1+KLGPa5BTeGXVb4nihdeBA1K
TBoO5G6SSlwpoBwwGgYJKoZIhvcNAQkHMQ0TC215IHBhc3N3b3JkMAoGCCqGSM49
BAMDA2cAMGQCMDR+te1J4fZTbio/O13xwS2brvRA37xCvUXTH0s/2r36nWhahFgt
mLGyR0IQ8ceF2gIwY8kdAQd6yDL0bKUsrMs6bB1gvdqBMFj7a4Za0riFSStpfUUP
buBrmI16HsX89Kam
-----END CERTIFICATE REQUEST-----
~~~~~

Text representation:

[comment]: <> (replace-data:csr/ecdsa-p384/x509csr.txt)
~~~~~
Certificate Request:
  Data:
    Version: v1 (0)
    Subject: CN=ecdsa-p384
    Subject Public Key Info:
      Public Key Algorithm: EC/P384
      Pub:
        04:dd:67:62:f0:35:89:94:51:37:2b:2f:e9:b5:2a:83:14:ad:
        10:e2:c4:36:3c:5a:58:49:e2:96:fe:51:aa:b9:bf:d0:3a:b0:
        38:d3:34:18:a0:bc:d8:32:80:ab:a0:bd:91:04:01:71:65:c0:
        48:b5:34:6b:54:10:9e:44:9f:cc:88:9e:4e:e8:70:b5:f8:a2:
        c6:3d:ae:41:4d:e1:97:55:be:27:8a:17:5e:04:0d:4a:4c:1a:
        0e:e4:6e:92:4a:5c:29
    Attributes:
      challengePassword: my password
  Signature Algorithm: SHA384WITHECDSA
  Signature Value:
    30:64:02:30:34:7e:b5:ed:49:e1:f6:53:6e:2a:3f:3b:5d:f1:
    c1:2d:9b:ae:f4:40:df:bc:42:bd:45:d3:1f:4b:3f:da:bd:fa:
    9d:68:5a:84:58:2d:98:b1:b2:47:42:10:f1:c7:85:da:02:30:
    63:c9:1d:01:07:7a:c8:32:f4:6c:a5:2c:ac:cb:3a:6c:1d:60:
    bd:da:81:30:58:fb:6b:86:5a:d2:b8:85:49:2b:69:7d:45:0f:
    6e:e0:6b:98:8d:7a:1e:c5:fc:f4:a6:a6
~~~~~

### C509 Type 3 Certification Request

- C509 type 3 certification request converted from the X.509 certification request in {{x509csr-ecdsa-p384}}.

[comment]: <> (replace-size:csr/ecdsa-p384/c509csr-t3.hex)
Plain hex (228 bytes):

[comment]: <> (replace-data:csr/ecdsa-p384/c509csr-t3.hex)
~~~~~
8703016A65636473612D7033383402586104DD6762F035899451372B2FE9B52A8314
AD10E2C4363C5A5849E296FE51AAB9BFD03AB038D33418A0BCD83280ABA0BD910401
7165C048B5346B54109E449FCC889E4EE870B5F8A2C63DAE414DE19755BE278A175E
040D4A4C1A0EE46E924A5C298201D8796B6D792070617373776F72645860347EB5ED
49E1F6536E2A3F3B5DF1C12D9BAEF440DFBC42BD45D31F4B3FDABDFA9D685A84582D
98B1B2474210F1C785DA63C91D01077AC832F46CA52CACCB3A6C1D60BDDA813058FB
6B865AD2B885492B697D450F6EE06B988D7A1EC5FCF4A6A6
~~~~~

Annotated hex:

[comment]: <> (replace-data:csr/ecdsa-p384/c509csr-t2.diag)
~~~~~
  0: 87             # C509CertificationRequest=array[7]
  1:   02             # [0]. c509CertificationRequestType=2
  2:   01             # [1]. subjectSignatureAlgorithm=ecdsa-with-sh
                      #      a384 (1)
  3:   6A             # [2]. subject=char[10]
  4:     65636473612D70333834 # "ecdsa-p384"
 14:   02             # [3]. subjectPublicKeyAlg=EC public key on
                      #      curve secp384r1 (2)
 15:   58 61          # [4]. subject public key=EC point=byte[97]
 17:     04DD6762F035899451372B2FE9B52A8314AD10E2C4363C5A5849E296FE
 46:     51AAB9BFD03AB038D33418A0BCD83280ABA0BD9104017165C048B5346B
 75:     54109E449FCC889E4EE870B5F8A2C63DAE414DE19755BE278A175E040D
104:     4A4C1A0EE46E924A5C29
114:   82             # [5]. attributes=array[2]
                        #---CRAttributes[0]---
115:     01             # [0]. type=ChallengePassword (1)
116:     D8 79          # [1]. value: tag=121: alternative 0,
                        #      PRINTABLE STRING
118:       6B             # char[11]
119:         6D792070617373776F7264 # "my password"
130:   58 60          # [6]. signature value=byte[96]
132:     347EB5ED49E1F6536E2A3F3B5DF1C12D9BAEF440DFBC42BD45D31F4B3F
161:     DABDFA9D685A84582D98B1B2474210F1C785DA27C5D36FFA887A38BACB
190:     D8D7D241E770B513B034E32ACB43D5AF979E122E2FAB403D4D30DF44D0
219:     77C5A05E1E07981567
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
-----BEGIN PRIVATE KEY-----
MEECAQAwEwYHKoZIzj0CAQYIKoZIzj0DAQcEJzAlAgEBBCAuLpSUUN6u6VJJyQvn
Nyvkz9ca/kKUfUPG7W+OGX6ZCQ==
-----END PRIVATE KEY-----
~~~~~

### X.509 Certification Request {#x509csr-dhsig-sha256}

- The peer private key and certificate are given in {{key-selfsign-secp256r1}} and {{x509-selfsign-secp256r1}}.

[comment]: <> (replace-size:csr/dhsig-sha256/x509csr.pem)
PEM content (206 bytes):

[comment]: <> (replace-data:csr/dhsig-sha256/x509csr.pem)
~~~~~
-----BEGIN CERTIFICATE REQUEST-----
MIHLMIGVAgEAMBcxFTATBgNVBAMMDGRoc2lnLXNoYTI1NjBZMBMGByqGSM49AgEG
CCqGSM49AwEHA0IABMTUozeP7JAAXLsNPq8TCbSCkpr1ZhSvJqnyLbg8TzR3sLqX
Et+CE3SfkY1979+zKmxD3wlxXQDke+BRnSDksxSgHDAaBgkqhkiG9w0BCQcxDQwL
bXkgcGFzc3dvcmQwCgYIKwYBBQUHBhoDJQAwIgQgCKW6hVQ4LZIOFihpg9OcVWST
6wIOKgvdwZd4lBNtB9k=
-----END CERTIFICATE REQUEST-----
~~~~~

Text representation:

[comment]: <> (replace-data:csr/dhsig-sha256/x509csr.txt)
~~~~~
Certificate Request:
  Data:
    Version: v1 (0)
    Subject: CN=dhsig-sha256
    Subject Public Key Info:
      Public Key Algorithm: EC/P256
      Pub:
        04:c4:d4:a3:37:8f:ec:90:00:5c:bb:0d:3e:af:13:09:b4:82:
        92:9a:f5:66:14:af:26:a9:f2:2d:b8:3c:4f:34:77:b0:ba:97:
        12:df:82:13:74:9f:91:8d:7d:ef:df:b3:2a:6c:43:df:09:71:
        5d:00:e4:7b:e0:51:9d:20:e4:b3:14
    Attributes:
      challengePassword: my password
  Signature Algorithm: sa-ecdhPop-sha256-hmac-sha256
  Signature Value:
    Hash Value:
      08:a5:ba:85:54:38:2d:92:0e:16:28:69:83:d3:9c:55:64:93:
      eb:02:0e:2a:0b:dd:c1:97:78:94:13:6d:07:d9
~~~~~

### C509 Type 3 Certification Request

- C509 type 3 certification request converted from the X.509 certification request in {{x509csr-dhsig-sha256}}.

[comment]: <> (replace-size:csr/dhsig-sha256/c509csr-t3.hex)
Plain hex (132 bytes):

[comment]: <> (replace-data:csr/dhsig-sha256/c509csr-t3.hex)
~~~~~
87030E6C64687369672D73686132353601584104C4D4A3378FEC90005CBB0D3EAF13
09B482929AF56614AF26A9F22DB83C4F3477B0BA9712DF8213749F918D7DEFDFB32A
6C43DF09715D00E47BE0519D20E4B31482016B6D792070617373776F7264582008A5
BA8554382D920E16286983D39C556493EB020E2A0BDDC1977894136D07D9
~~~~~

Annotated hex:

- See the annotated hex for the C509 type 2 certification request in {{c509csr-dhsig-sha256}}. The only differences are the certification request type and the signature value.

### C509 Type 2 Certification Request {#c509csr-dhsig-sha256}

- The peer private key and certificate are given in {{key-selfsign-secp256r1}} and {{c509-selfsign-secp256r1}}.

[comment]: <> (replace-size:csr/dhsig-sha256/c509csr-t2.hex)
Plain hex (132 bytes):

[comment]: <> (replace-data:csr/dhsig-sha256/c509csr-t2.hex)
~~~~~
87020E6C64687369672D73686132353601584104C4D4A3378FEC90005CBB0D3EAF13
09B482929AF56614AF26A9F22DB83C4F3477B0BA9712DF8213749F918D7DEFDFB32A
6C43DF09715D00E47BE0519D20E4B31482016B6D792070617373776F726458202C23
7A82D11BD92EF29A69EA046128BF2CFF2F07ABF2499E966D81D712E4637C
~~~~~

Annotated hex:

[comment]: <> (replace-data:csr/dhsig-sha256/c509csr-t2.diag)
~~~~~
  0: 87             # C509CertificationRequest=array[7]
  1:   02             # [0]. c509CertificationRequestType=2
  2:   0E             # [1]. subjectSignatureAlgorithm=sa-ecdhPop-sh
                      #      a256-hmac-sha256 (14)
  3:   6C             # [2]. subject=char[12]
  4:     64687369672D736861323536 # "dhsig-sha256"
 16:   01             # [3]. subjectPublicKeyAlg=EC public key on
                      #      curve secp256r1 (1)
 17:   58 41          # [4]. subject public key=EC point=byte[65]
 19:     04C4D4A3378FEC90005CBB0D3EAF1309B482929AF56614AF26A9F22DB8
 48:     3C4F3477B0BA9712DF8213749F918D7DEFDFB32A6C43DF09715D00E47B
 77:     E0519D20E4B314
 84:   82             # [5]. attributes=array[2]
                        #---CRAttributes[0]---
 85:     01             # [0]. type=ChallengePassword (1)
 86:     6B             # [1]. value=char[11]
 87:       6D792070617373776F7264 # "my password"
 98:   58 20          # [6]. signature
                      #      value=DhSigStatic.hashValue=byte[32]
100:     2C237A82D11BD92EF29A69EA046128BF2CFF2F07ABF2499E966D81D712
129:     E4637C
~~~~~

## ECDH PoP With SHA-384 And HMAC-SHA384 {#csr-dhsig-sha384}

- Signature algorithm: sa-ecdhPop-sha384-hmac-sha384
- Signature value: all fields are present.
- CR attributes: none.

### Private Key

[comment]: <> (replace-data:key/dhsig-secp384r1/prikey.pem)
~~~~~
-----BEGIN PRIVATE KEY-----
ME4CAQAwEAYHKoZIzj0CAQYFK4EEACIENzA1AgEBBDBUbYpmsdFTjH5242lnccrR
+S9hKNFLUk7CGvyvn2qPBrvxTwFk0g+y5Kb0yGm9mFc=
-----END PRIVATE KEY-----
~~~~~

### X.509 Certification Request {#x509csr-dhsig-sha384}

- The peer private key and certificate are given in {{key-selfsign-secp384r1}} and {{x509-selfsign-secp384r1}}.

[comment]: <> (replace-size:csr/dhsig-sha384/x509csr.pem)
PEM content (261 bytes):

[comment]: <> (replace-data:csr/dhsig-sha384/x509csr.pem)
~~~~~
-----BEGIN CERTIFICATE REQUEST-----
MIIBATCBlgIBADAXMRUwEwYDVQQDDAxkaHNpZy1zaGEzODQwdjAQBgcqhkjOPQIB
BgUrgQQAIgNiAAQKxeNs56ZnUpvRp6BZmvKELxvkk9P+0gApVXSg2LvxoQfBSIrP
CZoyRp67HFpzLWnC+TVnIoaHKc6IeEx72NBX1j4l/dDyjDWbpsWMJXHYxoOIaqaj
ift9JLlim5l0N3WgADAKBggrBgEFBQcGGwNaADBXMCMwHTEbMBkGA1UEAwwSc2Vs
ZnNpZ24tc2VjcDM4NHIxAgISNAQwD2dTs3Gjb9kiZ3hF59DgzhB/2WonQkMZMcUY
EKaPr3weGTBEiMk+FCxXLRBT0O1G
-----END CERTIFICATE REQUEST-----
~~~~~

Text representation:

[comment]: <> (replace-data:csr/dhsig-sha384/x509csr.txt)
~~~~~
Certificate Request:
  Data:
    Version: v1 (0)
    Subject: CN=dhsig-sha384
    Subject Public Key Info:
      Public Key Algorithm: EC/P384
      Pub:
        04:0a:c5:e3:6c:e7:a6:67:52:9b:d1:a7:a0:59:9a:f2:84:2f:
        1b:e4:93:d3:fe:d2:00:29:55:74:a0:d8:bb:f1:a1:07:c1:48:
        8a:cf:09:9a:32:46:9e:bb:1c:5a:73:2d:69:c2:f9:35:67:22:
        86:87:29:ce:88:78:4c:7b:d8:d0:57:d6:3e:25:fd:d0:f2:8c:
        35:9b:a6:c5:8c:25:71:d8:c6:83:88:6a:a6:a3:89:fb:7d:24:
        b9:62:9b:99:74:37:75
    Attributes:
  Signature Algorithm: sa-ecdhPop-sha384-hmac-sha384
  Signature Value:
    Issuer: CN=selfsign-secp384r1
    Serial Number:
      12:34
    Hash Value:
      0f:67:53:b3:71:a3:6f:d9:22:67:78:45:e7:d0:e0:ce:10:7f:
      d9:6a:27:42:43:19:31:c5:18:10:a6:8f:af:7c:1e:19:30:44:
      88:c9:3e:14:2c:57:2d:10:53:d0:ed:46
~~~~~

### C509 Type 3 Certification Request

- C509 type 3 certification request converted from the X.509 certification request in {{x509csr-dhsig-sha384}}.

[comment]: <> (replace-size:csr/dhsig-sha384/c509csr-t3.hex)
Plain hex (190 bytes):

[comment]: <> (replace-data:csr/dhsig-sha384/c509csr-t3.hex)
~~~~~
87030F6C64687369672D736861333834025861040AC5E36CE7A667529BD1A7A0599A
F2842F1BE493D3FED200295574A0D8BBF1A107C1488ACF099A32469EBB1C5A732D69
C2F9356722868729CE88784C7BD8D057D63E25FDD0F28C359BA6C58C2571D8C68388
6AA6A389FB7D24B9629B9974377580837273656C667369676E2D7365637033383472
3142123458300F6753B371A36FD922677845E7D0E0CE107FD96A2742431931C51810
A68FAF7C1E19304488C93E142C572D1053D0ED46
~~~~~

Annotated hex:

- See the annotated hex for the C509 type 2 certification request in {{c509csr-dhsig-sha384}}. The only differences are the certification request type and the signature value.

### C509 Type 2 Certification Request {#c509csr-dhsig-sha384}

- The peer private key and certificate are given in {{key-selfsign-secp384r1}} and {{c509-selfsign-secp384r1}}.

[comment]: <> (replace-size:csr/dhsig-sha384/c509csr-t2.hex)
Plain hex (190 bytes):

[comment]: <> (replace-data:csr/dhsig-sha384/c509csr-t2.hex)
~~~~~
87020F6C64687369672D736861333834025861040AC5E36CE7A667529BD1A7A0599A
F2842F1BE493D3FED200295574A0D8BBF1A107C1488ACF099A32469EBB1C5A732D69
C2F9356722868729CE88784C7BD8D057D63E25FDD0F28C359BA6C58C2571D8C68388
6AA6A389FB7D24B9629B9974377580837273656C667369676E2D7365637033383472
3142123458300C5E7EADF9C902D6ED67ACBEF42EFD563A0D6478182726571B3D9F04
DEFD2693ED3CDF0AEEF102AF104F1871BC9DEB75
~~~~~

Annotated hex:

[comment]: <> (replace-data:csr/dhsig-sha384/c509csr-t2.diag)
~~~~~
  0: 87             # C509CertificationRequest=array[7]
  1:   02             # [0]. c509CertificationRequestType=2
  2:   0F             # [1]. subjectSignatureAlgorithm=sa-ecdhPop-sh
                      #      a384-hmac-sha384 (15)
  3:   6C             # [2]. subject=char[12]
  4:     64687369672D736861333834 # "dhsig-sha384"
 16:   02             # [3]. subjectPublicKeyAlg=EC public key on
                      #      curve secp384r1 (2)
 17:   58 61          # [4]. subject public key=EC point=byte[97]
 19:     040AC5E36CE7A667529BD1A7A0599AF2842F1BE493D3FED200295574A0
 48:     D8BBF1A107C1488ACF099A32469EBB1C5A732D69C2F9356722868729CE
 77:     88784C7BD8D057D63E25FDD0F28C359BA6C58C2571D8C683886AA6A389
106:     FB7D24B9629B99743775
116:   80             # [5]. attributes=array[0]
117:   83             # [6]. signature value=DhSigStatic=array[3]
118:     72             # [0]. issuer=char[18]
119:       73656C667369676E2D73656370333834 # "selfsign-secp384"
135:       7231                             # "r1"
137:     42             # [1]. certificateSerialNumber=byte[2]
138:       1234
140:     58 30          # [2]. hashValue=byte[48]
142:       0C5E7EADF9C902D6ED67ACBEF42EFD563A0D6478182726571B3D9F04
170:       DEFD2693ED3CDF0AEEF102AF104F1871BC9DEB75
~~~~~

## ECDH PoP With SHA-512 And HMAC-SHA512 {#csr-dhsig-sha512}

- Signature algorithm: sa-ecdhPop-sha512-hmac-sha512
- Signature value: only the `hashValue` field is present.
- CR attributes: none.

### Private Key

[comment]: <> (replace-data:key/dhsig-secp521r1/prikey.pem)
~~~~~
-----BEGIN PRIVATE KEY-----
MF8CAQAwEAYHKoZIzj0CAQYFK4EEACMESDBGAgEBBEGYAuJjVrO97/rkH82IyZSy
71Rtimax0VONomS0XClgUOA1+6U8bwhyjFiEMQSJC5mrLpJeuO5Z6IGl/uwnS5It
LA==
-----END PRIVATE KEY-----
~~~~~

### X.509 Certification Request {#x509csr-dhsig-sha512}

- The peer private key and certificate are given in {{key-selfsign-secp521r1}} and {{x509-selfsign-secp521r1}}.

[comment]: <> (replace-size:csr/dhsig-sha512/x509csr.pem)
PEM content (278 bytes):

[comment]: <> (replace-data:csr/dhsig-sha512/x509csr.pem)
~~~~~
-----BEGIN CERTIFICATE REQUEST-----
MIIBEjCBvAIBADAXMRUwEwYDVQQDDAxkaHNpZy1zaGE1MTIwgZswEAYHKoZIzj0C
AQYFK4EEACMDgYYABAForTEuNgFjmmHiGcPDj768sqCrVOaYYW88gPqP9et8WCn8
TPILotzY/IQV0uf+a5KoRHQNfOpLnpJPAQ6l0Jf7jQCBOWzQvsXh2AFUdvWHzndS
5L1WKiYOyqL3CuTNL02dv0dKWJjFWj/bc27z8ftrjLbG2OSj33K2rYutzXyocWjg
36AAMAoGCCsGAQUFBwYcA0UAMEIEQFVhQrOx1Ln079OAviGTXuYsv6QfKOdJlwhQ
MGyKRSHcaWXfJKfjQADdc8I4GcxHXfy/XSpiroUT60ENw+vrAkc=
-----END CERTIFICATE REQUEST-----
~~~~~

Text representation:

[comment]: <> (replace-data:csr/dhsig-sha512/x509csr.txt)
~~~~~
Certificate Request:
  Data:
    Version: v1 (0)
    Subject: CN=dhsig-sha512
    Subject Public Key Info:
      Public Key Algorithm: EC/P521
      Pub:
        04:01:68:ad:31:2e:36:01:63:9a:61:e2:19:c3:c3:8f:be:bc:
        b2:a0:ab:54:e6:98:61:6f:3c:80:fa:8f:f5:eb:7c:58:29:fc:
        4c:f2:0b:a2:dc:d8:fc:84:15:d2:e7:fe:6b:92:a8:44:74:0d:
        7c:ea:4b:9e:92:4f:01:0e:a5:d0:97:fb:8d:00:81:39:6c:d0:
        be:c5:e1:d8:01:54:76:f5:87:ce:77:52:e4:bd:56:2a:26:0e:
        ca:a2:f7:0a:e4:cd:2f:4d:9d:bf:47:4a:58:98:c5:5a:3f:db:
        73:6e:f3:f1:fb:6b:8c:b6:c6:d8:e4:a3:df:72:b6:ad:8b:ad:
        cd:7c:a8:71:68:e0:df
    Attributes:
  Signature Algorithm: sa-ecdhPop-sha512-hmac-sha512
  Signature Value:
    Hash Value:
      55:61:42:b3:b1:d4:b9:f4:ef:d3:80:be:21:93:5e:e6:2c:bf:
      a4:1f:28:e7:49:97:08:50:30:6c:8a:45:21:dc:69:65:df:24:
      a7:e3:40:00:dd:73:c2:38:19:cc:47:5d:fc:bf:5d:2a:62:ae:
      85:13:eb:41:0d:c3:eb:eb:02:47
~~~~~

### C509 Type 3 Certification Request

- C509 type 3 certification request converted from the X.509 certification request in {{x509csr-dhsig-sha512}}.

[comment]: <> (replace-size:csr/dhsig-sha512/c509csr-t3.hex)
Plain hex (219 bytes):

[comment]: <> (replace-data:csr/dhsig-sha512/c509csr-t3.hex)
~~~~~
8703106C64687369672D736861353132035885040168AD312E3601639A61E219C3C3
8FBEBCB2A0AB54E698616F3C80FA8FF5EB7C5829FC4CF20BA2DCD8FC8415D2E7FE6B
92A844740D7CEA4B9E924F010EA5D097FB8D0081396CD0BEC5E1D8015476F587CE77
52E4BD562A260ECAA2F70AE4CD2F4D9DBF474A5898C55A3FDB736EF3F1FB6B8CB6C6
D8E4A3DF72B6AD8BADCD7CA87168E0DF805840556142B3B1D4B9F4EFD380BE21935E
E62CBFA41F28E749970850306C8A4521DC6965DF24A7E34000DD73C23819CC475DFC
BF5D2A62AE8513EB410DC3EBEB0247
~~~~~

Annotated hex:

- See the annotated hex for the C509 type 2 certification request in {{c509csr-dhsig-sha512}}. The only differences are the certification request type and the signature value.

### C509 Type 2 Certification Request {#c509csr-dhsig-sha512}

- The peer private key and certificate are given in {{key-selfsign-secp521r1}} and {{c509-selfsign-secp521r1}}.

[comment]: <> (replace-size:csr/dhsig-sha512/c509csr-t2.hex)
Plain hex (219 bytes):

[comment]: <> (replace-data:csr/dhsig-sha512/c509csr-t2.hex)
~~~~~
8702106C64687369672D736861353132035885040168AD312E3601639A61E219C3C3
8FBEBCB2A0AB54E698616F3C80FA8FF5EB7C5829FC4CF20BA2DCD8FC8415D2E7FE6B
92A844740D7CEA4B9E924F010EA5D097FB8D0081396CD0BEC5E1D8015476F587CE77
52E4BD562A260ECAA2F70AE4CD2F4D9DBF474A5898C55A3FDB736EF3F1FB6B8CB6C6
D8E4A3DF72B6AD8BADCD7CA87168E0DF80584094CC61B7E6F773B095DF7B233CA455
4B45B1743F8A0CBF96144B2232788A3BDE9A8BF2D1CF2B4C3A3C7BB7262567FA33E9
5A4774098D9975968A9D2AEDFF3274
~~~~~

Annotated hex:

[comment]: <> (replace-data:csr/dhsig-sha512/c509csr-t2.diag)
~~~~~
  0: 87             # C509CertificationRequest=array[7]
  1:   02             # [0]. c509CertificationRequestType=2
  2:   10             # [1]. subjectSignatureAlgorithm=sa-ecdhPop-sh
                      #      a512-hmac-sha512 (16)
  3:   6C             # [2]. subject=char[12]
  4:     64687369672D736861353132 # "dhsig-sha512"
 16:   03             # [3]. subjectPublicKeyAlg=EC public key on
                      #      curve secp521r1 (3)
 17:   58 85          # [4]. subject public key=EC point=byte[133]
 19:     040168AD312E3601639A61E219C3C38FBEBCB2A0AB54E698616F3C80FA
 48:     8FF5EB7C5829FC4CF20BA2DCD8FC8415D2E7FE6B92A844740D7CEA4B9E
 77:     924F010EA5D097FB8D0081396CD0BEC5E1D8015476F587CE7752E4BD56
106:     2A260ECAA2F70AE4CD2F4D9DBF474A5898C55A3FDB736EF3F1FB6B8CB6
135:     C6D8E4A3DF72B6AD8BADCD7CA87168E0DF
152:   80             # [5]. attributes=array[0]
153:   58 40          # [6]. signature
                      #      value=DhSigStatic.hashValue=byte[64]
155:     94CC61B7E6F773B095DF7B233CA4554B45B1743F8A0CBF96144B223278
184:     8A3BDE9A8BF2D1CF2B4C3A3C7BB7262567FA33E95A4774098D9975968A
213:     9D2AEDFF3274
~~~~~

## Unsigned PoP With X25519 Key {#csr-x25519}

- Signature algorithm: unsigned
- CR attributes: a `privateKeyPossessionStatement` attribute without the `cert` field.

### Private Key

[comment]: <> (replace-data:key/x25519/prikey.pem)
~~~~~
-----BEGIN PRIVATE KEY-----
MC4CAQAwBQYDK2VuBCIEIPJNe3l0Mqeq8Fwp4DL6opcnehT4qMe0d+/4nSIVodQc
-----END PRIVATE KEY-----
~~~~~

### X.509 Certification Request {#x509csr-x25519}

[comment]: <> (replace-size:csr/x25519/x509csr.pem)
PEM content (135 bytes):

[comment]: <> (replace-data:csr/x25519/x509csr.pem)
~~~~~
-----BEGIN CERTIFICATE REQUEST-----
MIGEMHMCAQAwETEPMA0GA1UEAwwGeDI1NTE5MCowBQYDK2VuAyEAiv9Rb6xxJEFQ
5w+Sd/St9/sp9Bp6SogovUdnIvwbfwigLzAtBgorBgEEAYGsYAIBMR8wHTAbMBYx
FDASBgNVBAMMC2RlbW8gaXNzdWVyAgECMAoGCCsGAQUFBwYkAwEA
-----END CERTIFICATE REQUEST-----
~~~~~

Text representation:

[comment]: <> (replace-data:csr/x25519/x509csr.txt)
~~~~~
Certificate Request:
  Data:
    Version: v1 (0)
    Subject: CN=x25519
    Subject Public Key Info:
      Public Key Algorithm: X25519
      Pub:
        8a:ff:51:6f:ac:71:24:41:50:e7:0f:92:77:f4:ad:f7:fb:29:
        f4:1a:7a:4a:88:28:bd:47:67:22:fc:1b:7f:08
    Attributes:
      PrivateKeyPossessionStatement:
        signer:
          Issuer: CN=demo issuer
          Serial Number:
            02
  Signature Algorithm: unsigned
  Signature Value: <empty>
~~~~~

### C509 Type 3 Certification Request

- C509 type 3 certification request converted from the X.509 certification request in {{x509csr-x25519}}.

[comment]: <> (replace-size:csr/x25519/c509csr-t3.hex)
Plain hex (64 bytes):

[comment]: <> (replace-data:csr/x25519/c509csr-t3.hex)
~~~~~
870305667832353531390858208AFF516FAC71244150E70F9277F4ADF7FB29F41A7A
4A8828BD476722FC1B7F088202836B64656D6F206973737565724102F640
~~~~~

Annotated hex:

- See the annotated hex for the C509 type 2 certification request in {{c509csr-x25519}}. The only differences are the certification request type and the signature value.

### C509 Type 2 Certification Request {#c509csr-x25519}

[comment]: <> (replace-size:csr/x25519/c509csr-t2.hex)
Plain hex (64 bytes):

[comment]: <> (replace-data:csr/x25519/c509csr-t2.hex)
~~~~~
870205667832353531390858208AFF516FAC71244150E70F9277F4ADF7FB29F41A7A
4A8828BD476722FC1B7F088202836B64656D6F206973737565724102F640
~~~~~

Annotated hex:

[comment]: <> (replace-data:csr/x25519/c509csr-t2.diag)
~~~~~
 0: 87             # C509CertificationRequest=array[7]
 1:   02             # [0]. c509CertificationRequestType=2
 2:   05             # [1]. subjectSignatureAlgorithm=unsigned (5)
 3:   66             # [2]. subject=char[6]
 4:     783235353139    # "x25519"
10:   08             # [3]. subjectPublicKeyAlg=X25519 (8)
11:   58 20          # [4]. subject public key=EC point=byte[32]
13:     8AFF516FAC71244150E70F9277F4ADF7FB29F41A7A4A8828BD476722FC
42:     1B7F08
45:   82             # [5]. attributes=array[2]
                       #---CRAttributes[0]---
46:     02             # [0]. type=PrivateKeyPossessionStatement (2)
47:     83             # [1]. value=array[3]
48:       6B             # [0]. issuer=char[11]
49:         64656D6F20697373756572 # "demo issuer"
60:       41             # [1]. certificateSerialNumber=byte[1]
61:         02
62:       F6             # [2]. cert=<null>
63:   40             # [6]. signature value=byte[0]
~~~~~

## Unsigned PoP With X25519 Key And Cert {#csr-x25519-withcert}

- Signature algorithm: unsigned
- CR attributes: a `privateKeyPossessionStatement` attribute with the `cert` field.

### Private Key

[comment]: <> (replace-data:key/x25519/prikey.pem)
~~~~~
-----BEGIN PRIVATE KEY-----
MC4CAQAwBQYDK2VuBCIEIPJNe3l0Mqeq8Fwp4DL6opcnehT4qMe0d+/4nSIVodQc
-----END PRIVATE KEY-----
~~~~~

### X.509 Certification Request {#x509csr-x25519-withcert}

[comment]: <> (replace-size:csr/x25519-withcert/x509csr.pem)
PEM content (433 bytes):

[comment]: <> (replace-data:csr/x25519-withcert/x509csr.pem)
~~~~~
-----BEGIN CERTIFICATE REQUEST-----
MIIBrTCCAZoCAQAwGjEYMBYGA1UEAwwPeDI1NTE5LXdpdGhjZXJ0MCowBQYDK2Vu
AyEAiv9Rb6xxJEFQ5w+Sd/St9/sp9Bp6SogovUdnIvwbfwigggFLMIIBRwYKKwYB
BAGBrGACATGCATcwggEzMCgwIjEgMB4GA1UEAwwXc2ltcGxlLXNlbGZzaWduLWVk
MjU1MTkCAhI0MIIBBTCBuKADAgECAgISNDAFBgMrZXAwIjEgMB4GA1UEAwwXc2lt
cGxlLXNlbGZzaWduLWVkMjU1MTkwHhcNMjUwMTAyMDAwMDAwWhcNMjYwMTAyMDAw
MDAwWjAiMSAwHgYDVQQDDBdzaW1wbGUtc2VsZnNpZ24tZWQyNTUxOTAqMAUGAytl
cAMhAEYnCuwPMoN+Eod50wsknFMdbULBrCnkAjKO3Hn6wr6VoxIwEDAOBgNVHQ8B
Af8EBAMCB4AwBQYDK2VwA0EAwgTwfCZY8TtRPUqp5Vx0jzPeWf6+9hwRP+nXABcW
E8oMcEZNc1Jc8efHyETF27vSZ0BcAPfdnm5f7YwaytWyCTAKBggrBgEFBQcGJAMB
AA==
-----END CERTIFICATE REQUEST-----
~~~~~

Text representation:

[comment]: <> (replace-data:csr/x25519-withcert/x509csr.txt)
~~~~~
Certificate Request:
  Data:
    Version: v1 (0)
    Subject: CN=x25519-withcert
    Subject Public Key Info:
      Public Key Algorithm: X25519
      Pub:
        8a:ff:51:6f:ac:71:24:41:50:e7:0f:92:77:f4:ad:f7:fb:29:
        f4:1a:7a:4a:88:28:bd:47:67:22:fc:1b:7f:08
    Attributes:
      PrivateKeyPossessionStatement:
        signer:
          Issuer: CN=simple-selfsign-ed25519
          Serial Number:
            12:34
        cert:
          Certificate:
            Version: v3 (2)
            Serial Number:
              12:34
            Issuer: CN=simple-selfsign-ed25519
            Validity:
              Not Before: Thu Jan 02 01:00:00 CET 2025
              Not After : Fri Jan 02 01:00:00 CET 2026
            Subject: CN=simple-selfsign-ed25519
            Subject Public Key Info:
              Public Key Algorithm: ED25519
              Pub:
                46:27:0a:ec:0f:32:83:7e:12:87:79:d3:0b:24:9c:53:1d:
                6d:42:c1:ac:29:e4:02:32:8e:dc:79:fa:c2:be:95
            X509v3 extensions:
              X509v3 keyUsage: critical
                digitalSignature
            Signature Algorithm: ED25519
            Signature Value:
              c2:04:f0:7c:26:58:f1:3b:51:3d:4a:a9:e5:5c:74:8f:33:de:
              59:fe:be:f6:1c:11:3f:e9:d7:00:17:16:13:ca:0c:70:46:4d:
              73:52:5c:f1:e7:c7:c8:44:c5:db:bb:d2:67:40:5c:00:f7:dd:
              9e:6e:5f:ed:8c:1a:ca:d5:b2:09
  Signature Algorithm: unsigned
  Signature Value: <empty>
~~~~~

### C509 Type 3 Certification Request

- C509 type 3 certification request converted from the X.509 certification request in {{x509csr-x25519-withcert}}.

[comment]: <> (replace-size:csr/x25519-withcert/c509csr-t3.hex)
Plain hex (230 bytes):

[comment]: <> (replace-data:csr/x25519-withcert/c509csr-t3.hex)
~~~~~
8703056F7832353531392D77697468636572740858208AFF516FAC71244150E70F92
77F4ADF7FB29F41A7A4A8828BD476722FC1B7F088202837773696D706C652D73656C
667369676E2D65643235353139421234588F8B034212340CF61A6775D7001A69570A
807773696D706C652D73656C667369676E2D656432353531390C582046270AEC0F32
837E128779D30B249C531D6D42C1AC29E402328EDC79FAC2BE95205840C204F07C26
58F13B513D4AA9E55C748F33DE59FEBEF61C113FE9D700171613CA0C70464D73525C
F1E7C7C844C5DBBBD267405C00F7DD9E6E5FED8C1ACAD5B20940
~~~~~

Annotated hex:

- See the annotated hex for the C509 type 2 certification request in {{c509csr-x25519-withcert}}. The only differences are the certification request type and the signature value.

### C509 Type 2 Certification Request {#c509csr-x25519-withcert}

[comment]: <> (replace-size:csr/x25519-withcert/c509csr-t2.hex)
Plain hex (230 bytes):

[comment]: <> (replace-data:csr/x25519-withcert/c509csr-t2.hex)
~~~~~
8702056F7832353531392D77697468636572740858208AFF516FAC71244150E70F92
77F4ADF7FB29F41A7A4A8828BD476722FC1B7F088202837773696D706C652D73656C
667369676E2D65643235353139421234588F8B024212340CF61A6775D7001A69570A
807773696D706C652D73656C667369676E2D656432353531390C582046270AEC0F32
837E128779D30B249C531D6D42C1AC29E402328EDC79FAC2BE9520584025623EF445
34BC07269D6071FB3BBBA8F22FF3ED3B65B5C85456151C0F5F9FC259C2932C1F3184
D5088823EBB0B85AE5B7FE9578D2778F10E088944ACB28CC0740
~~~~~

Annotated hex:

[comment]: <> (replace-data:csr/x25519-withcert/c509csr-t2.diag)
~~~~~
  0: 87             # C509CertificationRequest=array[7]
  1:   02             # [0]. c509CertificationRequestType=2
  2:   05             # [1]. subjectSignatureAlgorithm=unsigned (5)
  3:   6F             # [2]. subject=char[15]
  4:     7832353531392D7769746863657274 # "x25519-withcert"
 19:   08             # [3]. subjectPublicKeyAlg=X25519 (8)
 20:   58 20          # [4]. subject public key=EC point=byte[32]
 22:     8AFF516FAC71244150E70F9277F4ADF7FB29F41A7A4A8828BD476722FC
 51:     1B7F08
 54:   82             # [5]. attributes=array[2]
                        #---CRAttributes[0]---
 55:     02             # [0]. type=PrivateKeyPossessionStatement
                        #      (2)
 56:     83             # [1]. value=array[3]
 57:       77             # [0]. issuer=char[23]
 58:         73696D706C652D73656C667369676E2D # "simple-selfsign-"
 74:         65643235353139                   # "ed25519"
 81:       42             # [1]. certificateSerialNumber=byte[2]
 82:         1234
 84:       58 8F          # [2]. cert=byte[143]
 86:         8B024212340CF61A6775D7001A69570A807773696D706C652D7365
113:         6C667369676E2D656432353531390C582046270AEC0F32837E1287
140:         79D30B249C531D6D42C1AC29E402328EDC79FAC2BE952058402562
167:         3EF44534BC07269D6071FB3BBBA8F22FF3ED3B65B5C85456151C0F
194:         5F9FC259C2932C1F3184D5088823EBB0B85AE5B7FE9578D2778F10
221:         E088944ACB28CC07
229:   40             # [6]. signature value=byte[0]
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
Plain hex (8 bytes):

[comment]: <> (replace-data:csrt/undefined-csrt/c509csrt.hex)
~~~~~
8700F7F7F7F7F7F7
~~~~~

Annotated hex:

[comment]: <> (replace-data:csrt/undefined-csrt/c509csrt.diag)
~~~~~
0: 87             # C509CertificationRequestTemplate=array[7]
1:   00             # [0]. c509CertificationRequestTemplateType=0
2:   F7             # [1]. c509CertificationRequestType=<undefined>
3:   F7             # [2]. subjectSignatureAlgorithm=<undefined>
4:   F7             # [3]. subject=<undefined>
5:   F7             # [4]. subjectPublicKeyAlgorithm=<undefined>
6:   F7             # [5]. subjectPublicKey=<undefined>
7:   F7             # [6]. extensions=<undefined>
~~~~~

## With One Element in Each Field {#oneelement-csrt}

- c509CertificationRequestType: one element
- subjectSignatureAlgorithm: one element
- subject: one element
- subjectPublicKeyAlgorithm: one element
- extensionsRequest: one element

[comment]: <> (replace-size:csrt/oneelement-csrt/c509csrt.hex)
Plain hex (18 bytes):

[comment]: <> (replace-data:csrt/oneelement-csrt/c509csrt.hex)
~~~~~
87008102810084010101F78101F78303F4F7
~~~~~

Annotated hex:

[comment]: <> (replace-data:csrt/oneelement-csrt/c509csrt.diag)
~~~~
 0: 87             # C509CertificationRequestTemplate=array[7]
 1:   00             # [0]. c509CertificationRequestTemplateType=0
 2:   81             # [1]. c509CertificationRequestType=array[1]
 3:     02             # [0]. C509SimpleOCSPRequest (2)
 4:   81             # [2]. subjectSignatureAlgorithm: array[1]
 5:     00             # [0]. ecdsa-with-sha256 (0)
 6:   84             # [3]. subject=array[4], 1 attribute
                       #---attribute[0]---
 7:     01             # [0]. type=1: commonName
 8:     01             # [1]. minOccurs=1
 9:     01             # [2]. maxOccurs=1
10:     F7             # [3]. value=<undefined>
11:   81             # [4]. subjectPublicKeyAlgorithm: array[1]
12:     01             # [0]. EC public key on curve secp256r1 (1)
13:   F7             # [5]. subjectPublicKey=<undefined>
14:   83             # [6]. extensions=array[3]
                       #---extension[0]---
15:     03             # [0]. type=SubjectAlternativeName (3)
16:     F4             # [1]. required
17:     F7             # [2]. value=<undefined>
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
Plain hex (153 bytes):

[comment]: <> (replace-data:csrt/complex-csrt/c509csrt.hex)
~~~~~
87008202038301492B0601040181FD590982492B0601040181FD590A420500900101
01F7040101624445492B0601040181FD590B0101F7492B0601040181FD590C01014D
0C0B636F6E73742D76616C75658301492B0601040181FD590982492B0601040181FD
590A420500F78C08F4F702F51860492B0601040181FD590DF4F7492B0601040181FD
590EF44D0C0B636F6E73742D76616C7565
~~~~~

Annotated hex:

[comment]: <> (replace-data:csrt/complex-csrt/c509csrt.diag)
~~~~~
  0: 87             # C509CertificationRequestTemplate=array[7]
  1:   00             # [0]. c509CertificationRequestTemplateType=0
  2:   82             # [1]. c509CertificationRequestType=array[2]
  3:     02             # [0]. C509SimpleOCSPRequest (2)
  4:     03             # [1]. unknown (3)
  5:   83             # [2]. subjectSignatureAlgorithm: array[3]
  6:     01             # [0]. ecdsa-with-sha384 (1)
  7:     49             # [1]. byte[9]: 
  8:       2B0601040181FD5909 # oid: 1.3.6.1.4.1.32473.9
 17:     82             # [2]. array[2]
 18:       49             # [0]. algorithm=byte[9]: 
 19:         2B0601040181FD590A # oid: 1.3.6.1.4.1.32473.10
 28:       42             # [1]. parameters=byte[2]
 29:         0500
 31:   90             # [3]. subject=array[16], 4 attributes
                        #---attribute[0]---
 32:     01             # [0]. type=1: commonName
 33:     01             # [1]. minOccurs=1
 34:     01             # [2]. maxOccurs=1
 35:     F7             # [3]. value=<undefined>
                        #---attribute[1]---
 36:     04             # [0]. type=4: country
 37:     01             # [1]. minOccurs=1
 38:     01             # [2]. maxOccurs=1
 39:     62             # [3]. value=char[2]
 40:       4445            # "DE"
                        #---attribute[2]---
 42:     49             # type=byte[9]: 
 43:       2B0601040181FD590B # oid: 1.3.6.1.4.1.32473.11
 52:     01             # [1]. minOccurs=1
 53:     01             # [2]. maxOccurs=1
 54:     F7             # [3]. value=<undefined>
                        #---attribute[3]---
 55:     49             # type=byte[9]: 
 56:       2B0601040181FD590C # oid: 1.3.6.1.4.1.32473.12
 65:     01             # [1]. minOccurs=1
 66:     01             # [2]. maxOccurs=1
 67:     4D             # [3]. value=byte[13]
 68:       0C0B636F6E73742D76616C7565
 81:   83             # [4]. subjectPublicKeyAlgorithm: array[3]
 82:     01             # [0]. EC public key on curve secp256r1 (1)
 83:     49             # [1]. byte[9]: 
 84:       2B0601040181FD5909 # oid: 1.3.6.1.4.1.32473.9
 93:     82             # [2]. array[2]
 94:       49             # [0]. algorithm=byte[9]: 
 95:         2B0601040181FD590A # oid: 1.3.6.1.4.1.32473.10
104:       42             # [1]. parameters=byte[2]
105:         0500
107:   F7             # [5]. subjectPublicKey=<undefined>
108:   8C             # [6]. extensions=array[12]
                        #---extension[0]---
109:     08             # [0]. type=ExtendedKeyUsage (8)
110:     F4             # [1]. required
111:     F7             # [2]. value=<undefined>
                        #---extension[1]---
112:     02             # [3]. type=KeyUsage (2)
113:     F5             # [4]. optional
114:     18 60          # [5]. value=96: [keyCertSign, cRLSign]
                        #---extension[2]---
116:     49             # [6]. type=byte[9]: 
117:       2B0601040181FD590D # oid: 1.3.6.1.4.1.32473.13
126:     F4             # [7]. required
127:     F7             # [8]. value=<undefined>
                        #---extension[3]---
128:     49             # [9]. type=byte[9]: 
129:       2B0601040181FD590E # oid: 1.3.6.1.4.1.32473.14
138:     F4             # [10]. required
139:     4D             # [11]. value=byte[13]
140:       0C0B636F6E73742D76616C7565
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

The authors thank Marco Tiloca for reviewing and commenting on intermediate versions of the draft.

# Change log
{:unnumbered}

> **Note to RFC Editor:** Please remove this section prior to publication of the final version of this document.

## Since draft-ietf-cose-c509-test-vectors-02
{:numbered="false"}

- Replace abc.com with abc.example.com
- Add Section Overview
- Better annotated Hex
- Merge separate leading line with only "8B" / "87" with other lines in the hex block.

## Since draft-ietf-cose-c509-test-vectors-01
{:numbered="false"}

- Align with draft-ietf-cose-cbor-encoded-cert-20

- Add the leading byte for CBOR array
  - 0x8B for C509Certificate
  - 0x87 for C509CertificationRequest
  - 0x87 for C509CertificationRequestTemplate

- Better indention for extensions, RDN attributes, CR attributes, certificate policies, etc.

- Fixed examples
  - Section 3.6: replace the empty subject of the self-signed certificate with non-empty one.
  - Section 3.12 and Section 3.13: replace empty signature value in non-selfsigned certificate with non-empty one.
