# SAML2 AuthnFlow with Discovery Service

````
sequenceDiagram
 User-Agent->>+Service Provider: GET a protected resource
 Service Provider-->>User-Agent: Response Redirect (302) to discovery service
 User-Agent->>+Discovery Service: GET /disco?entityID=sp.fqdn&return=https://sp.fqdn.org/disco
 Discovery Service-->>User-Agent:  An HTML page containing all the authn endpoints available for that SP
 User-Agent->>Discovery Service: GET the selected Authn endpoint
 Discovery Service-->>-User-Agent: Response Redirect (302) /disco?entityID=idp.fqdn
 User-Agent->>Service Provider: GET /disco?entityID=idp.fqdn
 Service Provider-->>-User-Agent: Response (JS auto submit form) authn forged for the requested idp
 User-Agent->>+Identity Provider: POST the authn request
 Identity Provider-->>User-Agent: Response with the Authentication page (HTML form)
 User-Agent->>Identity Provider: POST credentials (other MFA may occour)
 Identity Provider-->>User-Agent: Response Authentication succesfull (Data and privacy agreement may occour)
 User-Agent->>Identity Provider: Data and privacy agreement may occour
 Identity Provider-->>-User-Agent: Response (JS auto submit form) Authn Response forged for the requesting Service Provider
 User-Agent->>+Service Provider: POST Authn Response
 Service Provider->>Service Provider: Format/Signing/Encryption/Attributes and other policy validations
 Service Provider-->>User-Agent: Response That protected resource
````


# SAML2 SPID AuthnFlow

````
sequenceDiagram
 rect rgba(0, 0, 255, .1)
 User-Agent->>+Service Provider: GET a protected resource
 Service Provider-->>-User-Agent: Response Redirect (302) to SPIDBUTTON PAGE
 User-Agent->>+Service Provider: GET saml2/login/?idp=https://entityid.idp.spid.it&next=/sp/return/page 
 Service Provider-->>-User-Agent: HTTP-POST Binding Response (JS auto submit form) authn forged for the requested idp
 end
 
 rect rgba(252, 226, 233.1)
 User-Agent->>+Identity Provider: POST the sp authn request
 Identity Provider->>Identity Provider: SAML2 validation

 Identity Provider-->>User-Agent: Response with the Authentication page (HTML form)
 User-Agent->>Identity Provider: POST credentials (other MFA may occour)
 Identity Provider-->>User-Agent: Response Authentication succesfull (consent and privacy agreement occour)
 User-Agent->>Identity Provider: GET User give his consent
 Identity Provider-->>-User-Agent: Response (JS auto submit form) Authn Response forged for the requesting Service Provider
 end
 
 rect rgba(227, 252, 236.1)
 User-Agent->>+Service Provider: POST Authn Response
 Service Provider->>Service Provider: SAML2 validation, attribute check
 Service Provider-->>User-Agent: Redirect to next page `/sp/return/page`
 end
````

# SAML2 SPID AA public profile

````
sequenceDiagram
 Note right of User-Agent: User have been authenticated to IdP and have an active session to SP
 User-Agent->>Service Provider: GET a protected resource that needs Extended User Attributes
 
 Service Provider->>Service Provider: assert that needed Attributes belongs to a public profile
 Service Provider-->>Attribute Authority: GET Attributes
````

# SAML2 SPID AA private profile

````
sequenceDiagram
 Note right of User-Agent: User have been authenticated to IdP and have an active session to SP
 User-Agent->>Service Provider: GET a protected resource that needs Extended User Attributes
 
 Service Provider->>Service Provider: assert needed Attributes belongs to a private scope
 Service Provider->>Service Provider: assert Attribute Authority to query
 Service Provider->>Service Provider: assert to have a user consent token to get attributes from AA -> it have not
 
 Service Provider->>User-Agent: Informational page that explain what happens: there'll be a request to a AA for these attributes
 User-Agent->>Service Provider: GET Confirmation
 Service Provider->>User-Agent: REDIRECT https://aa.spid.it/protected/resource/?idp=https://entityid.idp.spid.it&next=https://sp.spid.it/sp/return/attributes/resource 
 

 rect rgba(0, 0, 255, .1)
 User-Agent->>+AA: GET the protected resource
 AA->>User-Agent: An Informational page about what's happening
 AA-->>-User-Agent: HTTP-POST Binding Response (JS auto submit form) with an authn request to IdP
 end
 
 rect rgba(252, 226, 233.1)
 User-Agent->>+Identity Provider: POST the sp authn request
Note over User-Agent,Identity Provider: A SAML2 Authn occour between AA and IDP
 Identity Provider-->>-User-Agent: Response (JS auto submit form) Authn Response forged for the requesting Service Provider (AA)
 end

 User-Agent->>+AA: POST Authn Response
 AA-->>AA: Create a token for SP$USER
 AA-->>-User-Agent: HTTP-POST Binding Response (JS auto submit form) the Token to SP
 User-Agent->>+Service Provider: POST token (may additional security envelop can occour)
 Service Provider->>Service Provider: Store the token-user-sp
 Service Provider-->>-User-Agent: Redirect to resource
````
