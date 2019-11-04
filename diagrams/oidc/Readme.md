# Render with https://www.websequencediagrams.com

  title OIDC select the Provider via Discovery Service
 # v1.0a

 User-Agent->Relying-Party\n(Service Provider): GET /login
 Relying-Party\n(Service Provider)-->User-Agent: Http Response Redirect (302)\n https://ds.fqdn.org/disco?entityID=rp.fqdn&return=https://rp.fqdn.org/disco
 User-Agent->Discovery Service \n(Select Identity Provider): GET /disco?entityID=rp.fqdn&return=https://rp.fqdn.org/disco
 Discovery Service \n(Select Identity Provider)-->User-Agent: Http Response /disco?entityID=rp.fqdn&return=https://rp.fqdn.org/disco
  note right of User-Agent: Select from a list of Providers reachable by rp.fqdn.org
 User-Agent->Relying-Party\n(Service Provider): GET /disco?entityID=op.fqdn


############################################


  title OIDC select the Provider via RP Internal resource (Wayf)
 # v1.0a

 User-Agent->Relying-Party\n(Service Provider): GET /login
 Relying-Party\n(Service Provider)-->User-Agent: Http Response
   note right of User-Agent: Select from a list of Providers reachable by rp.fqdn.org
 User-Agent->Relying-Party\n(Service Provider): GET /rp?uid=&iss=https://op.fqdn.org


############################################
  title OIDC Provider Provider Discovery
 # v1.0a

 Relying-Party\n(Service Provider)->OpenId Provider: GET https://op.fqdn.org/.well-known/openid-configuration
 OpenId Provider-->Relying-Party\n(Service Provider): Response {JSON metadata}
  note right of Relying-Party\n(Service Provider): Fetch OP JWKs from its 'jwk_uris'
 Relying-Party\n(Service Provider)->OpenId Provider: GET https://op.fqdn.org/static/jwks.json
 OpenId Provider-->Relying-Party\n(Service Provider): Response JWKs
 Relying-Party\n(Service Provider)->Relying-Party\n(Service Provider): Store OP JWK Keypair


############################################
  
  title OIDC Dynamic Registration Endpoint
 # v1.0a

 Relying-Party\n(Service Provider)->+OpenId Provider: POST /registration\ndata:{\n"redirect_uris": ["https://rp.fqdn.org/authz_cb/djop"],\n"application_type": "web",\n "token_endpoint_auth_method": "client_secret_basic",\n "grant_types": ["authorization_code"],\n "post_logout_redirect_uris": ["https://rp.fqdn.org"],\n "contacts": ["ops@rp.fqdn.org"],\n "response_types": ["code"],\n "jwks_uri": "https://rp.fqdn.org/static/jwks.json"}
 OpenId Provider-->Relying-Party\n(Service Provider): GET fetch keys from https://rp.fqdn.org/static/jwks.json
 Relying-Party\n(Service Provider)->OpenId Provider: Response {"keys":\n [{"kty": "RSA","use": "sig","kid": "[...]","n": "[...]", "e": "AQAB"},\n {"kty": "EC","use": "sig", "kid": "kid": "[...]", "crv": "P-256", "x": "[...]", "y": "[...]"}]}
 OpenId Provider->OpenId Provider: Build RP KeyBundle and store session (cookie)
 OpenId Provider-->-Relying-Party\n(Service Provider): Response {"grant_types": ["authorization_code"],\n "post_logout_redirect_uris": ["https://rp.fqdn.org"],\n "jwks_uri": "https://rp.fqdn.org/static/jwks.json",\n "contacts": ["ops@example.com"],\n "registration_client_uri": "registration?client_id=ISFDRFTWpcZg",\n "client_id": "ISFDRFTWpcZg",\n "application_type": "web",\n "token_endpoint_auth_method": "client_secret_basic",\n "client_id_issued_at": 1572511631,\n "client_secret_expires_at": 1572943631,\n "registration_access_token": "IdcdmVmihUfRkNfXsC6OaNaKoXzgk8Xx",\n "response_types": ["code"],\n "redirect_uris": ["https://rp.fqdn.org/authz_cb/djop"],\n"client_secret": "bd2bfaea4b08c70d873ec0776454fc68a6231105e642baf7ee1a2cb1"}
 Relying-Party\n(Service Provider)->Relying-Party\n(Service Provider): Validate authorization code\n with OP Keybundle


