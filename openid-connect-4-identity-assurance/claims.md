# Claims {#claims}

## Additional Claims about End-Users {#userclaims}

In order to fulfill the requirements of some jurisdictions on identity assurance, this specification defines the following Claims for conveying user data in addition to the Claims defined in the OpenID Connect specification [@!OpenID]:

* `place_of_birth`: a structured Claim representing the End-User’s place of birth. It consists of the following fields:
	* `country`: [@!ISO3166-1] Alpha-2 (e.g., DE) or [@!ISO3166-3] 
	* `region`: State, province, prefecture, or region component
	* `locality`: city or other locality
* `nationality`: represents the user’s nationality in format [@!ISO3166-1] Alpha-2, e.g., DE
* `birth_family_name`: family name someone has when he or she is born, or at least from the time he or she is a child. This term can be used by a person who changes the family name later in life for any reason.
* `birth_given_name`: given name someone has when he or she is born, or at least from the time he or she is a child. This term can be used by a person who changes the given name later in life for any reason.
* `birth_middle_name`: middle name someone has when he or she is born, or at least from the time he or she is a child. This term can be used by a person who changes the middle name later in life for any reason.
* `salutation`: End-User’s salutation, e.g. “Mr.”
* `title`: End-User’s title, e.g. “Dr.”

## `txn` Claim

Strong identity verification typically requires the participants to keep an audit trail of the whole process. 

The `txn` Claim as defined in [@!RFC8417] is used in the context of this extension to build audit trails across the parties involved in an OpenID Connect transaction. 

If the OP issues a `txn`, it MUST maintain a corresponding audit trail, which at least consists of the following details: 

* the transaction id,
* the authentication methods employed, and
* the transaction type (e.g. scope values).

This transaction data MUST be stored as long as it is required to store transaction data for auditing purposes by the respective regulation. 

The RP requests this Claim like any other Claim via the `claims` parameter or as part of a default Claim set identified by a scope value. 

The `txn` value MUST allow an RP to obtain these transaction details if needed.

Note: the mechanism to obtain the transaction details from the OP and their format is out of scope of this specification. 
    



