# Examples

The following sections show examples of `verified_person_data` objects. 

The first section illustrate the contents of this object in case of a notified eID system. 

The second and third section show JSON snippets of the identity assurance by an unregulated OP, providing the RP with verification evidences for different verification methods along with the actual user claims. 

Subsequent sections contain examples of different channels and combinations of verified and non-verified user claims.

## Notified eID system

```JSON
{  
   "verified_person_data":{  
      "verification":{  
         "trust_framework":"https://openid.net/trust_frameworks/eidas/loa_substantial"
      },
      "claims":{  
         "given_name":"Max",
         "family_name":"Meier",
         "birthdate":"1956-01-28",
         "place_of_birth":{  
            "country":"DE",
            "locality":"Musterstadt"
         },
         "nationality":"DE",
         "address":{  
            "locality":"Maxstadt",
            "postal_code":"12344",
            "country":"DE",
            "street":"An der Sanddüne 22"
         }
      }
   }
}
```

## Unregulated eID system (identity_document)

```JSON
{  
   "verified_person_data":{  
      "verification":{  
         "trust_framework":"https://openid.net/trust_frameworks/de/aml",
         "date":"2013-02-21",
         "id":"676q3636461467647q8498785747q487",
         "method":"identity_document",
         "identity_document":{  
            "country":"DE",
            "type":"https://openid.net/id_document/idcard",
            "issuer":"Stadt Augsburg",
            "number":"53554554",
            "date_of_issuance":"2012-04-23",
            "date_of_expiry":"2022-04-22",
            "method":"https://openid.net/verification_method/pipp_shop",
            "organization":"Deutsche Post AG",
            "agent":"Steffen Schuster"
         }
      },
      "claims":{  
         "given_name":"Max",
         "family_name":"Meier",
         "birthdate":"1956-01-28",
         "place_of_birth":{  
            "country":"DE",
            "locality":"Musterstadt"
         },
         "nationality":"DE",
         "address":{  
            "locality":"Maxstadt",
            "postal_code":"12344",
            "country":"DE",
            "street":"An der Sanddüne 22"
         }
      }
   }
}
```

## Unregulated eID system (eID)

```JSON
{  
   "verified_person_data":{  
      "verification":{  
         "trust_framework":"https://openid.net/trust_frameworks/de/aml",
         "date":"2013-02-21",
         "id":"676q3636461467647q8498785747q487",
         "method":"eID",
         "eID":{  
            "country":"DE",
            "type":"https://openid.net/id_document/idcard",
            "identifier":"1d464976-d1fc-4460-a2de-9796d2b120fc"
         }
      },
      "claims":{  
         "given_name":"Max",
         "family_name":"Meier",
         "birthdate":"1956-01-28",
         "place_of_birth":{  
            "country":"DE",
            "locality":"Musterstadt"
         },
         "nationality":"DE",
         "address":{  
            "locality":"Maxstadt",
            "postal_code":"12344",
            "country":"DE",
            "street":"An der Sanddüne 22"
         }
      }
   }
}
```


## Verified Person Data in User Info Response

### Request

In this example we assume the RP uses the scope parameter to request the email address and , additionally, the claims parameter, to request verified person data. 

The scope value is: `scope=openid email`

The value of the claims parameter is:

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

### User Info Response

The respective user info response would be

```http
HTTP/1.1 200 OK
Content-Type: application/json

{  
   "sub":"248289761001",
   "email":"janedoe@example.com",
   "email_verified":true,
   "verified_person_data":{  
      "verification":{  
         "trust_framework":"https://openid.net/trust_frameworks/de/aml",
         "date":"2013-02-21",
         "id":"676q3636461467647q8498785747q487",
         "method":"identity_document",
         "identity_document":{  
            "country":"DE",
            "type":"https://openid.net/id_document/idcard",
            "issuer":"Stadt Augsburg",
            "number":"53554554",
            "date_of_issuance":"2012-04-23",
            "date_of_expiry":"2022-04-22",
            "method":"https://openid.net/verification_method/pipp_bank",
            "organization":"Deutsche Post AG",
            "agent":"Steffen Schuster"
         }
      },
      "claims":{  
         "given_name":"Max",
         "family_name":"Meier",
         "birthdate":"1956-01-28"
      }
   }
}
```

## Verified Person Data in ID Tokens

### Request

In this case, the RP requests verfied person data along with other user claims in the claims parameter and allocates the response to the ID Token (delivered from the token endpoint in case of grant type `code`). 

