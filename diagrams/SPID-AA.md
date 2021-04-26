````
sequenceDiagram
    User->>SP: 1. HTTP Request<br>Risorsa accessibile con Attributi estesi
    Note over SP: Un JWT access_token<br> viene approvvigionato<br>secondo quanto definito in ModI
    SP->>AA: 2. HTTP Request<br>Attributi estesi<br>Http Header conforme al profilo di Authenticazione ModI
    AA-->>AA: Validazione bearer token
    AA-->>SP: 3. HTTP Response 200 <br>Attributi estesi in formato JWT o JSON
    SP -->>+User: 4. HTTP/1.1 200 Found<br>Pagina della risorsa protetta
````

````
sequenceDiagram
    User->>SP: 1. HTTP Request<br>Risorsa con <br>Attributi estesi
    SP->>AA: 2. HTTP Request<br>Attributi estesi
    AA-->>SP: 3. HTTP Response 403 <br>{error_description: "consent required"}
    SP->>SP: Create an Authz Request to AS
    SP -->>+User: 4. HTTP/1.1 302 Found<br>Location: https://aa.it/authorization?...
    User->>-AS: 5. Authorization request using https://aa.it/authorization?...
    AS->>AS: Processes Authorization request and <br>create an Authn Request to<br> a SPID IdP
    
    AS-->>User: 6. Authn Request to be forwarded to IdP
    User->>IdP: 7. Authn Request 
````


````
sequenceDiagram
    Note over AS,User: User selects the IdP or<br> a idp_hinting has been involved<br> by SP AuthnRequest
    AS->>AS: Processes Authorization request and <br>create an Authn Request to<br> a SPID IdP
    
    AS-->>User: 5c. Authn Request to be forwarded to IdP
    User->>IdP: Authn Request
    IdP-->>User: 5d. Login form
    User->>IdP: 5e. Credentials submission
    
    IdP-->>User: Show user consent page
    User->>IdP: Give consent

    IdP-->>User: 5f. Authn Response to be forwarded to AS
````

````
sequenceDiagram
    User-->AS: Sends Authn Response creted by IdP
    AS-->>User: 6a. Show user consent page
    User->>AS: 6b. Give consent
    AS-->AS: 6c. Processes and validate<br> the Request
    AS-->>User: 6d. HTTP Response 302 - URL with an authorization response
    User->>SP: Sends Authz response with code
````

````
sequenceDiagram
    SP->>AS: 7a. Token endpoint Request using authz code and private_key_jwt
    AS->>AS: 7b. Validate requests, requester and scopes
    AS-->>SP: 7c. Token Response <br>{"access_token": “...”,<br>"token_type": "Bearer",<br>"refresh_token": "...",<br>"expires_in": 3600,<br>"id_token": "..."}
````

````
sequenceDiagram
    User->>SP: 1. HTTP Request<br>Risorsa con <br>Attributi estesi
    SP->>AA: 2. HTTP Request<br>Attributi estesi
    AA-->>SP: 3. HTTP Response 403 <br>{error_description: "consent required"}
    SP->>SP: Create an Authz Request to AS
    SP -->>+User: 4a. HTTP/1.1 302 Found<br>Location: https://aa.it/authorization?...
    User->>-AS: 4b. Authorization request using https://aa.it/authorization?...
    AS->>AS: Processes Authorization request and <br>create an Authn Request to<br> a SPID IdP
````
