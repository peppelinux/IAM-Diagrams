
https://mermaid-js.github.io/mermaid-live-editor/

````
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
````


https://textart.io/sequence
````
object user rp op authority
user->rp: Request to a protected resource
rp->user: Discovery Page
user->rp: Select the desidered Identity Provider op.umu.se
rp->op: Federation Entity Configuration Request to https://op.umu.se/.well-known/openid-federation
op->rp: Federation Entity Configuration Response
rp->rp: Response Validation Route to authority_hint<brhttps://umu.se
rp->authority: Federation Entity Configuration Request https://umu.se/.well-known/openid-federation
authority->rp: Federation Entity Configuration Response Content-Type: application/jose
rp->rp: Response Validation
rp->authority: Request to Authority about its descendant /oidc/fedapi?sub=https%3A%2F%2Fop.umu.se&iss=https%3A%2F%2Fumu.se
rp->rp: Validate Response Acquire policy
rp->rp: Trust chain validation
rp->user: HTTP 302  https://op.umu.se/authorize? request=eyJhbG[...]
user->op: Automatic Client Registration Request


````


[SPID RP to OP](https://mermaid-js.github.io/mermaid-live-editor/edit#eyJjb2RlIjoic2VxdWVuY2VEaWFncmFtXG4gIFVzZXIgLT4-IFNQSUQvQ0lFIFJQOiBSZXF1ZXN0IHRvIGEgcHJvdGVjdGVkIHJlc291cmNlXG4gIFNQSUQvQ0lFIFJQIC0tPj4gVXNlcjogRGlzY292ZXJ5IFBhZ2VcbiAgVXNlciAtPj4gU1BJRC9DSUUgUlA6IFNlbGVjdCB0aGUgZGVzaWRlcmVkIElkZW50aXR5IFByb3ZpZGVyIG9wLnNwaWQuaXRcbiAgU1BJRC9DSUUgUlAgLT4-IFNQSUQvQ0lFIChPUCk6IEZlZGVyYXRpb24gRW50aXR5IENvbmZpZ3VyYXRpb24gUmVxdWVzdDxicj5odHRwczovL29wLnNwaWQuaXQvLndlbGwta25vd24vb3BlbmlkLWZlZGVyYXRpb25cbiAgU1BJRC9DSUUgKE9QKSAtLT4-IFNQSUQvQ0lFIFJQOiBGZWRlcmF0aW9uIEVudGl0eSBDb25maWd1cmF0aW9uIFJlc3BvbnNlPGJyPkNvbnRlbnQtVHlwZTogYXBwbGljYXRpb24vam9zZVxuICBTUElEL0NJRSBSUCAtPj4gU1BJRC9DSUUgUlA6IFJlc3BvbnNlIFZhbGlkYXRpb248YnI-Um91dGUgdG8gYXV0aG9yaXR5X2hpbnQ8YnI-IGh0dHBzOi8vcmVnaXN0cnkuc3BpZC5pdFxuXG4gIFNQSUQvQ0lFIFJQIC0-PiBTUElEL0NJRSBGZWQgQXV0aDogRmVkZXJhdGlvbiBFbnRpdHkgQ29uZmlndXJhdGlvbiBSZXF1ZXN0PGJyPmh0dHBzOi8vcmVnaXN0cnkuc3BpZC5pdC8ud2VsbC1rbm93bi9vcGVuaWQtZmVkZXJhdGlvblxuICBTUElEL0NJRSBGZWQgQXV0aCAtLT4-IFNQSUQvQ0lFIFJQOiBGZWRlcmF0aW9uIEVudGl0eSBDb25maWd1cmF0aW9uIFJlc3BvbnNlPGJyPkNvbnRlbnQtVHlwZTogYXBwbGljYXRpb24vam9zZVxuICBTUElEL0NJRSBSUCAtPj4gU1BJRC9DSUUgUlA6IFJlc3BvbnNlIFZhbGlkYXRpb25cbiAgU1BJRC9DSUUgUlAgLT4-IFNQSUQvQ0lFIEZlZCBBdXRoOiBSZXF1ZXN0IHRvIEF1dGhvcml0eSBhYm91dCBpdHMgZGVzY2VuZGFudDxicj4vb2lkYy9mZWRhcGk_c3ViPWh0dHBzJTNBJTJGJTJGb3Auc3BpZC5pdFxuICBTUElEL0NJRSBSUCAtPj4gU1BJRC9DSUUgUlA6IFZhbGlkYXRlIFJlc3BvbnNlPGJyPkFjcXVpcmUgcG9saWN5XG5cbiAgU1BJRC9DSUUgUlAgLT4-IFNQSUQvQ0lFIFJQOiBUcnVzdCBjaGFpbiB2YWxpZGF0aW9uXG4gIFNQSUQvQ0lFIFJQIC0tPj4gVXNlcjogSFRUUCAzMDIgPGJyPmh0dHBzOi8vb3Auc3BpZC5pdC9hdXRob3JpemU_PGJyPnJlcXVlc3Q9ZXlKaGJHWy4uLl1yck9TZ3c8YnI-JnJlc3BvbnNlX3R5cGU9Y29kZTxicj4mY2xpZW50X2lkPWh0dHBzJTNBJTJGJTJGcnAuc3BpZC5pdDxicj4mcmVkaXJlY3RfdXJpPWh0dHBzJTNBJTJGJTJGcnAuc3BpZC5pdC9vcGVuaWQvY2FsbGJhY2s8YnI-JnNjb3BlPW9wZW5pZFxuICBVc2VyIC0-PiBTUElEL0NJRSAoT1ApOiBBdXRvbWF0aWMgQ2xpZW50IFJlZ2lzdHJhdGlvbiBSZXF1ZXN0IiwibWVybWFpZCI6IntcbiAgXCJ0aGVtZVwiOiBcImRlZmF1bHRcIlxufSIsInVwZGF0ZUVkaXRvciI6ZmFsc2UsImF1dG9TeW5jIjp0cnVlLCJ1cGRhdGVEaWFncmFtIjpmYWxzZX0)

````
sequenceDiagram
  User ->> SPID/CIE RP: Request to a protected resource
  SPID/CIE RP -->> User: Discovery Page
  User ->> SPID/CIE RP: Select the desidered Identity Provider op.spid.it
  SPID/CIE RP ->> SPID/CIE (OP): Federation Entity Configuration Request<br>https://op.spid.it/.well-known/openid-federation
  SPID/CIE (OP) -->> SPID/CIE RP: Federation Entity Configuration Response<br>Content-Type: application/jose
  SPID/CIE RP ->> SPID/CIE RP: Response Validation<br>Route to authority_hint<br> https://registry.spid.it

  SPID/CIE RP ->> SPID/CIE Fed Auth: Federation Entity Configuration Request<br>https://registry.spid.it/.well-known/openid-federation
  SPID/CIE Fed Auth -->> SPID/CIE RP: Federation Entity Configuration Response<br>Content-Type: application/jose
  SPID/CIE RP ->> SPID/CIE RP: Response Validation
  SPID/CIE RP ->> SPID/CIE Fed Auth: Request to Authority about its descendant<br>/oidc/fedapi?sub=https%3A%2F%2Fop.spid.it
  SPID/CIE RP ->> SPID/CIE RP: Validate Response<br>Acquire policy

  SPID/CIE RP ->> SPID/CIE RP: Trust chain validation
  SPID/CIE RP -->> User: HTTP 302 <br>https://op.spid.it/authorize?<br>request=eyJhbG[...]rrOSgw<br>&response_type=code<br>&client_id=https%3A%2F%2Frp.spid.it<br>&redirect_uri=https%3A%2F%2Frp.spid.it/openid/callback<br>&scope=openid
  User ->> SPID/CIE (OP): Automatic Client Registration Request
````
