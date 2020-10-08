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
*   G. Claims binding
*   H. Various OpenID providers deployment architectures
*   I. Use-case specific requirements


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

*   1.OpenID Provider’s capability to issue self-issued responses is an extension of the core OpenID Connect protocol 
    *   RPs configured to work with OIDC OPs must work with SIOPs = SIOP request is OIDC request
    *   SIOP request currently does not include redirect_uri which is required in the rest of the specification
    *   This would eliminate a scenario where SIOP does not work with non-SIOP OIDC request - what are the minimal requirements for OP?
*   2.SIOP support for requesting claims to transmit identity characteristics is optional. 
    *   = SIOP can be used just for logins
*   3.SIOP should support Implicit Grant and Authorization Code Grant
    *   Implicit Grant for mobile device SIOPs and Authorization Code Grant for Web-based SIOPs?

B.SIOP response

*   4.SIOP should be able to return Verifiable Credentials and Verifiable Presentations in the response
    *   SIOP expected to return VC/VP in the OIDC authentication response 1/ wrapped in the id_token? 2/ directly as VC/VPs?

C. Key recovery and key rotation
*   5.Key information should be derived either by using DIDs resolved into DID documents, or sub_jwks with URNs (Cryptographic attestation of the claims)
    *   SIOP identifiers can be represented by DIDs (in addition to hash of a public key) 
    *   Sub claim & Sub_jwk claim
        *   During initial discussion, consensus was around adding layer of indirection, with ‘sub’ value being URI
        *   there is one current implementation where constant sub in SIOP is used to collude a user across several apps provided by the same company for the purpose of SSO

D.Trust model between RP and SIOP (need to account for a special use-case where RP and SIOP are on the same device?)

*   6.SIOP must be able to advertise that it is a SIOP-enabled OP
    *   RPs must be able to differentiate SIOP and OP since the key retrieval and security checks work differently (different trust model), and the sub is used differently.
        *   OP: iss value -> openid-configuration -> jwks_uri -> signing key for id token
        *   SIOP: just the key as represented in the Id token or DID based resolution, etc.
    *   Current way is to use openid:// schema. openid:// schema is non-normative according to OIDC, overloaded and not registered with IANA. 
    *   if openid:// is an optional indirection layer and it is often opaque to the OP that receives the request - should it still be conflated as how a SIOP request is identified?  introducing a new schema would not solve the problem
        *   Option: introducing a new claim that explicitly advertises SIOP-enabled OP 
        *   Another option: Add more paths to uri openid://vc/Authenticator - if a wallet other than Authenticator comes in, they just ignore and use openid://vc
*   7.SIOP must be able to advertise configuration information to the RP
    *   Config info that needs to be communicated is - crypto limitations (RS256 is mandatory), and credential format 
    *   + information of to which certain user SIOP is bound to
        *   What about delegation scenarios where data subject and data controller are different? => This could be left out initially?
        *   Could be an alias referring to a location where the OP metadata can be obtained
    *   What are MUST configuration information that all SIOPs need to support? Currently Iss = self-issued.me defines such static configuration values but if requirement 1 holds true, iss would have to be an https url as per Section 2.0 of OpenID Connect core	
        *   &lt;> Self-Issued Tokens must be clearly recognizable as such. If any iss can be specified how does the RP know the sub_jwk should be trusted to assert it? Current use of fixed ISS is correct from security point of view.
    *   Option: process akin to DID Discovery using holder identifier in the iss field?
    *   Option: advertise support in OP metadata registration
