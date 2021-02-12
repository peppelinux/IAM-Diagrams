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

# SAML2 SPID AA without Consent

````


````
