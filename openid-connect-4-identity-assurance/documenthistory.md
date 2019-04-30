# Document History

   [[ To be removed from the final specification ]]
   
   -03
   
   * changed `eidas_loa_substantial/high` to `eidas_ial_substantial/high` 
   * fixed typos in examples
   
   -02
   
   * added new request parameter `purpose`
   * simplified/reduced number of verification methods
   * simplfied identifiers
   * added `identity_documents_supported` to metadata section
   * improved examples
   
   -01 

   *  fixed some typos
   *  remove organization element (redundant) (issue 1080)
   *  allow other user claims in the `claims` sub element (issue 1079)
   *  changed legal_context to trust_framework
   *  added explanation how the content of the verification element is determined by the trust framework
   *  added URI-based identifiers for trust_framework, identity_document and (verification) method
   *  added example attestation for notified/regulated eID system
   *  adopted OP metadata section accordingly 
   *  changed error behavior for `max_age` member to alig with OpenID Core
   *  Added feature to let the RP express requirements for verification data (trust framework, identity documents, verification method)
   *  Added privacy consideration section and added text on legal basis for data exchange
   *  Added explanation about regulated and un-regulated eID systems
   
   -00 (WG document)

   *  turned the proposal into a WG document
   *  changed name
   *  added terminology section and reworked introduction
   *  added several examples (ID Token vs User Info, non-verified & verified claims, aggregated & distributed claims)
   *  incorporated text proposal of Marcos Sanz regarding max_age
   *  added IANA registration for new error code `unable_to_meet_requirement`