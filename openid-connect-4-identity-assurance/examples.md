# Examples

The following sections show examples of `verified_person_data` objects. 

The first and second section show JSON snippets of the general identity assurance case, where the RP is provided with verification evidence for different verification methods along with the actual Claims about the End-User.

The third section illustrates how the contents of this object could look like in case of a notified eID system under eIDAS, where the OP does not need to provide evidence of the identity verification process to the RP. 

Subsequent sections contain examples for using the `verified_person_data` claim on different channels and in combination with other (unverified) Claims.

## id_document

```JSON
{  
   "verified_person_data":{  
      "verification":{  
         "trust_framework":"de_aml",
         "date":"2013-02-21",
         "verification_process":"676q3636461467647q8498785747q487",
         "evidence":[  
            {  
               "type":"id_document",
               "method":"pipp",
               "document":{  
                  "type":"idcard",
                  "issuer":{  
                     "name":"Stadt Augsburg",
                     "country":"DE"
                  },
                  "number":"53554554",
                  "date_of_issuance":"2012-04-23",
                  "date_of_expiry":"2022-04-22"
               }
            }
         ]
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
            "street_address":"An der Sanddüne 22"
         }
      }
   }
}
```

## id_document + utility bill

```JSON
{  
   "verified_person_data":{  
      "verification":{  
         "trust_framework":"de_aml",
         "date":"2013-02-21",
         "verification_process":"676q3636461467647q8498785747q487",
         "evidence":[  
            {  
               "type":"id_document",
               "method":"pipp",
               "document":{  
                  "document_type":"de_erp_replacement_idcard",
                  "issuer":{  
                     "name":"Stadt Augsburg",
                     "country":"DE"
                  },
                  "number":"53554554",
                  "date_of_issuance":"2012-04-23",
                  "date_of_expiry":"2022-04-22"
               }
            },
            {  
               "type":"utility_bill",
               "provider":{  
                  "name":"Stadtwerke Musterstadt",
                  "country":"DE",
                  "region":"Thüringen",
                  "street_address":"Energiestrasse 33"
               },
               "date":"2013-01-31"
            }
         ]
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
            "street_address":"An der Sanddüne 22"
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
         "trust_framework":"eidas_ial_substantial"
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
            "street_address":"An der Sanddüne 22"
         }
      }
   }
}
```


## Verified Person Data in UserInfo Response

### Request

In this example we assume the RP uses the scope parameter to request the email address and, additionally, the claims parameter, to request verified person data. 

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

### UserInfo Response

The respective UserInfo response would be

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
         "verification_process":"676q3636461467647q8498785747q487",
         "evidence":[  
            {  
               "type":"id_document",
               "method":"pipp",
               "document":{  
                  "type":"idcard",
                  "issuer":{  
                     "name":"Stadt Augsburg",
                     "country":"DE"
                  },
                  "number":"53554554",
                  "date_of_issuance":"2012-04-23",
                  "date_of_expiry":"2022-04-22"
               }
            }
         ]
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

In this case, the RP requests verfied person data along with other Claims about the End-User in the claims parameter and allocates the response to the ID Token (delivered from the token endpoint in case of grant type `code`). 

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
         "verification_process":"676q3636461467647q8498785747q487",
         "evidence":[  
            {  
               "type":"id_document",
               "method":"pipp",
               "document":{  
                  "type":"idcard",
                  "issuer":{  
                     "name":"Stadt Augsburg",
                     "country":"DE"
                  },
                  "number":"53554554",
                  "date_of_issuance":"2012-04-23",
                  "date_of_expiry":"2022-04-22"
               }
            }
         ]
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
      Ghlcm9wLmNvbSIsInZlcmlmaWVkX3BlcnNvbl9kYXRhIjp7InZlcmlmaWNhdGlvbiI6eyJ0cnVzdF
      9mcmFtZXdvcmsiOiJlaWRhc19pYWxfc3Vic3RhbnRpYWwifSwiY2xhaW1zIjp7ImdpdmVuX25hbWU
      iOiJNYXgiLCJmYW1pbHlfbmFtZSI6Ik1laWVyIiwiYmlydGhkYXRlIjoiMTk1Ni0wMS0yOCJ9fX0.
      6xBFL69A7LFPf_ISb6ZOHrDN3CTjqYmlSaBn-xATy8k"
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
         "endpoint":"https://server.yetanotherop.com/claim_source",
         "access_token":"ksj3n283dkeafb76cdef"
      }
   }
}
```