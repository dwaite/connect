# OP Metadata {#opmetadata}

The OP advertises its capabilities with respect to verified person data in its openid-configuration (see [@!OpenID-Discovery]) using the following new elements:

`trust_frameworks_supported` This is a JSON array containing all supported trust frameworks and assurance levels.

`verified_person_data_supported` This JSON array contains all claims supported within `verified_person_data`.

`identity_documents_supported` This JSON array contains all identity documents utilized by the OP for identity verification.

`verification_methods_supported` This element is a JSON array containing at the top level the verification methods as defined in Section [verification](#verification). `eID` is  a simple JSON string whereas `identity_document` is contained in a JSON object and is itself an array containing all identity document proofing methods employed by the respective OP. 

This is an example openid-configuration snippet:

```json
{  
...
   "trust_frameworks_supported":[
     "nist_800_63A_ial_2",
     "nist_800_63A_ial_3"
   ]
   "verified_person_data_supported":[  
      "given_name",
      "family_name",
      "birthdate",
      "place_of_birth",
      "nationality",
      "address"
   ],
   "identity_documents_supported":[  
       "idcard",
       "passport",
       "driving_permit"
   ]
   "verification_methods_supported":[  
      {  
         "identity_document":[  
            "pipp",
            "sripp"
         ]
      },
      "eID"
   ]
...
}
```

The OP MUST support the claims parameter and needs to publish this in its openid-configuration using the claim `claims_parameter_supported`. 
