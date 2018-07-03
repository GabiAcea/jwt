## Nodejs JWT in 10 minutes

Full tutorial:
https://www.codementor.io/olatundegaruba/5-steps-to-authenticating-node-js-with-jwt-7ahb5dmyr

clone the project

### Installations
* npm install

### Run

* node server.js


Some info gathered

To notice:

     The latter part is achieved by using an algorithm that generates the signature of the token using the first 2 parts and by providing a secret key. If the data in the token is modified in any way, the signature won't match. Hence the validation of the token fails.
     It's a token based authentication system.
    JWTs are not restricted to present session-like information about the authenticated user itself; they can also be used to delegate access to clients that act on behalf of the user
     Nowadays we have different  requirements, i.e. hybrid application or single page application contacting multiple backends (split up into separate micro-service authetication servers, databases, image processing servers, etc). In these types of scenarios, the session cookie we get from one server won't correspond to another server. JWT to the rescue.

Pros:

    No Session to Manage (stateless): The JWT is a self contained token which has authetication  information, expire time information, and other user defined claims digitally signed.
    Portable: A single token can be used with multiple backends. ????? dezvolta, da exemple
    No Cookies Required, So It's Very Mobile Friendly ????? dezvolta partea cu mobile
    Good Performance: It reduces the network round trip time. ?????????????
    Decoupled/Decentralized: The token can be generated anywhere. Authentication can happen on the resource server, or easily separated into its own server.
    No Database Table : This implies fewer DB queries, which implies faster response time. In case you are using paid services  that charge per query basis, JWT might reduce the costs marginally.
    But these can be resolved using tools like Redis in case of sessions
    Used across services : You can have one authorization server that deals with the Login/Registration and generates the token, all the subsequent requests will need not have to go to the authorization server as the only the Auth-server will have have the private key, and rest of the severs will have the public-key to verify the signature.  ?????????????????


Cons:

    Compromised Secret Key : The best and the worst thing about JWT is that it relies on just one Key. Consider that the Key is leaked by a careless or a rogue developer/administrator, the whole system is compromised! The only way to recover from this point is to generate a new Key(Key-pair) that will be used across systems here on. This would me all the existing client tokens are invalidated and each user would have to login again. Image one day 100% of Facebook users will be logged out.
    Crypto-algo can be deprecated: JWT relies completely on the Signing algorithm. Now, though it is not frequent, but in the past many Encryption/Signing algorithms have been deprecated. 
    This article shows how you can crack the Wifi password of a WEP Encrypted Wifi which was the most common type of encryption not more than a year ago. The hack was based on the weakness of the crypto algorithm. So, in case of JWT, if such a thing happens, yet again, every user on the platform will have to login again.
    Yet again one will have to wait till all the JWT libraries update with the latest crypto-algo.
    Data Overhead : The size of the JWT token will be more than that of a normal Session token. The more data you add in the JWT token, the longer it gets linearly. Remember, each request needs the token in it for request verification. So say, a 1 KB JWT token implies each request will have 1KB over-head upload which is really bad in cases of low speed net connectivity.  ???? ce date intra in JWT pe langa key, expiration date si user-id
    Complicated to understand: JWT uses cryptographic Signature algorithms to verify the data and get the user-id from the token. Understanding the Signing Algo in itself requires basics of cryptography. So, in case if the developer is not completely educated s/he might introduce security loopholes in the system. 


Security issues and fixes:

    The main problem with Web Storage — and the reason why I decided not to use it — is Cross-Site Scripting Attacks (XSS). Since Web Storage is accessible through Javascript, this exposes us. If we store the JWT using HTTP cookies with the HttpOnly cookie flag, then Javascript never has access to the JWT, thus mitigating XSS attacks. If we combine it with the Secure cookie flag, then the JWT will only be sent through HTTPS, protecting it against Man-in-the-middle attacks. ========??? exemplu de cookie ===========

    However, there is one big threat for HTTP cookies: Cross-site request forgery (CSRF). Since cookies are sent by the browser by default, an attacker might trick a user into clicking on a malicious link and submitting an authenticated request to our app.

    The good news is that CSRF attacks can be mitigated with a very simple solution: requiring all requests to include a token, usually called a "CSRF token". The token can be sent to the client on login, and then required as a header on every future request.

    Conclusion: Even though both Web Storage and HTTP cookies can be compromised through different attacks, CSRF attacks are easier to mitigate than XSS attacks, so I decided to go with HTTP cookies for web clients.

    Since mobile clients do not suffer from XSS attacks, on the mobile clients I opted for local storage.

    Bonus: you can include a copy of the CSRF token as part of the JWT claims. This enables CSRF validation on your server to be stateless.

    Dealing with compromised JWTs

    There are 2 common scenarios in which a JWT might be compromised:

        An authenticated device (like a laptop or phone) is lost or stolen.
        In this case, we should be able to revoke all tokens for a user, i.e. log them out from all devices.

        The JWT is stolen: either through a Man-in-the-middle attack, a memory leak, or some other attack.
        This is a trickier case, but we can mitigate it by detecting irregularities in the client requests. For example, if a client makes requests from different IP addresses or sends conflicting user-agents, we might want to revoke the JWT and require re-authorization.
        read more
