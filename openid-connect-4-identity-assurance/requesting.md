# Requesting Verified Person Data

## Requesting User Claims

Verified person data can be requested on the level of individual user claims by utilzing the `claims` parameter as defined in Section 5.5. of the OpenID Connect specification]@!OpenID]. 

Bassically, the claim `verified_person_data` is added to the section "userinfo" or "id_token" of the `claims` parameter. 

Since `verified_person_data` contains the effective user claims in a nested `claims` element, the syntax is extended to include expression on nested elements as follows. The `verified_person_data` element includes a `claims`element, which in turn includes the desired user claims as keys with a `null` value. An example is shown in the following:

```json
{  
   "userinfo":{  
      "verified_person_data":{  
         "claims":{  
            "given_name":null,
            "family_name":null,
            "birthdate":null,
            "place_of_birth":null,
            "nationality":null,
            "address":null
         }
      }
   }	
}
```

Use of the `claims` parameter allows the RP to exactly select the user claims needed for their use case, this extension therefore allows RPs to fulfill the requirement for data minimization.

Note: A `claims` sub-element with value `null` is interpreted as a request for all possible claims. An example is shown in the following:

```json
{  
   "userinfo":{  
      "verified_person_data":{  
         "claims":null
      }
   }	
}
```

Note: The `claims` sub-element can be omitted, which is equivalent to a claims element whose value is `null`.

Note: If the `claims` sub-element is empty or contains a claim other than the claims listed in the Section [claims](#claimselement), the OP will abort the transaction with an `invalid_request` error.

## Requesting Data of a certain age

The RP may also express a requirement regarding the age of the verification data, i.e., the time elapsed since the verification process asserted in the `verification` element has taken place. 

This, again, requires an extension to the syntax as defined in Section 5.5. of the OpenID Connect specification]@!OpenID] due to the nested nature of the `verified_person_data` claim.

The RP uses a `max_age` field on the sub-element `date` of the `verification` sub-claim that defines the maximum time elapsed since the verification process took place in seconds. The following is an example:

```json
{  
   "userinfo":{  
      "verified_person_data":{  
         "verification":{  
            "date":{  
               "max_age":"63113852"
            }
         },
         "claims":null
      }
   }
}
```

The IDP SHOULD try to fulfill this requirement. If the verification data of the user is older than the requested `max_age`, it SHOULD attempt to refresh the user’s verification by sending her through a online identity verification process, e.g. by utilizing an electronic ID card or a video identification approach. 

If the IDP is unable to fulfill the requirement, there are two possible outcomes of the transaction:

* If the RP did not request `date` as essential claim, the IDP will provide the RP with the data available and the RP may decide how to use the data. 

* If the RP requested `date` as essential claim, the IDP will abort the transaction and respond with the (new) error code `unable_to_meet_requirement`. 

The following is an example of a claims parameter requesting `date` as essential claim.

```json
{  
   "userinfo":{  
      "verified_person_data":{  
         "verification":{  
            "date":{  
               "max_age":"63113852",
               "essential":true
            }
         }
      }
   }
}
```