############################################



  title OIDC Authorization and Authentication
 # v1.0a
 User-Agent<->Relying-Party\n(Service Provider): Redirect (302): GET https://op.fqdn.org/authorization?\nscope=openid+profile+email+address+phone&\nresponse_type=code&redirect_uri=https%3A%2F%2Frp.fqdn.org%2Fauthz_cb%2Fdjop&\nnonce=VheGmPQTsIunXiZSJUBuij7k&\nclient_id=ISFDRFTWpcZg&\nstate=sjX33fkHGIRLi8g5BFGmOb7B3YM4i1G3 Relying-Party\n(Service Provider)->OpenId Provider: GET https://op.fqdn.org/.well-known/openid-configuration
 User-Agent->OpenId Provider: GET the previous url
 OpenId Provider->+OpenId Provider: Parse request
 
 OpenId Provider-->User-Agent: Response: Authentication HTML Form
 User-Agent->OpenId Provider: POST username, password and token
 OpenId Provider->-OpenId Provider: Authenticate the user

 OpenId Provider-->User-Agent: Redirect (302): https://rp.fqdn.org/authz_cb/djop?\nstate=sjX33fkHGIRLi8[...]&\ncode=R111RTB5UW9x[...]&\nsession_state=433769f923[...].zujddLIZnA1NKWux&\nclient_id=ISFDRFTWpcZg&\nscope=openid+profile+email+address+phone&\niss=https%3A%2F%2Fop.fqdn.org'
  note right of User-Agent: Authorization granted to that scopes


############################################


  title OIDC Id Token
 # v1.0a
 User-Agent->+Relying-Party\n(Service Provider): GET https://rp.fqdn.org/authz_cb/djop?\nstate=sjX33fkHGIRLi8[...]&\ncode=R111RTB5UW9x[...]&\nsession_state=433769f923[...].zujddLIZnA1NKWux&\nclient_id=ISFDRFTWpcZg&\nscope=openid+profile+email+address+phone&\niss=https%3A%2F%2Fop.fqdn.org'
 Relying-Party\n(Service Provider)->Relying-Party\n(Service Provider): Verify response with {'client_id': 'ISFDRFTWpcZg',\n 'skew': 15,\n 'verify': True,\n 'keyjar': <KeyJar(issuers=['', 'https://op.fqdn.org'])>,\n 'iss': 'https://op.fqdn.org'}
 
 Relying-Party\n(Service Provider)->OpenId Provider: POST to https://op.fqdn.org/token: \n'headers': {\n'Content-Type': 'application/x-www-form-urlencoded',\n'Authorization': 'Basic SVNGRFJGVFdwY1pnOmJkMmJm[...]'\nData: code=Z0FBQUFBQmR1[...]%3D,\nstate=sjX33fkHGIRLi[...]\ngrant_type=authorization_code,\nredirect_uri=https://rp.fqdn.org/Fauthz_cb/Fdjop,\nclient_id=ISFDRFTWpcZg
  OpenId Provider->OpenId Provider: Validate request
 
 OpenId Provider-->Relying-Party\n(Service Provider): Returns the Id Token encoded in JWT format\n{'auth_time': 1572511651,\n 'sub': '8032[...]',\n'iss': 'https://op.fqdn.org',\n'email': 'peppe@ciao.it',\n'iat': 1572511662,\n'nonce': 'VheGmPQTsIunXiZSJUBuij7k',\n'acr': 'user_authn_context.INTERNETPROTOCOLPASSWORD',\n'kid': 'bWtoRz[...]',\n'aud': ['ISFDRFTWpcZg'],\n'exp': 1572511962}\n
  Relying-Party\n(Service Provider)->Relying-Party\n(Service Provider): Signature verification
 
 Relying-Party\n(Service Provider)->OpenId Provider: GET https://op.fqdn.org/userinfo\nHeaders: Authorization': 'Bearer eyJraWQiOiJUC[...]'}
  OpenId Provider->OpenId Provider: Validate request
 OpenId Provider-->Relying-Party\n(Service Provider): Response {"family_name": "dm",\n "given_name": "peppe",\n "phone_number": null,\n "email": "peppe@ciao.it",\n "sub": "8032[...]"}'}
  Relying-Party\n(Service Provider)->Relying-Party\n(Service Provider): Signature verification
 
 Relying-Party\n(Service Provider)-->-User-Agent: Http Redirect (302): URL https://rp.fqdn.org/authz_cb/djop?state=sjX33fkHGI[...]&\nsession_state=4337[...].zujddLIZnA1NKWux&\nclient_id=ISFDRFTWpcZg&\nscope=openid+profile+email+address+phone&\niss=https%3A%2F%2Fop.fqdn.org
 User-Agent->Relying-Party\n(Service Provider): GET the preivous url
 Relying-Party\n(Service Provider)-->User-Agent: Page showing Userinfo data


