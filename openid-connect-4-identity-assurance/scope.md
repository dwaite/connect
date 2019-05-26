# Scope and Requirements

The extension specified in this document SHALL

* allow an OpenID Connect RP to request the minimal identity data set it needs (data minimization) and to express requirements regarding this data and the evidences and processes employed,
* allow an OP to attest identity data obtained and maintained in accordance with a certain law, and
* allow the RP to map the data obtained from the OP to other laws, e.g. it shall be possible to use identity data maintained in accordance with the Anti Money Laundering Law to fulfill requirements defined by eIDAS.

The scope of the extension is to define mechanisms to assert verified person data. 

It SHALL be possible to use existing OpenID Connect claims and other extensions defined beside this extension within the same OpenID Connect transaction and especially to render the same ID Token or other representations utilized to convey user claims. For example, OpenID Connect already defines additional claims to inform the RP of the verification status of the `phone_number` and `email` claims. For the same reason, this extension SHALL allow the OP to attest verified and unverified claims in the same transaction.

This extension MAY introduce new user claims to cover user attributes not currently covered by OpenID Connect, one example would be the place of birth. 

The representation defined in this specification is intended to be used to provide RPs with verified person data via any appropriate channel. In the context of OpenID Connnect, verified person data can be attested in ID Tokens or as part of the User Info response. It is also possible to utilize the format described here in OAuth Token Introspection responses (see [@?RFC7662] and [@?I-D.ietf-oauth-jwt-introspection-response]) to provide resource servers with 
verified person data.  

This extension defines a way to assert verified claims only. There is no need to cover unverified claims, or rather Claims without further information about the assurance, since they are already covered by OpenID Connect Core [@!OpenID]. 

Even for asserting verified claims, this extension shall attempt to utilize existing OpenID Connect claims if possible and reasonable. It MUST, however, ensure RPs cannot interpret unverified claims as verified claims.

The representation defined in this extension SHALL allow RPs to obtain assertions about verified claims from different OPs using different trust frameworks.

This extension SHALL be usable by OPs operating under a certain regulation related to identity assurance, such as eIDAS notified eID systems, as well as other OPs. Strictly regulated OPs can attest identity data without the need to provide further evidences since they are approved to operate accoriding to well-defined rules with clearly defined liability. For example in case of eIDAS, the peer review ensures eIDAS compliance and the respective member state takes the liability for the identities asserted by its notified eID systems. Every other OP not operating under such well-defined conditions is typically required to provide the RP data about the identity verification process along with identity evidences to allow the RP to conduct their own risk assessment. 

From a technical perspective, this means this specification needs to support cases, where basically verified person data are provided along with the information about the regulatory framework (trust framework) the OP complies with and the respective level of assurance, whereas there shall also be support to externalize information about the identity verification process with the OP to the RP.

This extension SHALL be trully international and support identity assurance for different and across jurisdictions. It SHALL be extensible to support new trust frameworks, verification methods, and respective identity evidences.

Note: data transfer between OP and RP requires a legal basis that MUST be established upfront or in the course of the OpenID process. 
