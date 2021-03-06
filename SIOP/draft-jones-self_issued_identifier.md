# OpenID Self Issued Identifiers

## Warning

This document is not an OIDF International Standard. It is distributed for review and comment. It is subject to change without notice and may not be referred to as an International Standard.

Recipients of this draft are invited to submit, with their comments, notification of any relevant patent rights of which they are aware and to provide supporting documentation.

## Copyright notice & license
The OpenID Foundation (OIDF) grants to any Contributor, developer, implementer, or other interested party a non-exclusive, royalty free, worldwide copyright license to reproduce, prepare derivative works from, distribute, perform and display, this Implementers Draft or Final Specification solely for the purposes of (i) developing specifications, and (ii) implementing Implementers Drafts and Final Specifications based on such documents, provided that attribution be made to the OIDF as the source of the material, but that such attribution does not indicate an endorsement by the OIDF.

The technology described in this document was made available from contributions from various sources, including members of the OpenID Foundation and others. Although the OpenID Foundation has taken steps to help ensure that the technology is available for distribution, it takes no position regarding the validity or scope of any intellectual property or other rights that might be claimed to pertain to the implementation or use of the technology described in this specification or the extent to which any license under such rights might or might not be available; neither does it represent that it has made any independent effort to identify any such rights. The OpenID Foundation and the contributors to this specification make no (and hereby expressly disclaim any) warranties (express, implied, or otherwise), including implied warranties of merchantability, non-infringement, fitness for a particular purpose, or title, related to this specification, and the entire risk as to implementing this specification is assumed by the implementer. The OpenID Intellectual Property Rights policy requires contributors to offer a patent promise not to assert certain patent claims against other contributors and against implementers. The OpenID Foundation invites any interested party to bring to its attention any copyrights, patents, patent applications, or other proprietary rights that may cover technology that may be required to practice this specification.



## Foreword

The OpenID Foundation (OIDF) promotes, protects and nurtures the OpenID community and technologies. As a non-profit international standardizing body, it is comprised by over 160 participating entities (workgroup participants). The work of preparing implementer drafts and final international standards is carried out through OIDF workgroups in accordance with the OpenID Process. Participants interested in a subject for which a workgroup has been established has the right to be represented in that workgroup. International organizations, governmental and non-governmental, in liaison with OIDF, also take part in the work. OIDF collaborates closely with other standardizing bodies in the related fields.


## Introduction


### Notational Conventions

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT",
"SHOULD", "SHOULD NOT", "RECOMMENDED", "NOT RECOMMENDED", "MAY", and
"OPTIONAL" in this document are to be interpreted as described in BCP
14 [RFC2119] [RFC8174] when, and only when, they appear in all
capitals, as shown here.

# **OpenID Self Issued Identifiers **


## 1. Scope

This document specifies the methods for an application to:
* support self-issued identifiers in an OpenID Connect environment.
* enable a strong identifier that can survive changes to current authentication methods.
* format tokens that are bound to the issuer and recipient. (It does not support bearer tokens)

### 1.1 Overview

This document describes an extension to the [OIDC], with particular attention to section 7 on self-issued identifiers. It is designed for maximum compatibility with [SIOP].

The OpenID Connect protocol for Self-Issued Identifiers follows these steps.
##### 1 The optional RP discovery endpoint may provide information about the method it supports.
##### 2 The user agent may query the RP discovery endpoint to learn about policies and methods of the RP.
##### 3 The user browser navigates to the RP and selects a "Personal Identifier" " option on the RP web site.
##### 4 The RP responds with a messaged directed to the User Agent via the browser.
##### 5 The User Agent may query the user for consent
##### 6 The user selects any options on information to be provided to the RP.
##### 7 The user agent sends a identity Token to the RP
##### 8 The user - RP interchange continues in the user browser.


## 2. Normative references
The following referenced documents are indispensable for the application of this document. For dated references, only the edition cited applied. For undated references, the latest edition of the referenced document (including any amendments) applies.

[BCP14] - Key words for use in RFCs to Indicate Requirement Levels
[BCP14]: https://tools.ietf.org/html/bcp14

[BCP195] - Recommendations for Secure Use of Transport Layer Security (TLS) and Datagram Transport Layer Security (DTLS)
[BCP195]: https://tools.ietf.org/html/bcp195

[BCP212] - OAuth 2.0 for Native Apps
[BCP212]: https://tools.ietf.org/html/bcp212

[DID-CORE] - Decentralized Identifiers (DIDs) v1.0. Drummond Reed + 5, 2020-07-31. W3C Working Draft.
[DID-CORE]: https://www.w3.org/TR/did-core/

[RFC6749] - The OAuth 2.0 Authorization Framework
[RFC6749]: https://tools.ietf.org/html/rfc6749

