# Verified Data Representation 

This extension to OpenID Connect wants to ensure that RPs cannot mix up verified and unverified Claims and incidentally process unverified Claims as verified Claims. 

The representation proposed therefore provides the RP with the verified Claims within a container element `verified_claims`. This container is composed of the verification evidence related to a certain verification process and the corresponding Claims about the End-User which were verified in this process.

This section explains the structure and meaning of `verified_claims` in detail. A machine-readable syntax definition is given as JSON schema in (#json_schema). It can be used to automatically validate JSON documents containing a  `verified_claims` element. 

`verified_claims` consists of the following sub-elements:

* `verification`: REQUIRED. Object that contains all data about the verification process.
* `claims`: REQUIRED. Object that is the container for the verified Claims about the End-User. 

Note: implementations MUST ignore any sub-element not defined in this specification or extensions of this specification. 

## verification Element {#verification}

This element contains the information about the process conducted to verify a person's identity and bind the respective person data to a user account.

The `verification` element consists of the following elements: 

`trust_framework`: REQUIRED. String determing the trust framework governing the identity verification process and the identity assurance level of the OP. 

An example value is `eidas_ial_high`, which denotes a notified eID system under eIDAS [@?eIDAS] providing identity assurance at level of assurance "High".

An initial list of standardized values is defined in [Trust Frameworks](#predefined_values_tf). Additional trust framework identifiers can be introduced [how?]. RPs SHOULD ignore `verified_claims` claims containing a trust framework id they don't understand.

The `trust_framework` value determines what further data is provided to the RP in the `verification` element. A notified eID system under eIDAS, for example, would not need to provide any further data whereas an OP not governed by eIDAS would need to provide verification evidence in order to allow the RP to fulfill its legal obligations. An example of the latter is an OP acting under the German Anti-Money laundering law (`de_aml`).

`date`: Time stamp in ISO 8601:2004 [@!ISO8601-2004] YYYY-MM-DD format representing the date when identity verification took place. Presence of this element might be required for certain trust frameworks. 

`verification_process`: Unique reference to the identity verification process as performed by the OP. Used for backtracing in case of disputes or audits. Presence of this element might be required for certain trust frameworks. 

Note: While `id` referes to the identity verification process at the OP, the `txn` claim refers to a particular OpenID Connect transaction in which the OP to attested the user's verified identity data towards a RP. 

`evidence` is a JSON array containing the evidence the OP used to verifiy the user's identity as separate JSON objects. Every object contains the property `type` determining it's respective type. The RP uses this information to process the `evidence` property appropriately. 

Note: implementations MUST ignore any sub-element not defined in this specification or extensions of this specification. 

### Evidence 

The following types of evidence are defined:

* "id_document": verification based on any kind of government issued identity document 
* "utility_bill": verification based on an utility bill
* "qes": verification based on a eIDAS Qualified Electronic Signature

#### id_document

The following elements are contained in an `id_document` evidence sub-element. 

`method`: REQUIRED. The method used to verify the id document. Predefined values are given in  [Verification Methods](#predefined_values_vm)

`verifier`: OPTIONAL. A JSON object denoting the legal entity that performed the identity verification on behalf of the OP. This object SHOULD only be included if the OP did not perform the identity verification itself. This object consists of the following properties:

* `organization`: String denoting the organization which performed the verification on behalf of the OP. 
* `agent`: Agent (person) who conducted the verification. The agent may be identified by Name or an identifier which can be resolved into the agent’s name during an audit.

`document`: A JSON object representing the id document used to perform the id verification. It consists of the following properties:

* `type`: REQUIRED. String denoting the type of the id document. Standardized values are defined in [Identity Documents](#predefined_values_idd). The OP MAY use other than the predefined values in which case the RPs will either be unable to process the assertion, just store this value for audit purposes, or apply bespoken business logic to it.
* `number`: String representing the number of the identity document.
* `issuer`: A JSON object containg information about the government agency that issued this identity document. This object consists of the following properties:
	*  `name`: REQUIRED. Designation of the issuer of the identity document
	*  `country`: String denoting the country where the document was issued, format: ISO 3166-1 Alpha-2, e.g. "DE".
* `date_of_issuance`: REQUIRED if this attribute exists for the particular type of document. The date the document was issued as ISO 8601:2004 YYYY-MM-DD format.
* `date_of_expiry`: REQUIRED if this attribute exists for the particular type of document. The date the document will expire as ISO 8601:2004 YYYY-MM-DD format. 

#### utility_bill

The following elements are contained in a `utility_bill` evidence sub-element. 

`provider`: REQUIRED. A JSON object identifying the respective provider that issued the bill. The object consists of the following properties:

* `name`: A String designating the provider.
* All elements of the OpenID Connect `address` Claim ([@!OpenID])

`date`: A ISO 8601:2004 YYYY-MM-DD formated string containing the date when this bill was issued.

#### qes

The following elements are contained in a `qes` evidence sub-element. 

`issuer`: REQUIRED. A String denoting the trust service provider that created the eletronic signatue. 

`issued_at`: REQUIRED. The date the signature was created as ISO 8601:2004 YYYY-MM-DD format

`serial_number`: REQUIRED. String containing the serial number of the certificate used to sign.


## claims Element {#claimselement}

The `claims` element contains the claims about the End-User which were verified by the process and according to the policies determined by the corresponding `verification` element. 

The `claims` element MAY contain one or more of the following Claims as defined in Section 5.1 of the OpenID Connect specification [@!OpenID]

* `name`
* `given_name`
* `middle_name`
* `family_name`
* `birthdate`
* `address`

or the claims defined in (#userclaims).

The `claims` element MAY also contain other claims given the value of the respective claim was verified in the verification process represented by the sibling `verification` element. 
