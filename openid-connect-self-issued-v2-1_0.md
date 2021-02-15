# Self-Issued OpenID Provider V2, draft 01

Kristina Yasuda, Microsoft, [kristina.yasuda@microsoft.com](mailto:kryasuda@microsoft.com)

Michael B. Jones, Microsoft, [mbj@microsoft.com](mailto:mbj@microsoft.com)

Tobias Looker, Mattr, [tobias.looker@mattr.global](mailto:tobias.looker@mattr.global)


December 8, 2020

1. Introduction

    1.1. Scope
    
    1.2. Terms and Definitions
    
    1.3. Abbreviations
    
    1.4. Protocol Flow

2. Discovery and Registration
    
    2.1. Self-Issued OpenID Provider Discovery
    
    2.2. Relying Party Registration
        
      2.2.1. Passing Relying Party Registration Metadata by Value
        
      2.2.2. Passing Relying Party Registration Metadata by Reference
        
      2.2.3. Relying Party Registration Metadata Values
            
      2.2.3.1. Sub Types
        
      2.2.4. Relying Party Registration Metadata Error Response

3. Identifier Portability and Verifiable Presentation Support
    
    3.1.  Self-Issued OpenID Provider Request
    
    3.2.  Self-Issued OpenID Provider Response
    
    3.3. Self-Issued ID Token Validation

4. References
    
    4.1. Normative References
    
    4.2. Non-Normative References
    
    4.3. Relationships to other documents

5. Notices

Appendix A.History


## 1. Introduction
OpenID Connect supports Self-Issued OpenID Providers(Self-Issued OPs) - personal OpenID Providers(OPs) that issue self-signed ID Tokens, enabling portability of the identities among providers.

This specification defines how a Holder provides ID Token to the Relying Party(RP) through the Self-Issued OP, and how a Holder asks and receives attested claims that can be included in the ID Token, when SIOP is deployed on a device rather than on a server.

Specifications for the few additional parameters and for the values of some parameters are defined in this section. Self-Issued OpenID Provider is an extension to OpenID Connect 1.0, and aspects not defined in this section must follow OpenID Connect 1.0.