[RFC6819] - OAuth 2.0 Threat Model and Security Considerations
[RFC6819]: https://tools.ietf.org/html/rfc6819

[RFC8259] - The JavaScript Object Notation (JSON) Data Interchange Format
[RFC8259]: https://datatracker.ietf.org/doc/rfc8259/

[OIDC] - OpenID Connect Core 1.0 incorporating errata set 1
[OIDC]: https://openid.net/specs/openid-connect-core-1_0.html

[OpenID.Discovery] - OpenID Connect Discovery 1.0
[OpenID.Discovery]: https://openid.net/specs/openid-connect-discovery-1_0.html

[OpenID.Registration] - OpenID Connect Registration 1.0
[OpenID.Registration]: http://openid.net/specs/openid-connect-registration-1_0.html

[JWT] - JSON Web Token
[JWT]: http://tools.ietf.org/html/draft-ietf-oauth-json-web-token

[JWS] - JSON Web Signature
[JWS]: http://tools.ietf.org/html/draft-ietf-jose-json-web-signature

[JWE] - JSON Web Encryption
[JWE]: http://tools.ietf.org/html/draft-ietf-jose-json-web-encryption

[JWA] - JSON Web Algorithms
[JWA]: http://tools.ietf.org/html/draft-ietf-jose-json-web-algorithms


## 3. Terms and definitions
For the purpose of this document, the terms defined in [RFC6749] and [OpenID Connect Core][OIDC] apply.

**Authenticator** - A collection of software and hardware that can be used to authenticate the user

**UA** - User Agent, which is the holder of the credential used by the user to authenticate themselves and issue authorization tokens to the Resource Servers authorizing access to user Personal Information.

## 4. Symbols and abbreviated terms

**CP** - any of Claim Provider, Certificate Provider, Credential Service Provider

**DID** - Decentralized Identifier from [DID-CORE]

**DID Document** - DID Document from [DID-CORE]

**HTTP** - Hyper Text Transfer Protocol

**TLS** - Transport Layer Security

**URL** - Uniform Resource Locator

## 5. OpenID Self Issued Identifiers

### 5.1 Introduction

Personal information can be stored in various places: locally or at various online resource servers. The document targets the use of the [OIDC] (section 7) Self Issued Identifier to control access to that personal information by providing the means to those resource servers to recognize the user's intent to consent to enable access to that Personal information. Note that Claims providers can be used by the User Agent to acquire claims to be included in the Az Token. This document assumes that the "sub" element continues to be a key id as specified in the OpenID Connect specification but broadens the scope of meaning to include indirect references to the key and key location.  (n.b. -- that last statement might be better worded)


### 5.2 Subject Identifier

*** Warning *** A strict "key ID" as defined in [OIDC] section 7 will not work with this definition of the sub. But a work around is possible for sites that must deal with both definitions since a straight "Key id" already excludes a colon (":").

This document defines the SUB as a URI (or URL) that can still use a local key, the RECOMMENDED syntax for a local key ID is data:{key id}.  Now any URL can be used to allow the SUB to serve as a real subject ID. In particular this allows a `did:` URL prefix to be compatible with the [DID-CORE]. Any URL MUST provide a method to securely recover the key(s) that are used to sign this [OIDC] JWT.

OpenID Self Issued Identifiers provide a way for a user to exercise fine grained control over who can see their identifier as well as have access to their personal resources even as their current authenticators become inadequate to the task for any reason.

### 5.3 Discovery of Relying Party Methods

Note that the user will typically approach the Relying Party with their browser. In the best case the user can understand the policies and methods of the Relying Party before they user clicks on any link on the Relying Party web site that would cause an authentication process to commence. Also note that the User Agent will not send any user information to the Relying Party without asking for user consent first.

#### 5.3.1 Default Methods

An HTTP extension "Personal" would allow the use of a different redirection method in place of, or as well as, the OPENID:// defined in the [OIDC] core sped section 7.3. At the time of publication this section requires the user to add a browser extension to enable this header or select a user agent that will send the header.

Syntax for the Personal Header:

Personal: version, method encoded as base64url.

The version is a REQUIRED string, to be 1.0, from this spec.
The method is optional but MUST be base64url encoded if present. 

// to be removed - use 0.1 test value before publication 

If the header is not present and the OPENID:// method is not enabled by the browser the user MUST be warned to expect an odd search result display by browsers until the search engines are primed with a more user friendly response.

#### 5.3.2 User query of Relying Party Methods

The Relying Party MAY provide a discovery endpoint where the user agent can discover methods beyond that provided in the default methods.

TK

### 5.4 Request from the Relying Party to the User Agent

TK

### 5.5 Recovery

