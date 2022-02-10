# RH SSO/Keycloak OpenID Connect with Quarkus

This project shows how to use OpenID Connect to manage access your rest APIs.
We are using Keycloak / RH SSO as our OAuth 2.0/OpenID Connect server.

To run in `production`, you need to configure these properties:

1. `%prod.quarkus.oidc.auth-server-url` to point into your Keycloak server Realm.
2. `%prod.quarkus.oidc.client-id` with your client id.
3. `%prod.quarkus.oidc.credentials.secret` with your client credentials.

Note that `%prod` means it is used for production configuration.

You don't need to have a Keycloak server installed. Quarkus will deploy containerized
Keycloak instance when you run `mvn quarkus:dev` command. You can play with the Keycloak
through DevUI provided.

In this project, we provided realm configuration in `quarkus-realm.json` file. You can import
into your keycloak when creating new realm. There are 3 (three) users, `admin`, `alice` and `jdoe`.

To test your application, we need to retrieve access token for each user. We can test by using `curl`
command.
    > export access_token=$(\
    curl --insecure -X POST https://<KEYCLOAK_SERVER_URL>/auth/realms/quarkus/protocol/openid-connect/token \
    --user backend-service:secret \
    -H 'content-type: application/x-www-form-urlencoded' \
    -d 'username=alice&password=alice&grant_type=password' | jq --raw-output '.access_token' \
 )

 export admin_access_token=$(\
    curl --insecure -X POST https://<KEYCLOAK_SERVER_URL>/auth/realms/quarkus/protocol/openid-connect/token \
    --user backend-service:secret \
    -H 'content-type: application/x-www-form-urlencoded' \
    -d 'username=admin&password=admin&grant_type=password' | jq --raw-output '.access_token' \
 )

After retrieving the tokens, we can test our API access:
> curl -v -X GET \
  http://localhost:8080/api/users/me \
  -H "Authorization: Bearer "$access_token
 > curl -v -X GET \
   http://localhost:8080/api/admin \
   -H "Authorization: Bearer "$admin_access_token"

For every authorized access, you will received HTTP 200 OK with some data.
