How Sign In with Apple differs from OpenID Connect
==================================================

This document tracks the differences between Apple’s implementation of “Sign In With Apple” and the OpenID Connect protocol.
The former apparently aims to be an implementation of the latter but is not completely aligned (yet).
This document - driven by the OpenID Foundation’s Certification team and the identity community at large - may help to close the gap and/or to help developers to cope with the differences.
Improvements made by Apple based on this input are recorded in the "Fixed" section below.

### Spec Violations

- The `code id_token` response type returns the response parameters including `id_token` and `code` as query parameters, not in the fragment.
    - It makes ID Token and Authorization Code potentially leak through referrer etc. ID Token is a set of personal data and leaking it will pose privacy risk. An authorization code obtained by the attacker in this way may be used for the Code Insertion Attack.

### Fixed

The following issues have been addressed by Apple after the initial release of this document:

- When `nonce` is provided in the `code` or `code id_token` grant types, it isn’t included in the `id_token` returned.
    - Not having the `nonce` removes the protocol's ability to thwart Cross Site Request Forgery Attack.
- The `code id_token` response type does not include `c_hash` in the returned `id_token`.
    - It makes it possible for Attackers to insert the authorization code they have obtained (Code Insertion Attack). 
- When `max_age` is requested, the `id_token` does not include an `auth_time` claim.
    - The Client may want to limit access to protect the user if the authentication at the Provider was done too long ago but the lack of support for `max_age` does not allow for such a decision. 
      Currently, Sign in with Apple seem to require the re-authentication everytime so auth_time can be inferred but it harms the interoperability. 
- Providing a `prompt` parameter with any value (e.g. `login` or `consent`) or empty results in a 400 with no body.
    - Both desensitizing users by asking users too much action and not being able to ask for explicit consent increases the privacy risk for end users. 
      These parameters are there to enable hitting the right balance but Sign in with Apple just bails harting the interoperability. 
- Exchanging the authorization code according to https://developer.apple.com/documentation/signinwithapplerestapi/generate_and_validate_tokens should present a non-standard `grant_type=authorization_token` but using the standards-compliant `grant_type=authorization_code` actually works whereas the former does not, so the documentation is incorrect.

TODO: Run against the OIDF OpenID Connect certification tool and provide the results and explanation.

### Peculiarities

- No Discovery document is published at https://appleid.apple.com/.well-known/openid-configuration which makes developers have to read through the Apple docs to find out about endpoints, scopes, signing algorithms, authentication methods, etc.
- No UserInfo endpoint is provided, which means all of the claims about users have to be included in the (expiring and potentially large) `id_token`.
- Does not include different claims in the `id_token` based on requested scopes.
- The token endpoint does not accept `client_secret_basic` as a client authentication method (required for OpenID Connect certification) and which is actually the default method to use for Clients when there’s no Discovery document that says otherwise
- Authentication at the token endpoint requires a (custom) JWT assertion as a `client_secret` in a `client_secret_post` authentication method whereas the more appropriate `private_key_jwt` authentication method as defined in RFC 7523 could have been used.
- Using unsupported or wrong parameters (e.g. non-existing `response_type`, `scope`, `client_id`, or `redirect_uri`) always results in the same message in the browser that says “Your request could not be completed because of an error. Please try again later.” without any explanation about what happened, why this is an error, or how to fix it.
- The Authorization Code grant type (for public Clients) does not use PKCE [RFC 7636] to avoid code injection and code replay attacks.
- When using the sample app available at https://github.com/aaronpk/sign-in-with-apple-example, adding `openid` as a `scope` (which is used to signal that an OpenID Connect authentication is being requested) leads to an error message and it works just with `name` and `email` as `scope` values; this behavior seems to be inconsistent across Clients: for some it works, or at least does not lead to errors, for others it does not work and ends with an error.
- The `scope` value of only the very first request by an application is respected. If an application initially requests only the `name` scope, and the user allows it, it is then impossible to later also request the `email` scope. 

### Acknowledgements

Thanks to the following people for their contributions to this analysis:

- Hans Zandbelt
- Filip Skokan
- Joseph Heenan
- Serkan Ozkan
- Amir Sharif
- Chuck Mortimore
- Jeff Lombardo
- Nov Matake
- Nat Sakimura
- Michael B. Jones
- Aaron Parecki
