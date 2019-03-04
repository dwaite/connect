# OP Metadata {#opmetadata}

The OP advertises its capabilities with respect to verified person data in its openid-configuration (see [@!OpenID-Discovery]) using the following new elements:

`verified_person_data_supported` This JSON array contains all claims supported within `verified_person_data`.

`verification_methods_supported` This element is a JSON array containing at the top level the verification methods as defined in Section [verification](#verification). `eID` is  a simple JSON string whereas `identity_document` is contained in a JSON object and is itself an array containing all identity document proofing methods employed by the respective organisation. 

This is an example openid-configuration snippet:

```json
{  
...
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
            "Physical In-Person Proofing (bank)",
            "Physical In-Person Proofing (shop)",
            "Physical In-Person Proofing (courier)",
            "Supervised remote In-Person Proofing"
         ]
      },
      "eID"
   ]
...
}
```

The OP MUST support the claims parameter and needs to publish this in its openid-configuration using the claim `claims_parameter_supported`. 
