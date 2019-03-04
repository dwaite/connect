# Scope and Requirements

The extension specified in this document shall

* allow a OpenID Connect RP to request identity data and to express requirements regarding this data and the evidences and processes employed,
* allow an OP to attest identity data obtained and maintained in accordance with a certain law, and
* allow the RP to map the data obtained from the OP to other laws, e.g. it shall be possible to use identity data maintained in accordance with the Anti Money Laundering Law to fulfill requirements defined by eIDAS.

The scope of the extension is to define mechanisms to assert person data only. 

It SHALL be possible to use existing OpenID Connect claims and other extensions defined beside this extension within the same OpenID Connect transaction and especially to render the same ID token or other representations utilized to convey user claims. For example, OpenID Connect already defines additional claims to inform the RP of the verification status of the `phone_number` and `email` claims.

If necessary, this extension will introduce new user claims to cover user attributes currently uncovered by OpenID Connect, one examples is the `place_of_birth`. 

The extension MUST allow the OP to attest verified and unverified claims in the same response.

The representation defined in this specification is intended to be used to provide RPs with verified person data via any appropriate channel. In the context of OpenID Connnect, verified person data can be attested in ID Tokens or as part of the User Info response. It is also possible to utilize the format described here in OAuth Token Introspection responses (see [@?RFC7662] and [@?I-D.ietf-oauth-jwt-introspection-response]) to provide resource servers with 
verified person data.  

This extension defines a way to assert verified claims only since self asserted claims, or better put claims without further information about the assurance, are already covered by OpenID Connect. 

Even for asserting verified claims, this extension shall attempt to utilize existing OpenID Connect claims if possible and reasonable. It MUST, however, ensure developers cannot interpret non-verified claims as verified claims.

The representation defined in this extension SHALL allow RPs to obtain assertions about verified claims from different OPs using different trust frameworks.
