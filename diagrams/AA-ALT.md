SPID SAML2 semplice

````
sequenceDiagram
    Utente->>SP: richiesta di accesso
    SP-->>Utente: pagina selezione IdP
    Utente->>SP: selezione IdP
    SP->>SP: creazione authn request SAML2
    SP-->>Utente: richiesta di Autenticazione per IdP
    Utente->>+IDP: inoltro richiesta di autenticazione
    IDP-->>Utente: pagina immissione credenziali
    Utente->>IDP: sottomissione credenziali
    IDP-->>Utente: pagina consenso al rilascio degli attributi
    Utente->>IDP: consenso

    IDP-->>-Utente: autenticazione avvenuta: SAML2 response per SP
    Utente->>SP: SAML2 response
    SP-->>Utente: accesso consentito
 ````
 
 SAML2 AA IPZS
 
 ````
 sequenceDiagram
 Note right of User-Agent: utente già autenticato presso il SP
 User-Agent->>Service Provider: richiesta accesso ad area protetta da attributi estesi
 
 Service Provider->>User-Agent: pagina informativa per l'interrogazione di una AA 
 User-Agent->>Service Provider: consenso
 Service Provider->>Service Provider: creazione SAML2 authn request
 Service Provider-->>User-Agent: Inoltro SAML2 Authn request
 
 User-Agent->>AA: SAML2 Authn request
 AA->>AA: validazione della richiesta
 AA-->>User-Agent: creazione SAML2 Authn request

 User-Agent->>Identity Provider: inoltro della authn request della AA
Note over User-Agent,Identity Provider: credenziali e pagina del consenso al rilascio degli attributi
 Identity Provider-->>User-Agent: SAML2 Response

 User-Agent->>AA: inoltro SAML2 Response IDP
 Note over User-Agent,AA: pagina del consenso al rilascio degli attributi

 AA->>AA: validazione
 AA-->>User-Agent: SAML2 Response AA
 User-Agent->>Service Provider: SAML2 Response
 Service Provider->>User-Agent: accesso alla area protetta
 ````
 
 SAML2 AA semplificato
 
 ````
 sequenceDiagram
 Note right of User-Agent: utente già autenticato presso il SP
 User-Agent->>Service Provider: richiesta accesso ad area protetta da attributi estesi
 
 Service Provider->>User-Agent: pagina informativa per l'interrogazione di una AA 
 User-Agent->>Service Provider: consenso
 Service Provider->>Service Provider: creazione SAML2 authn request
 Service Provider-->>User-Agent: Inoltro SAML2 Authn request
 
 User-Agent->>AA: SAML2 Authn request
 AA->>AA: validazione della richiesta
 AA-->>User-Agent: creazione SAML2 Authn request

 User-Agent->>Identity Provider: inoltro della authn request della AA
Note over User-Agent,Identity Provider: credenziali e pagina del consenso al rilascio degli attributi
 Identity Provider-->>User-Agent: SAML2 Response

 User-Agent->>AA: inoltro SAML2 Response IDP
 Note over User-Agent,AA: pagina del consenso al rilascio degli attributi

 AA->>AA: validazione
 AA-->>User-Agent: SAML2 Response AA
 User-Agent->>Service Provider: SAML2 Response
 Service Provider->>User-Agent: accesso alla area protetta
 ````
