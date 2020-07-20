# Use OAuth in intranet

Scenario:, you want to use Google as the identity provider
(authentication source) to authenticate user login to your
web application by a DNS name (or IP address) routable only
within your office network.

Besides configuring your web application to use external authentication,
you will have to log on to Google Cloud Platform (GCP) to add your application
as OAuth client and provide one or more "Authorized redirect URIs",
similar to the steps described here,
<https://fusionauth.io/docs/v1/tech/identity-providers/google>

It is actually fine to specify URI of your web application that
cannot be resolved or reached from the Internet - Google will not
initiate connection to it.
The URI is merely used by Google to redirect browsers back to the
application as part of the OAuth protocol.

[<img width="90%" src="https://dba-presents.com/images/mythoughts/OAuth2/OAuth2_algorithm.png">](https://dba-presents.com/index.php/other/my-thoughts/125-what-is-openid-oauth2-and-google-sign-in)


In summary, the networking prerequisites are as follows.
The same requirements apply for other OAuth identity provider

- browser needs to connect to application server, by DNS or IP, and get response
- browser needs to connect to GCP and get response
- application server needs to connect to GCP and get response

