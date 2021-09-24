# Getting Started

## API Authentication

- All requests must be made using TLS (https).
- All requests are authenticated using a [Basic Auth](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Authorization) Authorization header.
- Your Organization ID and API Secret will serve as your username and password fields respectively when creating your Basic Auth Header.
- All `Production` requests should use `https://app.current.tech/api/v2` as the host while all `stage/sandbox` requests should use `https://stage-app.current.tech/api/v2`
- All requests and responses should include an `application/json` content-type header.

- We have a Postman collection you can download <a href="https://cdn.current.tech/deleloper-resources/api-resources/Current_API.postman_collection" download="Current API.json" target="_blank">here</a>.

## Standard Responses Examples

### Successful Response

```json
{
    "success": true,
    "message": "User Created Successfully",
    "payload": {
        "id": "45a6cdf6-2f27-4d2e-b640-35046d9a4982"
    }
}
```

*See specific routes for payload fields

### Error Response

```json
{
    "success": false,
    "message": "Missing Authorization Header",
    "payload": null
}
```

<details>
<summary><code><b>Common Error Responses</b></code></summary>

> | http code | http status message     | response                                                                                                                                                                                           |
> |-----------|-------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
> | `400`     | `BAD REQUEST`           | `{`<br />&nbsp;&nbsp;&nbsp;&nbsp;`"success":false,`<br />&nbsp;&nbsp;&nbsp;&nbsp;`"message": { custom_message },`<br />&nbsp;&nbsp;&nbsp;&nbsp;`"payload":null`<br />`}`                |
> | `401`     | `UNAUTHORIZED`          | `{`<br />&nbsp;&nbsp;&nbsp;&nbsp;`"success":false,`<br />&nbsp;&nbsp;&nbsp;&nbsp;`"message":"Could not verify credentials",`<br />&nbsp;&nbsp;&nbsp;&nbsp;`"payload":null`<br />`}`                |
> | `403`     | `FORBIDDEN`             | `{`<br />&nbsp;&nbsp;&nbsp;&nbsp;`"success":false,`<br />&nbsp;&nbsp;&nbsp;&nbsp;`"message":"You do not have access to this resource",`<br />&nbsp;&nbsp;&nbsp;&nbsp;`"payload":null`<br />`}`     |
> | `404`     | `NOT FOUND`             | `{`<br />&nbsp;&nbsp;&nbsp;&nbsp;`"success":false,`<br />&nbsp;&nbsp;&nbsp;&nbsp;`"message":"Resource not found",`<br />&nbsp;&nbsp;&nbsp;&nbsp;`"payload":null`<br />`}`                          |
> | `500`     | `INTERNAL SERVER ERROR` | `{`<br />&nbsp;&nbsp;&nbsp;&nbsp;`"success":false,`<br />&nbsp;&nbsp;&nbsp;&nbsp;`"message":"Uncaught Server Error",`<br />&nbsp;&nbsp;&nbsp;&nbsp;`"payload":null`<br />`}`                       |

</details>

## ROUTES

### /auth - User Authentication

<details>
 <summary><code>GET</code> <code><b>/login/{user_id}</b></code> <code>(NOTE: Only use if you are handling user authentication on your own server. Creates a session and redirect URL for specified user_id)</code></summary>

#### Parameters

##### URL Parameters

> | name       |  type      | data type     | description                                                           |
> |------------|------------|---------------|-----------------------------------------------------------------------|
> | `user_id`  | `required` | `string`      | `the user id returned from /signup auth api request`                  |

##### Data Parameters

> None

#### Responses

> | http code | http status message | content-type                      | response                                                                                             |
> |-----------|---------------------|-----------------------------------|------------------------------------------------------------------------------------------------------|
> | `200`     | `OK`           | `application/json`                | `"id":"{ user_id }","redirect_url":"{ app_host }/dashboard?__csst={ unique_token }","approved":boolean}` |

*Standard error responses are used for most common errors.

#### Example cURL

