# Introduction {#Introduction}

This specification defines an extension to OpenID Connect [@!OpenID] to address the use case of strong identity verification of a natural person in accordance with certain laws. Examples include Anti Money Laundering Laws, Telecommunication Acts, Anti Terror Laws, and regulations on trust services, such as eIDAS [@?eIDAS].

In such use cases, the Relying Parties (RPs) need to know the assurance level of the Claims about the End-User attested by the OpenID Connect Providers (OPs) along with evidence related to the identity verification process´. 

The `acr` Claim, as defined in Section 2 of the OpenID Connect specification [@!OpenID], is suited to attest information about the authentication performed in a OpenID Connect transaction, but identity assurance requires a different representation. 

The reason is authentication is an aspect of an OpenID Connect transaction while identity assurance is a property of a certain Claim or a group of Claims and several of them will typically be conveyed to the RP as the result of an OpenID Connect transaction.  

For example, the assurance an OP typically will be able to attest for an e-mail address will be “self-asserted” or “verified by opt-in or similar mechanism”. The family name of a user, in contrast, might have been verified in accordance with the respective Anti Money Laundering Law by showing an ID Card to a trained employee of the OP operator. 

Identity assurance therefore requires a way to convey assurance data along with and coupled to the respective Claims about the End-User. This specification proposes a suitable representation and mechanisms the RP will utilize to request verified person data and accompanying identity assurance data and for the OP to represent the identity assurance data. 

## Terminology 

This section defines some terms relevant to the topic covered in this documents, heavily inspired by NIST SP 800-63A [@?NIST-SP-800-63a].

* Identity Proofing - process in which a user provides evidence to an OP or claim provider reliably identifying themselves, thereby allowing the OP to assert that identification at a useful identity assurance level.

* Identify Verification - process conducted by the OP or a claim provider to verify the user's identity.

* Identity Assurance - process in which the OP or a claim provider attests identity data of a certain user with a certain assurance towards a RP, typically expressed by way of an assurance level. Depending on legal requirements, the OP may also be required to provide evidence of the identity verification process to the RP.

* Verified Person Data - Claims about a person whose binding to a particular use account were verified in the course of an identity verification process.

[1]: https://pages.nist.gov/800-63-3/sp800-63a.html "NIST Special Publication 800-63A, Digital Identity Guidelines, Enrollment and Identity Proofing Requirements"


    
