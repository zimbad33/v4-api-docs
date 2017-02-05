Overview
========

The purpose of this document is to provide detailed descriptions for RewardOps API calls, parameters and responses. These can be tested using our [interactive API console](https://pangea-int.rewardops.net/api_docs/console).

Introduction
============

Introduction
============

## Security

### HTTPS

All requests must be performed using SSL via the HTTPS protocol.

### OAuth

RewardOps uses [OAuth 2.0](http://oauth.net/2/) with the Client Credentials grant type. This grant type requires you to use your `client_id` and `client_secret` to create a temporary access token, which is sent with subsequent API requests.

#### Generating an access token

To get an access token, send a POST request to `https://app.rewardops.net/api/v4/auth/token`. The request must have an `Authorization` field in the header. The value of the field must be `Basic $ENCODED_CREDENTIALS`, where `$ENCODED_CREDENTIALS` is your `client_id` and `client_secret`, separated by a colon (`client_id:client_secret`) and base64 encoded.

The JSON response will contain an `access_token` that you can use in subsequent requests. Tokens are valid for two hours. When your token has expired, you'll need to request another token before making further API calls. The API server will respond with an error when you make a request using an expired or otherwise invalid token.

##### Example

```nginx
# Base64-encode the client_id and client_secret, separated by a colon. Then use the base64 string in the Authorization header.

echo -n "CLIENT_ID:CLIENT_SECRET" | openssl enc -base64 -A | { read ENCODED_CREDENTIALS; curl https://demo.rewardops.net/api/v4/auth/token --header "Authorization: Basic $ENCODED_CREDENTIALS" --data "grant_type=client_credentials" --verbose; }

# Response:

{"access_token":"ac7523d4011492d83bfa1ecd5f62ec3c3a1962e1fee84fa53305fc7c75d29c8b","token_type":"bearer","expires_in":7200,"created_at":1428705205}
```

#### Using access tokens

Once you've generated an access token, you can use it to make requests to the API. All API requests must have an `Authorization` field in the header. The value of the field must be `Bearer $ACCESS_TOKEN`, where `$ACCESS_TOKEN` is the valid access token that you created.

## Versioning

Different versions of the API have different base URLs. The base URL for API v4 is `https://app.rewardops.net/api/v4`.

## Request headers

| Headers |   |
|---------|---|
| **Authorization** | Must contain `Bearer $ACCESS_TOKEN`, where `$ACCESS_TOKEN` is a valid access token |
| **Accept** | Specify the desired response format [optional [application/json]] |
| **Content-Type** | Specify the format of the data payload [optional [application/json]] |
