# Claims {#claims}

## User Claims {#userclaims}

In order to fulfill the requirements of some jurisdictions on identity assurance, this specification defines the following claims for conveying user data:

* `place_of_birth`: a structured claim representing the end-user’s place of birth. It consists of the following fields:
	* `country`: ISO 3166-1 Alpha-2, e.g. DE, or ISO 3166-3
	* `locality`: city or other locality
* `nationality`: represents the user’s nationality in format ISO 3166-1 Alpha-2, e.g. DE
* `birth_name`: family name someone has when he or she is born, or at least from the time he or she is a child. This term can be used by a man or a woman who changes a name later in life for any reason.

## transaction_id

Strong identity verification typically requires the participants to keep an audit trail of the whole process, in order to be able to trace it back to its origins in case of a dispute or an audit. 

This specification supports building an audit trail across the boundaries between OP and RP by introducing the claim `transaction_id`.

The `transaction_id` claim is a string value, which MUST be unique within the context of the respective OP, uniquely identify a certain transaction at the OP, and allow an RP to obtain further transaction details if needed.   

The OP MUST maintain a corresponding audit trail for any transaction which lead to the attestation of verified person data. Those details consist of 

* the transaction id
* the authentication methods employed
* the transaction type (e.g. scope values)

The OP MUST store the transaction data as long as it is required to store transaction data for auditing purposes by its respective regulation. 

The RP requests this claim like any other claim via the claims parameter or as part of a default claim set identified by a scope value. 

    



