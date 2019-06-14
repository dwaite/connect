# Transaction-specific Purpose {#purpose}

This specification introduces the request parameter `purpose` to allow a RP
to state the purpose for the transfer of user data it is asking for.

`purpose` OPTIONAL. String describing the purpose for obtaining certain user data from the OP. The purpose MUST NOT be shorter than 3 characters and MUST NOT be longer than 300 characters. If these rules are violated, the authentication request MUST fail and the OP returns an error `invalid_request` to the RP.

The OP MUST display this purpose in the respective user consent screen(s) in order to inform the user about the designated use of the data to be transferred or the authorization to be approved. 

In order to ensure a consistent UX, the RP MAY send the `purpose` in a certain language and request the OP to use the same language using the `ui_locales` parameter.

If the parameter `purpose` is not present in the request, the OP MAY utilize a description that was pre-configured for the respective RP.

Note: In order to prevent injection attacks, the IDP MUST escape the text appropriately before it will be shown in a user interface. The IDP MUST expect special characters in the URL decoded purpose text provided by the RP. The IDP MUST ensure that any special characters in the purpose text cannot be used to inject code into the web interface of the OP (e.g. cross-site scripting, defacing). Proper escaping MUST be applied by the OP. The OP SHALL NOT remove characters from the purpose text to this end.

