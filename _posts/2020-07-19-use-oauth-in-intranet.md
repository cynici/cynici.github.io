# Use OAuth in intranet

Say, you want to use Google as the identity provider
to authenticate user login to your web application which uses a
DNS name (or IP address) accessible only within your office network.

Besides cnofiguring your web application to use external authentication,
you will have to log on to Google Cloud Platform to add your application
as OAuth client and provide one or more "Authorized redirect URIs",
similar to the steps described here,
<https://fusionauth.io/docs/v1/tech/identity-providers/google>

It is actually fine to specify URI that cannot be resolved or reached
from the Internet - Google will not attempt to connect to it.
The URI is merely used by Google to redirect browsers back to the
application as part of the OAuth protocol.