This specification assumes that the user has continuing needs to use their identifiers to access their personal resources, even as the authentication factors become obsolete or are lost. More description can be found in this description of [Did Recovery] or this for [Regeneration]. The following are some of the known ways that loss of use of an authentication factor can occur:
* 1 The device holding the authentication factor is lost.
* 2 The algorithm or key strength is declared obsolete or unable to continue to protect the user's authentication factors.
* 3 A key has become compromised or is otherwise no longer usable.
* 4 The user wishes to enable more than one authenticator to access their resources.
* 5 The identifier method has become obsolete or no longer supported.

For these or any other reason the user needs to enable a different key and bind that key to their resources. Recovery of access uses the same process as identity theft which makes it similar to sim swapping to gain access to a user's phone number, which is often used in identify proofing. This document is designed to provide secure means for that recovery without increasing the risk of identity theft.

Note that recovery of the user access, which is the topic of this specification, may also be a part of a larger process that includes the recovery of user data.

(The point here was to include some of the recovery methods including those generated in the [DIF]. It is an open question whether recovery mechanism should be normative or in an appendix.)
Some thoughts:
* Key is stored in a (e.g.) pfx file that is encrypted to a user secret.
* Key is contained in a removable data store and can be restored if the user is authenticated.
* Key could be shared between multiple user devices and any one could back up the other. (Android already enables this.)
* A Seed can be used to create the key see examples in: [Emtrust], [SeedQuest] and [DCM],
* WebAuthn dual PRF function that takes two salts are returns two keys, use that with 2FA to get decryption key for user secret.
* OpenID Moderna Account forward spec OpenID Connect Account Porting https://openid.net/specs/openid-connect-account-porting-1_0.html also requires a trusted server.
* Include the WebAuthn credentialID in the did document as part of bootstrapping info. Only the person with the authenticator would be able to decrypt the user secrets into the wallet. (John Bradley)
* A collection of pictures (or phrases) could be presented to the user until the key data was fully recovered. (No small number of matches would suffice.) One example is [Fuzzy Vault]. Another is Account Recovery with Expanded Password System [Regeneration].
* A set of biometric data can be used to grant access as described in [Horcrux].
* A new key could be generated and bound to the user's ID. This would require assistance from a trusted server as well as some helper authentication factors.

The following helper functions can be used to enable recovery when a trusted server approach is chosen.
* A FIDO / Web AuthN key is created on a key fob that can be used to initialize the user's identity on any device.
* A QR could be used to provide the secret information to unlock the user's secrets.

### 5.x Persistent User ID

(The need for this section is up for reconsideration)

The [OIDC] specification defines the format of the subject ID [sub] to be a type of key id. That can be used for the life of the key, but when any of the above recovery conditions are triggered the user that wants to have continued access to the resources that were bound to that key must have some means to seamlessly maintain that access.

Wherever a self-issued ID meets the requirement for recovery, it will be carried in the transmission packets as a [puid]. The data in the field MUST be structured as a URL or URI. The intention is that the [puid] would survive any of the recovery conditions listed in section 5.2. It is possible that the nature of the PUID binds it to authentication methods that become compromised or obsoleted. In that case it may not be possible for the PUID to be reused.

Resolution of the PUID into a set of current keys and authenticators -- is a nice to have, but is it required for this purpose??

Note that one other interpretation of the PUID is a "Previously Used ID" which could help with recovery efforts where major parts of the SUB where changed. For Example from did:method_1:{unique ID} to did:method_2:{unique ID} for the case where the {unique ID} might be invariant.

In the use of PUID as previously used ID, PUID_JWK MUST be used unless the ID can be resolved into the keys.

### 5.6 Signed Claims

A collection of attributes or other data elements and are collected together into a signed jose structure. Encryption may also be added but is NOT RECOMMENDED. The header of the jose token MUST identify the signer and cryptography used. This has the same structure as an encoded and signed jwt, but is intended to be embedded inside a jwt. A signed jose token is always treated like a string in the enclosing jwt. There are two distinct type of signed claims.

#### 5.6.1 General Claims

These claims can be about issues that are not bound to the client or the subject. Examples of this type of claim are the mobile authentication assurance claim about the user agent or a proof-of-presence claim made by the user agent about the contemporaneous session between the user and the user agent.

#### 5.6.2 Client Bound Claims

When a session is created between the user agent and the client, the sub field may be a pairwise binding from this user agent to the targeted client. In that case the claim may need to be generated during the current session between the two, or a cached claim that was created during a prior interaction. The header of the jose structure will reference the sub field in this case and the signature will be from the key of that sub.

### 5.7 IdToken

