# Requirements for OIDC Self-Issued OpenId Provider

**Introduction**

[Chapter 7 of OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html#SelfIssued) introduces the Self-issued OpenID Provider (SIOP), a personal, self-hosted OP that issues self-signed[ ID Tokens](https://openid.net/specs/openid-connect-core-1_0.html#IDToken) directly to the RP.  SIOP operates on behalf of a human or non-human subject to share information contained in a SIOP store by including it in an ID Token signed using an identifier and keys controlled by the subject.

It was agreed in OpenID Foundation OpenID Connect Working Group to work on a separate specification covering only the SIOP part of OpenID Connect core (OIDC) specification. This document enumerates requirements for SIOP to help define the scope of a new version of SIOP specification - what will and will not be included. To better explain the requirements, this document provides examples of options that could meet the requirements, but it is not the intention of the document to suggest one option over the other.

**Scope** 

*   A. SIOP request
*   B. SIOP response
*   C. Key recovery and key rotation
*   D. Trust model between RP and SIOP
*   E. Issuance of the claims
*   F. Privacy protection


**Terminology**

Avoid using terms that do not have standards based definition (such as SSI)

**Use-Cases (non-exhausive)**

*   Cross-device authentication?
*   Identity (set of data related to the entity) needs to be provable as attested at the time of attestation and cannot be taken away
*   Realizing portability of the identifiers - being able to change the SIOP without losing the ability to identify
*   Just log-in using SIOP vs. transmission of claims
*   RP and SIOP are on the same device

**Requirements**

A.SIOP request

*   1.Not all OpenID Providers must have the capability to issue self-signed id_tokens, and that capability remains specific to Self-Issued OpenID Providers (which can be native apps, PWAs, etc.)
*   2.SIOP can be used for log in and/or requesting claims to transmit identity characteristics. 
*   3.SIOP supports Implicit Flow. 
    *   SIOPs being native apps, PWA, browser apps, etc. cannot be expected to support Authorization Code Flow

B.SIOP response

*   4.SIOP is able to return Verifiable Credentials and Verifiable Presentations in the response
    *   VC/VP returned in the OIDC authentication response, wrapped in the id_token
    *   VCs/VPs should be supported in any data format, including JSON-LD, following W3C [VC-DATA-MODEL]

C. Key recovery and key rotation (cryptography itself is out of scope)

*   5.Key information should be derived either by using sub_jwks with URNs (Cryptographic attestation of the claims), or DIDs resolved into DID documents, or OpenID Connect Federation entity statements.
    *   SIOP identifiers can be represented by DIDs (in addition to hash of a public key) 
    *   Adding layer of indirection, with ‘sub’ value being URI

        
D.Trust model between RP and SIOP (Discovery and Registration)

*   6.RP can discover SIOP, and differentiate it from not SIOP-enabled OPs
    *   SIOP associates itself with a custom schema `openid://`. Relying Party (RP) call `openid://` when sending a request to a SIOP.
*   7-1.SIOP can dynamically advertise not-static parameters, such as subject identifier type(JWK thumbprint or DIDs), cryptography used, and scopes supported
    *   if DIDs are supported, specific DID methods supported must be enumerated
*   7-2.For the static parameters not mentioned in 7-1, RP must use static configuration information of SIOP
    *   there should be two types of static configuration information - each for JWK thumbprint and DID scenario
    *   otherwise, RPs supporting only JWK thumbprint `sub` would have to support cryptography required only for using DIDs
*   8.RP does not have to pre-register with SIOP, but must communicate metadata using the registration parameter iwhen RP is without a webserver or in the client_id(redirect_uri) when RP can host a webserver
    *   how to prevent malicious RPs from using client_id of another RP?
    *   when DIDs are used, RP's cient_id must be a DID, with SIOP getting a redirect_uri value from well-known configuration document
    *   OpenID Connect Federation style trust negotiation (chapter 6.2) cannot work since it require SIOP to maintain an endpoint

E.Issuance of the claims (SIOP - Claims Provider)

*   9. [should this part rely on a separate specification?] SIOP providers can use Claims provider 
    *   This is not in the rest of OIDC, but needed in SIOP, where OP is not an issuer of identity data and SIOP acts more like a wallet
    *   SIOP could be recommended to perform OIDC Dynamic Client Registration, acting as a Client to the Claims Provider
    *   What does the client need to specify in a request? Concrete claims sources? Schemas? Trust Frameworks?
    *   Would  standardizing this limit the claims provider that can issue claims to SIOP? a separate topic, if the claims sources are themselves OIDC OPs.

F.Privacy protection

*   10.RPs should understand the security/privacy posture of SIOP
    *   Levels of Assurance are under regulated environment
    *   Should `iss` remain `self-issued.me`?
*   11.SIOP should support pairwise identifiers
    *   pairwise keys where SIOP creates a URI for each client and key pairs that are used with it. omnidirectional, and ephemeral?
*   12.Relationship between old and new identifiers/keys can be attested 
    *   Attestations made in the past should remain valid
*   13.RP must be able to receive the claims when the end-user is offline without colluding with the Claims Provider
    *   Configurations of claims provider
        *   “Frozen claims”: stored by SIOP when first requested by a given client
        *   “Frozen refresh”: use frozen claim till expired then fetch again
        *   “Realtime”: fetch on every presentation
    *   Introduce distributed claims model?
    *   Would this be Provider specific?


**Note**
*   Selective Disclosure of the claims is left to the implementations
*   Not sure if there are specific requirements to enable support for PWA-based SIOPs?
*   Is there a need to account for both situations where RP and SIOP and on the same/different devices?
	
	
**Reference **

This requirement list is build upon various discussions and drafts whose non-exhaustive list can be found below:

* [https://nat.sakimura.org/2018/12/11/todo-list-for-self-issued-op/](https://nat.sakimura.org/2018/12/11/todo-list-for-self-issued-op/)
* [https://docs.google.com/document/d/1Q1kTbCjvr0LoD7fsgrtUW0-olorkeebyD7I6mBByMc4/edit#](https://docs.google.com/document/d/1Q1kTbCjvr0LoD7fsgrtUW0-olorkeebyD7I6mBByMc4/edit#)
* [https://bitbucket.org/openid/connect/issues/1180/siop-laundry-list](https://bitbucket.org/openid/connect/issues/1180/siop-laundry-list)
* [https://bitbucket.org/openid/connect/src/master/SIOP/draft-jones-self_issued_identifier.md](https://bitbucket.org/openid/connect/src/master/SIOP/draft-jones-self_issued_identifier.md)
* [https://hackmd.io/YL3juOZrRH65XV51bZR6dQ?view](https://hackmd.io/YL3juOZrRH65XV51bZR6dQ?view)