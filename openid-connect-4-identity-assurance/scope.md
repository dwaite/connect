# Scope and Requirements

The scope of the extension is to define a mechanism to assert verified claims, in general, and to introduce new user Claims required in the identity assurance space, for example the place of birth. 

The RP will be able to request the minimal data set it needs (data minimization) and to express requirements regarding this data and the evidence and the identity verification processes employed by the OP.

This extension will be usable by OPs operating under a certain regulation related to identity assurance, such as eIDAS notified eID systems, as well as other OPs. Strictly regulated OPs can attest identity data without the need to provide further evidence since they are approved to operate according to well-defined rules with clearly defined liability. 

For example in case of eIDAS, the peer review ensures eIDAS compliance and the respective member state takes the liability for the identities asserted by its notified eID systems. Every other OP not operating under such well-defined conditions is typically required to provide the RP data about the identity verification process along with identity evidence to allow the RP to conduct their own risk assessment and to map the data obtained from the OP to other laws. For example, it shall be possible to use identity data maintained in accordance with the Anti Money Laundering Law to fulfill requirements defined by eIDAS.

From a technical perspective, this means this specification allows the OP to attest verified user Claims along with information about the respective trust framework (and assurance level) but also supports the externalization of information about the identity verification process.

The representation defined in this specification can be used to provide RPs with verified person data via any appropriate channel. In the context of OpenID Connnect, verified person data can be attested in ID Tokens or as part of the User Info response. It is also possible to utilize the format described here in OAuth Token Introspection responses (see [@?RFC7662] and [@?I-D.ietf-oauth-jwt-introspection-response]) to provide resource servers with 
verified person data.   

This extension is intended to be trully international and support identity assurance for different and across jurisdictions. The extension is therefore extensible to support new trust frameworks, verification methods, and respective identity evidence.

In order to give implementors as much flexibility as possible, this extension can be used in conjunction with existing OpenID Connect claims and other extensions within the same OpenID Connect assertion (e.g ID Token oder UserInfo response) utilized to convey user Claims. 

For example, OpenID Connect [@!OpenID] defines Claims for representing family name and given name of a user without a verification status. Those Claims can be used in the same OpenID Connect assertion beside verified claims represented accoriding to this extension. 

In the same way, existing claims to inform the RP of the verification status of the `phone_number` and `email` claims can be used together with this extension.

Even for asserting verified claims, this extension will utilize existing OpenID Connect claims if possible and reasonable. The extension will, however, ensure RPs cannot interpret unverified claims as verified claims.
