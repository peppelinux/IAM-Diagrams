# https://textart.io/sequence
# https://mermaid-js.github.io/mermaid-live-editor/

sequenceDiagram
  user ->> wiki.ligo.org (RP): Request to a protected resource
  wiki.ligo.org (RP) -->> user: Discovery Page
  user ->> wiki.ligo.org (RP): Select the desidered Identity Provider op.umu.se
  wiki.ligo.org (RP) ->> op.umu.se (OP): Federation Entity Configuration Request<br>https://op.umu.se/.well-known/openid-federation
  op.umu.se (OP) -->> wiki.ligo.org (RP): Federation Entity Configuration Response<br>Content-Type: application/jose
  wiki.ligo.org (RP) ->> wiki.ligo.org (RP): Response Validation<br>Route to authority_hint<br> https://umu.se
  
  loop until authority_hint != iss
    wiki.ligo.org (RP) ->> umu.se (authority): Federation Entity Configuration Request<br>https://umu.se/.well-known/openid-federation
    umu.se (authority) -->> wiki.ligo.org (RP): Federation Entity Configuration Response<br>Content-Type: application/jose
    wiki.ligo.org (RP) ->> wiki.ligo.org (RP): Response Validation
    wiki.ligo.org (RP) ->> umu.se (authority): Request to Authority about its descendant<br>/oidc/fedapi?sub=https%3A%2F%2Fop.umu.se&iss=https%3A%2F%2Fumu.se
    wiki.ligo.org (RP) ->> wiki.ligo.org (RP): Validate Response<br>Acquire policy
  end

  wiki.ligo.org (RP) ->> wiki.ligo.org (RP): Trust chain validation
  wiki.ligo.org (RP) -->> user: HTTP 302 <br>https://op.umu.se/authorize?<br>request=eyJhbG[...]rrOSgw<br>&response_type=code<br>&client_id=https%3A%2F%2Fwiki.ligo.org<br>&redirect_uri=https%3A%2F%2Fwiki.ligo.org/openid/callback<br>&scope=openid+profile+email<br>
  user ->> op.umu.se (OP): Automatic Client Registration Request
