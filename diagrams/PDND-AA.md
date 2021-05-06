sequenceDiagram
    Fruitore ->> PDND: Richiesta di Discovery Erogatore<br> per l'acquisizione di attributi qualificati<br>per tipologia
    PDND -->> Fruitore: Risposta con identificativo univoco<br>di uno o più soggetti Erogatori
    Fruitore ->> PDND: Richiesta acquisizione Attributi<br>del soggetto Erogatore destinatario<br>[schema di interoperabilità, contratto ...]
    PDND -->> Fruitore: Risposta con specifiche Erogatore
    Fruitore ->> Fruitore: Valutazione meccanismo di interazione:<br> 1. Diretto (Machine2Machine)<br>&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp2. Mediato dall'utente (autenticazione e consenso)
    Fruitore ->> Erogatore (AA): Richiesta di attributi qualificati secondo il modello di interoperabilità

sequenceDiagram
    User->>Fruitore: 1. HTTP Request<br>Risorsa accessibile con Attributi estesi
    Note over Fruitore,PDND: il meccanismo di Discovery può avvenire
    Fruitore->>AA: 2. HTTP Request<br>Attributi estesi
    AA-->>Fruitore: 3. HTTP Response 200 <br>Attributi estesi in formato JWT
    Fruitore -->>+User: 4. HTTP/1.1 200 <br>Pagina della risorsa protetta

sequenceDiagram
    User->>Fruitore: HTTP Request<br>Risorsa accessibile con Attributi estesi
    Fruitore ->> Fruitore: Valuta se già in possesso di una autorizzazione presso la AA <br>per il soggetto richiedente: Negativo.
    Fruitore ->> Fruitore: Valuta se già registrato come client presso la AA: Negativo
    Fruitore->>PDND: Richiesta di autorizzazione (voucher)
    PDND-->>Fruitore: Voucher di autorizzazione
    Fruitore->>AA: HTTP Request<br>PDND Voucher in HTTP Authorization Header<br>presso /registration endpoint 
    AA-->>Fruitore: Http Response di avvenuta registrazione
    Fruitore -->>User: HTTP/1.1 302<br>Location: https://aa.it/authorization?...
    User->>AA: Inoltro richiesta di Authorizzazione  https://aa.it/authorization?...

sequenceDiagram
    User->>Fruitore: 1. HTTP Request<br>Risorsa accessibile con Attributi estesi
    Note over Fruitore,PDND: il meccanismo di Discovery può avvenire
    Fruitore->>PDND: 2. Richiesta di autorizzazione (voucher)
    PDND-->>Fruitore: 3. Voucher di autorizzazione
    Fruitore->>AA: 4. HTTP Request<br>Attributi estesi<br>Http Header authorization con Voucher PDND
    AA->>AA: Validazione bearer token
    AA-->>Fruitore: 5. HTTP Response 200 Found<br>Attributi estesi in formato JWT
    Fruitore -->>+User: 6. HTTP/1.1 200 Found<br>Pagina della risorsa protetta

sequenceDiagram
    User->>Fruitore: 1. HTTP Request<br>Risorsa accessibile con Attributi estesi
    Fruitore->>PDND: 2. Richiesta di autorizzazione (voucher)
    PDND-->>Fruitore: 3. Voucher di autorizzazione
    Fruitore->>AA: 4. HTTP Request<br>Attributi estesi<br>HTTP Header Authorization con Voucher PDND
    AA->>AA: Validazione bearer token
    AA-->>Fruitore: 5. HTTP Response 200 Found<br>Attributi in formato JWT firmato
    Fruitore -->>+User: 6. HTTP/1.1 200 Found<br>Pagina della risorsa protetta
  
  sequenceDiagram
    User->>Fruitore: 1. HTTP Request<br>Risorsa accessibile con Attributi estesi
    Fruitore ->> Fruitore: Valuta se già in possesso di una autorizzazione presso la AA <br>per il soggetto richiedente: Negativo.
    Fruitore ->> Fruitore: Valuta se già registrato come client presso la AA: Negativo
    Fruitore->>PDND: Richiesta di autorizzazione (voucher)
    PDND-->>Fruitore: Voucher di autorizzazione
    Fruitore->>AA: 2. HTTP Request<br>PDND Voucher in HTTP Authorization Header<br>presso /registration endpoint 
    AA-->>Fruitore: Http Response di avvenuta registrazione
    Fruitore -->>User: HTTP/1.1 302<br>Location: https://aa.it/authorization?...
    User->>AA: Inoltro richiesta di autorizzazione presso: https://aa.it/authorization?...