> ```bash
>  curl --request GET \
>  --url https://{app | stage-app}.current.tech/api/v2/auth/login/{ user_id } \
>  --header 'Accept: application/json' \
>  --header 'Content-Type: application/json' \
>  --header 'Authorization: Basic {{ Generated_Basic_Auth_Token }}' \

</details>

<details>
 <summary><code>POST</code> <code><b>/login</b></code> <code>(Authenticates user credentials, sets a session and returns a redirect URL)</code></summary>

#### Parameters

##### URL Parameters

> None

##### Data Parameters

> | name       |  type      | data type              | description                                                           |
> |------------|------------|------------------------|-----------------------------------------------------------------------|
> | `email`    | `required` | `string`               | `user email`                                                          |
> | `password` | `required` | `string`               | `user password`                                                       |
> | `id`       | `required` | `string`               | `user id - used to compare incoming and outgoing users`               |

#### Responses

> | http code | http status message | content-type                      | response                                                                                             |
> |-----------|---------------------|-----------------------------------|------------------------------------------------------------------------------------------------------|
> | `200`     | `OK`           | `application/json`                | `"id":"{ user_id }","redirect_url":"{ app_host }/dashboard?__csst={ unique_token }","approved":boolean}` |

*Standard error responses are used for most common errors.

#### Example cURL

> ```bash
>  curl --request POST \
>  --url https://{app | stage-app}.current.tech/api/v2/auth/signup \
>  --header 'Accept: application/json' \
>  --header 'Content-Type: application/json' \
>  --header 'Authorization: Basic {{ Generated_Basic_Auth_Token }}' \
>  --data '
>  {
>    "email": "john.doe@email.com",
>    "password": "mysupersecretpassword",
>    "id": "0s9df-af9sf-uaSD-sFD09DFSDFJD"
>  }
>  '
> ```

</details>

<details>
 <summary><code>POST</code> <code><b>/signup</b></code> <code>(creates an ambassador application with status 'pending' and returns redirect URL)</code></summary>

#### Parameters

##### URL Parameters

> None

##### Data Parameters

> | name           |  type        | data type             | description                                                                                                                                                                                                                                                                                                                                                                                                                                  |
> |----------------|--------------|-----------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
> | `email`        | `required`   | `string`              | `email address of the applicant`                                                                                                                                                                                                                                                                                                                                                                                                             |
> | `first_name`   | `required`   | `string`              | `first name of the applicant`                                                                                                                                                                                                                                                                                                                                                                                                                |
> | `last_name`    | `required`   | `string`              | `last name of the applicant`                                                                                                                                                                                                                                                                                                                                                                                                                 |
> | `password`     | `required *` | `string`              | `optional if you indent to use your own authentication for login`                                                                                                                                                                                                                                                                                                                                                                            |
> | `dob`          | `optional`   | `string`              | `ISO 8601 Date`                                                                                                                                                                                                                                                                                                                                                                                                                              |
> | `gender`       | `optional`   | `string`              | `gender of applicant - could be male, female, other, refused, etc.`                                                                                                                                                                                                                                                                                                                                                                          |
> | `location_str` | `optional`   | `string`              | `represents unstructured address or location of applicant`                                                                                                                                                                                                                                                                                                                                                                                   |
> | `location`     | `optional`   | `JSON`                | `location or address of applicant.`<br/>`{`<br/>&nbsp;&nbsp;&nbsp;&nbsp;`line_1:string`<br/>&nbsp;&nbsp;&nbsp;&nbsp;`line_2: string - usually Apt. # or unit #`<br/>&nbsp;&nbsp;&nbsp;&nbsp;`locality: string - usually city`<br/>&nbsp;&nbsp;&nbsp;&nbsp;`region: string - usually state/province`<br/>&nbsp;&nbsp;&nbsp;&nbsp;`country: string - conforms to ISO 3166-1 alpha-2`<br/>&nbsp;&nbsp;&nbsp;&nbsp;`postal_code: string`<br/>`}` |
> | `tag`          | `optional`   | `string`              | `tag to be displayed in pending approvals section of Current. Common use case is to track application source domain or referrer`                                                                                                                                                                                                                                                                                                             |
> | `tracking_code`| `optional`   | `string`              | `This is a discount/tracking code, optionally supplied by ambassador or client.`                                                                                                                                                                                                                                                                                                                                                             |

*Note: if both location and location_str are provided we will use the location object as it provides a better user experience later on.
#### Responses

> | http code | http status message | content-type                      | response                                                                                                                                                                                            |
> |-----------|---------------------|-----------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
> | `201`     | `CREATED`           | `application/json`                | `{`<br/>&nbsp;&nbsp;&nbsp;&nbsp;`"id":"{ user_id }",`<br/>&nbsp;&nbsp;&nbsp;&nbsp;`"redirect_url":"{app_host}/join?__clt={unique_token}",`<br/>&nbsp;&nbsp;&nbsp;&nbsp;`"approved":boolean`<br/>`}` |
> | `302`     | `FOUND`             | `application/json`                | `{`<br/>&nbsp;&nbsp;&nbsp;&nbsp;`"id":"{ user_id }",`<br/>&nbsp;&nbsp;&nbsp;&nbsp;`"redirect_url":"{app_host}/join?__clt={unique_token}",`<br/>&nbsp;&nbsp;&nbsp;&nbsp;`"approved":boolean`<br/>`}` |

*Standard error responses are used for most common errors.

#### Example cURL

> ```bash
>  curl --request POST \
>  --url https://{app | stage-app}.current.tech/api/v2/auth/signup \
>  --header 'Accept: application/json' \
>  --header 'Content-Type: application/json' \
>  --header 'Authorization: Basic {{ Generated_Basic_Auth_Token }}' \
>  --data '
>  {
>    "email": "john.doe@email.com",
>    "password": "supersecretpassword",
>    "first_name": "John",
>    "last_name": "Doe"
>  }
>  '
> ```

</details>

<details>
 <summary><code>POST</code> <code><b>/password_reset_init</b></code> <code>(Generates and returns a password reset token that is valid for 15 minutes. This token must be provided along with the new password to complete the reset.)</code></summary>

#### Parameters

##### URL Parameters

> None

##### Data Parameters

> | name           |  type        | data type             | description                      |
> |----------------|--------------|-----------------------|----------------------------------|
> | `email`        | `required`   | `string`              | `email address of the user`      |

#### Responses

> | http code | http status message | content-type                      | response      |
> |-----------|---------------------|-----------------------------------|---------------|
> | `200`     | `SUCCESS`           | `application/json`                | `{{ token }}` |

*Standard error responses are used for most common errors.

#### Example cURL

> ```bash
>  curl --request POST \
>  --url https://{app | stage-app}.current.tech/api/v2/auth/password_reset_init \
>  --header 'Accept: application/json' \
>  --header 'Content-Type: application/json' \
>  --header 'Authorization: Basic {{ Generated_Basic_Auth_Token }}' \
>  --data '
>  {
>    "email": "john.doe@email.com"
>  }
>  '
> ```

</details>

<details>
 <summary><code>POST</code> <code><b>/password_reset_complete</b></code> <code>(Completes the password reset flow.)</code></summary>

#### Parameters

##### URL Parameters

> None

##### Data Parameters

> | name           |  type        | data type             | description                        |
> |----------------|--------------|-----------------------|------------------------------------|
> | `token`        | `required`   | `string`              | `token generated in the init step` |
> | `password`     | `required`   | `string`              | `new password created by user`     |

#### Responses

> | http code | http status message | content-type                      | response                                                          |
> |-----------|---------------------|-----------------------------------|-------------------------------------------------------------------|
> | `200`     | `SUCCESS`           | `application/json`                | `"redirect_url":"{ app_host }/dashboard?__csst={ unique_token }"` |

*Standard error responses are used for most common errors.

#### Example cURL

> ```bash
>  curl --request POST \
>  --url https://{app | stage-app}.current.tech/api/v2/auth/password_reset_complete \
>  --header 'Accept: application/json' \
>  --header 'Content-Type: application/json' \
>  --header 'Authorization: Basic {{ Generated_Basic_Auth_Token }}' \
>  --data '
>  {
>    "token": "{ generated_token }",
>    "password": "{ new_password }"
>  }
>  '
> ```

</details>

### Other Routes

<details>
 <summary><code>PUT</code> <code><b>/sales_attribution/{order_id}</b></code></summary>

#### Parameters

##### URL Parameters

> | name       |  type      | data type     | description                                                           |
> |------------|------------|---------------|-----------------------------------------------------------------------|
> | `order_id`  | `required` | `string`      | `the order id from your store that you are trying to either reject or modify the attribution to an ambassador`                  |

##### Data Parameters

> | name           |  type        | data type             | description                                                                                                                                           |
> |----------------|--------------|-----------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------|
> | `new_amount`   | `optional`   | `number (cents)`      | `new amount of ambassador sales attribution in cents (example: $1.01 -> 101, $123.45 -> 12345)`                                                       |
> | `rejected`     | `optional`   | `boolean`             | `set rejected to true or false for an ambassador sales attribution (attribution will not be paid out to ambassador if rejected = true)`               |

*Note: even though both data parameters are listed as optional, you must include one of them for a successful ambassador sales attribution modification.</br>
*Note: setting a new_amount for an attribution that was previously rejected, will result in unrejecting the attribution, and setting it to the new_amount provided.
#### Responses

> | http code | http status message  | content-type                     | response                                                             |
> |-----------|----------------------|----------------------------------|----------------------------------------------------------------------|
> | `202`     | `ACCEPTED`           | `application/json`               | `{`<br/>&nbsp;&nbsp;&nbsp;&nbsp;`"modified_attributions": 1`<br/>`}` |

*Standard error responses are used for most common errors.

#### Example cURL Attribution Adjustment

> ```bash
>  curl --request POST \
>  --url https://{app | stage-app}.current.tech/api/v2/auth/sales-attribution/{order_number} \
>  --header 'Accept: application/json' \
>  --header 'Content-Type: application/json' \
>  --header 'Authorization: Basic {{ Generated_Basic_Auth_Token }}' \
>  --data '
>  {
>    "new_amount": 1000,
>  }
>  '
> ```

#### Example cURL Attribution Rejection

> ```bash
>  curl --request POST \
>  --url https://{app | stage-app}.current.tech/api/v2/auth/sales-attribution/{order_number} \
>  --header 'Accept: application/json' \
>  --header 'Content-Type: application/json' \
>  --header 'Authorization: Basic {{ Generated_Basic_Auth_Token }}' \
>  --data '
>  {
>    "rejected": true,
>  }
>  '
> ```

</details>

<details>
 <summary><code>GET</code> <code><b>/discount_code/{discount_code}</b></code></summary>

#### Parameters

##### URL Parameters

> | name             |  type      | data type     | description                                                           |
> |------------------|------------|---------------|-----------------------------------------------------------------------|
> | `discount_code`  | `required` | `string`      | `discount_code input into shop`                                       |

##### Data Parameters

> None

#### Responses

> | http code | http status message | content-type                 | response                                                                                                             |
> |-----------|---------------------|------------------------------|----------------------------------------------------------------------------------------------------------------------|
> | `200`     | `OK`                | `application/json`           | `"discount_code":"{ discount_code }","code_exists":boolean,"ambassador_commission":string,"user_id":"{user_uuid}"}`  |

*Standard error responses are used for most common errors.

#### Example cURL

> ```bash
>  curl --request GET \
>  --url https://{app | stage-app}.current.tech/api/v2/discount_code/{ discount_code }} \
>  --header 'Accept: application/json' \
>  --header 'Content-Type: application/json' \
>  --header 'Authorization: Basic {{ Generated_Basic_Auth_Token }}' \

</details>

<details>
 <summary><code>POST</code> <code><b>/conversion</b></code></summary>

#### Parameters

##### URL Parameters

> | name            |  type      | data type               | description                                                                                                      |
> |-----------------|------------|-------------------------|------------------------------------------------------------------------------------------------------------------|
> | `identifier`    | `required` | `string`                | `Unique token identifier supplied in Current provided Pixel`                                                     |
> | `token`         | `required` | `string \| null`        | `tracking token - either discount code or token from cookie`                                                     |
> | `last_touch`    | `required` | `string \| int \| null` | `timestamp of tracking token being set. Important for cookie based tracking. Expects milliseconds int or string` |
> | `order_id`      | `required` | `string`                | `brand shop generated order_id`                                                                                  |
> | `discount_code` | `required` | `string \| null`        | `discount_code input into shop`                                                                                  |
> | `cart_obj`      | `required` | `object`                | `Must include subtotal in dollars and cents eg. '55.23'. Other data will be stored but not used.`                |

##### Data Parameters

> None

#### Responses

> | http code | http status message | content-type                      | response          |
> |-----------|---------------------|-----------------------------------|-------------------|
> | `200`     | `OK`           | `application/json`                | `{ "success": true }`  |

*Standard error responses are used for most common errors.

#### Example cURL

> ```bash
>  curl --request GET \
>  --url https://{app | stage-app}.current.tech/api/v2/conversion \
>  --header 'Accept: application/json' \
>  --header 'Content-Type: application/json' \
>  --header 'Authorization: Basic {{ Generated_Basic_Auth_Token }}' \

</details>
