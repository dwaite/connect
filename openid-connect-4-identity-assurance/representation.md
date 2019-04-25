# Verified Data Representation 

This extension to OpenID Connect wants to ensure that RPs cannot mixup verified and unverified claims and incidentally process unverified claims as verified claims. 

The representation proposed therefore provides the RP with the verified claims within a structured claim `verified_person_data`. This structured claim is composed of the verification evidences related to a certain verification process and the corresponding user claims which were verified in this process.

The `verified_person_data` claim consists of the following sub-elements:

* `verification`: This element contains all data about the verification process.
* `claims`: This element is the container for the verified user claims. 

## verification Element {#verification}

This element contains the information about the process conducted to verify a person's identity and bind the respective person data to a user account.

The `verification` element consists of the following elements: 

`trust_framework`: REQUIRED. String determing the trust framework governing the identity verification process and the identity assurance level of the OP. 

An example value is `eidas_loa_high`, which denotes a notified eID system under eIDAS providing identity assurance at level of assurance "High".

Standardized values are defined in [Trust Frameworks](#predefined_values_tf).

The `trust_framework` value determines what further data is provided to the RP in the `verification` element. A notified eID system under eIDAS, for example, would not need to provide any further data whereas an OP not governed by eIDAS would need to provide verification evidences in order to allow the RP to fulfill its legal obligations. An example of the latter is an OP acting under the German Anti-Money laundering law (`de_aml`).

`date`: CONDITIONALLY REQUIRED. Time stamp in ISO 8601:2004 YYYY-MM-DD format representing the date when identity verification took place.

`id`: CONDITIONALLY REQUIRED. Unique reference to the identity verification process as performed by the OP. Used for backtracing in case of disputes or audits. Note: In contrast to this field, the claim `transaction_id` refers to the transaction leading the OP to attest the user's verified identity data towards a RP.

`method`: CONDITIONALLY REQUIRED. Method utilized for identity verification - Depending on the value of "method", there will be different additional sub-elements with the name of the method in the verification element - possible values are

* "identity_document": verification of a physical document 
* "eID": verification using an electronic ID Card

### Method-specific elements

The following elements are contained in an `identity_document` sub-element. 

`country`: String denoting the country where the document was issued, format: ISO 3166-1 Alpha-2, e.g. "DE".

`type`: REQUIRED. String denoting the type of the id document, standardized values are defined in [Identity Documents](#predefined_values_idd).

The OP MAY use other than the predefined values in which case the RPs will either be unable to process the assertion, just store this value for audit purposes, or apply bespoken business logic to it.

`issuer`: REQUIRED. String representing the issuer of the identity document

`number`: REQUIRED. String representing the number of the identity document

`date_of_issuance`: CONDITIONALLY REQUIRED. The date the document was issued as ISO 8601:2004 YYYY-MM-DD format, if this attribute exists for the particular type of document.

`date_of_expiry`: CONDITIONALLY REQUIRED. The date the document will expire as ISO 8601:2004 YYYY-MM-DD format, if this attribute exists for the particular type of document.

`method`: The method used to verify the document, predefined values are given in  [Verification Methods](#predefined_values_vm)

`organization`: CONDITIONALLY REQUIRED. String denoting the organization which performed the verification on behalf of the OP. Can be omitted if this is the OP itself.

`agent`: OPTIONAL. Agent (person) who conducted the verification. The agent may be identified by Name or an identifier which can be resolved into the agent’s name during an audit.

The following elements are contained in an `eID` sub-element:

`country`: REQUIRED. Country where the eID was issued: ISO 3166-1 Alpha-2, e.g. DE

`type`: String denoting the type of eID, standardized values are defined in [Identity Documents](#predefined_values_idd).

`identifier`: REQUIRED. person identifier obtained by the OP from the eID system

## claims Element {#claimselement}

The `claims` element contains the user claims which were verified by the process and according to the policies determined by the corresponding `verification` element. 

The `claims` element MAY contain one or more of the following user claims as defined in Section 5.1 of the OpenID Connect specification [@!OpenID]

* `name`
* `given_name`
* `middle_name`
* `family_name`
* `birthdate`
* `address`

or of the following claims as defined in Section [User Claims](#userclaims):

* `place_of_birth` 
* `nationality`
* `birth_name`

The `claims` element MAY also contain other claims given the value of the respective claim was verified in the verification process represented by the sibling `verification` element. 
