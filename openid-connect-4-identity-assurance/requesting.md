# Requesting Verified Claims

## Requesting Verified Claims about the End-User

Verified person data can be requested on the level of individual Claims about the End-User by utilizing the `claims` parameter as defined in Section 5.5. of the OpenID Connect specification [@!OpenID]. 

The claim `verified_person_data` is added to the `userinfo` or `id_token` element of the `claims` parameter. 

Since `verified_person_data` contains the effective Claims about the End-User in a nested `claims` element, the syntax is extended to include expressions on nested elements as follows. The `verified_person_data` element includes a `claims` element, which in turn includes the desired Claims as keys with a `null` value. An example is shown in the following:

```json
{  
   "userinfo":{  
      "verified_person_data":{  
         "claims":{  
            "given_name":null,
            "family_name":null,
            "birthdate":null
         }
      }
   }	
}
```

Use of the `claims` parameter allows the RP to exactly select the Claims about the End-User needed for its use case. This extension therefore allows RPs to fulfill the requirement for data minimization.

RPs MAY indicate that a certain claim is essential to the sucessful completion of the user journey by utilizing the `essential` field as defined in Section 5.5.1. of the OpenID Connect specification [@!OpenID]. The following example designates both given as well as family name as being essential.

```json
{  
   "userinfo":{  
      "verified_person_data":{  
         "claims":{  
            "given_name":{"essential": true},
            "family_name":{"essential": true},
            "birthdate":null
         }
      }
   }	
}
```

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

Note: If the `claims` sub-element is empty or contains a claim not fulfilling the requirements defined in (#claimselement), the OP will abort the transaction with an `invalid_request` error.

## Defining constraints on requested Claims {#constraintedclaims}

The RP MAY express requirements regarding the elements in the `verification` sub-element.

This, again, requires an extension to the syntax as defined in Section 5.5. of the OpenID Connect specification [@!OpenID] due to the nested nature of the `verified_person_data` claim.

Section 5.5.1 of the OpenID Connect specification [@!OpenID] defines a query syntax that allows for the member value of the claim being requested to be a JSON object with additional information/constraints on the claim. For doing so it defines three members (`essential`, `value` and `values`) with special query 
meanings and allows for other special members to be defined (while stating that any members that are not understood must be ignored).

This specification re-uses that mechanism and introduces a new such member `max_age` (see below).

To start with, the RP MAY limit the possible values of the elements `trust_framework`, `evidence/id_document/document/type`, and `identity_document/method` by utilizing the `value` or `values` fields. 

The following example shows that the RP wants to obtain an attestation based on AML and limited to users who were identified in a bank branch using government issued id documents.

```json
{  
   "userinfo":{  
      "verified_person_data":{  
         "verification":{  
            "trust_framework":{  
               "value":"de_aml"
            },
            "id_document":{  
               "document":{  
                  "type":{  
                     "values":[  
                        "idcard",
                        "passport"
                     ]
                  }
               },
               "method":{  
                  "value":"pipp"
               }
            }
         },
         "claims":null
      }
   }
}
```

The RP MAY also express a requirement regarding the age of the verification data, i.e., the time elapsed since the verification process asserted in the `verification` element has taken place. 

This specification therefore defines a new member `max_age`.

`max_age`: OPTIONAL. Is a JSON number value only applicable to claims that contain dates or timestamps. It defines the maximum time (in seconds) to be allowed to elapse since the value of the date/timestamp up to the point in time of the request. The OP should make the calculation of elapsed time starting from the last valid second of the date value. The following is an example of a request for claims where the verification process of the data is not allowed to be older than 63113852 seconds.

The following is an example:

```json
{  
   "userinfo":{  
      "verified_person_data":{  
         "verification":{  
            "date":{  
               "max_age":63113852
            }
         },
         "claims":null
      }
   }
}
```

The OP SHOULD try to fulfill this requirement. If the verification data of the user is older than the requested `max_age`, the OP MAY attempt to refresh the user’s verification by sending her through a online identity verification process, e.g. by utilizing an electronic ID card or a video identification approach. 

If the OP is unable to fulfill the requirement (even in case it is marked as being `essential`), it will provide the RP with the data available and the RP may decide how to use the data. The OP MUST NOT return an error in case it cannot return all claims requested as essential claims.