*   8.RP must be able to register with SIOP 
    *   Currently, section 7.2.1 of OIDC core defines an optional Authorization Request parameter ‘registration’ 
    *   Introducing the MUSTs (like JWT-based id_token)?
    *   Is it possible to have mechanisms akin to dynamic client registration without metadata endpoint for content negotiation? (RP doing Dynamic Client Registration is not realistic since SIOP that is usually on mobile cannot be expected to maintain a server with registration endpoint)
        *   Option: OpenID Connect Federation specification draft has some mechanisms for late binding/registration of clients that can be useful for SIOP
        *   Option: Having the client store the registration information (rather than the Authz server) = Encode necessary registration information about the Client into the client_id value returned by the initial registration of the Client. Per [Stateless Dynamic Client Registration](https://openid.net/specs/openid-connect-registration-1_0.html#StatelessRegistration)

E.Issuance of the claims (SIOP - Claims Provider)

*   9.SIOP providers can be registered with the Claims provider 
    *   This is not in the rest of OIDC, but needed in SIOP, where OP is not an issuer of identity data and SIOP acts more like a wallet
    *   SIOP could be recommended to perform OIDC Dynamic Client Registration, acting as a Client to the Claims Provider
    *   What does the client need to specify in a request? Concrete claims sources? Schemas? Trust Frameworks?
    *   Would  standardizing this limit the claims provider that can issue claims to SIOP? a separate topic, if the claims sources are themselves OIDC OPs.

F.Privacy protection

*   10.RPs should understand the security/privacy posture of SIOP
    *   Levels of Assurance are under regulated environment   
*   11.SIOP should support pairwise, omnidirectional, and ephemeral identifiers (= enables key rotation?)
    *   pairwise keys where SIOP creates a URI for each client and key pairs that are used with it. omnidirectional keys for certain cases and client amnesia for others.
*   12.Relationship between old and new identifiers/keys can be attested 
    *   Attestations made in the past should remain valid
*   13.RP must be able to receive the claims when the end-user is offline without colluding with the Claims Provider
    *   Configurations of claims provider
        *   “Frozen claims”: stored by SIOP when first requested by a given client
        *   “Frozen refresh”: use frozen claim till expired then fetch again
        *   “Realtime”: fetch on every presentation
    *   Introduce distributed claims model?
    *   Would this be Provider specific?

G.Claims Binding

H.Various OpenID providers deployment architectures (Authentication flows?)

*   14.Support PWA-based SIOP implementations 
    *   Alternative for a protocol-based URL needed
*   15.SIOP should support browser flow path, device flow path and combination of both
    *   Response_mode needs to be suited for multi-device flows
    *   Kiosk use-cases from Mattr


I.Use-case specific requirements

*   16.SIOP could support rich identity information sharing with RP (optional)
    *   RP should support rich assertion formats such as JSON-LD proofs (caveat: JSON-LD proofs spec is stable, but not published)
*   17.SIOP should allow for selective disclosure of claims in claim sets 
    *   Hard to reconcile distributed and aggregated claims spec with W3C VC spec
*   18.SIOP should allow offline authentication
    *   Needed in the scenarios where individuals get id_tokens/VCs issued based on offline authentication. ie refugee scenario where UNHCR employee verifies a refugee based on paper documents and issues a digital certificate to refugee's wallet. refugee does not have to enter id/pass
	
	
**Reference **

This requirement list is build upon various discussions and drafts whose non-exhaustive list can be found below:

* [https://nat.sakimura.org/2018/12/11/todo-list-for-self-issued-op/](https://nat.sakimura.org/2018/12/11/todo-list-for-self-issued-op/)
* [https://docs.google.com/document/d/1Q1kTbCjvr0LoD7fsgrtUW0-olorkeebyD7I6mBByMc4/edit#](https://docs.google.com/document/d/1Q1kTbCjvr0LoD7fsgrtUW0-olorkeebyD7I6mBByMc4/edit#)
* [https://bitbucket.org/openid/connect/issues/1180/siop-laundry-list](https://bitbucket.org/openid/connect/issues/1180/siop-laundry-list)
* [https://bitbucket.org/openid/connect/src/master/SIOP/draft-jones-self_issued_identifier.md](https://bitbucket.org/openid/connect/src/master/SIOP/draft-jones-self_issued_identifier.md)
* [https://hackmd.io/YL3juOZrRH65XV51bZR6dQ?view](https://hackmd.io/YL3juOZrRH65XV51bZR6dQ?view)