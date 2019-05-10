# OP Metadata {#opmetadata}

The OP advertises its capabilities with respect to verified person data in its openid-configuration (see [@!OpenID-Discovery]) using the following new elements:

`trust_frameworks_supported` This is a JSON array containing all supported trust frameworks and assurance levels.

`verified_person_data_supported` This JSON array contains all claims supported within `verified_person_data`.

`evidences_supported` This JSON array contains all types of identity evidences the OP uses.

`id_documents_supported` This JSON array contains all identity documents utilized by the OP for identity verification.

`id_documents_verification_methods_supported` This element is a JSON array containing the id document verification methods a OP supports as defined in Section [verification](#verification). 
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
   "evidences_supported":[
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
...
}
```

The OP MUST support the claims parameter and needs to publish this in its openid-configuration using the claim `claims_parameter_supported`. 
