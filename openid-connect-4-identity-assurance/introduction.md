# Introduction {#Introduction}

This specification defines an extension to OpenID Connect [@!OpenID] to address the use case of strong identity verification of a natural person in accordance with certain laws. Examples include Anti Money Laundering Laws, Telecommunication Acts, Anti Terror Laws, and regulations on trust services, such as eIDAS [@?eIDAS].

In such use cases, the Relying Parties (RPs) need to know the assurance level of the user Claims attested by the OpenID Connect Providers (OPs) along with evidences related to the identity verification process (identity assurance). 

Identity assurance significantly differs from authentication assurance, which requires a different representation in the OpenID Connect protocol that is defined in this specification.

The assurance level for authentication is a property of a certain OpenID Connect transaction, determined by the authentication means employed and the underlying user account management processes. The `acr` Claim as defined in Section 2 of the OpenID Connect specification [@!OpenID] is sufficient to convey this information. 

The identity assurance for user Claims, i.e. the binding of a certain Claim value to the person controlling the respective user account, typically varies among the different user Claims. For example, the assurance an OP typically will be able to attest for an e-mail address will be “self-asserted”, “verified by opt-in”, or “verified by the respective e-mail provider via an attribute exchange protocol”. The family name of a user, in contrast, might have been verified in accordance with the respective Anti Money Laundering Law by showing an ID Card to a trained employee of the OP operator. 

Identity assurance therefore requires a way to convey assurance data along with and coupled to the respective user Claims. This specification proposes a suitable representation and mechanisms the RP will utilize to request verified person data and accompanying identity assurance data. 

## Terminology 

This section defines some terms relevant to the topic covered in this documents, heavily inspired by NIST SP 800-63A [@?NIST-SP-800-63a].

* Identity Proofing - process in which a user provides evidence to an OP reliably identifying themselves, thereby allowing the OP to assert that identification at a useful identity assurance level.

* Identify Verification - process conducted by the OP to verify the user's identity.

* Identity Assurance - process in which the OP attests identity data of a certain user with a certain assurance towards a RP, typically expressed by way of an assurance level. Depending on legal requirements, the OP may also be required to provide evidences of the identity proofing process to the RP.

* Verified Person Data - Claims about a person whose binding to a particular use account were verified in the course of an identity verification process.

[1]: https://pages.nist.gov/800-63-3/sp800-63a.html "NIST Special Publication 800-63A, Digital Identity Guidelines, Enrollment and Identity Proofing Requirements"


    
