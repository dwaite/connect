# Verified Data Representation 

This extension to OpenID Connect wants to ensure that RPs cannot mixup verified and unverified claims and incidentally process unverified claims as verified claims. 

The representation proposed therefore provides the RP with the verified claims within a structured claim `verified_person_data`. This structured claim is composed of the verification evidences related to a certain verification process and the corresponding user claims which were verified in this process.

The `verified_person_data` claim consists of the following sub-elements:

* `verification`: This element contains all data about the verification process.
* `claims`: This element is the container for the verified user claims. 

## verification Element {#verification}

This element contains the information about the process conducted to verify a person's identity and bind the respective person data to a user account.

The `verification` element consists of the following elements: 

`organization`: REQUIRED. String value identifying the organisation that verified the person's identity.

`legal_context`: REQUIRED. JSON Object determing the legal context governing the identification verification process conducted by the `organisation`. This object consists of the following fields:

* `country`: OPTIONAL. ISO 3166-1 Alpha-2, e.g. "DE".
* `regulation`: Designation of the act or set of acts or directives, e.g. "Geldwäschegesetz". 

Standardized values are: 
* "DE", "Geldwäschegesetz"
* [TBD]

Note by the editor: It might be reasonable to rename the `legal_context` element to `trust_framework` or something similar to clearly indicate it can also be used to cover NIST SP 800-3A or eIDAS Identity Proofing including the respective assurance levels.


`date`: REQUIRED. Time stamp in ISO 8601:2004 YYYY-MM-DD format representing the date when identity verification took place.

`id`: REQUIRED. Unique reference to the identity verification process as performed by the verifying `organization`. Used for backtracing in case of disputes or audits. Note: In contrast to this field, the claim `transaction_id` refers to the transaction leading the OP to attest the user's verified identity data towards a RP.

`method`: REQUIRED. Method utilized for identity verification - Depending on the value of "method", there will be different additional sub-elements with the name of the method in the verification element - possible values are

* "identity_document": verification of a physical document 
* "eID": verification using an electronic ID Card
* [TBD]

### Method-specific elements

The following elements are contained in an `identity_document` sub-element. 

`country`: String denoting the country where the document was issued, format: ISO 3166-1 Alpha-2, e.g. "DE".

`type`: REQUIRED. String denoting the type of the id document, standardized values are:

* "ID Card": national ID Card
* "Passport": Passport
It is also possible to set `type` to country-specific types (free text). In this case, RPs will either just store this value for audit purposes or apply bespoke business logic to it.

`issuer`: REQUIRED. String representing the issuer of the identity document

`number`: REQUIRED. String representing the number of the identity document

`date_of_issuance`: CONDITIONALLY REQUIRED. The date the document was issued as ISO 8601:2004 YYYY-MM-DD format, if this attribute exists for the particular type of document.

`date_of_expiry`: CONDITIONALLY REQUIRED. The date the document will expire as ISO 8601:2004 YYYY-MM-DD format, if this attribute exists for the particular type of document.

`method`: The method used to verify the document, standardized values are:

* "Physical In-Person Proofing (bank)"
* "Physical In-Person Proofing (shop)"
* "Physical In-Person Proofing (courier)"
* "Supervised remote In-Person Proofing"

`organization`: CONDITIONALLY REQUIRED. String denoting the organization which performed the verification on behalf of the organisation denoted in the enclosing `verification` element. Can be omitted if this is the same organisation as in the enclosing `verification` element.

`agent`: OPTIONAL. Agent (person) who conducted the verification. The agent may be identified by Name or an identifier which can be resolved into the agent’s name during an audit.

The following elements are contained in an `eID` sub-element:

`country`: REQUIRED. Country where the eID was issued: ISO 3166-1 Alpha-2, e.g. DE

`type`: type of the eID, standardized value is:

* "ID Card": national ID Card
* It is also possible to set type to country-specific designations

`identifier`: REQUIRED. person identifier obtained by the verifying organization from the eID system

## claims Element {#claimselement}

The `claims` element contains the user claims which were verified by the process and according to the policies determined by the corresponding `verification` element. 

The `claims` element MUST only contain one or more of the following user claims as defined in Section 5.1 of the OpenID Connect specification [@!OpenID]

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
