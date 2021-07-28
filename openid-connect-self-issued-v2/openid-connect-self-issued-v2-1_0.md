%%%
title = "Self-Issued OpenID Provider V2, draft 02"
abbrev = "siop-v2"
ipr = "none"
workgroup = "connect"
keyword = ["security", "openid", "ssi"]

[seriesInfo]
name = "Internet-Draft"
value = "openid-connect-self-issued-v2-1_0-02"
status = "standard"

[[author]]
initials="K."
surname="Yasuda"
fullname="Kristina Yasuda"
organization="Microsoft"
    [author.address]
    email = "kristina.yasuda@microsoft.com"

[[author]]
initials="M."
surname="Jones"
fullname="Michael B. Jones" 
organization="Microsoft"
    [author.address]
    email = "mbj@microsoft.com"

[[author]]
initials="T."
surname="Looker"
fullname="Tobias Looker"
organization="Mattr"
    [author.address]
    email = "tobias.looker@mattr.global"


%%%

.# Abstract

OpenID Connect defines mechanism by which an End-user can leverage an OpenID Provider (OP) to release identity information (such as authentication and attributes) to a Relying Party (RP) which can act on that information. 

This specification extends OpenID Connect with the concept of a Self-Issued OpenID Provider (Self-Issued OP), an OP which is within the End-User’s local control. End-users can leverage Self-Issued OPs to authenticate themselves and present claims directly to the Relying Parties. This allows users to interact with RPs directly, without relying on a third-party provider or requiring the End-User to operate their own hosted infrastructure.

{mainmatter}

# Introduction

This specification extends OpenID Connect with the concept of a _Self-Issued OpenID Provider_ (Self-Issued OP), an OP which is within the End-User’s local control. End-users can leverage Self-Issued OPs to authenticate themselves and present claims directly to Relying Parties. This allows users to interact with RPs directly, without relying on a third-party provider or requiring the End-User to operate their own hosted infrastructure.

An OP releases identity information such as End-user authentication in the form of an ID Token. An RP will typically trust an ID token based on a combination of any relationship between the RP and OP, a reputation-based stake of the OP in providing correct information, and the OP’s relationship with the shared End-Users.

A _Self-Issued ID Token_ is an ID Token issued by a Self-Issued OP. Such a token  differs from a ID Token in that traditional OpenID Connect Provider, as the trust relationship is with the End-user. 

This specification extends OpenID Connect Core 1.0 to account for the protocol and policy-level changes needed to support this model of self-issued identity. Aspects not defined in this specification are expected to continue to follow OpenID Connect Core 1.0.

