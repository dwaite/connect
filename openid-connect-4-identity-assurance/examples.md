# Examples

The following sections show examples of `verified_person_data` objects. The first two sections show JSON snippets of the different verification methods. Subsequent sections contain examples of different channels and combinations of verified and non-verified user claims.

## identity_document

```JSON
{  
   "verified_person_data":{  
      "verification":{  
         "organization":"Bank Y",
         "legal_context":{  
            "country":"DE",
            "regulation":"Geldwäschegesetz"
         },
         "date":"2013-02-21",
         "id":"676q3636461467647q8498785747q487",
         "method":"identity_document",
         "identity_document":{  
            "country":"DE",
            "type":"ID Card",
            "issuer":"Stadt Augsburg",
            "number":"53554554",
            "date_of_issuance":"2012-04-23",
            "date_of_expiry":"2022-04-22",
            "method":"Physical In-Person Proofing (shop)",
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

## eID

```JSON
{  
   "verified_person_data":{  
      "verification":{  
         "organization":"Bank Y",
         "legal_context":{  
            "country":"DE",
            "regulation":"Geldwäschegesetz"
         },
         "date":"2013-02-21",
         "id":"676q3636461467647q8498785747q487",
         "method":"eID",
         "eID":{  
            "country":"DE",
            "type":"ID Card",
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
         "organization":"Bank Y",
         "legal_context":{  
            "country":"DE",
            "regulation":"Geldwäschegesetz"
         },
         "date":"2013-02-21",
         "id":"676q3636461467647q8498785747q487",
         "method":"identity_document",
         "identity_document":{  
            "country":"DE",
            "type":"ID Card",
            "issuer":"Stadt Augsburg",
            "number":"53554554",
            "date_of_issuance":"2012-04-23",
            "date_of_expiry":"2022-04-22",
            "method":"Physical In-Person Proofing (shop)",
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
         "organization":"Bank Y",
         "legal_context":{  
            "country":"DE",
            "regulation":"Geldwäschegesetz"
         },
         "date":"2013-02-21",
         "id":"676q3636461467647q8498785747q487",
         "method":"identity_document",
         "identity_document":{  
            "country":"DE",
            "type":"ID Card",
            "issuer":"Stadt Augsburg",
            "number":"53554554",
            "date_of_issuance":"2012-04-23",
            "date_of_expiry":"2022-04-22",
            "method":"Physical In-Person Proofing (shop)",
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
         "JWT":"eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9.eyJ2ZXJpZmllZF9wZXJzb25fZGF0YSI6e
yJ2ZXJpZmljYXRpb24iOnsib3JnYW5pemF0aW9uIjoiQmFuayBZIiwibGVnYWxfY29udGV4dCI6eyJjb3VudHJ
5IjoiREUiLCJyZWd1bGF0aW9uIjoiR2VsZHfDpHNjaGVnZXNldHoifSwiZGF0ZSI6IjIwMTMtMDItMjEiLCJpZ
CI6IjY3NnEzNjM2NDYxNDY3NjQ3cTg0OTg3ODU3NDdxNDg3IiwibWV0aG9kIjoiaWRlbnRpdHlfZG9jdW1lbnQ
iLCJpZGVudGl0eV9kb2N1bWVudCI6eyJjb3VudHJ5IjoiREUiLCJ0eXBlIjoiSUQgQ2FyZCIsImlzc3VlciI6I
lN0YWR0IEF1Z3NidXJnIiwibnVtYmVyIjoiNTM1NTQ1NTQiLCJkYXRlX29mX2lzc3VhbmNlIjoiMjAxMi0wNC0
yMyIsImRhdGVfb2ZfZXhwaXJ5IjoiMjAyMi0wNC0yMiIsIm1ldGhvZCI6IlBoeXNpY2FsIEluLVBlcnNvbiBQc
m9vZmluZyAoc2hvcCkiLCJvcmdhbml6YXRpb24iOiJEZXV0c2NoZSBQb3N0IEFHIiwiYWdlbnQiOiJTdGVmZmV
uIFNjaHVzdGVyIn19LCJjbGFpbXMiOnsiZ2l2ZW5fbmFtZSI6Ik1heCIsImZhbWlseV9uYW1lIjoiTWVpZXIiL
CJiaXJ0aGRhdGUiOiIxOTU2LTAxLTI4In19fQ.qrc-5ij_1tH4e4CaRvtBxbksTxCGuUlQ0DBatve9TBLpb8lS
N52UED2sKOvlIkKfepXt5e0i1O3kZbdUhDhMK-OI0jNnoIZByevCs_hpZp7oM0iBOjrzUsZbVTAo8eBMc6pmjP
CoxGg4ca0D_97WEIAMJ_P2ohhl_AoxzBNAyNc"
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