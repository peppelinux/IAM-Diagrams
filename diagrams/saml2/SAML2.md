# SAML2 simple
````
sequenceDiagram
    Utente->>SP: richiesta di accesso
    SP-->>Utente: richiesta per IDP
    Utente->>+IDP: inoltro richiesta di accesso
    IDP-->>Utente: pagina immissione credenziali
    Utente->>IDP: sottomissione credenziali e consenso

    IDP-->>-Utente: Autenticazione avvenuta: esito per SP
    Utente->>SP: inoltro esito

````


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
 Service Provider->>Service Provider: assert Attribute Authority to query (check Registry)
 Service Provider->>Service Provider: assert to have a user consent token to get attributes from AA -> it have not
 
 Service Provider->>User-Agent: Informational page that explain what happens: there'll be a request to a AA for these attributes
 User-Agent->>Service Provider: GET Confirmation
 Service Provider-->>User-Agent: REDIRECT https://aa.spid.it/protected/resource/?idp=https://entityid.idp.spid.it&next=https://sp.spid.it/sp/return/attributes/resource 
 

 rect rgba(0, 0, 255, .1)
 User-Agent->>+AA: GET the protected resource
 AA-->>User-Agent: Informational page about what's happening. A form with a hidden authn request from AA to IdP
 end
 
 rect rgba(252, 226, 233.1)
 User-Agent->>+Identity Provider: POST the SP (AA) authn request
Note over User-Agent,Identity Provider: A SAML2 Authn occour between AA and IDP - See SAML2 Authn Flow
 Identity Provider-->>-User-Agent: Response (JS auto submit form) Authn Response for the requesting Service Provider (AA)
 end

 User-Agent->>+AA: POST Authn Response
 AA-->>AA: Create a token for SP-USER
 AA-->>-User-Agent: HTTP-POST Binding Response (JS auto submit form) with the Token to SP
 User-Agent->>+Service Provider: POST token (may additional security envelop can occour)
 Service Provider->>Service Provider: Store the token-user-sp
 Service Provider-->>-User-Agent: Redirect to resource
````

SP to AA with Token Exchange

````
sequenceDiagram
 Note right of User-Agent: User have been authenticated to IdP and still have an active session to SP
 User-Agent->>Service Provider: GET a protected resource that needs Extended User Attributes
 
 Service Provider->>Service Provider: assert Attribute Authority to query
 Service Provider->>Service Provider: assert token possession (user consent)<br>to get attributes from AA <br>on behalf the user
 
 alt token is unavailable or expired
 Service Provider-->>User-Agent: Informational page<br>an AA is going to be involved<br>ask consent to the user
 User-Agent->>Service Provider: User Consent

 rect rgba(0, 0, 255, .1)
 Service Provider->>+AA: OAuth2 Client authz with PKCE
 AA-->>Service Provider: Response: OAuthn authz Code 
 Service Provider->>AA: Token exchange method
 AA->>AA: Store the token-user-sp
 AA-->>Service Provider: Access Token obtained
 end
 
 end
 
 Service Provider->>AA: Rest HTTP request for Attributes<br>with token
 AA-->>Service Provider: Response with Attributes
 Service Provider-->>User-Agent: Redirect to resource
````


Token exchange SAML2 - OAuth2
````
sequenceDiagram
 Note right of Service Provider: The Service Provider already have been registered to the Attribute Authority
 
 rect rgba(0, 0, 255, .1)
 Service Provider->>AA: Authz Request<br>HTTP POST to https://aa.example.org/oauth2/authorization<br>parameters<br>scope response_type state code_challenge code_challenge_method client_id
 AA->>Service Provider: Authz Granted<br>HTTP GET to https://sp.example.org/authz_code_endpoint/registered_path<br>code client_id scope state iss
 end
 
 rect rgba(226, 252, 229, .1)
 Service Provider->>AA: Token Exchance<br>HTTP POST with Basic Auth<br>https://aa.example.org/oauth2/token/exchange/saml2<br>-<br>client_id state code code_verifier grant_type <br> requested_token_type subject_token subject_token_type
 Note over AA: Optionally send a <br>consent request to <br> user
 AA->>AA: Stores the token<br>related to<br>SP, user, scope.
 AA-->>Service Provider: OK Status 200
 AA->>Service Provider: HTTP JSON Response with: <br>access_token issued_token_type token_type expires_in
 Service Provider-->>AA: OK Status 200
 end

 Service Provider->>Service Provider: Stores the token<br>related to<br>AA, user, scope.
````


Big Picture
````
sequenceDiagram
    IDP->>SP: SAML2 Response<br>succesfull Status
    
    rect rgba(255, 219, 167, .1)
    SP->>User: Ask consent
    User-->>SP: Gave Consent
    end

    rect rgba(167, 255, 167, .1)
    SP->>STS: Authorization Request to exchange <br>a SAML2 Bearer Token <br>with an access token <br>for other scopes
    STS-->>SP: Authorization granted
    SP->>STS: Request to exchange the SAML2 bearer token 
    STS-->>SP: Access Token
    end

    SP->>RS: Request resources with Access Token
````