Note: This specification replaces [Self-Issued OpenID Connect Provider DID Profile v0.1](https://identity.foundation/did-siop/) and was written as a working item of a liaison between Decentralized Identity Foundation and OpenID Foundation. 

# Use-cases

### Resilience against Sudden or Planned OP Unavailability
  
  An OpenID Provider's infrastructure may become unavailable or even destroyed due to natural disasters such as hurricanes, tsunamis and fires, or may be removed from service as a planned business decision. As Self-Issued OPs are local to the End-user environment, there is resilience against such events simultaneously affecting a significant portion of otherwise unimpacted End-Users.

### Authentication at the edge

  As internet-connected smartphones have risen in availability, traditionally in-person interactions and services have begun to be optimized with digital alternatives. These services often have requirements for digital authentication and for other identity credentials. Self-Issued OPs can provide this authentication directly, without needing to delegate to remote, hosted OPs. This potentially allows for increased efficiency as well as allowing for authentication in environments which may have reduced connectivity.

### Sharing credentials from several issuers in one transaction 

  When End-users apply to open a banking account online, in most countries they are required to submit scanned versions of the required documents. These documents are usualy issued by different authorities, and hard to be verified in a digital form. A Self-issued OP directly representing the user may have access to a greater set of such information as credentials, while a traditional OP may not have a business relationship which enables access to such a breadth of information. Self-Issued OPs could aggregate credentials from multiple sources, then release them within a single transaction to a relying party. The relying party can then verify the authenticity of the information to make the necessary business decisions.

### End-user choice in identity policy

  End-users often use several hosted OpenID Providers for different Relying Parties. While there are many reasons to do this, often this is done to have separately maintained identities, such as keeping a work-related persona separate from a personal persona. An End-user may do this to specifically represent different sets of claims, or because they are unsure if the OP policy allows different relying parties to potentially correlate between themselves. The usage of multiple OPs can create friction later, as the end-user may return later having forgot which OP they used for the relying party.

  A Self-Issued OP implementation can be selected by the End-user based on its capability to represent and isolate personas from one another. The same Self-Issued OP software may be used locally for multiple personas, and can track which persona was used for which relying party. The ability to aggregate third-party credentials and present them enables a single Self-Issued OP to be chosen as a preferred source of identity by the End-user, based on its capabilities and privacy reputation.

# Scope 

As a Self-Issued OP may be running locally as a native application or progressive web application, the RP may not have a network-addressable endpoint to communicate directly with the OP. This specification leverages the implicit flow of OpenID Connect to communicate with such locally-running OP, and extends OpenID Connect Discovery to represent the differences from traditional OPs.

This document is scoped for a deployment model where Self-Issued OP is deployed on an End-user's device.

This specification defines:

* Invocation of Self-Issued OP

  This specification defines extensions to the OpenID Connect implicit flow to invoke a Self-Issued OP from a Relying Party. It also proposes user experience behavior when no appropriate Self-Issued Op is available locally.

* Negotiation of supported functionality between a RP and Self-Issued OP

  OpenID Connect typically leverages a registration between a Client (acting as a RP) and the OP, which pre-establishes supported functionality before a request has been made. This may be done statically, or may leverage a combination of OpenID Connect Discovery and OpenID Connect Dynamic Client Registration.
  
  Relying Parties typically cannot pre-establish registration with a Self-Issued OP, as each End-user might be represented by a different, locally-controlled Self-Issued OP instance. This specification extends the authentication request with a mechanism with additional dynamic registration techniques for feature negotiation.

* The format and processing requirements of Self-Issued ID Tokens
  
* Including self-asserted (non-verifiable) claims within a Self-Issued ID Token

* Usage of cryptographically-verifiable identifiers as a way for RPs to identify the Authenticated user 

  A _cryptographically-verifiable subject identifier_ is a subject identifier which is either based upon or resolves to cryptographic key material. Self-Issued OPs can prove possession of the underlying key during the OpenID message exchange with the RP, in order to strongly re-authenticate as the End-user associated with that identifier.
  
  This specification defines a subject identifier which is a thumbprint of the public key material. The public key itself is shared within the id_token as a JWK. The cryptographic algorithm needs to be supported by both the Self-Issued OP and RP.

  This specification also allows for _resolvable identifiers_, which are URI which have one or more defined processes for resolving specific kinds of data. Such identifiers may be used as the subject identifier when they resolve to a statement on the cryptographic identity/identities of the subject. 
  
  Both the Self-Issued OP and the RP need to understand how to resolve such identifiers and would need to support the underlying cryptographic algorithms for an identity. One example of how to resolve such identifiers would be the use of _Decentralized Identifiers_ (DIDs), and specifically the individual DID methods. For compatibility with the existing JWS/JWE-based cryptography of OpenID Connect, it is assumed such resolved cryptographic identities are representable as a JWKS, where individual identities have a cryptographic algorithm and can be differentiated by a key identifier.
  
The following are considered out of scope of this document:

* Presentation of aggregated credentials

  A traditional OP could release additional claims about the user within an ID Token such as a verified email address. The trust in the validity of the claim is based on the trust between the RP and OP. The trust model of a Self-Issued OP is such that this mechanism typically cannot be used to assert verified claims. This specification extends OpenID Connect to allow for any OP to present additional credentials from other sources, to provide a trusted source for End-user claims or claims about other parties whom the End-user is authorized to represent.

  The mechanism for requesting and returning such credentials is out of scope of this document. For example: Verifiable Presentations usage is being defined within the OIDC4VP specification, where it can be used for both Self-Issued OPs and traditional OPs.

* Provisioning of aggregated credentials

  The mechanism for a Self-Issued OP to acquire credentials which can be presented is out of scope of this document. Similar to presentation, a traditional OP may also wish to acquire third-party credentials to present to Relying Parties. One mechanism to provision credentials is being defined within the Claims Aggregation specification.

# Terms and definitions

Common terms in this document come from four primary sources: DID-CORE, VC-DATA, RFC6749 and OpenID-Core. In the case where a term has a definition that differs, the definition below is authoritative.

- Trust framework
    -  a legally enforceable set of specifications, rules, and agreements that govern a multi-party system established for a common purpose, designed for conducting specific types of transactions among a community of participants, and bound by a common set of requirements. [OIX]

- Cryptographically-verifiable identifier
    -  an identifier which is either based upon or resolves to cryptographic key material.

## Abbreviations 

* Self-Issued OP: Self-Issued OpenID Provider
* RP: Relying Party
* OP: OpenID Provider

# Protocol Flow

Self-Issued OpenID Provider Request is an OpenID Connect Authentication Request that results in an End-user providing ID Token to the Relying Party through the Self-Issued OP. ID Token MAY include attested claims about the End-user.

~~~ ascii-art
+----------+                                             +----------------+
|          |                                             |                |
|          |---(1) Self-Issued OpenID Provider Request-->|                |
|          |       (Authentication Request)              |                |
|          |       +------------+                        |                |
|          |       |            |                        |                |         
|          |       |  End-User  |                        |                |         
|    RP    |       |            |<--(2) AuthN & AuthZ--->| Self-Issued OP |
|          |       |            |                        |                |
|          |       +------------+                        |                |
|          |                                             |                |
|          |<--(3) Self-Issued OpenID Provider Response--|                |
|          |       (ID Token)                            |                |
|          |                                             |                |   
+----------+                                             +----------------+
~~~
Figure: Self-Issued Op Protocol Flow

# Discovery and Negotiation

## Self-Issued OpenID Provider Discovery

When the End-user first interacts with the RP there are no established means to signal where to direct the request for an available Self-Issued OP application. Even if possible, such signals may be susceptible to fingerprinting and passive tracking of the End-user.

The RP is therefore responsible for selecting where to direct the request URL. When the RP wants to support the End-user's choice oto select from multiple possible Self-Issued OP applications, it MAY present a static list of the available choices. This is very similar to the process of supporting multiple different social networks.

Alternatively the RP MAY belong to at least one trust framework. The trust framework is then responsible for hosting a public website that maintains the latest platform specific metadata for all supported Self-Issued OP applications, known as app-link or universal link at the time of publication. The RP forms the request URL to that shared website and any of the supported installed applications will instead be launched and given the request to process. If none are available, the website will be displayed with the static list for the End-user to choose from to install or use.

The trust framework MAY be operated by just one RP, but due to the required maintenance of every application's metadata (which may change frequently) this burden SHOULD be shared across multiple RPs. The same trust framework MAY also be used to host metadata about the supported RPs such that the Self-Issued OP applications can verify the origin of the incoming request as part of the framework as well.

The legacy usage of custom protocol schemas such as `openid:` as a way to invoke any installed Self-Issued OP is NOT RECOMMENDED due to the security issues (see (invocation-using-custom-schema) in Privacy Considerations section).

### Self-Issued OpenID Provider Discovery Metadata

If the input identifier for the discovery process contains the domain self-issued.me, dynamic discovery is not performed. Instead, then the following static configuration values are used:

* authorization_endpoint
    * REQUIRED. MUST include `openid:`, could also include additional custom schema.
* issuer
    * REQUIRED. MUST be `https://self-issued.me/v2`
* response_types_supported
    * REQUIRED. MUST be `id_token`
* scopes_supported
    * REQUIRED. A JSON array of strings representing supported scopes. Valid values include `openid`, `profile`, `email`, `address`, and `phone`.
* subject_types_supported
    * REQUIRED. A JSON array of strings representing supported subject types. Valid values include `pairwise` and `public`.
* id_token_signing_alg_values_supported
    * REQUIRED. ID token signing alg values supported. Valid values include `RS256`, `ES256`, `ES256K`, and `EdDSA`.
* request_object_signing_alg_values_supported
    * REQUIRED. Request object signing alg values supported. Valid values include `none`, `RS256`, `ES256`, `ES256K`, and `EdDSA`. 

The following is a non-normative example of the supported Self-issued OP Discovery metadata values:

```
  {
   "authorization_endpoint":
    "openid:",
   "issuer":
    "https://self-issued.me/v2",
   "response_types_supported":
    ["id_token"],
   "scopes_supported":
    ["openid", "profile", "email", "address", "phone"],
   "subject_types_supported":
    ["pairwise"],
    "id_token_signing_alg_values_supported":
    ["ES256", "ES256K"],
   "request_object_signing_alg_values_supported":
    ["ES256", "ES256K"]
  }
```

## Relying Party Registration

Relying Party must communicate which metadata parameters it supports. If Self-Issued OP and RP mutually support a compatible set of parameters, Self-Issued OP flow continues. If they do not, Self-Issued OP returns an error. Metadata parameters should preferrably be sent by reference as a URI, but when RP cannot host a webserver, they can be sent by value.

OpenID Connect defines the following negotiation parameters to enable Relying Party to provide information about itself to a Self-Issued OP that would normally be provided to an OP during Dynamic Client Registration:
  
`registration` **OPTIONAL**. This parameter enables RP Registration Metadata to be passed in a single, self-contained parameter. The value is a JSON object containing RP Registration Metadata values. 

`registration_uri` **OPTIONAL**. This parameter enables RP Registration Metadata to be passed by reference, rather than by value. The request_uri value is a URL using the https scheme referencing a resource containing RP Negotiation Metadata values.

RP MUST use either of there parameters, but if one of these parameters is used, the other MUST NOT be used in the same request.

RP Negotiation metadata values are defined in Section 4.3 and Section 2.1 of the OpenID Connect Dynamic RP Registration 1.0 [OpenID.Registration] specification.

If Self-Issued OP supports the same parameters, Self-Issued OpenID Provider flow continues, if Self-Issued OP does not support, it returns an error. 

If no error is returned, the RP must proceed as if it had obtained the following Client Registration Response: 

* client_id
    * `redirect_uri` value of the Client.
* client_secret_expires_at
    * 0

Metadata parameters should preferably be sent by reference as a URI using `registration_uri` parameter, but when RP cannot host a webserver, metadata parameters should be sent by value using `registration` parameter. 

`registration` and `registration_uri` parameters SHOULD NOT be used when the OP is not a Self-Issued OP. 


### Passing Relying Party Registration Metadata by Value
    
The `registration` SIOP Request parameter enables RP Registration Metadata to be passed in a single, self-contained parameter.
    
The registration parameter value is represented in an OAuth 2.0 request as a UTF-8 encoded JSON object (which ends up being form-urlencoded when passed as an OAuth parameter). When used in a Request Object value, per Section 6.1, the JSON object is used as the value of the registration member.


### Passing Relying Party Registration Metadata by Reference

The `registration_uri` SIOP Request parameter enables RP Registration Metadata to be passed by reference. 

This parameter is used identically to the request parameter, other than that the Relying Party registration metadata value is retrieved from the resource at the specified URL, rather than passed by value.

The contents of the resource referenced by the URL MUST be a RP Registration Metadata Object. The scheme used in the `registration_uri` value MUST be https. The request_uri value MUST be reachable by the Self-Issued OP, and SHOULD be reachable by the RP.

### Relying Party Registration Metadata Values

This extension defines the following RP Registration Metadata values, used by the RP to provide information about itself to the Self-Issued OP:

* `authorization_endpoint`
    * REQUIRED. MUST include `openid:`, could also include additional custom schema.
* `issuer`
    * REQUIRED. MUST be `https://self-issued.me/v2`
* `response_types_supported`
    * REQUIRED. MUST be `id_token`
* `scopes_supported`
    * REQUIRED. A JSON array of strings representing supported scopes. Valid values include `openid`, `profile`, `email`, `address`, and `phone`.
* `subject_types_supported`
    * REQUIRED. A JSON array of strings representing supported subject types. Valid values include `pairwise` and `public`.
* `subject_identifier_types_supported`
    * REQUIRED. A JSON array of strings representing supported subject identifier types. Valid values include `jkt` and `did`. 
* `did_methods_supported`
    * OPTIONAL. A JSON array of strings representing supported DID methods. Valid values must take the value of `Method Name` in Chapter 9 of [did-spec-registries](https://w3c.github.io/did-spec-registries/#did-methods), such as `did:peer:` RP can indicate support for any DID method by omitting `did_methods_supported`, while including `did` in `subject_identifier_types_supported'. 
* `credential_formats_supported`
    * REQUIRED. A JSON array of strings representing supported credential formats. Valid values include `jwt`, `jwt_vc`, `jwt_vp`, `ldp_vc`, and `ldp_vp`. 
* `id_token_signing_alg_values_supported`
    * REQUIRED. ID token signing alg values supported. Valid values include `RS256`, `ES256`, `ES256K`, and `EdDSA`.
* `request_object_signing_alg_values_supported`
    * REQUIRED. Request object signing alg values supported. Valid values include `none`, `RS256`, `ES256`, `ES256K`, and `EdDSA`.

Other registration parameters defined in [OpenID.Registration] could be used. Examples are explanatory parameters such as `policy_uri`, `tos_uri`, and `logo_uri`. If the RP uses more than one Redirection URI, the `redirect_uris` parameter would be used to register them. Finally, if the RP is requesting encrypted responses, it would typically use the `jwks_uri`, `id_token_encrypted_response_alg` and `id_token_encrypted_response_enc` parameters.

Registration parameter may include decentralized identifier of the RP.

The following is a non-normative example of the supported RP Registration Metadata Values:

```json
  {
   "subject_identifier_types_supported":
    ["did", "jkt"],
    "did_methods_supported":
    ["did:key:", "did:example:"]
  }
```

## Sub Types

A sub type is used by Self-Issued OP to advertise which types of identifiers are supported for the `sub` claim. Two types are defined by this specification:

* `jkt`
    * JWK Thumbprint Subject sub type. When this subject sub type is used, the `sub` claim value MUST be the base64url encoded representation of the thumbprint of the key in the `sub_jwk` claim. [RFC7638]
    
* `did`
    * Decentralized sub type. When this sub type is used,  the `sub` value MUST be a DID defined in [DID-CORE]. 
    
NOTE: Consider adding a subject type for OpenID Connect Federation entity statements.

## Relying Party Registration Metadata Error Response

This extension defines the following error codes that MUST be returned when Self-Issued OP does not support all of the Relying Party Registration metadata values received from the Relying Party in the registration parameter:

* `did_methods_not_supported`
    * The Self-Issued OP does not support all of the DID methods included in `did_methods_supported` parameter.
* `subject_identifier_types_not_supported`
    * The Self-Issued OP does not support all of the subject identifier types included in `subject_identifier_types_supported` parameter.
* `credential_formats_not_supported`
    * The Self-Issued OP does not support all of the credential formats included in `credential_formats_supported` parameter.
* `value_not_supported`
    * The Self-Issued OP does not support more than one of the RP Registration Metadata values defined in Section 4.3. When not supported metadata values are DID methods, subject identifier types, or credential formats, more specific error message must be used.
* `invalid_registration_uri`
    * The registration_uri in the Self-Issued OpenID Provider request returns an error or contains invalid data.
* `invalid_registration_object`
    * The registration parameter contains an invalid RP Registration Metadata Object.

Error response must be made in the same manner as defined in Section 3.1.2.6.


# Identifier Portability and Verifiable Presentation Support

## Self-Issued OpenID Provider Request

The RP sends the Authentication Request to the Authorization Endpoint with the following parameters:

* `scope`
    * REQUIRED. `scope` parameter value, as specified in Section 3.1.2.
* `response_type`
    * REQUIRED. Constant string value `id_token`.
* `client_id`
    * REQUIRED. Client ID value for the Client, which in this case contains the `redirect_uri` value of the RP.
* `redirect_uri`
    * REQUIRED. MUST equal to `client_id` value. MUST be included for compatibility reasons.
* `id_token_hint`
    * OPTIONAL. id_token_hint parameter value, as specified in Section 3.1.2. If the ID Token is encrypted to the Self-Issued OP, the sub (subject) of the signed ID Token MUST be sent as the kid (Key ID) of the JWE. 
* `claims`
    * OPTIONAL. claims parameter value, as specified in Section 5.5.
* `registration`
    * OPTIONAL. This parameter is used by the RP to provide information about itself to a Self-Issued OP that would normally be provided to an OP during Dynamic RP Registration, as specified in Section 2.2.1.  
* `registration_uri`
    * OPTIONAL. This parameter is used by the RP to provide information about itself to a Self-Issued OP that would normally be provided to an OP during Dynamic RP Registration, as specified in Section 2.2.2. 
* `request`
    * OPTIONAL. Request Object value, as specified in Section 6.1. The Request Object MAY be encrypted to the Self-Issued OP by the RP. In this case, the sub (subject) of a previously issued ID Token for this RP MUST be sent as the kid (Key ID) of the JWE. 
* `request_uri`
    * OPTIONAL. URL where Request Object value can be retrieved from, as specified in Section 6.2.
    
When `request` or `request_uri` parameters are NOT present, `registration` or `registration_uri` parameters MUST be present in the request. When `request` or `request_uri` parameters are present, `registration` or `registration_uri` parameters MUST be included in either of those parameters.

Other parameters MAY be sent. Note that all Claims are returned in the ID Token.

The entire URL MUST NOT exceed 2048 ASCII characters.

The following is a non-normative example HTTP 302 redirect response by the RP, which triggers the User Agent to make an Authentication Request to the Self-Issued OP (with line wraps within values for display purposes only):

```
  HTTP/1.1 302 Found
  Location: openid://?
    response_type=id_token
    &client_id=https%3A%2F%2Fclient.example.org%2Fcb
    &redirect_uri=https%3A%2F%2Fclient.example.org%2Fcb
    &scope=openid%20profile
    &state=af0ifjsldkj
    &nonce=n-0S6_WzA2Mj
    &registration=%7B%22logo_uri%22%3A%22https%3A%2F%2F
      client.example.org%2Flogo.png%22%7D
```


## Self-Issued OpenID Provider Response

Self-Issued OpenID Provider Response is returned when Self-Issued OP supports all of the Relying Party Registration metadata values received from the Relying Party in the registration parameter. If even one of the Relying Party Registration Metadata Values is not supported, Self-Issued OP MUST return an error according to Section 4.4.

This extension defines the following claims to be included in the ID token for use in Self-Issued OpenID Provider Responses: 

* `sub`
    * REQUIRED. Subject identifier value, represented by a URI. When sub type is `jkt`, the value is the base64url encoded representation of the thumbprint of the key in the `sub_jwk` Claim. When sub type is `did`, the value is a decentralized identifier. The thumbprint value is computed as the SHA-256 hash of the octets of the UTF-8 representation of a JWK constructed containing only the REQUIRED members to represent the key, with the member names sorted into lexicographic order, and with no white space or line breaks. For instance, when the kty value is RSA, the member names e, kty, and n are the ones present in the constructed JWK used in the thumbprint computation and appear in that order; when the kty value is EC, the member names crv, kty, x, and y are present in that order. Note that this thumbprint calculation is the same as that defined in the JWK Thumbprint [RFC7638] specification.
* `sub_jwk`
    * REQUIRED. a secure binding between the subject of the verifiable credential and the subject identifier (and related keys) of the holder who creates the presentation. When subr type is `jkt`, the key is a bare key in JWK [JWK] format (not an X.509 certificate value). When sub type is `did`, sub_jwk MUST contain a kid that is a DID URL referring to the verification method in the Self-Issued OP's DID Document that can be used to verify the JWS of the id_token directly or indirectly. The sub_jwk value is a JSON object. Use of the `sub_jwk` Claim is NOT RECOMMENDED when the OP is not Self-Issued. 
* vp
    * OPTIONAL. A JSON object, that represents a JWT verifiable presentation, following W3C Verifiable Credentials Specification [VC-DATA-MODEL]. Verifiable Credentials must be embedded in the Verifiable Presentation following W3C Verifiable Credentials Specification [VC-DATA-MODEL]
    
Verifiable Presentation is data derived from one or more Verifiable Credentials, issued by one or more issuers, that is shared with a specific verifier. Verifiable Credential is a set of one or more claims made by an issuer.

Self-Issued OP may present credentials to the RP using Verifiable Presentation credential format by including it in the `vp` claim inside the ID token. 

Whether the Self-Issued OP is a mobile client or a web client, response is the same as the normal Implicit Flow response with the following refinements. Since it is an Implicit Flow response, the response parameters will be returned in the URL fragment component, unless a different Response Mode was specified.

1. The `iss` (issuer) Claim Value is `https://self-issued.me/v2`.
1. A `sub_jwk` Claim is present, with its value being the public key used to check the signature of the ID Token.
1. The `sub` (subject) Claim value is either the base64url encoded representation of the thumbprint of the key in the `sub_jwk` Claim or a decentralized identifier. 
1. No Access Token is returned for accessing a UserInfo Endpoint, so all Claims returned MUST be in the ID Token.

## Verifiable Presentation Support

Self-Issued OP and the RP that wish to support request and presentation of Verifiable Presentations MUST be compliant with OpenID Connect for Verifiable Presentations [@!OIDC4VP] and W3C Verifiable Credentials Specification [VC-DATA-MODEL].  

Verifiable Presentation is a tamper-evident presentation encoded in such a way that authorship of the data can be trusted after a process of cryptographic verification. Certain types of verifiable presentations might contain data that is synthesized from, but do not contain, the original verifiable credentials (for example, zero-knowledge proofs). [VC-DATA-MODEL] 

See [@!OIDC4VP] on how to support multiple credential formats such as JWT and Linked Data Proofs. 

## Self-Issued ID Token Validation

To validate the ID Token received, the RP MUST do the following:

1. The Relying Party (RP) MUST validate that the value of the `iss` (issuer) Claim is `https://self-isued.me`. If iss contains a different value, the ID Token is not Self-Issued, and instead it MUST be validated according to Section 3.1.3.
1. The RP MUST validate that the `aud` (audience) Claim contains the value of the `redirect_uri` that the RP sent in the Authentication Request as an audience.
1. The RP MUST validate the signature of the ID Token. When sub type is`jkt`, validation is done according to JWS [JWS] using the algorithm specified in the alg Header Parameter of the JOSE Header, using the key in the `sub_jwk` Claim. When sub type is`did`, vvalidation is done using the key derived as a result of DID Resolution as defined in [DID-CORE]. The key is a bare key in JWK format (not an X.509 certificate value) when sub type is`jkt` or may be another key format when sub type is `did`.
1. Default `alg` value is RS256. It MAY also be ES256, ES256K or EdDSA.
1. The RP MUST validate that the`sub` claim is bound to the `sub_jwk` value. When sub type is`jkt`, the RP MUST validate that the sub Claim value is the base64url encoded representation of the thumbprint of the key in the `sub_jwk` Claim, as specified in Section 6. When sub type is `did`, the RP MUST validate that the `kid` of the `sub_jwk` claim matches the verification method from the DID Document that is obtained by resolving decentralized identifier included in `sub` claim.
1. The current time MUST be before the time represented by the `exp` Claim (possibly allowing for some small leeway to account for clock skew).
 The `iat` Claim can be used to reject tokens that were issued too far away from the current time, limiting the amount of time that nonces need to be stored to prevent attacks. The acceptable range is RP specific.
1. If a `nonce` value was sent in the Authentication Request, a `nonce` Claim MUST be present and its value checked to verify that it is the same value as the one that was sent in the Authentication Request. The RP SHOULD check the `nonce `value for replay attacks. The precise method for detecting replay attacks is RP specific.

The following is a non-normative example of a base64url decoded Self-Issued ID Token (with line wraps within values for display purposes only):

```
  {
   "iss": "https://self-issued.me/v2",
   "sub": "NzbLsXh8uDCcd-6MNwXF4W_7noWXFZAfHkxZsRGC9Xs",
   "aud": "https://client.example.org/cb",
   "nonce": "n-0S6_WzA2Mj",
   "exp": 1311281970,
   "iat": 1311280970,
   "sub_jwk": {
     "kty":"RSA",
     "n": "0vx7agoebGcQSuuPiLJXZptN9nndrQmbXEps2aiAFbWhM78LhWx
     4cbbfAAtVT86zwu1RK7aPFFxuhDR1L6tSoc_BJECPebWKRXjBZCiFV4n3oknjhMs
     tn64tZ_2W-5JsGY4Hc5n9yBXArwl93lqt7_RN5w6Cf0h4QyQ5v-65YGjQR0_FDW2
     QvzqY368QQMicAtaSqzs8KJZgnYb9c7d0zgdAZHzu6qMQvRL5hajrn1n91CbOpbI
     SD08qNLyrdkt-bFTWhAI4vMQFh6WeZu0fM4lFd2NcRwr3XPksINHaQ-G_xBniIqb
     w0Ls1jF44-csFCur-kEgU8awapJzKnqDKgw",
     "e":"AQAB"
    }
　}
```

The following is a non-normative example of an ID token containing a verfiable presentation (with line wraps within values for display purposes only): 
```
  {
   "iss": "https://self-issued.me/v2",
   "sub": "NzbLsXh8uDCcd-6MNwXF4W_7noWXFZAfHkxZsRGC9Xs",
   "aud": "https://client.example.org/cb",
   "nonce": "n-0S6_WzA2Mj",
   "exp": 1311281970,
   "iat": 1311280970,
   "sub_jwk": {
     "kty":"RSA",
     "n": "0vx7agoebGcQSuuPiLJXZptN9nndrQmbXEps2aiAFbWhM78LhWx
     4cbbfAAtVT86zwu1RK7aPFFxuhDR1L6tSoc_BJECPebWKRXjBZCiFV4n3oknjhMs
     tn64tZ_2W-5JsGY4Hc5n9yBXArwl93lqt7_RN5w6Cf0h4QyQ5v-65YGjQR0_FDW2
     QvzqY368QQMicAtaSqzs8KJZgnYb9c7d0zgdAZHzu6qMQvRL5hajrn1n91CbOpbI
     SD08qNLyrdkt-bFTWhAI4vMQFh6WeZu0fM4lFd2NcRwr3XPksINHaQ-G_xBniIqb
     w0Ls1jF44-csFCur-kEgU8awapJzKnqDKgw",
     "e":"AQAB"
    },
    "verifiable_presentations":[
      {
         "format":"vp_jwt",
         "presentation":"ewogICAgImlzcyI6Imh0dHBzOi8vYm9vay5pdHNvdXJ3ZWIub...IH0="
      }
   ],
　}
```

Note: Further processing steps are required if the authentication response contains verifiable presentations - see [@!OIDC4VP].

## Security Considerations
 
### Invocation using Custom Schema {invocation-using-custom-schema}

Usage of custom schemas as a way to invoke a Self-Issued OP may lead to phishing attacks and undefined behavior. 

Custom schema is a mechanism offered by Mobile Operating System providers. If an application developer registers custom schema with the application, that application will be invoked when a request containing custom schema is received by the device.

Any malicious app can register the custom schema already used by another app, imitate the user interface and impersonate a good app. 

When more than one Self-issued OP with the same custom schema has been installed on one device, the behavior of Self-Issued OP is undefined. 

## Privacy Considerations
 
### Selective disclosure and un-linkable presentations
Usage of decentralized identifiers does not automatically prevent possible RP correlation. If a status check of the presentation is done, IdP / SIOP correlation can occur.

Consider supporting selective disclosure and un-linkable presentations using zero-knowledge proofs or single-use credentials instead of traditional correlatable signatures.
    
# References
 
## Normative References
 
* [DID-CORE] https://github.com/w3c/did-core (not yet a ratified draft)
* [VC-DATA] https://www.w3.org/TR/vc-data-model/
* [RFC6749] https://tools.ietf.org/html/rfc6749
* [RFC6750] https://tools.ietf.org/html/rfc6750
* [OpenID.Core] https://openid.net/specs/openid-connect-core-1_0.html
* [RFC7638] https://tools.ietf.org/html/rfc7638
* [OpenID.Registration] https://openid.net/specs/openid-connect-registration-1_0.html
* [did-spec-registries] https://w3c.github.io/did-spec-registries/#did-methods
 
## Non-Normative References
 
* [draft-jones-self_issued_identifier] https://bitbucket.org/openid/connect/src/master/SIOP/draft-jones-self_issued_identifier.md
* [siop-requirements] https://bitbucket.org/openid/connect/src/master/SIOP/siop-requirements.md
* [OIX] https://openidentityexchange.org/networks/87/item.html?id=365
 
 
# Relationships to other documents 
 
The scope of this draft was an extention to OpenID Connect Chapter 7 Self-Issued OpenID Provider. However, some sections of it could become applicable more generally to the entire OpenID Connect specification.
 
# IANA Considerations
 
TBD

# Notices

Copyright (c) 2020 The OpenID Foundation.

The OpenID Foundation (OIDF) grants to any Contributor, developer, implementer, or other interested party a non-exclusive, royalty free, worldwide copyright license to reproduce, prepare derivative works from, distribute, perform and display, this Implementers Draft or Final Specification solely for the purposes of (i) developing specifications, and (ii) implementing Implementers Drafts and Final Specifications based on such documents, provided that attribution be made to the OIDF as the source of the material, but that such attribution does not indicate an endorsement by the OIDF.

The technology described in this specification was made available from contributions from various sources, including members of the OpenID Foundation and others. Although the OpenID Foundation has taken steps to help ensure that the technology is available for distribution, it takes no position regarding the validity or scope of any intellectual property or other rights that might be claimed to pertain to the implementation or use of the technology described in this specification or the extent to which any license under such rights might or might not be available; neither does it represent that it has made any independent effort to identify any such rights. The OpenID Foundation and the contributors to this specification make no (and hereby expressly disclaim any) warranties (express, implied, or otherwise), including implied warranties of merchantability, non-infringement, fitness for a particular purpose, or title, related to this specification, and the entire risk as to implementing this specification is assumed by the implementer. The OpenID Intellectual Property Rights policy requires contributors to offer a patent promise not to assert certain patent claims against other contributors and against implementers. The OpenID Foundation invites any interested party to bring to its attention any copyrights, patents, patent applications, or other proprietary rights that may cover technology that may be required to practice this specification.
   
# Document History

* 01
    * Version proposed for working group adoption

{backmatter}

<reference anchor="OIDC4VP" target="https://openid.net/specs/openid-connect-4-verifiable-presentations-1_0.html">
  <front>
    <title>OpenID Connect Core 1.0 incorporating errata set 1</title>
    <author initials="O." surname="Terbu" fullname="Oliver Terbu">
      <organization>ConsenSys Mesh</organization>
    </author>
    <author initials="T." surname="Lodderstedt" fullname="Torsten Lodderstedt">
      <organization>yes.com</organization>
    </author>
    <author initials="K." surname="Yasuda" fullname="Kristina Yasuda">
      <organization>Microsoft</organization>
    </author>
    <author initials="A." surname="Lemmon" fullname="Adam Lemmon">
      <organization>Convergence.tech</organization>
    </author>
    <author initials="T." surname="Looker" fullname="Tobias Looker">
      <organization>Mattr</organization>
    </author>
   <date day="20" month="May" year="2021"/>
  </front>
</reference>