Note: This specification replaces [Self-Issued OpenID Connect Provider DID Profile v0.1](https://identity.foundation/did-siop/) and was written as a working item of a liaison between Decentralized Identity Foundation and OpenID Foundation. 

## 1.1. Scope 
This document is scoped for a deployment model where Self-Issued OP is deployed on a user's device.

In scope:

- Discovery of Self-Issued OP
- Registration of metadata supported by RP with Self-Issued OP
- Mechanism for RPs to identify the authenticated user while the Holder being able to modify cryptographic keys used to prove control over the identifier
- Mechanism for Self-Issued OPs to present claims using additional credential formats that enable the Holder to prove posession over the claims.


Out of Scope:

- Claims issuance flow that defines how Self-Issued OP requests and receives claims from a Claims Provider acting in RP's capacity. Self-Issued OP can present those claims to the RP in Self-Issued OpenID Provider response defined in this document. 

## 1.2. Terms and definitions
Common terms in this document come from four primary sources: [DID-CORE],[VC-DATA], [RFC6749] and [OpenID.Core]. In the case where a term has a definition that differs, the definition below is authoritative.

## 1.3. Abbreviations 
- Self-Issued OP: Self-Issued OpenID Provider
- RP: Relying Party

## 1.4. Protocol Flow

Self-Issued OpenID Provider Request is an OpenID Connect Authentication Request that results in a Holder providing ID Token to the Relying Party through the Self-Issued OP. ID Token MAY include attested claims about the Holder.

```
+----------+                                                    +--------+
|          |                                                    |        |
|          |-------(1) Self-Issued OpenID Provider Request----->|        |
|          |          (OpenID Connect Authentication Request)   |        |
|          |                     +--------+                     |        |
|          |                     |        |                     |        |         
|          |                     |  Hol-  |                     |        |         
|    RP    |                     |  der   |<-(2) AuthN & AuthZ->|   OP   |
|          |                     |        |                     | (Self- |
|          |                     +--------+                     | Issued |
|          |                                                    |   OP)  |
|          |<------(3) Self-Issued OpenID Provider Response-----|        |
|          |                 (ID Token)                         |        |
|          |                                                    |        |   
+----------+                                                    +--------+
```
    

## 2 Discovery and Registration

## 2.1. Self-Issued OpenID Provider Discovery
Self-Issued OP MUST associate a custom schema `openid://` with itself. Relying Party MUST call `openid://` when sending a request to a Self-Issued OP.

Note: When more than one Self-issued OP with the same custom schema has been installed on one device, there could be confusion over which Self-Issued OP gets invoked. 

## 2.2. Relying Party Registration

Relying Party must communicate which configuration parameters it supports. If SIOP supports the same parameters, SIOP flow continues, if SIOP does not support, it returns an error. Configuration parameters should preferrably sent by reference as a URI, but when RP cannot host a webserver, they can be sent by value.

OpenID Connect defines the following registration parameters to enable Relying Party to provide information about itself to a Self-Issued OP that would normally be provided to an OP during Dynamic RP Registration:
  
- registration
    - OPTIONAL. This parameter enables RP Registration Metadata to be passed in a single, self-contained parameter. The value is a JSON object containing RP Registration Metadata values. 

- registration_uri
    - OPTIONAL. This parameter enables RP Registration Metadata to be passed by reference, rather than by value. The request_uri value is a URL using the https scheme referencing a resource containing RP Registration Metadata values.

RP MUST use either of there parameters, but if one of these parameters is used, the other MUST NOT be used in the same request.

RP Registration Metadata values are defined in Section 4.3 and Section 2.1 of the OpenID Connect Dynamic RP Registration 1.0 [OpenID.Registration] specification.

If Self-Issued OP supports the same parameters, Self-Issued OpenID Provider flow continues, if Self-Issued OP does not support, it returns an error. 

If no error is returned, the RP must proceed as if it had obtained the following Client Registration Response: 

- client_id
    - `redirect_uri` calue of the Client.
- client_secret_expires_at
    - 0

Configuration values should preferably sent by reference as a URI using `registration_uri` parameter, but when RP cannot host a webserver, configuration values should be sent by value using `registration` parameter. 

`registration` and `registration_uri` parameters SHOULD NOT be used when the OP is not a Self-Issued OP. 


### 2.2.1. Passing Relying Party Registration Metadata by Value
    
The `registration` SIOP Request parameter enables RP Registration Metadata to be passed in a single, self-contained parameter.
    
The registration parameter value is represented in an OAuth 2.0 request as a UTF-8 encoded JSON object (which ends up being form-urlencoded when passed as an OAuth parameter). When used in a Request Object value, per Section 6.1, the JSON object is used as the value of the registration member.

The Registration parameters that would typically be used in requests to Self-Issued OPs are policy_uri, tos_uri, and logo_uri. If the RP uses more than one Redirection URI, the redirect_uris parameter would be used to register them. Finally, if the RP is requesting encrypted responses, it would typically use the jwks_uri, id_token_encrypted_response_alg and id_token_encrypted_response_enc parameters.

Registration parameter may include decentralized identifier of the RP.

### 2.2.2. Passing Relying Party Registration Metadata by Reference

The `registration_uri` SIOP Request parameter enables RP Registration Metadata to be passed by reference. 

This parameter is used identically to the request parameter, other than that the Relying Party registration metadata value is retrieved from the resource at the specified URL, rather than passed by value.

The contents of the resource referenced by the URL MUST be a RP Registration Metadata Object. The scheme used in the `registration_uri` value MUST be https. The request_uri value MUST be reachable by the Self-Issued OP, and SHOULD be reachable by the RP.

### 2.2.3. Relying Party Registration Metadata Values

This extension defines the following RP Registration Metadata values, used by the RP to provide information about itself to the Self-Issued OP:

- authorization_endpoint
    - REQUIRED. MUST include `openid:`, could also include additional custom schema.
- issuer
    - REQUIRED. MUST be `https://self-issued.me/v2`
- response_types_supported
    - REQUIRED. MUST be `id_token`
- scopes_supported
    - REQUIRED. A JSON array of strings representing supported scopes. Valid values include `openid`, `profile`, `email`, `address`, and `phone`.
- subject_types_supported
    - REQUIRED. A JSON array of strings representing supported subject types. Valid values include `pairwise` and `public`.
- subject_identifier_types_supported
    - REQUIRED. A JSON array of strings representing supported subject identifier types. Valid values include `jkt` and concrete did methods supported. DID methods supported must take the value of `Method Name` in Chapter 9 of [did-spec-registries](https://w3c.github.io/did-spec-registries/#did-methods), such as `did:peer:`
- did_methods_supported
    - OPTIONAL. A JSON array of strings representing supported DID methods. Valid values include DID method names expressed following [DID] specification, for example `did:web`. RP can indicate support for any DID method by omitting `did_methods_supported`, While including `did` in `subject_identifier_types_supported'.
- credential_formats_supported
    - REQUIRED. A JSON array of strings representing supported credential formats. Valid values include `jwt`, `jwt_vc`, `jwt_vp`, `ldp_vc`, and `ldp_vp`. 
- id_token_signing_alg_values_supported
    - REQUIRED. ID token signing alg values supported. Valid values include `RS256`, `ES256`, `ES256K`, and `EdDSA`.
- request_object_signing_alg_values_supported
    - REQUIRED. Request object signing alg values supported. Valid values include `none`, `RS256`, `ES256`, `ES256K`, and `EdDSA`.
    

The following is a non-normative example of RP Registration Metadata Values supported by Self-Issued OP:


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
   "subject_identifier_types_supported":
    ["did:web:", "did:ion:"],
    "credential_formats_supported":
    ["jwt","jwt_vp"],
    "id_token_signing_alg_values_supported":
     ["ES256", "ES256K"],
   "request_object_signing_alg_values_supported":
     ["ES256", "ES256K"]
  }
```

#### 2.2.3.1. Sub Types

A sub type is used by Self-Issued OP to advertise which types of identifiers are supported for the `sub` claim. Two types are defined by this specification:

- jkt 
    - JWK Thumbprint Subject sub type. When this subject sub type is used, the `sub` Claim value MUST be the base64url encoded representation of the thumbprint of the key in the `sub_jwk` Claim. [RFC7638]
    
- did
    - Decentralized sub type. When this sub type is used,  the `sub` value MUST be a DID defined in [DID-CORE]. 
    
NOTE: Consider adding a subject type for OpenID Connect Federation entity statements.

### 2.2.4. Relying Party Registration Metadata Error Response

This extension defines the following error codes that MUST be returned when Self-Issued OP does not support all of the Relying Party Registration metadata values received from the Relying Party in the registration parameter:

- did_methods_not_supported
    - The Self-Issued OP does not support all of the DID methods included in `did_methods_supported` parameter.
- subject_identifier_types_not_supported
    - The Self-Issued OP does not support all of the subject identifier types included in `subject_identifier_types_supported` parameter.
- credential_formats_not_supported
    - The Self-Issued OP does not support all of the credential formats included in `credential_formats_supported` parameter.
- value_not_supported
    - The Self-Issued OP does not support more than one of the RP Registration Metadata values defined in Section 4.3. When not supported metadata values are DID methods, subject identifier types, or credential formats, more specific error message must be used.
- invalid_registration_uri
    - The registration_uri in the Self-Issued OpenID Provider request returns an error or contains invalid data.
- invalid_registration_object
    - The registration parameter contains an invalid RP Registration Metadata Object.

Error response must be made in the same manner as defined in Section 3.1.2.6.


## 3. Identifier Portability and Verifiable Presentation Support

## 3.1.  Self-Issued OpenID Provider Request
The RP sends the Authentication Request to the Authorization Endpoint with the following parameters:

- scope
     - REQUIRED. `scope` parameter value, as specified in Section 3.1.2.
- response_type
    - REQUIRED. Constant string value `id_token`.
- client_id
    - REQUIRED. Client ID value for the Client, which in this case contains the `redirect_uri` value of the RP.
- redirect_uri
    - REQUIRED. MUST equal to `client_id` value. MUST be included for compatibility reasons.
- id_token_hint
    - OPTIONAL. id_token_hint parameter value, as specified in Section 3.1.2. If the ID Token is encrypted to the Self-Issued OP, the sub (subject) of the signed ID Token MUST be sent as the kid (Key ID) of the JWE. 
- claims
    - OPTIONAL. claims parameter value, as specified in Section 5.5.
- registration
    - OPTIONAL. This parameter is used by the RP to provide information about itself to a Self-Issued OP that would normally be provided to an OP during Dynamic RP Registration, as specified in Section 2.2.1.  
- registration_uri
    - OPTIONAL. This parameter is used by the RP to provide information about itself to a Self-Issued OP that would normally be provided to an OP during Dynamic RP Registration, as specified in Section 2.2.2. 
- request
    - OPTIONAL. Request Object value, as specified in Section 6.1. The Request Object MAY be encrypted to the Self-Issued OP by the RP. In this case, the sub (subject) of a previously issued ID Token for this RP MUST be sent as the kid (Key ID) of the JWE. 
- request_uri
    - OPTIONAL. URL where Request Object value can be retrieved from, as specified in Section 6.2.
    
When `request` or `reques_uri` parameters are NOT present, `registration` or `registration_uri` parameters MUST be present in the request. When `request` or `reques_uri` parameters are present, `registration` or `registration_uri` parameters MUST be included in either of those parameters.

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
    &registration_uri=https%3A%2F%2F
      client.example.org%2Frf.txt%22%7D
```


## 3.2.  Self-Issued OpenID Provider Response

Self-Issued OpenID Provider Response is returned when Self-Issued OP supports all of the Relying Party Registration metadata values received from the Relying Party in the registration parameter. If even one of the Relying Party Registration Metadata Values is not supported, Self-Issued OP MUST return an error according to Section 4.4.

This extension defines the following claims to be included in the ID token for use in Self-Issued OpenID Provider Responses: 

- sub
    - REQUIRED. Subject identifier value, represented by a URI. When sub type is `jkt`, the value is the base64url encoded representation of the thumbprint of the key in the `sub_jwk` Claim. When sub type is `did`, the value is a decentralized identifier. The thumbprint value is computed as the SHA-256 hash of the octets of the UTF-8 representation of a JWK constructed containing only the REQUIRED members to represent the key, with the member names sorted into lexicographic order, and with no white space or line breaks. For instance, when the kty value is RSA, the member names e, kty, and n are the ones present in the constructed JWK used in the thumbprint computation and appear in that order; when the kty value is EC, the member names crv, kty, x, and y are present in that order. Note that this thumbprint calculation is the same as that defined in the JWK Thumbprint [RFC7638] specification.
- sub_jwk
    - REQUIRED. a secure binding between the subject of the verifiable credential and the subject identifier (and related keys) of the holder who creates the presentation. When subr type is `jkt`, the key is a bare key in JWK [JWK] format (not an X.509 certificate value). When sub type is `did`, sub_jwk MUST contain a kid that is a DID URL referring to the verification method in the Self-Issued OP's DID Document that can be used to verify the JWS of the id_token directly or indirectly. The sub_jwk value is a JSON object. Use of the `sub_jwk` Claim is NOT RECOMMENDED when the OP is not Self-Issued.
- vp
    - OPTIONAL. A JSON object, that represents a JWT verifiable presentation, following W3C Verifiable Credentials Specification [VC-DATA-MODEL]. Verifiable Credentials must be embedded in the Verifiable Presentation following W3C Verifiable Credentials Specification [VC-DATA-MODEL]
    
Verifiable Presentation is data derived from one or more Verifiable Credentials, issued by one or more issuers, that is shared with a specific verifier. Verifiable Credential is a set of one or more claims made by an issuer.

Self-Issued OP may present credentials to the RP using Verifiable Presentation credential format by including it in the `vp` claim inside the ID token. 

Whether the Self-Issued OP is a mobile client or a web client, response is the same as the normal Implicit Flow response with the following refinements. Since it is an Implicit Flow response, the response parameters will be returned in the URL fragment component, unless a different Response Mode was specified.

1. The `iss` (issuer) Claim Value is `https://self-issued.me/v2`.
2. A `sub_jwk` Claim is present, with its value being the public key used to check the signature of the ID Token.
3. The `sub` (subject) Claim value is either the base64url encoded representation of the thumbprint of the key in the `sub_jwk` Claim or a decentralized identifier. 
4. No Access Token is returned for accessing a UserInfo Endpoint, so all Claims returned MUST be in the ID Token.


## 3.3. Self-Issued ID Token Validation
To validate the ID Token received, the RP MUST do the following:

1. The Relying Party (RP) MUST validate that the value of the `iss` (issuer) Claim is `https://self-isued.me`. If iss contains a different value, the ID Token is not Self-Issued, and instead it MUST be validated according to Section 3.1.3.
2. The RP MUST validate that the `aud` (audience) Claim contains the value of the `redirect_uri` that the RP sent in the Authentication Request as an audience.
3. The RP MUST validate the signature of the ID Token. When sub type is`jkt`, validation is done according to JWS [JWS] using the algorithm specified in the alg Header Parameter of the JOSE Header, using the key in the `sub_jwk` Claim. When sub type is`did`, vvalidation is done using the key derived as a result of DID Resolution as defined in [DID-CORE]. The key is a bare key in JWK format (not an X.509 certificate value) when sub type is`jkt` or may be another key format when sub type is `did`.
4. Default `alg` value is RS256. It MAY also be ES256, ES256K or EdDSA.
5. The RP MUST validate that the`sub` claim is bound to the `sub_jwk` value. When sub type is`jkt`, the RP MUST validate that the sub Claim value is the base64url encoded representation of the thumbprint of the key in the `sub_jwk` Claim, as specified in Section 6. When sub type is `did`, the RP MUST validate that the `kid` of the `sub_jwk` claim matches the verification method from the DID Document that is obtained by resolving decentralized identifier included in `sub` claim.
6. The current time MUST be before the time represented by the `exp` Claim (possibly allowing for some small leeway to account for clock skew).
 The `iat` Claim can be used to reject tokens that were issued too far away from the current time, limiting the amount of time that nonces need to be stored to prevent attacks. The acceptable range is RP specific.
8. If a `nonce` value was sent in the Authentication Request, a `nonce` Claim MUST be present and its value checked to verify that it is the same value as the one that was sent in the Authentication Request. The RP SHOULD check the `nonce `value for replay attacks. The precise method for detecting replay attacks is RP specific.


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
    },
    "vp": {
     "@context": [
      "https://www.w3.org/2018/credentials/v1",
      "https://www.w3.org/2018/credentials/examples/v1"
     ],
     "type": ["VerifiablePresentation"],
     "verifiableCredential": ["..."]
    }
　}
  
