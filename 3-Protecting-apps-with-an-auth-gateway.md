## Protecting apps with an auth gateway

At some point, most deveopers wind up in a situation where they're tasked with bootstrapping some form of Identity and Access Management (IAM) a project. If you're working with legacy applications, or perhaps within an environment where the overall environment is secure, but one or more applications need to be protected for some reason, you have likely run through a handful of traditional solutions to lock down the app without explicitly having to alter the application and its configuration. 

Traefik strikes an interesting balance in the solution space in that it support explicit auth configurations through mechanisms such as MTLS and Basic auth, but also allows you to leverage existing external auth providers like an OIDC or token service. 

### Forcing OIDC login
In this example, we see how we can restrict access to our services by forcing users to authentication via [Dex](https://github.com/dexidp/dex), a lightweight [OIDC](https://openid.net/connect/) server capable of federated authentication to multiple third-party IDP.  

```
cd auth-gateway
# start up Treafik, Dex, a couple whoami services, and our auth forwarder
docker-compose up -d
```

When the services all come up, you can go to [http://thing1]() in your browser. You will be redirected to the Dex server to authenticate. Once authenticated, you will be taken back to the orignal thing1 service. In this example, the auth session is tied to the domain name, so if you browse to [http://thing2/](), you will be redirected to authenticate again. It's a simple change to the Docker Compose file to enable single sign-on, adjust auth lifetime, etc.


### Alternatives
One of the distinguishing features of Traefik is that it does not try to integrate IAM into the proxy. Variations of this example can be created using your preferred IDP and authentication mechanism, they will also exist as an external service called by Traefik, never as a part of Traefik itself. 

This distinction is one of the primary things to look at when you're evaluating different API managers, service meshes, and edge proxies. Look at where the technology sits in the networking stack, if and/or how it handles IAM, and what changes, if any, are needed in you app to use the technology.
  
### Authentication vs isolation

Notice that we did not publish the ports of thing1 and thing2 in our Docker Compose file. While the auth gateway we put in place provides reasonably good security, it's always best to take a layered approach to protect your app. Utilization of private networks, forcing HTTPS, enforcing white lists, protecting against DoS and CSS attacks, etc. all contribute to a stronger security posture and should be used in support of whatever auth solution you choose.

  
