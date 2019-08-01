# OP Metadata {#opmetadata}

The OP advertises its capabilities with respect to verified Claims in its openid-configuration (see [@!OpenID-Discovery]) using the following new elements:

`verified_claims_supported`: Boolean value indicating support for `verified_claims`, i.e. the OpenID Connect for Identity Assurance extension. 

`trust_frameworks_supported` This is a JSON array containing all supported trust frameworks.

`evidence_supported` This JSON array contains all types of identity evidence the OP uses.

`id_documents_supported` This JSON array contains all identity documents utilized by the OP for identity verification.

`id_documents_verification_methods_supported` This element is a JSON array containing the id document verification methods a OP supports as defined in (#verification). 

`claims_in_verified_claims_supported` This JSON array contains all claims supported within `verified_claims`.

This is an example openid-configuration snippet:

```json
{  
...
   "verified_claims_supported":true,
   "trust_frameworks_supported":[
     "nist_800_63A_ial_2",
     "nist_800_63A_ial_3"
   ],
   "evidence_supported":[
      "id_document",
      "utility_bill",
      "qes"
   ]
   "id_documents_supported":[  
       "idcard",
       "passport",
       "driving_permit"
   ]
   "id_documents_verification_methods_supported":[  
       "pipp",
       "sripp",
       "eid"
   ]
   "claims_in_verified_claims_supported":[  
      "given_name",
      "family_name",
      "birthdate",
      "place_of_birth",
      "nationality",
      "address"
   ],
...
}
```

The OP MUST support the `claims` parameter and needs to publish this in its openid-configuration using the `claims_parameter_supported` element. 