```
   
## 4. References

### 4.1. Normative References
- [DID-CORE] https://github.com/w3c/did-core (not yet a ratified draft)
- [VC-DATA] https://www.w3.org/TR/vc-data-model/
- [RFC6749] https://tools.ietf.org/html/rfc6749
- [RFC6750] https://tools.ietf.org/html/rfc6750
- [OpenID.Core] https://openid.net/specs/openid-connect-core-1_0.html
- [RFC7638] https://tools.ietf.org/html/rfc7638
- [OpenID.Registration] https://openid.net/specs/openid-connect-registration-1_0.html
- [did-spec-registries] https://w3c.github.io/did-spec-registries/#did-methods

### 4.2. Non-Normative References
- [draft-jones-self_issued_identifier] https://bitbucket.org/openid/connect/src/master/SIOP/draft-jones-self_issued_identifier.md
- [siop-requirements] https://bitbucket.org/openid/connect/src/master/SIOP/siop-requirements.md

### 4.3. Relationships to other documents 
The scope of this draft was an extention to OpenID Connect Chapter 7 Self-Issued OpenID Provider. However, some sections of it could become applicable more generally to the entire OpenID Connect specification.

## 5. Notices
Copyright (c) 2020 The OpenID Foundation.

The OpenID Foundation (OIDF) grants to any Contributor, developer, implementer, or other interested party a non-exclusive, royalty free, worldwide copyright license to reproduce, prepare derivative works from, distribute, perform and display, this Implementers Draft or Final Specification solely for the purposes of (i) developing specifications, and (ii) implementing Implementers Drafts and Final Specifications based on such documents, provided that attribution be made to the OIDF as the source of the material, but that such attribution does not indicate an endorsement by the OIDF.

The technology described in this specification was made available from contributions from various sources, including members of the OpenID Foundation and others. Although the OpenID Foundation has taken steps to help ensure that the technology is available for distribution, it takes no position regarding the validity or scope of any intellectual property or other rights that might be claimed to pertain to the implementation or use of the technology described in this specification or the extent to which any license under such rights might or might not be available; neither does it represent that it has made any independent effort to identify any such rights. The OpenID Foundation and the contributors to this specification make no (and hereby expressly disclaim any) warranties (express, implied, or otherwise), including implied warranties of merchantability, non-infringement, fitness for a particular purpose, or title, related to this specification, and the entire risk as to implementing this specification is assumed by the implementer. The OpenID Intellectual Property Rights policy requires contributors to offer a patent promise not to assert certain patent claims against other contributors and against implementers. The OpenID Foundation invites any interested party to bring to its attention any copyrights, patents, patent applications, or other proprietary rights that may cover technology that may be required to practice this specification.



    
## Appendix A.History
- 01
    - Version proposed for working group adoption


