# Introduction {#Introduction}

There are use cases where Relying Parties need to know the assurance level of the user claims attested by the OpenID Connect OP. Whereas assurance levels for authentication are a property of user account management and a certain OpenID Connect transaction, the assurance for user claims, i.e. the binding of a certain claim value to the person controlling the respective user account, might vary among the different user claims. 

For example, the assurance an OP typically will be able to attest for an e-mail address will be “self-asserted”, “verified by opt-in”, or “verified by the respective e-mail provider via an attribute exchange protocol”. The family name of a user, in contrast, might have been verified in accordance with the respective Anti Money Laundering Law by showing an ID Card to a trained employee of the OP operator.   

This specification aims at providing an extension to OpenID Connect to address the single use case of strong identity verification of a natural person in accordance with a certain laws. In Germany, for example, those would be the Anti Money Laundering Law, the Telecommunication Act, and eIDAS.

## Terminology 

This section defines some terms relevant to the topic covered in this documents, heavily inspired by [NIST SP 800-63A][1].

* __Identity Proofing__ - process in which a user provides evidence to a OP reliably identifying themselves, thereby allowing the OP to assert that identification at a useful identity assurance level.

[1]: https://pages.nist.gov/800-63-3/sp800-63a.html "NIST Special Publication 800-63A, Digital Identity Guidelines, Enrollment and Identity Proofing Requirements"

* __Identify Verification__ - process conducted by the OP to verify the user's identity


* __Identity Assurance__ - process in which the OP attests identity data of a certain user with a certain assurance towards a RP, typically expressed by way of an assurance level. Depending on legal requirements, the OP may also provide evidences of the identity proofing process to the RP.


    
