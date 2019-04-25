# Examples

The following sections show examples of `verified_person_data` objects. 

The first and second section show JSON snippets of the general identity assurance case, where the RP is provided with verification evidences for different verification methods along with the actual user claims.

The third section illustrate how the contents of this object could look like in case of a notified eID system under eIDAS, where the OP does not need to provide evidences of the identity verification process to the RP. 

Subsequent sections contain examples for using the `verified_person_data` claim on different channels and in combination with other (non-verified) user claims.

## identity_document

```JSON
{  
   "verified_person_data":{  
      "verification":{  
         "trust_framework":"de_aml",
         "date":"2013-02-21",
         "id":"676q3636461467647q8498785747q487",
         "method":"identity_document",
         "identity_document":{  
            "country":"DE",
            "type":"idcard",
            "issuer":"Stadt Augsburg",
            "number":"53554554",
            "date_of_issuance":"2012-04-23",
            "date_of_expiry":"2022-04-22",
            "method":"pipp",
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
         "trust_framework":"de_aml",
         "date":"2013-02-21",
         "id":"676q3636461467647q8498785747q487",
         "method":"eID",
         "eID":{  
            "country":"DE",
            "type":"idcard",
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

## Notified eID system (eIDAS)

```JSON
{  
   "verified_person_data":{  
      "verification":{  
         "trust_framework":"eidas_loa_substantial"
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
   "iss":"https://server.example.com",
   "sub":"248289761001",
   "email":"janedoe@example.com",
   "email_verified":true,
   "verified_person_data":{  
      "verification":{  
         "trust_framework":"de_aml",
         "date":"2013-02-21",
         "id":"676q3636461467647q8498785747q487",
         "method":"identity_document",
         "identity_document":{  
            "country":"DE",
            "type":"idcard",
            "issuer":"Stadt Augsburg",
            "number":"53554554",
            "date_of_issuance":"2012-04-23",
            "date_of_expiry":"2022-04-22",
            "method":"pipp",
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
         "trust_framework":"de_aml",
         "date":"2013-02-21",
         "id":"676q3636461467647q8498785747q487",
         "method":"identity_document",
         "identity_document":{  
            "country":"DE",
            "type":"idcard",
            "issuer":"Stadt Augsburg",
            "number":"53554554",
            "date_of_issuance":"2012-04-23",
            "date_of_expiry":"2022-04-22",
            "method":"pipp",
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
   "iss":"https://server.example.com", 
   "sub":"248289761001",
   "email":"janedoe@example.com",
   "email_verified":true,
   "_claim_names":{  
      "verified_person_data":"src1"
   },
   "_claim_sources":{  
      "src1":{  
      "JWT":"eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJodHRwczovL3NlcnZlci5vd
GhlcmlkcC5jb20iLCJ2ZXJpZmllZF9wZXJzb25fZGF0YSI6eyJ2ZXJpZmljYXRpb24iOnsidHJ1c3RfZnJhb
WV3b3JrIjoiZWlkYXNfbG9hX3N1YnN0YW50aWFsIn0sImNsYWltcyI6eyJnaXZlbl9uYW1lIjoiTWF4IiwiZ
mFtaWx5X25hbWUiOiJNZWllciIsImJpcnRoZGF0ZSI6IjE5NTYtMDEtMjgifX19.sPDOj44--OdSwilFFVhpXZTSjmrGpEDSb2pSocitMsk"
      }
   }
}
```

## Distributed Claims

```http
HTTP/1.1 200 OK
Content-Type: application/json

{
   "iss":"https://server.example.com",  
   "sub":"248289761001",
   "email":"janedoe@example.com",
   "email_verified":true,
   "_claim_names":{  
      "verified_person_data":"src1"
   },
   "_claim_sources":{  
      "src1":{  
         "endpoint":"https://server.yetanotheridp.com/claim_source",
         "access_token":"ksj3n283dkeafb76cdef"
      }
   }
}
```