The `claims` parameter value is

```json
{  
   "id_token":{  
      "email":null,
      "preferred_username":null,
      "picture":null,
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

### ID Token

The respective ID Token could be

```json
{  
   "iss":"https://server.example.com",
   "sub":"24400320",
   "aud":"s6BhdRkqt3",
   "nonce":"n-0S6_WzA2Mj",
   "exp":1311281970,
   "iat":1311280970,
   "auth_time":1311280969,
   "acr":"urn:mace:incommon:iap:silver",
   "email":"janedoe@example.com",
   "preferred_username":"j.doe",
   "picture":"http://example.com/janedoe/me.jpg",
   "verified_person_data":{  
      "verification":{  
         "trust_framework":"https://openid.net/trust_frameworks/de/aml",
         "date":"2013-02-21",
         "id":"676q3636461467647q8498785747q487",
         "method":"identity_document",
         "identity_document":{  
            "country":"DE",
            "type":"https://openid.net/id_document/idcard",
            "issuer":"Stadt Augsburg",
            "number":"53554554",
            "date_of_issuance":"2012-04-23",
            "date_of_expiry":"2022-04-22",
            "method":"https://openid.net/verification_method/pipp_shop",
            "organization":"Deutsche Post AG",
            "agent":"Steffen Schuster"
         }
      },
      "claims":{  
         "given_name":"Max",
         "family_name":"Meier",
         "birthdate":"1956-01-28"
      }
   }
}
```

## Aggregated Claims
Note: line breaks for display purposes only

```http
HTTP/1.1 200 OK
Content-Type: application/json

{  
   "sub":"248289761001",
   "email":"janedoe@example.com",
   "email_verified":true,
   "_claim_names":{  
      "verified_person_data":"src1"
   },
   "_claim_sources":{  
      "src1":{  
      "JWT":"eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ2ZXJpZmllZF9wZXJzb25fZGF0YSI6ey
J2ZXJpZmljYXRpb24iOnsidHJ1c3RfZnJhbWV3b3JrIjoiaHR0cHM6Ly9vcGVuaWQubmV0L3RydXN0X2ZyYW
1ld29ya3MvZGUvYW1sIiwiZGF0ZSI6IjIwMTMtMDItMjEiLCJpZCI6IjY3NnEzNjM2NDYxNDY3NjQ3cTg0OT
g3ODU3NDdxNDg3IiwibWV0aG9kIjoiaWRlbnRpdHlfZG9jdW1lbnQiLCJpZGVudGl0eV9kb2N1bWVudCI6ey
Jjb3VudHJ5IjoiREUiLCJ0eXBlIjoiaHR0cHM6Ly9vcGVuaWQubmV0L2lkX2RvY3VtZW50L2lkY2FyZCIsIm
lzc3VlciI6IlN0YWR0IEF1Z3NidXJnIiwibnVtYmVyIjoiNTM1NTQ1NTQiLCJkYXRlX29mX2lzc3VhbmNlIj
oiMjAxMi0wNC0yMyIsImRhdGVfb2ZfZXhwaXJ5IjoiMjAyMi0wNC0yMiIsIm1ldGhvZCI6Imh0dHBzOi8vb3
BlbmlkLm5ldC92ZXJpZmljYXRpb25fbWV0aG9kL3BpcHMiLCJvcmdhbml6YXRpb24iOiJEZXV0c2NoZSBQb3
N0IEFHIiwiYWdlbnQiOiJTdGVmZmVuIFNjaHVzdGVyIn19LCJjbGFpbXMiOnsiZ2l2ZW5fbmFtZSI6Ik1heC
IsImZhbWlseV9uYW1lIjoiTWVpZXIiLCJiaXJ0aGRhdGUiOiIxOTU2LTAxLTI4In19fQ.snH3ADJDFwJ_7AM
lA3FNTbpmBIp0AMJZLRgnhY8caIk"
      }
   }
}
```

## Distributed Claims

```http
HTTP/1.1 200 OK
Content-Type: application/json

{  
   "sub":"248289761001",
   "email":"janedoe@example.com",
   "email_verified":true,
   "_claim_names":{  
      "verified_person_data":"src1"
   },
   "_claim_sources":{  
      "src1":{  
         "endpoint":"https://bank.example.com/claim_source",
         "access_token":"ksj3n283dke"
      }
   }
}
```