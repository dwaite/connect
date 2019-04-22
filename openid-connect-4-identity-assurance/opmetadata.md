# OP Metadata {#opmetadata}

The OP advertises its capabilities with respect to verified person data in its openid-configuration (see [@!OpenID-Discovery]) using the following new elements:

`trust_frameworks_supported` This is a JSON array containing all supported trust frameworks and assurance levels.

`verified_person_data_supported` This JSON array contains all claims supported within `verified_person_data`.

`verification_methods_supported` This element is a JSON array containing at the top level the verification methods as defined in Section [verification](#verification). `eID` is  a simple JSON string whereas `identity_document` is contained in a JSON object and is itself an array containing all identity document proofing methods employed by the respective OP. 

This is an example openid-configuration snippet:

```json
{  
...
   "trust_frameworks_supported":[
     "https://openid.net/trust_frameworks/nist-800-63A/ial_2",
     "https://openid.net/trust_frameworks/nist-800-63A/ial_3"
   ]
   "verified_person_data_supported":[  
      "given_name",
      "family_name",
      "birthdate",
      "place_of_birth",
      "nationality",
      "address"
   ],
   "verification_methods_supported":[  
      {  
         "identity_document":[  
            "https://openid.net/verification_method/pipp_bank",
            "https://yes.com/verification_method/pipp_shop",
            "https://yes.com/verification_method/pipp_courier",
            "https://yes.com/verification_method/srip"
         ]
      },
      "eID"
   ]
...
}
```

The OP MUST support the claims parameter and needs to publish this in its openid-configuration using the claim `claims_parameter_supported`. 
