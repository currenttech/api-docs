# Getting Started

## API Authentication

- All requests must be made using TLS (https).
- All requests are authenticated using a [Basic Auth](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Authorization) Authorization header.
- Your Organization ID and API Secret will serve as your username and password fields respectively when creating your Basic Auth Header.
- All `Production` requests should use `https://app.current.tech/api/v2` as the host while all `stage/sandbox` requests should use `https://stage-app.current.tech/api/v2`
- All requests and responses should include an `application/json` content-type header.

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
> | `400`     | `BAD REQUEST`           | `{`<br />&nbsp;&nbsp;&nbsp;&nbsp;`"success":false,`<br />&nbsp;&nbsp;&nbsp;&nbsp;`"message":"Missing Authorization header",`<br />&nbsp;&nbsp;&nbsp;&nbsp;`"payload":null`<br />`}`                |
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
> | `200`     | `CREATED`           | `application/json`                | `"id":"{ user_id }","redirect_url":"{app_host}/dashboard?__csst={unique_token}","verified":boolean}` |

*Standard error responses are used for most common errors.

#### Example cURL

> ```bash
>  curl --request GET \
>  --url https://{app | stage-app}.current.tech/api/v2/auth/login/{ user_id} \
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

#### Responses

> | http code | http status message | content-type                      | response                                                                                             |
> |-----------|---------------------|-----------------------------------|------------------------------------------------------------------------------------------------------|
> | `201`     | `CREATED`           | `application/json`                | `"id":"{ user_id }","redirect_url":"{app_host}/dashboard?__csst={unique_token}","verified":boolean}` |
> | `302`     | `FOUND`             | `application/json`                | `"id":"{ user_id }","redirect_url":"{app_host}/dashboard?__csst={unique_token}","verified":boolean}` |

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
>    "password": "mysupersecretpassword"
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

*Note: if both location and location_str are provided we will use the location object as it provides a better user experience later on.
#### Responses

> | http code | http status message | content-type                      | response                                                                                                                                                                                            |
> |-----------|---------------------|-----------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
> | `201`     | `CREATED`           | `application/json`                | `{`<br/>&nbsp;&nbsp;&nbsp;&nbsp;`"id":"{ user_id }",`<br/>&nbsp;&nbsp;&nbsp;&nbsp;`"redirect_url":"{app_host}/join?__clt={unique_token}",`<br/>&nbsp;&nbsp;&nbsp;&nbsp;`"verified":boolean`<br/>`}` |
> | `302`     | `FOUND`             | `application/json`                | `{`<br/>&nbsp;&nbsp;&nbsp;&nbsp;`"id":"{ user_id }",`<br/>&nbsp;&nbsp;&nbsp;&nbsp;`"redirect_url":"{app_host}/join?__clt={unique_token}",`<br/>&nbsp;&nbsp;&nbsp;&nbsp;`"verified":boolean`<br/>`}` |

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
>    "first_name": "John",
>    "last_name": "Doe"
>  }
>  '
> ```

</details>