This is the only token created by a User Agent to carry identifier information for a user. It is based on the [OIDC] IdToken and includes all of the mandatory fields from that token. In this specification it is always signed by the key identified in the sub element and always sent as a jose token. It may optionally be encrypted.

The concept of ephemeral id endpoint for temporary storage of iID Tokens as well as the use of references to ID Tokens needs to be discussed.

### 5.8 Flows

The only flow considered in the core spec is implicit, where the user device sends a single ID Token that is the sum of all the claims or grants that a user is providing the RP.

An alternate flow is the device flow where the user is operating on a large form device (like a laptop) using their phone as an authenticator.

# 6. Discovery

This specification assumes the following OP discovery metadata:
"id_token_signing_alg_values_supported": ["RS256"],
"request_object_signing_alg_values_supported": ["none", "RS256"]
The DID AuthN profile assumes the following OP discovery metadata:
"id_token_signing_alg_values_supported": ["RS256", "ES256K", "EdDSA"],
"request_object_signing_alg_values_supported":
   ["none", "RS256", "ES256K", "EdDSA"]

This change will allow DID AuthN enabled RPs to use additional signature algorithms commonly used amongst members of the SSI community.

# 7. User Experience Considerations

This section is non-normative.

[OIDC] uses the custom URL scheme openid://. Mobile browsers would open the app that registered that scheme. 
Desktop browser extensions/ plugins have support for similar functionality. It is out of the scope of the spec under 
which circumstances a QR code will be rendered. One option will be to provide the QR code if the user is using the desktop browser, 
and no browser extension/ plugin is available.

On Android, the user can choose which app should open if multiple apps registered the same custom URL scheme. 
On iOS, the behavior is undefined. One approach would be to check if the user is on an iOS device and then, 
will not render the button if this is a concern. A fallback on iOS could be the use of custom mime types, 
but unusual UX has to be considered. Note, this issue is not specific to SIOP only but affects all apps using custom URL schemes. 
In case a QR Code is used where the user has to open the app first and has to scan the QR Code, this issue is mitigated.

# 8. Security Considerations

Control of a user's own identity puts great power into the hands of a user. The flip side of that is with great power comes great responsibility. We know that users do not spend much time thinking about the security of normal everyday activity and can be fooled by simple scam attacks into releasing private information. The same will be true of self-issued identifiers, so it is important that any method for issuing an identifier help the user to maintain or recover control.


# 9. Privacy Considerations

The user interchanges that carry user private information MUST be encrypted. This may be by TLS or by encrypted jose packets. (question, should tls or alternate be MUST?)

# 10. IANA Considerations
* puid -  as an element
* guid - as a URI
* Personal - as an HTTP header

# 11. References

## 11.1 Normative References

## 11.2 Non-normative References

[Did Recovery] - DIF I&D WG: Starting work on cryptographic secret recovery
[Did Recovery]: https://medium.com/decentralized-identity/dif-id-wg-starting-work-on-cryptographic-secret-recovery-204117b6a2ab

[DIF] - Digital Identity Foundation.
[DIF]: https://identity.foundation/

[SIOP] - Oliver Terbu ed., Self-Issued OpenID Connect Provider DID Profile
[SIOP]: https://identity.foundation/did-siop/

[Fuzzy Vault] - Fuzzy Vault Encryption (contributed by Microsoft)
[Fuzzy Vault]: https://github.com/decentralized-identity/fuzzy-encryption/blob/master/fuzzy-encryption-construction.pdf

[Regeneration] - Hitoshi Kokumai,  Account Recovery with Expanded Password System
[Regeneration]: https://medium.com/@kokumai/account-recovery-with-expanded-password-system-c8766a194cca

[Horcrux] - Horcrux Protocol (contributed by VeridiumID)
[Horcrux]: https://github.com/decentralized-identity/horcrux/

[Emtrust] - Emtrust describes their seed technique for recreating a key from a phrase
[Emtrust]: https://github.com/Halialabs/did-spec/blob/gh-pages/readme.md

[SeedQuest] - SeedQuest has been discussed quite a bit on the various DIF calls
[SeedQuest]: https://github.com/reputage/seedQuest

[DCM] - There is also some work on "Decentralized Key Management Systems" (DKMS) in Hyperleder Indy
[DCM]: https://github.com/hyperledger/aries-rfcs/blob/master/concepts/0051-dkms/dkms-v4.md

[Claims Aggregation] - Claims Aggregation
[Claims Aggregation]: https://bitbucket.org/edmund_jay/oidc-claims-aggregation/src/master/OpenID%20Connect%20Claims%20Aggregation.md

[Client Bound Assertions] - Client Bound Assertions
[Client Bound Assertions]: https://mattrglobal.github.io/oidc-client-bound-assertions-spec/

# Authors

* Tom Jones
* Tobias Looker


