---
title: GroundControl API - Spacion GmbH

language_tabs:
  - php

toc_footers:
  - <a href='https://my.hyperspace.one/contact'>Imprint</a>

search: true
---

# Introduction

GroundControl API is the backend of GroundControl by Spacion GmbH. This documentation covers all available API actions, from authentication to endpoints. GroundControl API is a RESTful API.

We use `https://api.hyperspace.one` as an example URL. This URL has to be replaced by your URL where GroundControl API is hosted.

We provide example codes in PHP. You can view code examples in the dark area to the right, and you can switch the programming language of the examples with the tabs in the top right.

# Changelog

This is the changelog of this API documentation.

Date | Changes
---- | -------
2019-06-13 | <ul style="margin: 0;"><li>Added GET /users/rights</li><li>Added GET /devices</li><li>Added POST /devices</li><li>Added PUT /devices/{id}</li><li>Added DELETE /devices/{id}</li></ul>
2019-06-12 | <ul style="margin: 0;"><li>Added GET /photographers</li><li>Added POST /orders/{id}</li><li>Updated GET /faq</li><li>Added POST /faq</li><li>Added PUT /faq/{id}</li><li>Added DELETE /faq/{id}</li><li>Updated GET /ranks</li><li>Added POST /ranks</li><li>Added PUT /ranks/{id}</li><li>Added DELETE /ranks/{id}</li><li>Added GET /ranks/features</li><li>Added POST /ranks/features</li><li>Added PUT /ranks/features/{id}</li><li>Added DELETE /ranks/features/{id}</li></ul>

# Return values & error handling

> Successful request (HTTP status code `200`) without data

```json
{
  "success": true
}
```

> Failed request (HTTP status code != `200`)

```json
{
  "success": false,
  "errorCode": 403,
  "error": "No access to this resource"
}
```

GroundControl returns data in JSON format.

In case of a successful request, the HTTP status code will be `200`. Usually, you will get a JSON element `success` containing `true`. In some cases, no `success` element is returned, e.g. if you requested a list without pagination.

If your request fails, the HTTP status code will not be `200`. GroundControl API uses the default HTTP status codes (`403 Forbidden`, `404 Not Found`, ...) and own status codes (`1xxx`). You can also find the error code in the JSON element `errorCode`. Sometimes, there is additional information available in the `error` element.

# Headers

You can send several headers to GroundControl API:

Parameter | Default | Description
--------- | ------- | -----------
Authorization | - | Header for providing session token (currently supported: `Bearer xxx`)
X-Gc-Language | en | Language that should be used (currently supported: `de`, `en`)

# Authentication

For non-publicly endpoints or actions, GroundControl uses Bearer authentication with tokens. The login should be performed against the endpoint `POST /login`.

Please note that GroundControl API uses a blacklist to identify malicious IP addresses and blocks them after a defineable amount of failed tries (setting `blacklistTries`) in a defineable amount of time (setting `blacklistTime`).

The API discloses if the user with the given email address exists. This is intentional.

### API endpoint

```php
<?php
$req = [
  "email" => "john.doe@example.com",
  "password" => "Secret123!",
];

$ch = curl_init("https://api.hyperspace.one/login");
curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
curl_setopt($ch, CURLOPT_POSTFIELDS, http_build_query($req));
$res = curl_exec($ch);

if (curl_errno($ch)) {
  die(curl_error($ch));
}

curl_close($ch);
$res = json_decode($res, true);

print_r($res);
```

> The above request returns JSON structured like this:

```json
{
  "success": true,
  "token": "1533921b8b134efmk7ccbfa19a2c9f2d02919304017004f258daa71326aa2db3454db0405ee53de72a3d523b3b22ed54e4341424bcd014527de69fe745fe4fa0",
  "info": {
    // Info endpoint
  }
}
```

`POST /login`

### Query parameters

Parameter | Default | Description
--------- | ------- | -----------
email | - | **Required** Email address
password | - | **Required** Password

### Return values

Parameter | Description
--------- | -----------
token | Bearer token to use in further requests
info | Info endpoint data for authenticated user

### Failure codes

Failure Code | Meaning
---------- | -------
1000 | IP address in blacklist (too many failed attempts)
1001 | Unknown email address
1002 | Wrong password

### Logout

To terminate a session / make a bearer token invalid, just call `GET /logout` with the bearer token in `Authentication` header.

# System

## GET /info
```php
<?php
$ch = curl_init("https://api.hyperspace.one/info");
curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
$res = curl_exec($ch);

if (curl_errno($ch)) {
  die(curl_error($ch));
}

curl_close($ch);
$res = json_decode($res, true);

print_r($res);
```

> The above request returns JSON structured like this:

```json
{
  "success": true,
  "pageName": "HYPERSPACE",
  "recaptchaPublic": "6Leti3QUAAAAADyMymEKWfyBymJ4t0HdWHPMTPNr",
  "orderTimeout": "30",
  "currencyCode": "EUR",
  "maintenanceMode": false
}
```

The info endpoint is an central endpoint of GC API. It returns information about the configuration and - if logged in - about the authenticated user.

### Query parameters

Parameter | Default | Description
--------- | ------- | -----------
products | 0 | Include available products in response

### Return values

Parameter | Description
--------- | -----------
pageName | Name of the page
recaptchaPublic | **If enabled** reCaptcha public key
orderTimeout | Timeout for orders in minutes
currencyCode | Default currency code
maintenanceMode | Maintenance mode active
user | **If authenticated** Details about user

<aside style="color: white;">Additional values are possible based on your system configuration</aside>

## GET /products
```php
<?php
$ch = curl_init("https://api.hyperspace.one/products");
curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
$res = curl_exec($ch);

if (curl_errno($ch)) {
  die(curl_error($ch));
}

curl_close($ch);
$res = json_decode($res, true);

print_r($res);
```

> The above request returns JSON structured like this:

```json
[
  {
    "id":1,
    "name":"Early Bird",
    "description":"What do you get with this ticket?\r\n\r\n+ Access to the festival\r\n+ Wristband\r\n+ Parking\r\n+ Some more stuff\r\n- Some more stuff",
    "net_price":4.2,
    "gross_price":5,
    "price":5,
    "taxrate":19,
    "tax":0.8,
    "net_shipping":0.04,
    "gross_shipping":0.05,
    "tax_shipping":0.01,
    "pricePrefix":"",
    "priceSuffix":"\u20ac",
    "end_personalization":"0000-00-00 00:00:00",
    "sale_start":"0000-00-00 00:00:00",
    "sale_end":"0000-00-00 00:00:00",
    "available":true
  }
]
```

This endpoint returns the products defined in GroundControl.

### Return values

Each product has the following parameters:

Parameter | Description
--------- | -----------
id | Product ID
name | Product name (localized)
description | Product description (localized)
net_price | Price without tax
gross_price | Price with tax
price | End price
taxrate | Tax rate in percent
tax | Tax included in price
net_shipping | Shipping costs without tax
gross_shipping | Shipping costs including tax
tax_shipping | Tax included in shipping
pricePrefix | Prefix for pricing (e.g. $)
priceSuffix | Suffix for pricing (e.g. €)
end_personalization | End of ticket personalization timeframe
sale_start | Start date of sale (can be in future)
sale_end | End date of sale (can be in past)
available | Product in stock

## GET /faq
```php
<?php
$ch = curl_init("https://api.hyperspace.one/faq");
curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
$res = curl_exec($ch);

if (curl_errno($ch)) {
  die(curl_error($ch));
}

curl_close($ch);
$res = json_decode($res, true);

print_r($res);
```

> The above request returns JSON structured like this:

```json
{
  "success": true,
  "faq": [
    {
      "id": 2,
      "question":"WHERE CAN YOU PARK?",
      "answer":"At our PreParty in Music Dome there is free parking.",
      "localization": {
        "en": {
          "question": "WHERE CAN YOU PARK?",
          "answer": "At our PreParty in Music Dome there is free parking straight in front of the location. Because the Music Dome is located in the stadion of Roda JC Kerkrade, there are more than enough parking lots."
        },
        "de": {
          "question": "WO KANN ICH PARKEN?",
          "answer": "Bei unserer PreParty im Music Dome Kerkrade findest Du kostenlose Parkplätze direkt vor der Location. Da der Music Dome sich direkt im Stadiongebäude des Roda JC Kerkrade befindet, stehen hier genügend Stellplätze zur Verfügung."
        }
      }
    }
  ]
}
```

This endpoint returns the defined Frequently Asked Questions (FAQ).

### Return values

Parameter | Description
--------- | -----------
faq[] | Array with questions and answer

## GET /qrcode/{content}
```php
<?php
$ch = curl_init("https://api.hyperspace.one/qrcode/$content");
curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
$res = curl_exec($ch);

if (curl_errno($ch)) {
  die(curl_error($ch));
}

curl_close($ch);

header('content-type: image/png');
echo $res;
exit;
```

> The above request will show a QR code image representing `$content`

This endpoint generates a QR code as image. It is returned directly with `Content-Type image/png`.

### Query parameters

Parameter | Description
--------- | -----------
content | Value that QR code should represent

# Users

## PUT /register
```php
<?php
$ch = curl_init("https://api.hyperspace.one/register");
curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
curl_setopt($ch, CURLOPT_POSTFIELDS, http_build_query([
  "captcha" => "xxx",
  "email" => "john.doe@example.com",
  "firstname" => "John",
  "lastname" => "Doe",
]));
curl_setopt($ch, CURLOPT_CUSTOMREQUEST, "PUT");
$res = curl_exec($ch);

if (curl_errno($ch)) {
  die(curl_error($ch));
}

curl_close($ch);
$res = json_decode($res, true);

print_r($res);
```

> The above request returns JSON structured like this:

```json
{
  "success": true
}
```

New registrations by users should be handled using this endpoint.

As soon as a captcha has been solved correctly, this information is saved within the browser session on the API server. This means if there is an error in the registration process (excluding captcha error), the captcha must not be solved again. The information is reset as soon as the registration was successful. Therefore, another registration requires a new captcha.

During a successful registration, the user gets an email from GC-API containing his password he can login with.

### Query parameters

Parameter | Description
--------- | -----------
captcha | **If captcha activated** Captcha token
email | Email address
firstname | First name
lastname | Last name

### Failure codes

Failure Code | Meaning
---------- | -------
1000 | Invalid captcha token
1001 | Invalid email
1002 | Email already registered
1003 | No first name specified
1004 | No last name specified

## PUT /password
```php
<?php
$ch = curl_init("https://api.hyperspace.one/password");
curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
curl_setopt($ch, CURLOPT_POSTFIELDS, http_build_query([
  "new_pwd" => "xxx",
  "old_pwd" => "xxx",
]));
curl_setopt($ch, CURLOPT_CUSTOMREQUEST, "PUT");
curl_setopt($ch, CURLOPT_HTTPHEADER, [
  "Authorization: Bearer xxx",
]);
$res = curl_exec($ch);

if (curl_errno($ch)) {
  die(curl_error($ch));
}

curl_close($ch);
$res = json_decode($res, true);

print_r($res);
```

> The above request returns JSON structured like this:

```json
{
  "success": true
}
```

<aside style="color: white;">User authentication required</aside>

This endpoint allows users to change their account password. Requesting a password confirmation and checking for parity should be done in frontend.

### Query parameters

Parameter | Description
--------- | -----------
new_pwd | New password
old_pwd | Old password

### Failure codes

Failure Code | Meaning
---------- | -------
1000 | New password does not met requirements
1001 | Old password is invalid

## POST /password/email
```php
<?php
$ch = curl_init("https://api.hyperspace.one/password/email");
curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
curl_setopt($ch, CURLOPT_POSTFIELDS, http_build_query([
  "captcha" => "xxx",
  "email" => "john.doe@example.com",
]));
$res = curl_exec($ch);

if (curl_errno($ch)) {
  die(curl_error($ch));
}

curl_close($ch);
$res = json_decode($res, true);

print_r($res);
```

> The above request returns JSON structured like this:

```json
{
  "success": true
}
```

If a user has lost his password, a reset email can be requested using this endpoint.

As soon as a captcha has been solved correctly, this information is saved within the browser session on the API server. This means if there is an error in the password request process (excluding captcha error), the captcha must not be solved again. The information is reset as soon as the reset request was successful. Therefore, another reset request requires a new captcha.

After a successful reset request, the user gets an email from GC-API containing a reset link leading to the frontend configured in GroundControl.

### Query parameters

Parameter | Description
--------- | -----------
captcha | **If captcha activated** Captcha token
email | Email address

### Failure codes

Failure Code | Meaning
---------- | -------
1000 | Invalid captcha token
1001 | No user with this email found

## POST /password/check
```php
<?php
$ch = curl_init("https://api.hyperspace.one/password/check");
curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
curl_setopt($ch, CURLOPT_POSTFIELDS, http_build_query([
  "email" => "john.doe@example.com",
  "token" => "xxx",
]));
$res = curl_exec($ch);

if (curl_errno($ch)) {
  die(curl_error($ch));
}

curl_close($ch);
$res = json_decode($res, true);

print_r($res);
```

> If the token is valid, the response is:

```json
{
  "success": true
}
```

> If the token is invalid or expired, the response is:

```json
{
  "success": false
}
```

This endpoint can be used to verify a password reset token. This is useful to pre-check the token before actually requesting a new password from the user.

Please note that the reset token has a validity of 30 minutes.

### Query parameters

Parameter | Description
--------- | -----------
email | Email address
token | Password reset token

## POST /password/reset
```php
<?php
$ch = curl_init("https://api.hyperspace.one/password/reset");
curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
curl_setopt($ch, CURLOPT_POSTFIELDS, http_build_query([
  "captcha" => "xxx",
  "email" => "john.doe@example.com",
  "pwd" => "xxx",
]));
$res = curl_exec($ch);

if (curl_errno($ch)) {
  die(curl_error($ch));
}

curl_close($ch);
$res = json_decode($res, true);

print_r($res);
```

> The above request returns JSON structured like this:

```json
{
  "success": true
}
```

This endpoint is used to actually reset a users password. Therefore, the reset process data and the new password is needed. The password is instantly saved and a login with the new password is possible. Please note that the password reset token is valid only once and expires 30 minutes after issuance.

As soon as a captcha has been solved correctly, this information is saved within the browser session on the API server. This means if there is an error in the password reset process (excluding captcha error), the captcha must not be solved again. The information is reset as soon as the reset process was successful. Therefore, another reset process requires a new captcha.

### Query parameters

Parameter | Description
--------- | -----------
captcha | **If captcha activated** Captcha token
email | Email address
token | Password reset token
pwd | New password

### Failure codes

Failure Code | Meaning
---------- | -------
1000 | Invalid captcha token
1001 | Invalid reset token
1002 | No user with this email found
1003 | New password does not met requirements

## GET /users
```php
<?php
$ch = curl_init("https://api.hyperspace.one/users");
curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
curl_setopt($ch, CURLOPT_HTTPHEADER, [
  "Authorization: Bearer xxx",
]);
$res = curl_exec($ch);

if (curl_errno($ch)) {
  die(curl_error($ch));
}

curl_close($ch);
$res = json_decode($res, true);

print_r($res);
```

> The above request returns JSON structured like this:

```json
{
  "success": true,
  "itemCount": 1,
  "items": [
    {
      "id": 1,
      "firstname": "John",
      "lastname": "Doe",
      "email": "john.doe@example.com",
      "password": "$2y$10$B/c4MY1JhqZo7HMhZBlkO.AlwoapqH9fLOPfL4QhqhX.x2cdqo01a",
      "remember_token": null,
      "created_at": "2019-03-07 17:09:13",
      "updated_at": "2019-03-07 21:10:49",
      "language": "en",
      "pw_changed": "2019-03-07 21:10:49",
      "contact_person": 1,
      "instagram": "johndoe"
    }
  ]
}
```

<aside style="color: white;">Requires user authentication with right <b>get_users</b></aside>

This endpoint allows administrators to get a paginated list of all users in the system.

### Query parameters

Parameter | Default | Description
--------- | ------- | -----------
page | 1 | Page number
per_page | 20 | Items to display per page
filter | - | Wildcard filter for firstname, lastname, email
sort_field | created_at | Field to sort by
sort_direction | asc | Sorting direction

### Return values

The request returns an `itemCount` which is the number of all users in system. `items` contains the set of requested users according to pagination. The users have these properties:

Parameter | Description
--------- | -----------
id | User ID
firstname | First name
lastname | Last name
email | Email address
password | Hashed password
remember_token | Cookie token
created_at | Time of registration
updated_at | Time of last change
language | Last language used
pw_changed | Time of last password change
contact_person | User ID of contact person
instagram | Instagram handle

## GET /user/{id}
```php
<?php
$ch = curl_init("https://api.hyperspace.one/user/{id}");
curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
curl_setopt($ch, CURLOPT_HTTPHEADER, [
  "Authorization: Bearer xxx",
]);
$res = curl_exec($ch);

if (curl_errno($ch)) {
  die(curl_error($ch));
}

curl_close($ch);
$res = json_decode($res, true);

print_r($res);
```

> The above request returns JSON structured like this:

```json
{
  "success": true,
  "id": 1,
  "firstname": "John",
  "lastname": "Doe",
  "email": "john.doe@example.com",
  "password": "$2y$10$B/c4MY1JhqZo7HMhZBlkO.AlwoapqH9fLOPfL4QhqhX.x2cdqo01a",
  "remember_token": null,
  "created_at": "2019-03-07 17:09:13",
  "updated_at": "2019-03-07 21:10:49",
  "language": "en",
  "pw_changed": "2019-03-07 21:10:49",
  "contact_person": 1,
  "instagram": "johndoe"
}
```

<aside style="color: white;">Requires user authentication with right <b>get_user</b></aside>

This endpoint allows administrators to get details of a user.

Administrators are only allowed to access users who have less or equal amount of rights. Administrator with wildcard right are allowed to access every user.

### Query parameters

Parameter | Default | Description
--------- | ------- | -----------
id | - | User ID

### Return values

Parameter | Description
--------- | -----------
id | User ID
firstname | First name
lastname | Last name
email | Email address
password | Hashed password
remember_token | Cookie token
created_at | Time of registration
updated_at | Time of last change
language | Last language used
pw_changed | Time of last password change
contact_person | User ID of contact person
instagram | Instagram handle

### Failure codes

Failure Code | Meaning
---------- | -------
1000 | User not found
1001 | No user access

## PUT /user/{id}
```php
<?php
$ch = curl_init("https://api.hyperspace.one/user/{id}");
curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
curl_setopt($ch, CURLOPT_POSTFIELDS, http_build_query([
  "instagram" => "johndoe.new",
]));
curl_setopt($ch, CURLOPT_HTTPHEADER, [
  "Authorization: Bearer xxx",
]);
curl_setopt($ch, CURLOPT_CUSTOMREQUEST, "PUT");
$res = curl_exec($ch);

if (curl_errno($ch)) {
  die(curl_error($ch));
}

curl_close($ch);
$res = json_decode($res, true);

print_r($res);
```

> The above request returns JSON structured like this:

```json
{
  "success": true
}
```

<aside style="color: white;">Requires user authentication with right <b>create_user</b></aside>

This endpoint allows administrators to edit user details.

Administrators are only allowed to access users who have less or equal amount of rights. Administrator with wildcard right are allowed to access every user.

### Query parameters

Parameter | Default | Description
--------- | ------- | -----------
id | - | User ID

You can also use the parameters `email`, `firstname`, `lastname`, `password` and `instagram` to update these properties. Parameters not specified are ignored and cannot throw any errors.

If you specify an array named `rights`, you can set the rights of a user. All rights granted to the user before not included in this array are deleted.

### Failure codes

Failure Code | Meaning
---------- | -------
1001 | Invalid email specified
1002 | Email already in use
1005 | Password does not met requirements

## DELETE /user/{id}
```php
<?php
$ch = curl_init("https://api.hyperspace.one/user/{id}");
curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
curl_setopt($ch, CURLOPT_HTTPHEADER, [
  "Authorization: Bearer xxx",
]);
curl_setopt($ch, CURLOPT_CUSTOMREQUEST, "DELETE");
$res = curl_exec($ch);

if (curl_errno($ch)) {
  die(curl_error($ch));
}

curl_close($ch);
$res = json_decode($res, true);

print_r($res);
```

> The above request returns JSON structured like this:

```json
{
  "success": true
}
```

<aside style="color: white;">Requires user authentication with right <b>delete_user</b></aside>

This endpoint allows administrators to delete a user.

Administrators are only allowed to access users who have less or equal amount of rights. Administrator with wildcard right are allowed to access every user.

### Query parameters

Parameter | Default | Description
--------- | ------- | -----------
id | - | User ID

## POST /user
```php
<?php
$ch = curl_init("https://api.hyperspace.one/user");
curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
curl_setopt($ch, CURLOPT_HTTPHEADER, [
  "Authorization: Bearer xxx",
]);
curl_setopt($ch, CURLOPT_POSTFIELDS, http_build_query([
  "email" => "john.doe@example.com",
  "firstname" => "John",
  "lastname" => "Doe",
  "password" => "xxx",
  "instagram" => "johndoe",
  "rights" => ["promoter"],
  "send_mail" => true,
  "send_promoter_mail" => true,
]));
$res = curl_exec($ch);

if (curl_errno($ch)) {
  die(curl_error($ch));
}

curl_close($ch);
$res = json_decode($res, true);

print_r($res);
```

> The above request returns JSON structured like this:

```json
{
  "success": true,
  "id": 1
}
```

<aside style="color: white;">Requires user authentication with right <b>create_user</b></aside>

This endpoint allows administrators to create new users.

### Query parameters

Parameter | Default | Description
--------- | ------- | -----------
email | - | Email address
firstname | - | First name
lastname | - | Last name
password | - | Password
instagram | - | **Optional** Instagram handle
rights | [] | Array of rights
send_mail | false | Send welcome mail
send_promoter_mail | false | Send promoter welcome mail

The welcome mails are being sent with the users password.

### Return values

Parameter | Description
--------- | -----------
id | ID of newly created user

### Failure codes

Failure Code | Meaning
---------- | -------
1001 | Invalid email specified
1002 | Email already in use
1003 | No firstname specified
1004 | No lastname specified
1005 | Password does not met requirements

## GET /users/rights
```php
<?php
$ch = curl_init("https://api.hyperspace.one/users/rights");
curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
curl_setopt($ch, CURLOPT_HTTPHEADER, [
  "Authorization: Bearer xxx",
]);
$res = curl_exec($ch);

if (curl_errno($ch)) {
  die(curl_error($ch));
}

curl_close($ch);
$res = json_decode($res, true);

print_r($res);
```

> The above request returns JSON structured like this:

```json
{
  "success": true,
  "rights": {
    "settings": "Manage system settings",
    "get_users": "View customer list",
    "get_user": "View customer details",
    "create_user": "Create customers",
    "delete_user": "Delete customers"
  }
}
```

<aside style="color: white;">Requires authentication with right <b>get_users</b></aside>

This endpoint allows staff members to get the available system rights with their localized description.

# Preregistrations

## POST /preregister
```php
<?php
$ch = curl_init("https://api.hyperspace.one/preregister");
curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
curl_setopt($ch, CURLOPT_POSTFIELDS, http_build_query([
  "captcha" => "xxx",
  "firstname" => "John",
  "lastname" => "Doe",
  "email" => "john.doe@example.com",
  "age" => "16",
]));
$res = curl_exec($ch);

if (curl_errno($ch)) {
  die(curl_error($ch));
}

curl_close($ch);
$res = json_decode($res, true);

print_r($res);
```

> The above request returns JSON structured like this:

```json
{
  "success": true
}
```

This endpoint handles new preregistrations by end user.

As soon as a captcha has been solved correctly, this information is saved within the browser session on the API server. This means if there is an error in the registration process (excluding captcha error), the captcha must not be solved again. The information is reset as soon as the registration was successful. Therefore, another registration requires a new captcha.

To confirm a preregistration, the user gets an email with a link to do this. The link uses the frontend configured in GroundControl.

### Query parameters

Parameter | Description
--------- | -----------
captcha | **If captcha activated** Captcha token
email | Email address
firstname | First name
lastname | Last name
age | Age (currently: `16` or `18`)

### Failure codes

Failure Code | Meaning
---------- | -------
1000 | Invalid captcha token
1001 | Invalid email
1002 | Email already registered
1003 | No first name specified
1004 | No last name specified
1005 | Invalid age

## GET /preregister/confirm/{token}
```php
<?php
$ch = curl_init("https://api.hyperspace.one/preregister/confirm/{token}");
curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
$res = curl_exec($ch);

if (curl_errno($ch)) {
  die(curl_error($ch));
}

curl_close($ch);
$res = json_decode($res, true);

print_r($res);
```

> The above request returns JSON structured like this:

```json
{
  "success": true
}
```

This endpoint is used to confirm pending preregistrations with the token from the registration email sent to the user.

### Query parameters

Parameter | Description
--------- | -----------
token | Confirmation token

### Failure codes

Failure Code | Meaning
---------- | -------
1000 | Invalid token length
1001 | Unknown token
1002 | Already confirmed

## PUT /preregister
```php
<?php
$ch = curl_init("https://api.hyperspace.one/preregister");
curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
curl_setopt($ch, CURLOPT_POSTFIELDS, http_build_query([
  "captcha" => "xxx",
  "email" => "john.doe@example.com",
]));
curl_setopt($ch, CURLOPT_CUSTOMREQUEST, "PUT");
$res = curl_exec($ch);

if (curl_errno($ch)) {
  die(curl_error($ch));
}

curl_close($ch);
$res = json_decode($res, true);

print_r($res);
```

> The above request returns JSON structured like this:

```json
{
  "success": true
}
```

This endpoint is used to resend an email for an existing preregistration and is called by the end user.

As soon as a captcha has been solved correctly, this information is saved within the browser session on the API server. This means if there is an error in the resend process (excluding captcha error), the captcha must not be solved again. The information is reset as soon as the resend was successful. Therefore, another resend requires a new captcha.

### Query parameters

Parameter | Description
--------- | -----------
captcha | **If captcha activated** Captcha token
email | Email address

### Failure codes

Failure Code | Meaning
---------- | -------
1000 | Invalid captcha token
1001 | Invalid email
1002 | Email not found
1003 | Already confirmed

## GET /preregistrations
```php
<?php
$ch = curl_init("https://api.hyperspace.one/preregistrations");
curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
curl_setopt($ch, CURLOPT_HTTPHEADER, [
  "Authorization: Bearer xxx",
]);
$res = curl_exec($ch);

if (curl_errno($ch)) {
  die(curl_error($ch));
}

curl_close($ch);
$res = json_decode($res, true);

print_r($res);
```

> The above request returns JSON structured like this:

```json
{
  "success": true,
  "itemCount": 1,
  "items": [
    {
      "email": "john.doe@example.com",
      "firstname": "John",
      "lastname": "Doe",
      "age": 18,
      "verified": 1,
      "token": "jKbCNxgggOLao5dc",
      "created_at": "2018-12-04 00:58:37",
      "updated_at": "2019-01-13 21:22:04"
    }
  ]
}
```

<aside style="color: white;">Requires user authentication with right <b>get_preregistrations</b></aside>

This endpoint allows administrators to get a paginated list of all preregistrations in the system.

### Query parameters

Parameter | Default | Description
--------- | ------- | -----------
page | 1 | Page number
per_page | 20 | Items to display per page
filter | - | Wildcard filter for firstname, lastname, email
sort_field | created_at | Field to sort by
sort_direction | asc | Sorting direction

### Return values

The request returns an `itemCount` which is the number of all preregistrations in system. `items` contains the set of requested preregistrations according to pagination. The preregistrations have these properties:

Parameter | Description
--------- | -----------
email | Email address
firstname | First name
lastname | Last name
age | Age
verified | Email address confirmed
token | Confirm token
created_at | Time of registration
updated_at | Time of last change (usually time of confirmation)

## PUT /preregistrations/{email}
```php
<?php
$ch = curl_init("https://api.hyperspace.one/preregistrations/{email}");
curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
curl_setopt($ch, CURLOPT_POSTFIELDS, http_build_query([
  "firstname" => "John",
]));
curl_setopt($ch, CURLOPT_HTTPHEADER, [
  "Authorization: Bearer xxx",
]);
curl_setopt($ch, CURLOPT_CUSTOMREQUEST, "PUT");
$res = curl_exec($ch);

if (curl_errno($ch)) {
  die(curl_error($ch));
}

curl_close($ch);
$res = json_decode($res, true);

print_r($res);
```

> The above request returns JSON structured like this:

```json
{
  "success": true
}
```

<aside style="color: white;">Requires user authentication with right <b>edit_preregistrations</b></aside>

This endpoint is used by staff to edit existing preregistrations.

### Query parameters

Parameter | Description
--------- | -----------
email | Email address

The body can contain any parameter you want to change included in `GET /preregistrations`.

### Failure codes

Failure Code | Meaning
---------- | -------
1000 | Email not found

## POST /preregistrations/{email}
```php
<?php
$ch = curl_init("https://api.hyperspace.one/preregistrations/{email}");
curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
curl_setopt($ch, CURLOPT_HTTPHEADER, [
  "Authorization: Bearer xxx",
]);
curl_setopt($ch, CURLOPT_CUSTOMREQUEST, "POST");
$res = curl_exec($ch);

if (curl_errno($ch)) {
  die(curl_error($ch));
}

curl_close($ch);
$res = json_decode($res, true);

print_r($res);
```

> The above request returns JSON structured like this:

```json
{
  "success": true
}
```

<aside style="color: white;">Requires user authentication with right <b>edit_preregistrations</b></aside>

This endpoint is used by staff to resend email for unconfirmed preregistrations.

### Query parameters

Parameter | Description
--------- | -----------
email | Email address

### Failure codes

Failure Code | Meaning
---------- | -------
1000 | Email not found

## DELETE /preregistrations/{email}
```php
<?php
$ch = curl_init("https://api.hyperspace.one/preregistrations/{email}");
curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
curl_setopt($ch, CURLOPT_HTTPHEADER, [
  "Authorization: Bearer xxx",
]);
curl_setopt($ch, CURLOPT_CUSTOMREQUEST, "DELETE");
$res = curl_exec($ch);

if (curl_errno($ch)) {
  die(curl_error($ch));
}

curl_close($ch);
$res = json_decode($res, true);

print_r($res);
```

> The above request returns JSON structured like this:

```json
{
  "success": true
}
```

<aside style="color: white;">Requires user authentication with right <b>edit_preregistrations</b></aside>

This endpoint is used by staff to remove preregistrations.

### Query parameters

Parameter | Description
--------- | -----------
email | Email address

### Failure codes

Failure Code | Meaning
---------- | -------
1000 | Email not found
1001 | Deletion failed

# Events

## GET /events
```php
<?php
$ch = curl_init("https://api.hyperspace.one/events");
curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
$res = curl_exec($ch);

if (curl_errno($ch)) {
  die(curl_error($ch));
}

curl_close($ch);
$res = json_decode($res, true);

print_r($res);
```

> The above request returns JSON structured like this:

```json
{  
  "success":true,
  "data": {  
    "all": {  
      "1": {  
        "id": 1,
        "slug": "the-abandoned-dome",
        "date": "2019-03-30 22:00:00",
        "name": "The Abandoned Dome",
        "name2": "HYPERSPACE",
        "location": "Music Dome Kerkrade",
        "img": "assets/bg.jpg",
        "design": 1,
        "artists": [  
          {  
            "name": "D-Block & S-te-fan",
            "img": "assets/artists/dblock_stefan.jpg",
            "set_time": "xx:xx - xx:xx",
            "tier": 1
          },
          {  
            "name": "Frequencerz",
            "img": "assets/artists/frequencerz.jpg",
            "set_time": "xx:xx - xx:xx",
            "tier": 1
          }
          {  
            "name": "Le Shuuk",
            "img": "assets/artists/leshuuk.jpg",
            "set_time": "xx:xx - xx:xx",
            "tier": 1
          }
        ],
        "products": [  

        ],
        "galleries": [  
          {  
            "slug": "the-abandoned-dome",
            "title": "The Abandoned Dome",
            "imageCount": 128
          }
        ]
      }
    }
  }
}
```

This endpoint returns the events defined in GroundControl.

### Return values

In the `data` element, there is a key `all` containing all events with their ID as key. If you have a featured event configured, there will be also a key `featured` in `data` containing the details of the featured event like the events in `all` are listed.

Each event has the following parameters:

Parameter | Description
--------- | -----------
id | Event ID
slug | Event slug
date | Event date
name | Event name
name2 | Event sub name
location | Event location
img | Background image for website
design | ID of website design
artists[] | List of artists performing
products[] | List of tickets/products available
galleries[] | List of assigned galleries

## GET /events/{slug}
```php
<?php
$ch = curl_init("https://api.hyperspace.one/events/{slug}");
curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
$res = curl_exec($ch);

if (curl_errno($ch)) {
  die(curl_error($ch));
}

curl_close($ch);
$res = json_decode($res, true);

print_r($res);
```

> The above request returns JSON structured like this:

```json
{  
  "success":true,
  "data": {  
    "id": 1,
    "slug": "the-abandoned-dome",
    "date": "2019-03-30 22:00:00",
    "name": "The Abandoned Dome",
    "name2": "HYPERSPACE",
    "location": "Music Dome Kerkrade",
    "img": "assets/bg.jpg",
    "design": 1,
    "artists": [  
      {  
        "name": "D-Block & S-te-fan",
        "img": "assets/artists/dblock_stefan.jpg",
        "set_time": "xx:xx - xx:xx",
        "tier": 1
      },
      {  
        "name": "Frequencerz",
        "img": "assets/artists/frequencerz.jpg",
        "set_time": "xx:xx - xx:xx",
        "tier": 1
      }
      {  
        "name": "Le Shuuk",
        "img": "assets/artists/leshuuk.jpg",
        "set_time": "xx:xx - xx:xx",
        "tier": 1
      }
    ],
    "products": [  

    ],
    "galleries": [  
      {  
        "slug": "the-abandoned-dome",
        "title": "The Abandoned Dome",
        "imageCount": 128
      }
    ]
  }
}
```

This endpoint returns the event requested by slug.

### Query parameters

Parameter | Description
--------- | -----------
slug | Event slug

### Return values

The event properties are in the `data` element:

Parameter | Description
--------- | -----------
id | Event ID
slug | Event slug
date | Event date
name | Event name
name2 | Event sub name
location | Event location
img | Background image for website
design | ID of website design
artists[] | List of artists performing
products[] | List of tickets/products available
galleries[] | List of assigned galleries

# Galleries

## GET /galleries
```php
<?php
$ch = curl_init("https://api.hyperspace.one/galleries");
curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
$res = curl_exec($ch);

if (curl_errno($ch)) {
  die(curl_error($ch));
}

curl_close($ch);
$res = json_decode($res, true);

print_r($res);
```

> The above request returns JSON structured like this:

```json
{
  "success": true,
  "count": 1,
  "data": [
    {
      "slug": "the-abandoned-dome",
      "title": "The Abandoned Dome",
      "event": {
        "id": 1,
        "slug": "the-abandoned-dome",
        "name": "The Abandoned Dome",
        "date": "2019-03-30 22:00:00"
      },
      "created": {
        "date": "2019-06-06 04:06:15.000000",
        "timezone_type": 3,
        "timezone": "UTC"
      },
      "public": 1,
      "imageCount": 128
    }
  ]
}
```

<aside style="color: white;">Hidden galleries are only shown for authenticated users with right <b>manage_images</b></aside>

This endpoint lists all galleries.

### Query parameters

Parameter | Default | Description
--------- | ------- | -----------
page | 1 | Page number
per_page | 20 | Items to display per page
filter | - | Wildcard filter for slug, title
sort_field | slug | Field to sort by
sort_direction | asc | Sorting direction

### Return values

The request returns an `count` which is the number of all galleries in system. `data` contains the set of requested galleries according to pagination. The galleries have these properties:

Parameter | Description
--------- | -----------
slug | Gallery slug
title | Gallery title
event | **If event** Event information **otherwise** *null*
created | Date of creation
public | Gallery is public
imageCount | Number of images in gallery

The `imageCount` includes hidden images only when user is authenticated and has the `manage_images` right.

## GET /galleries/{slug}
```php
<?php
$ch = curl_init("https://api.hyperspace.one/galleries/{slug}");
curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
curl_setopt($ch, CURLOPT_HTTPHEADER, [
  "Authorization: Bearer xxx",
]);
$res = curl_exec($ch);

if (curl_errno($ch)) {
  die(curl_error($ch));
}

curl_close($ch);
$res = json_decode($res, true);

print_r($res);
```

> The above request returns JSON structured like this:

```json
{
  "success": true,
  "count": 1,
  "data": {
    "slug": "the-abandoned-dome",
    "title": "The Abandoned Dome",
    "event": {
      "id": 1,
      "slug": "the-abandoned-dome",
      "name": "The Abandoned Dome",
      "date": "2019-03-30 22:00:00"
    },
    "created": {
      "date": "2019-06-06 04:06:15.000000",
      "timezone_type": 3,
      "timezone": "UTC"
    },
    "public": 1,
    "imageCount": 1,
    "images": [
      {
          "id": 68,
          "title": "the-abandoned-dome-126.jpg",
          "url": "https://storage.googleapis.com/gc-media-resized/thumb%40512_2019-05-28-00-01-23-47153063.jpg",
          "creator": "John Doe",
          "created": {
              "date": "2019-05-28 00:01:24.000000",
              "timezone_type": 3,
              "timezone": "UTC"
          },
          "public": 1
      }
    ]
  }
}
```

<aside style="color: white;">Hidden galleries are only available for authenticated users with right <b>manage_images</b>, hidden images are also shown only to users with this right</aside>

This endpoint get the details and the images of a specific gallery. The images are paginated and can be filtered. 

### Query parameters

Parameter | Default | Description
--------- | ------- | -----------
slug | - | Gallery slug
page | 1 | Page number
per_page | 20 | Items to display per page
filter | - | Wildcard filter for image title
sort_field | created_at | Field to sort by
sort_direction | asc | Sorting direction

### Return values

The response includes a parameter `count` which is the number of images available in the gallery. In the `data` object, you can find the gallery details with these properties:

Parameter | Description
--------- | -----------
slug | Gallery slug
title | Gallery title
event | **If event** Event information **otherwise** *null*
created | Date of creation
public | Gallery is public
imageCount | Number of images in gallery
images[] | Array of images according to filter and pagination

Each image has these properties:

Parameter | Description
--------- | -----------
id | Image ID
title | Image title (usually file name)
url | Image URL
creator | **If assigned** Name of creator **otherwise** *null*
created | Image upload time
public | Image is public

## POST /galleries
```php
<?php
$ch = curl_init("https://api.hyperspace.one/galleries");
curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
curl_setopt($ch, CURLOPT_HTTPHEADER, [
  "Authorization: Bearer xxx",
]);
curl_setopt($ch, CURLOPT_POSTFIELDS, http_build_query([
  "slug" => "my-gallery",
  "title" => "My first gallery",
  "public" => true,
  "event" => 5,
]));
$res = curl_exec($ch);

if (curl_errno($ch)) {
  die(curl_error($ch));
}

curl_close($ch);
$res = json_decode($res, true);

print_r($res);
```

> The above request returns JSON structured like this:

```json
{
  "success": true
}
```

<aside style="color: white;">Requires user authentication with right <b>manage_images</b></aside>

This endpoint allows administrators to create new galleries.

### Query parameters

Parameter | Default | Description
--------- | ------- | -----------
slug | - | Gallery slug
title | - | Gallery title
public | 0 | Gallery is public
event | 0 | ID of event

### Failure codes

Failure Code | Meaning
---------- | -------
1001 | No slug specified
1002 | Slug already in use
1003 | No title specified

## PUT /galleries/{slug}
```php
<?php
$ch = curl_init("https://api.hyperspace.one/galleries/{slug}");
curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
curl_setopt($ch, CURLOPT_POSTFIELDS, http_build_query([
  "title" => "New title",
]));
curl_setopt($ch, CURLOPT_HTTPHEADER, [
  "Authorization: Bearer xxx",
]);
curl_setopt($ch, CURLOPT_CUSTOMREQUEST, "PUT");
$res = curl_exec($ch);

if (curl_errno($ch)) {
  die(curl_error($ch));
}

curl_close($ch);
$res = json_decode($res, true);

print_r($res);
```

> The above request returns JSON structured like this:

```json
{
  "success": true
}
```

<aside style="color: white;">Requires user authentication with right <b>manage_images</b></aside>

This endpoint allows administrators to edit galleries.

### Query parameters

Parameter | Default | Description
--------- | ------- | -----------
slug | - | Gallery slug

You can also use the parameters `title`, `public` and `event` to update these properties. Only the properties specified are updated.

### Failure codes

Failure Code | Meaning
---------- | -------
1000 | Gallery not found

## DELETE /galleries/{slug}
```php
<?php
$ch = curl_init("https://api.hyperspace.one/galleries/{slug}");
curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
curl_setopt($ch, CURLOPT_HTTPHEADER, [
  "Authorization: Bearer xxx",
]);
curl_setopt($ch, CURLOPT_CUSTOMREQUEST, "DELETE");
$res = curl_exec($ch);

if (curl_errno($ch)) {
  die(curl_error($ch));
}

curl_close($ch);
$res = json_decode($res, true);

print_r($res);
```

> The above request returns JSON structured like this:

```json
{
  "success": true
}
```

<aside style="color: white;">Requires user authentication with right <b>manage_images</b></aside>

This endpoint allows administrators to delete galleries.

### Query parameters

Parameter | Default | Description
--------- | ------- | -----------
slug | - | Gallery slug

### Failure codes

Failure Code | Meaning
---------- | -------
1000 | Gallery not found

## GET /images
```php
<?php
$ch = curl_init("https://api.hyperspace.one/images");
curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
curl_setopt($ch, CURLOPT_HTTPHEADER, [
  "Authorization: Bearer xxx",
]);
$res = curl_exec($ch);

if (curl_errno($ch)) {
  die(curl_error($ch));
}

curl_close($ch);
$res = json_decode($res, true);

print_r($res);
```

> The above request returns JSON structured like this:

```json
{
  "success": true,
  "count": 1,
  "data": {
    "images": [
      {
        "id": 10,
        "title": "My image",
        "url": "https://storage.googleapis.com/gc-media-resized/thumb%40128_2019-05-26-18-36-43-96907598.png",
        "creator": null,
        "created": {
            "date": "2019-05-26 18:55:14.000000",
            "timezone_type": 3,
            "timezone": "UTC"
        },
        "galleries": ["my-gallery"],
        "public": 1
      }
    ]
  }
}
```

<aside style="color: white;">Requires authentication with right <b>manage_images</b></aside>

This endpoint allows administrators to list all images with respect to pagination and filtering.

### Query parameters

Parameter | Default | Description
--------- | ------- | -----------
page | 1 | Page number
per_page | 20 | Items to display per page
filter | - | Wildcard filter for title
sort_field | created_at | Field to sort by
sort_direction | asc | Sorting direction

### Return values

The request returns an `count` which is the number of all images in system. `data.images` contains the set of requested images according to pagination. The images have these properties:

Parameter | Description
--------- | -----------
id | Image ID
title | Image title (usually file name)
url | Image URL
creator | **If assigned** Name of creator **otherwise** *null*
created | Image upload time
galleries[] | Array of gallery slugs the image is assigned to
public | Image is public

## POST /images/upload
```php
<?php
$ch = curl_init("https://api.hyperspace.one/images/upload");
curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
curl_setopt($ch, CURLOPT_HTTPHEADER, [
  "Authorization: Bearer xxx",
]);
curl_setopt($ch, CURLOPT_POSTFIELDS, http_build_query([
  "file" => new \CurlFile($filePath, 'image/png', 'filename.png'),
]));
$res = curl_exec($ch);

if (curl_errno($ch)) {
  die(curl_error($ch));
}

curl_close($ch);
$res = json_decode($res, true);

print_r($res);
```

> The above request returns JSON structured like this:

```json
{
  "success": true,
  "url": "https://storage.googleapis.com/gc-media-resized/thumb%40512_2019-05-28-00-01-23-47153063.jpg"
}
```

<aside style="color: white;">Requires user authentication with right <b>manage_images</b></aside>

This endpoint allows administrators to upload image files and get back the URL.

### Query parameters

Parameter | Default | Description
--------- | ------- | -----------
file | - | Image file (as multipart/form-data)

### Return values

Parameter | Description
--------- | -----------
url | Image upload URL

### Failure codes

Failure Code | Meaning
---------- | -------
1001 | No file uploaded
1002 | Invalid file extension

## POST /images/upload + publish
```php
<?php
$ch = curl_init("https://api.hyperspace.one/images/upload");
curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
curl_setopt($ch, CURLOPT_HTTPHEADER, [
  "Authorization: Bearer xxx",
]);
curl_setopt($ch, CURLOPT_POSTFIELDS, http_build_query([
  "file" => new \CurlFile($filePath, 'image/png', 'filename.png'),
  "title" => "My new image",
  "galleries" => ["my-gallery"],
]));
$res = curl_exec($ch);

if (curl_errno($ch)) {
  die(curl_error($ch));
}

curl_close($ch);
$res = json_decode($res, true);

print_r($res);
```

> The above request returns JSON structured like this:

```json
{
  "success": true,
  "id": 5
}
```

<aside style="color: white;">Requires user authentication with right <b>manage_images</b></aside>

This is an extension to the previous `POST /images/upload` endpoint and allows directly creating an image in the system using the uploaded file by specifying more parameters.

The endpoint variants are distinguished by the `title` element being specified and non-empty.

### Query parameters

Parameter | Default | Description
--------- | ------- | -----------
file | - | Image file (as multipart/form-data)
title | - | Image title
galleries[] | [] | Array of galleries the image should be assigned to (can be empty)
public | 0 | Image is public
creator | 0 | User ID of image creator

### Return values

Parameter | Description
--------- | -----------
id | ID of newly created image

### Failure codes

Failure Code | Meaning
---------- | -------
1001 | No file uploaded
1002 | Invalid file extension

## POST /images
```php
<?php
$ch = curl_init("https://api.hyperspace.one/images");
curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
curl_setopt($ch, CURLOPT_HTTPHEADER, [
  "Authorization: Bearer xxx",
]);
curl_setopt($ch, CURLOPT_POSTFIELDS, http_build_query([
  "title" => "My new image",
  "url" => "https://storage.googleapis.com/gc-media-resized/thumb%40512_2019-05-28-00-01-23-47153063.jpg",
  "public" => true,
  "creator" => 7,
  "galleries" => ["my-gallery"],
]));
$res = curl_exec($ch);

if (curl_errno($ch)) {
  die(curl_error($ch));
}

curl_close($ch);
$res = json_decode($res, true);

print_r($res);
```

> The above request returns JSON structured like this:

```json
{
  "success": true,
  "id": 5
}
```

<aside style="color: white;">Requires user authentication with right <b>manage_images</b></aside>

This endpoint allows administrators to create an image from an URL.

### Query parameters

Parameter | Default | Description
--------- | ------- | -----------
title | - | Image title
url | - | Image URL (can be obtained from `POST /images/upload`)
public | 0 | Image is public
creator | 0 | User ID of creator (optional)
galleries[] | [] | Array of gallery slugs the image should be assigned to

### Return values

Parameter | Description
--------- | -----------
id | ID of the newly created image

### Failure codes

Failure Code | Meaning
---------- | -------
1001 | No title specified
1002 | Invalid URL specified

## PUT /images/{id}
```php
<?php
$ch = curl_init("https://api.hyperspace.one/images/{id}");
curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
curl_setopt($ch, CURLOPT_POSTFIELDS, http_build_query([
  "title" => "New title",
]));
curl_setopt($ch, CURLOPT_HTTPHEADER, [
  "Authorization: Bearer xxx",
]);
curl_setopt($ch, CURLOPT_CUSTOMREQUEST, "PUT");
$res = curl_exec($ch);

if (curl_errno($ch)) {
  die(curl_error($ch));
}

curl_close($ch);
$res = json_decode($res, true);

print_r($res);
```

> The above request returns JSON structured like this:

```json
{
  "success": true
}
```

<aside style="color: white;">Requires user authentication with right <b>manage_images</b></aside>

This endpoint allows administrators to edit images.

### Query parameters

Parameter | Default | Description
--------- | ------- | -----------
id | - | Image ID

You can also use the parameters `title`, `url`, `public`, `creator` and `galleries` to update these properties. Only the properties specified are updated.

Hereby, `creator` should be the user ID of the creator or `0`. `galleries` should be an array of gallery slugs the image should be assigned to, this array can also be empty if the image should not be assigned to any gallery. To remove an image from a gallery, simply leave out the gallery slug in the array.

### Failure codes

Failure Code | Meaning
---------- | -------
1000 | Image not found

## DELETE /images/{id}
```php
<?php
$ch = curl_init("https://api.hyperspace.one/images/{id}");
curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
curl_setopt($ch, CURLOPT_HTTPHEADER, [
  "Authorization: Bearer xxx",
]);
curl_setopt($ch, CURLOPT_CUSTOMREQUEST, "DELETE");
$res = curl_exec($ch);

if (curl_errno($ch)) {
  die(curl_error($ch));
}

curl_close($ch);
$res = json_decode($res, true);

print_r($res);
```

> The above request returns JSON structured like this:

```json
{
  "success": true
}
```

<aside style="color: white;">Requires user authentication with right <b>manage_images</b></aside>

This endpoint allows administrators to delete images.

### Query parameters

Parameter | Default | Description
--------- | ------- | -----------
id | - | Image ID

### Failure codes

Failure Code | Meaning
---------- | -------
1000 | Image not found

## GET /photographers
```php
<?php
$ch = curl_init("https://api.hyperspace.one/photographers");
curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
curl_setopt($ch, CURLOPT_HTTPHEADER, [
  "Authorization: Bearer xxx",
]);
$res = curl_exec($ch);

if (curl_errno($ch)) {
  die(curl_error($ch));
}

curl_close($ch);
$res = json_decode($res, true);

print_r($res);
```

> The above request returns JSON structured like this:

```json
{
  "success": true,
  "photographers": [
    {
      "id": 33,
      "name": "John Doe"
    }
  ]
}
```

<aside style="color: white;">Requires authentication with right <b>manage_images</b></aside>

This endpoint allows getting all users with `photographer` right, allowing image managers without user access to search for a creator.

### Return values

Parameter | Description
--------- | -----------
id | User ID
name | User full name

# Orders

## POST /order
```php
<?php
$ch = curl_init("https://api.hyperspace.one/order");
curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
curl_setopt($ch, CURLOPT_POSTFIELDS, http_build_query([
  "captcha" => "xxx",
  "firstname" => "John",
  "lastname" => "Doe",
  "email" => "john.doe@example.com",
  "address" => "Roermonder Str. 45",
  "postcode" => "52134",
  "city" => "Herzogenrath",
  "country" => "Germany",
  "paymentMethod" => "paypal",
  "products" => [
    5 => 1,
  ],
]));
curl_setopt($ch, CURLOPT_CUSTOMREQUEST, "PUT");
$res = curl_exec($ch);

if (curl_errno($ch)) {
  die(curl_error($ch));
}

curl_close($ch);
$res = json_decode($res, true);

print_r($res);
```

> The above request returns JSON structured like this:

```json
{
  "success": true,
  "id": 5,
  "token": "xxx",
  "payment": ""
}
```

New orders should be placed using this endpoint.

As soon as a captcha has been solved correctly, this information is saved within the browser session on the API server. This means if there is an error in the order process (excluding captcha error), the captcha must not be solved again. The information is reset as soon as the order was successful. Therefore, another order requires a new captcha.

### Query parameters

Parameter | Description
--------- | -----------
captcha | **If captcha activated** Captcha token
firstname | First name
lastname | Last name
email | Email address
address | Street address
postcode | Postcode
city | City
country | Country (currently supported: `Germany`, `Netherlands`, `Belgium`, `Luxembourg`)
paymentMethod | Payment method or `promoter` for promoter order
products[] | Array with product IDs as keys and quantity as value

There are some additional, optional parameters:

Parameter | Description
--------- | -----------
paymentInfo | Additional payment information (currently only bank code for iDEAL payments)
reflink | Hash of used reflink

### Return values

Parameter | Description
--------- | -----------
id | ID of newly placed order
token | Token of order
payment | Payment HTML to be displayed to the customer

### Failure codes

Failure Code | Meaning
---------- | -------
1000 | Invalid captcha token
1001 | No first name specified
1002 | No last name specified
1003 | Invalid email
1004 | No address specified
1005 | No postcode specified
1006 | No city specified
1007 | Invalid country
1008 | No products specified
1009 | Unknown products specified
1010 | Invalid quantity specified
1011 | Product is not available in general or in this quantity
1012 | Invalid payment method (unknown, or `promoter` when no authenticated user or no `promoter` right)
1013 | No products in order

## GET /order/{order_id}/{token}
```php
<?php
$ch = curl_init("https://api.hyperspace.one/order/{order_id}/{token}");
curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
$res = curl_exec($ch);

if (curl_errno($ch)) {
  die(curl_error($ch));
}

curl_close($ch);
$res = json_decode($res, true);

print_r($res);
```

> The above request returns JSON structured like this:

```json
{
  "success": true,
  "amount": 39.98,
  "status": "paid",
  "firstname": "test",
  "lastname": "facilitator",
  "email": "mail-facilitator@spacion.de",
  "address": "ESpachstr. 1",
  "postcode": "79111",
  "city": "Freiburg",
  "country": "DE",
  "shipping": 0,
  "shipping_taxrate": 0,
  "created": {
    "date": "2019-03-08 00:44:48.000000",
    "timezone_type": 3,
    "timezone": "UTC"
  },
  "updated": {
    "date": "2019-03-08 00:44:48.000000",
    "timezone_type": 3,
    "timezone": "UTC"
  },
  "payment_provider": "paypal",
  "items": [
    {
      "name": "Early Bird",
      "gross": 19.99,
      "net": 18.339449541284,
      "tax": 1.6505504587156,
      "amount": 2,
      "taxrate": 9,
      "pricePrefix": "",
      "priceSuffix": "€",
      "id": 4
    }
  ]
}
```

Use this endpoint to get details of an existing order.

### Query parameters

Parameter | Default | Description
--------- | ------- | -----------
order_id | - | Order ID
token | - | Order token

### Return values

Parameter | Description
--------- | -----------
amount | Total order amount
status | Order status (can be `paid`, `unpaid`, `cancelled`)
firstname | First name
lastname | Last name
email | Email address
address | Street address
postcode | Postcode
city | City
country | Country code (ISO 3166 ALPHA-2)
shipping | Shipping costs
shipping_taxrate | Included taxrate in shipping
created | Order creation time
updated | Order update time (usually payment/cancellation time)
payment_provider | Payment provider
items[] | Order items

If the order is unpaid, an element `payment` will contain the HTML code to be displayed to the customer for the payment.

### Failure codes

Failure Code | Meaning
---------- | -------
1000 | Order not found or token invalid

## GET /orders
```php
<?php
$ch = curl_init("https://api.hyperspace.one/orders");
curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
curl_setopt($ch, CURLOPT_HTTPHEADER, [
  "Authorization: Bearer xxx",
]);
$res = curl_exec($ch);

if (curl_errno($ch)) {
  die(curl_error($ch));
}

curl_close($ch);
$res = json_decode($res, true);

print_r($res);
```

> The above request returns JSON structured like this:

```json
{
  "success": true,
  "itemCount": 1,
  "paymentsConfirmed": 44.97,
  "paymentsPipeline": 0,
  "items": [
    {
      "id": 1,
      "status": "paid",
      "firstname": "John",
      "lastname": "Doe",
      "email": "john.doe@example.com",
      "address": "Roermonder Str. 45",
      "postcode": "52134",
      "city": "Herzogenrath",
      "country": "DE",
      "shipping": 4.99,
      "shipping_taxrate": 19,
      "created_at": "2018-11-28 13:06:21",
      "updated_at": "2018-11-28 13:06:21",
      "payment_provider": "paypal",
      "payment_id": "xxx",
      "token": "xxx",
      "token_intern": "xxx",
      "user": 10,
      "reflink": "",
      "payment_info": "",
      "amount": 44.97,
      "items": [
        {
          "id": 187,
          "order_id": 1,
          "product_id": 4,
          "quantity": 2,
          "price": 19.99,
          "taxrate": 9,
          "created_at": "2018-11-28 13:06:21",
          "updated_at": "2018-11-28 13:06:21"
        }
      ]
    }
  ]
}
```

<aside style="color: white;">Requires user authentication with right <b>view_orders</b></aside>

This endpoint allows administrators to get a paginated list of all orders in the system.

### Query parameters

Parameter | Default | Description
--------- | ------- | -----------
page | 1 | Page number
per_page | 20 | Items to display per page
filter | - | Wildcard filter for firstname, lastname, email, token, payment_id
sort_field | created_at | Field to sort by
sort_direction | asc | Sorting direction

### Return values

The request returns an `itemCount` which is the number of all orders in system. `paymentsConfirmed` is the sum of paid orders whereas `paymentsPipeline` is the sum of orders waiting for payment (not cancelled orders). `items` contains the set of requested orders according to pagination. The orders have these properties:

Parameter | Description
--------- | -----------
id | Order ID
status | Order status (can be `paid`, `unpaid`, `cancelled`)
firstname | First name
lastname | Last name
email | Email address
address | Street address
postcode | Postcode
city | City
country | Country code (ISO 3166 ALPHA-2)
shipping | Shipping costs
shipping_taxrate | Included taxrate in shipping
created | Order creation time
updated | Order update time (usually payment/cancellation time)
payment_provider | Payment provider
payment_id | Payment ID at provider
token | Order token
token_intern | Internal order token (used e.g. for IPN)
user | User ID
reflink | Hash of used reflink
payment_info | Additional payment info
amount | Order amount
items[] | Order items

## POST /orders/{id}
```php
<?php
$ch = curl_init("https://api.hyperspace.one/orders/{id}");
curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
curl_setopt($ch, CURLOPT_CUSTOMREQUEST, "POST");
curl_setopt($ch, CURLOPT_HTTPHEADER, [
  "Authorization: Bearer xxx",
]);
$res = curl_exec($ch);

if (curl_errno($ch)) {
  die(curl_error($ch));
}

curl_close($ch);
$res = json_decode($res, true);

print_r($res);
```

> The above request returns JSON structured like this:

```json
{
  "success": true
}
```

<aside style="color: white;">Requires user authentication with right <b>view_orders</b></aside>

This endpoint allows administrators to resend the confirmation email for a specific order. This order **must be paid**.

### Query parameters

Parameter | Default | Description
--------- | ------- | -----------
id | - | Order ID

## ANY /order/{order_id}/{token}/ipn/{payment_method}

> The request and response is individual for each payment module and not relevant as it is only processed by the payment provider.

This endpoint is called directly by the payment provider and calls the IPN routine of the payment module. If the payment was successful, this means automatically provisioning of user account (if not already existing) and tickets.

### Query parameters

Parameter | Description
--------- | -----------
order_id | Order ID
token | Order token
payment_method | Payment method

Additional parameters can be specified by the payment provider and are passed to the IPN routine. 

## POST /checkout/paypal
```php
<?php
$ch = curl_init("https://api.hyperspace.one/checkout/paypal");
curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
curl_setopt($ch, CURLOPT_POSTFIELDS, http_build_query([
  "orderId" => "xxx",
]));
curl_setopt($ch, CURLOPT_CUSTOMREQUEST, "PUT");
$res = curl_exec($ch);

if (curl_errno($ch)) {
  die(curl_error($ch));
}

curl_close($ch);
$res = json_decode($res, true);

print_r($res);
```

> The above request returns JSON structured like this:

```json
{
  "success": true,
  "orderId": 5,
  "orderToken": "xxx"
}
```

This endpoint is used to capture a confirmed payment via PayPal Express Checkout. The checkout process needs to be implemented in frontend, GroundControl API then fetches the specified Express order from PayPal, creates it in the system and marks it as paid.

### Query parameters

Parameter | Description
--------- | -----------
orderId | PayPal Express Checkout order ID
reflink | **Optional** Hash of used reflink

### Return values

Parameter | Description
--------- | -----------
orderId | ID of newly placed order
orderToken | Token of order

### Failure codes

Failure Code | Meaning
---------- | -------
1000 | No transaction ID
1001 | PayPal error (see `error`)
1002 | Incorrect payment type
1003 | Incorrent payment status
1004 | Invalid purchase units
1005 | Invalid payee
1006 | Invalid description
1007 | Product not found
1008 | Product not available
1009 | Invalid currency
1010 | Product is underpaid
1011 | Order already processed

## GET /ideal/banks
```php
<?php
$ch = curl_init("https://api.hyperspace.one/ideal/banks");
curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
$res = curl_exec($ch);

if (curl_errno($ch)) {
  die(curl_error($ch));
}

curl_close($ch);
$res = json_decode($res, true);

print_r($res);
```

> The above request returns JSON structured like this:

```json
[
    {
        "code": "ABNANL2A",
        "name": "ABN AMRO"
    },
    {
        "code": "ASNBNL21",
        "name": "ASN Bank"
    },
    {
        "code": "BUNQNL2A",
        "name": "bunq"
    }
]
```
<aside style="color: white;">Requires correct iDEAL API token</aside>

This endpoint returns the banks available for use with iDEAL. The results are cached for performance reasons.

Parameter | Description
--------- | -----------
code | Bank code, needs to be sent when doing payment
name | Name of the bank

### Failure codes

Failure Code | Meaning
---------- | -------
1000 | API fail (can be caused by wrong API token)

# Tickets

## GET /tickets
```php
<?php
$ch = curl_init("https://api.hyperspace.one/tickets");
curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
curl_setopt($ch, CURLOPT_HTTPHEADER, [
  "Authorization: Bearer xxx",
]);
$res = curl_exec($ch);

if (curl_errno($ch)) {
  die(curl_error($ch));
}

curl_close($ch);
$res = json_decode($res, true);

print_r($res);
```

> The above request returns JSON structured like this:

```json
[
    {
        "orderId": 204,
        "orderDate": "2019-02-02",
        "buyer": {
            "firstname": "John",
            "lastname": "Doe",
            "address": "Roermonder Str. 45",
            "postcode": "52134",
            "city": "Herzogenrath",
            "country": "Germany",
            "email": "richard@hyperspace.one"
        },
        "gross": 19.99,
        "shipping": 0,
        "shipping_taxrate": 0,
        "tickets": [
            {
                "id": 66,
                "name": "",
                "product_id": 4,
                "pdf_url": "https://api.hyperspace.one/ticket/66/pdf/647ij32yz9c58n",
                "pass_url": "https://api.hyperspace.one/ticket/66/pkpass/647ij32yz9c58n",
                "token": "647ij32yz9c58n",
                "qrcode": "66-647ij32yz9c58n",
                "product_name": "Early Bird",
                "event": {
                    "id": 1,
                    "slug": "the-abandoned-dome",
                    "date": "2019-03-30 22:00:00",
                    "name": "The Abandoned Dome",
                    "name2": "HYPERSPACE",
                    "location": "Music Dome Kerkrade",
                    "img": "assets/bg.jpg",
                    "design": 1,
                    "artists": [
                        {
                            "name": "D-Block & S-te-fan",
                            "img": "assets/artists/dblock_stefan.jpg",
                            "set_time": "xx:xx - xx:xx",
                            "tier": 1
                        }
                    ],
                    "products": [],
                    "galleries": [
                        {
                            "slug": "the-abandoned-dome",
                            "title": "The Abandoned Dome",
                            "imageCount": 128
                        }
                    ]
                }
            }
        ]
    }
]
```
<aside style="color: white;">Requires user authentication</aside>

Gives back all tickets the user have, grouped by order

### Query parameters

None

### Return values

The request returns a list of orders containing order information and the tickets assigned with the order.

Parameter | Description
--------- | -----------
orderId | ID of the order
orderDate | Date of the order
buyer[] | Buyer information
gross | Amount of the whole order
shipping | Shipping costs of the order
shipping_taxrate | Taxrate on shipping costs
tickets[] | Array of tickets

The following information is available within the tickets array:

Parameter | Description
--------- | -----------
id | Ticket ID
name | Name of the ticket holder (if personalized)
product_id | ID of the ticket product
pdf_url | Full URL to PDF
pass_url | Full URL to Wallet Pass
token | Token for URL authentication
qrcode | Content of QR code (to be scanned at entry)
product_name | Name of the ticket product
event | **If assigned to event** Event information

## POST /ticket/{id}/personalize
```php
<?php
$ch = curl_init("https://api.hyperspace.one/ticket/$id/personalize");
curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
curl_setopt($ch, CURLOPT_POSTFIELDS, http_build_query([
  "name" => "John Doe",
]));
curl_setopt($ch, CURLOPT_HTTPHEADER, [
  "Authorization: Bearer xxx",
]);
$res = curl_exec($ch);

if (curl_errno($ch)) {
  die(curl_error($ch));
}

curl_close($ch);
$res = json_decode($res, true);

print_r($res);
```

> The above request returns JSON structured like this:

```json
{
  "success": true
}
```
<aside style="color: white;">Requires user authentication</aside>

You should use this endpoint to personalize a ticket which needs personalization. A ticket can be personalized as long as defined in the ticket product. Unlimited name changes are allowed.

By default, a ticket is personalized to the buyers name.

### Query parameters

Parameter | Description
--------- | -----------
id | Ticket ID
name | Name of ticket owner

### Failure codes

Failure Code | Meaning
---------- | -------
1000 | Personalization period over
1001 | No name specified

## GET /ticket/{id}/pdf/{token}
This endpoint returns a PDF document directly as stream. The only authentication is done by ticket `id` and `token`, this data can be found in endpoint `GET /tickets`.

You can send a user who wants to download his ticket directly to this URL filled with his ticket information.

## GET /ticket/{id}/pkpass/{token}
This endpoint returns a Wallet file (mostly used by Apple) for a ticket directly as download. The only authentication is done by ticket `id` and `token`, this data can be found in endpoint `GET /tickets`.

You can send a user who wants to download his wallet file directly to this URL filled with his ticket information. If a user calls this URL e.g. with an iPhone, he will be asked directly to add the ticket to his wallet app. This also means that the user gets a notification by his app before the event.

# Ranks

## GET /ranks
```php
<?php
$ch = curl_init("https://api.hyperspace.one/ranks");
curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
$res = curl_exec($ch);

if (curl_errno($ch)) {
  die(curl_error($ch));
}

curl_close($ch);
$res = json_decode($res, true);

print_r($res);
```

> The above request returns JSON structured like this:

```json
[
  {
    "id": 1,
    "name": "Visitor",
    "points": 0,
    "features": []
  },
  {
    "id": 2,
    "name": "Flight Engineer",
    "points": 100,
    "features": []
  },
  {
    "id": 3,
    "name": "Commander",
    "points": 300,
    "features": ["Free entry"]
  }
]
```

This endpoint returns all ranks configured with GroundControl.

### Return values

Each rank has the following properties:

Parameter | Description
--------- | -----------
id | Rank ID
name | Name of rank
points | Points required
features[] | List of features coming with rank

## POST /ranks
```php
<?php
$ch = curl_init("https://api.hyperspace.one/ranks");
curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
curl_setopt($ch, CURLOPT_HTTPHEADER, [
  "Authorization: Bearer xxx",
]);
curl_setopt($ch, CURLOPT_POSTFIELDS, http_build_query([
  "name" => "Visitor",
  "points" => 0,
]));
$res = curl_exec($ch);

if (curl_errno($ch)) {
  die(curl_error($ch));
}

curl_close($ch);
$res = json_decode($res, true);

print_r($res);
```

> The above request returns JSON structured like this:

```json
{
  "success": true,
  "id": 1
}
```

<aside style="color: white;">Requires user authentication with right <b>settings</b></aside>

This endpoint allows administrators to create new ranks.

### Query parameters

Parameter | Default | Description
--------- | ------- | -----------
name | - | Rank name
points | - | Points required to reach rank

### Return values

Parameter | Description
--------- | -----------
id | ID of newly created rank

### Failure codes

Failure Code | Meaning
---------- | -------
1001 | No name specified
1002 | Invalid points specified

## PUT /ranks/{id}
```php
<?php
$ch = curl_init("https://api.hyperspace.one/ranks/{id}");
curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
curl_setopt($ch, CURLOPT_POSTFIELDS, http_build_query([
  "name" => "Flight Engineer",
]));
curl_setopt($ch, CURLOPT_HTTPHEADER, [
  "Authorization: Bearer xxx",
]);
curl_setopt($ch, CURLOPT_CUSTOMREQUEST, "PUT");
$res = curl_exec($ch);

if (curl_errno($ch)) {
  die(curl_error($ch));
}

curl_close($ch);
$res = json_decode($res, true);

print_r($res);
```

> The above request returns JSON structured like this:

```json
{
  "success": true
}
```

<aside style="color: white;">Requires user authentication with right <b>settings</b></aside>

This endpoint allows administrators to edit ranks.

### Query parameters

Parameter | Default | Description
--------- | ------- | -----------
id | - | Rank ID

You can also use the parameters `name` and `points` to update these properties. Parameters not specified are ignored and not updated.

## DELETE /ranks/{id}
```php
<?php
$ch = curl_init("https://api.hyperspace.one/ranks/{id}");
curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
curl_setopt($ch, CURLOPT_HTTPHEADER, [
  "Authorization: Bearer xxx",
]);
curl_setopt($ch, CURLOPT_CUSTOMREQUEST, "DELETE");
$res = curl_exec($ch);

if (curl_errno($ch)) {
  die(curl_error($ch));
}

curl_close($ch);
$res = json_decode($res, true);

print_r($res);
```

> The above request returns JSON structured like this:

```json
{
  "success": true
}
```

<aside style="color: white;">Requires user authentication with right <b>settings</b></aside>

This endpoint allows administrators to delete a rank.

### Query parameters

Parameter | Default | Description
--------- | ------- | -----------
id | - | Rank ID

## GET /ranks/features
```php
<?php
$ch = curl_init("https://api.hyperspace.one/ranks/features");
curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
$res = curl_exec($ch);

if (curl_errno($ch)) {
  die(curl_error($ch));
}

curl_close($ch);
$res = json_decode($res, true);

print_r($res);
```

> The above request returns JSON structured like this:

```json
[
  {
    "id":1,
    "feature":"Freier Eintritt",
    "points":500
  }
]
```

This endpoint returns all rank features configured with GroundControl.

### Return values

Each rank has the following properties:

Parameter | Description
--------- | -----------
id | Feature ID
feature | Feature description
points | Points required

## POST /ranks/features
```php
<?php
$ch = curl_init("https://api.hyperspace.one/ranks/features");
curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
curl_setopt($ch, CURLOPT_HTTPHEADER, [
  "Authorization: Bearer xxx",
]);
curl_setopt($ch, CURLOPT_POSTFIELDS, http_build_query([
  "feature" => "Free entrance",
  "points" => 500,
]));
$res = curl_exec($ch);

if (curl_errno($ch)) {
  die(curl_error($ch));
}

curl_close($ch);
$res = json_decode($res, true);

print_r($res);
```

> The above request returns JSON structured like this:

```json
{
  "success": true,
  "id": 1
}
```

<aside style="color: white;">Requires user authentication with right <b>settings</b></aside>

This endpoint allows administrators to create new rank features.

### Query parameters

Parameter | Default | Description
--------- | ------- | -----------
feature | - | Feature description
points | - | Points required to activate feature

### Return values

Parameter | Description
--------- | -----------
id | ID of newly created feature

### Failure codes

Failure Code | Meaning
---------- | -------
1001 | No description specified
1002 | Invalid points specified

## PUT /ranks/features/{id}
```php
<?php
$ch = curl_init("https://api.hyperspace.one/ranks/features/{id}");
curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
curl_setopt($ch, CURLOPT_POSTFIELDS, http_build_query([
  "name" => "Free Entrance",
]));
curl_setopt($ch, CURLOPT_HTTPHEADER, [
  "Authorization: Bearer xxx",
]);
curl_setopt($ch, CURLOPT_CUSTOMREQUEST, "PUT");
$res = curl_exec($ch);

if (curl_errno($ch)) {
  die(curl_error($ch));
}

curl_close($ch);
$res = json_decode($res, true);

print_r($res);
```

> The above request returns JSON structured like this:

```json
{
  "success": true
}
```

<aside style="color: white;">Requires user authentication with right <b>settings</b></aside>

This endpoint allows administrators to edit rank features.

### Query parameters

Parameter | Default | Description
--------- | ------- | -----------
id | - | Feature ID

You can also use the parameters `feature` and `points` to update these properties. Parameters not specified are ignored and not updated.

## DELETE /ranks/features/{id}
```php
<?php
$ch = curl_init("https://api.hyperspace.one/ranks/features/{id}");
curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
curl_setopt($ch, CURLOPT_HTTPHEADER, [
  "Authorization: Bearer xxx",
]);
curl_setopt($ch, CURLOPT_CUSTOMREQUEST, "DELETE");
$res = curl_exec($ch);

if (curl_errno($ch)) {
  die(curl_error($ch));
}

curl_close($ch);
$res = json_decode($res, true);

print_r($res);
```

> The above request returns JSON structured like this:

```json
{
  "success": true
}
```

<aside style="color: white;">Requires user authentication with right <b>settings</b></aside>

This endpoint allows administrators to delete a rank feature.

### Query parameters

Parameter | Default | Description
--------- | ------- | -----------
id | - | Feature ID

## GET /achievements
```php
<?php
$ch = curl_init("https://api.hyperspace.one/achievements");
curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
curl_setopt($ch, CURLOPT_HTTPHEADER, [
  "Authorization: Bearer xxx",
]);
$res = curl_exec($ch);

if (curl_errno($ch)) {
  die(curl_error($ch));
}

curl_close($ch);
$res = json_decode($res, true);

print_r($res);
```

> The above request returns JSON structured like this:

```json
[
  {
      "id": 15,
      "achievement": "promoter_order",
      "points": 4,
      "date": {
          "date": "2019-02-08 17:48:28.000000",
          "timezone_type": 3,
          "timezone": "UTC"
      }
  },
  {
      "id": 13,
      "achievement": "promoter_order",
      "points": 8,
      "date": {
          "date": "2019-02-08 17:48:18.000000",
          "timezone_type": 3,
          "timezone": "UTC"
      }
  }
]
```

<aside style="color: white;">Requires user authentication</aside>

This endpoint returns all achievements reached with pagination. The last achievements are shown first.

### Query parameters

Parameter | Default | Description
--------- | ------- | -----------
page | 1 | Page number
per_page | 20 | Items to display per page

### Return values

Each achievement has the following properties:

Parameter | Description
--------- | -----------
id | ID of achievement
achievement | Achievement type
points | Points assigned
date | Date of achievement

## POST /achievement
```php
<?php
$ch = curl_init("https://api.hyperspace.one/achievement");
curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
curl_setopt($ch, CURLOPT_HTTPHEADER, [
  "Authorization: Bearer xxx",
]);
curl_setopt($ch, CURLOPT_POSTFIELDS, http_build_query([
  "user" => 1,
  "achievement" => "promoter_order",
  "points" => 5,
]));
$res = curl_exec($ch);

if (curl_errno($ch)) {
  die(curl_error($ch));
}

curl_close($ch);
$res = json_decode($res, true);

print_r($res);
```

> The above request returns JSON structured like this:

```json
{
  "success": true,
  "pushTokens": []
}
```

<aside style="color: white;">Requires user authentication with right <b>create_achievements</b></aside>

This endpoint allows administrators to create new achievements for a user.

### Query parameters

Parameter | Default | Description
--------- | ------- | -----------
user | - | User ID or email address
achievement | - | Achievement type
points | - | Points to be assigned

### Return values

Parameter | Description
--------- | -----------
pushTokens | Array of push tokens (strings) of user

### Failure codes

Failure Code | Meaning
---------- | -------
1000 | User not found
1001 | No achievement specified
1002 | Invalid points

# Promoter

## GET /promoter/orders
```php
<?php
$ch = curl_init("https://api.hyperspace.one/promoter/orders");
curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
curl_setopt($ch, CURLOPT_HTTPHEADER, [
  "Authorization: Bearer xxx",
]);
$res = curl_exec($ch);

if (curl_errno($ch)) {
  die(curl_error($ch));
}

curl_close($ch);
$res = json_decode($res, true);

print_r($res);
```

> The above request returns JSON structured like this:

```json
{
  "success": true,
  "itemCount": 1,
  "items": [
    {
      "id": 207,
      "status": "paid",
      "firstname": "John",
      "lastname": "Doe",
      "email": "john.doe@example.com",
      "address": "Roermonder Str. 45",
      "postcode": "52134",
      "city": "Herzogenrath",
      "country": "Germany",
      "createdAt": {
        "date": "2019-02-08 17:47:00.000000",
        "timezone_type": 3,
        "timezone": "UTC"
      },
      "updatedAt": {
        "date": "2019-02-08 17:47:00.000000",
        "timezone_type": 3,
        "timezone": "UTC"
      },
      "reflink": "",
      "amount": 5.05,
      "items": [
        {
          "productId": 1,
          "productName": "Early Bird",
          "quantity": 1,
          "price": 5,
          "singlePrice": 5
        }
      ]
    }
  ]
}
```

<aside style="color: white;">Requires user authentication with right <b>promoter</b></aside>

This endpoint allows promoters to view orders they have placed. As they have entered the customer data by theirselves, there is no privacy problem in showing this data to promoters.

### Query parameters

Parameter | Default | Description
--------- | ------- | -----------
page | 1 | Page number
per_page | 20 | Elements per page
filter | - | Wildcard filter for `firstname`, `lastname`, `email`, `token` or `payment_id`
sort_field | created_at | Field to sort by
sort_direction | asc | Sort direction

### Return values

The request returns an `itemCount` which is the number of all orders of this promoter in system. `items` contains the set of requested orders according to pagination. The orders have these properties:

Parameter | Description
--------- | -----------
id | Order ID
status | Order status (usually `paid`)
firstname | Buyer first name
lastname | Buyer last name
email | Buyer email address
address | Buyer address
postcode | Buyer postcode
city | Buyer city
country | Buyer country
createdAt | Date of order placed
updatedAt | Date of order modified
reflink | Reflink used
amount | Order amount
items[] | Products ordered

# Affiliate

## GET /affiliate
```php
<?php
$ch = curl_init("https://api.hyperspace.one/affiliate");
curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
curl_setopt($ch, CURLOPT_HTTPHEADER, [
  "Authorization: Bearer xxx",
]);
$res = curl_exec($ch);

if (curl_errno($ch)) {
  die(curl_error($ch));
}

curl_close($ch);
$res = json_decode($res, true);

print_r($res);
```

> The above request returns JSON structured like this:

```json
{
  "success": true,
  "data": [
    {
      "hash": "ylnfc7",
      "user_id": 9,
      "clicks": 1,
      "orders": 1,
      "products": {
        "4": 3
      },
      "status": true,
      "description": ""
    }
  ]
}
```

<aside style="color: white;">Requires user authentication with right <b>affiliate</b></aside>

This endpoint allows affiliates to view their reflinks and statistics.

### Return values

The reflinks are listed in the `data` element. Each reflink has these properties:

Parameter | Description
--------- | -----------
hash | Reflink hash
user_id | Assigned user (logged in user)
clicks | Performed clicks
orders | Placed orders (only paid orders are counted)
products[] | Array of buyed products (by ID; key) and quantities (value)
status | Reflink active
description | Reflink description 

## POST /affiliate
```php
<?php
$ch = curl_init("https://api.hyperspace.one/affiliate");
curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
curl_setopt($ch, CURLOPT_HTTPHEADER, [
  "Authorization: Bearer xxx",
]);
curl_setopt($ch, CURLOPT_POSTFIELDS, http_build_query([
  "description" => "My new link",
]));
$res = curl_exec($ch);

if (curl_errno($ch)) {
  die(curl_error($ch));
}

curl_close($ch);
$res = json_decode($res, true);

print_r($res);
```

> The above request returns JSON structured like this:

```json
{
  "success": true,
  "hash": "xxx"
}
```

<aside style="color: white;">Requires user authentication with right <b>affiliate</b></aside>

This endpoint allows affiliates to create new reflinks.

### Query parameters

Parameter | Default | Description
--------- | ------- | -----------
description | "" | Reflink description

### Return values

Parameter | Description
--------- | -----------
hash | Hash of newly created reflink

## PUT /affiliate/{hash}
```php
<?php
$ch = curl_init("https://api.hyperspace.one/affiliate/{hash}");
curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
curl_setopt($ch, CURLOPT_HTTPHEADER, [
  "Authorization: Bearer xxx",
]);
curl_setopt($ch, CURLOPT_POSTFIELDS, http_build_query([
  "description" => "My new description",
  "status" => true,
]));
curl_setopt($ch, CURLOPT_CUSTOMREQUEST, "PUT");
$res = curl_exec($ch);

if (curl_errno($ch)) {
  die(curl_error($ch));
}

curl_close($ch);
$res = json_decode($res, true);

print_r($res);
```

> The above request returns JSON structured like this:

```json
{
  "success": true
}
```

<aside style="color: white;">Requires user authentication with right <b>affiliate</b></aside>

This endpoint allows affiliates to edit their reflinks.

Administrators with **reflinks** right can use this endpoint to update any reflink.

### Query parameters

Parameter | Default | Description
--------- | ------- | -----------
hash | - | Reflink hash
description | "" | Reflink description
status | false | Reflink active

## DELETE /affiliate/{hash}
```php
<?php
$ch = curl_init("https://api.hyperspace.one/affiliate/{hash}");
curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
curl_setopt($ch, CURLOPT_HTTPHEADER, [
  "Authorization: Bearer xxx",
]);
curl_setopt($ch, CURLOPT_CUSTOMREQUEST, "DELETE");
$res = curl_exec($ch);

if (curl_errno($ch)) {
  die(curl_error($ch));
}

curl_close($ch);
$res = json_decode($res, true);

print_r($res);
```

> The above request returns JSON structured like this:

```json
{
  "success": true
}
```

<aside style="color: white;">Requires user authentication with right <b>affiliate</b></aside>

This endpoint allows affiliates to delete their reflinks.

Administrators with **reflinks** right can use this endpoint to delete any reflink.

### Query parameters

Parameter | Default | Description
--------- | ------- | -----------
hash | - | Reflink hash

## POST /affiliate/click/{hash}
```php
<?php
$ch = curl_init("https://api.hyperspace.one/affiliate/click/{hash}");
curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
curl_setopt($ch, CURLOPT_CUSTOMREQUEST, "POST");
$res = curl_exec($ch);

if (curl_errno($ch)) {
  die(curl_error($ch));
}

curl_close($ch);
$res = json_decode($res, true);

print_r($res);
```

> The above request returns JSON structured like this:

```json
{
  "success": true
}
```

This endpoint increases a reflink click count by 1.

### Query parameters

Parameter | Default | Description
--------- | ------- | -----------
hash | - | Reflink hash

### Failure codes

Failure Code | Meaning
---------- | -------
1000 | Reflink not found or inactive

## GET /affiliate/orders
```php
<?php
$ch = curl_init("https://api.hyperspace.one/affiliate/orders");
curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
curl_setopt($ch, CURLOPT_HTTPHEADER, [
  "Authorization: Bearer xxx",
]);
$res = curl_exec($ch);

if (curl_errno($ch)) {
  die(curl_error($ch));
}

curl_close($ch);
$res = json_decode($res, true);

print_r($res);
```

> The above request returns JSON structured like this:

```json
{
  "success": true,
  "itemCount": 1,
  "items": [
    {
      "id": 212,
      "status": "paid",
      "firstname": "John",
      "lastname": "D.",
      "city": "Herzogenrath",
      "country": "Germany",
      "createdAt": {
        "date": "2019-02-26 20:33:19.000000",
        "timezone_type": 3,
        "timezone": "UTC"
      },
      "updatedAt": {
        "date": "2019-02-26 20:33:19.000000",
        "timezone_type": 3,
        "timezone": "UTC"
      },
      "reflink": "ylnfc7",
      "amount": 39.98,
      "items": [
        {
          "productId": 4,
          "productName": "Early Bird",
          "quantity": 2,
          "price": 39.98,
          "singlePrice": 39.98
        }
      ]
    }
  ]
}
```

<aside style="color: white;">Requires user authentication with right <b>promoter</b></aside>

This endpoint allows affiliates to view orders that were placed using their reflink. Only paid orders are shown. The newest orders are shown first.

### Query parameters

Parameter | Default | Description
--------- | ------- | -----------
page | 1 | Page number
per_page | 20 | Elements per page

### Return values

The request returns an `itemCount` which is the number of all paid orders of this affiliate in system. `items` contains the set of requested orders according to pagination. The orders have these properties:

Parameter | Description
--------- | -----------
id | Order ID
status | Order status (can only be `paid`)
firstname | Buyer first name
lastname | Buyer last name (only first character for privacy reasons)
city | Buyer city
country | Buyer country
createdAt | Date of order placed
updatedAt | Date of order modified (usually payment date)
reflink | Reflink used
amount | Order amount
items[] | Products ordered

## GET /reflinks
```php
<?php
$ch = curl_init("https://api.hyperspace.one/reflinks");
curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
curl_setopt($ch, CURLOPT_HTTPHEADER, [
  "Authorization: Bearer xxx",
]);
$res = curl_exec($ch);

if (curl_errno($ch)) {
  die(curl_error($ch));
}

curl_close($ch);
$res = json_decode($res, true);

print_r($res);
```

> The above request returns JSON structured like this:

```json
{
  "success": true,
  "data": [
    {
      "hash": "ylnfc7",
      "user_id": 9,
      "clicks": 1,
      "orders": 1,
      "products": {
        "4": 3
      },
      "status": true,
      "description": ""
    }
  ]
}
```

<aside style="color: white;">Requires user authentication with right <b>reflinks</b></aside>

This endpoint allows administrators to view all reflinks in system and their statistics.

### Return values

The reflinks are listed in the `data` element. Each reflink has these properties:

Parameter | Description
--------- | -----------
hash | Reflink hash
user_id | Assigned user
clicks | Performed clicks
orders | Placed orders (only paid orders are counted)
products[] | Array of buyed products (by ID; key) and quantities (value)
status | Reflink active
description | Reflink description

# Notifications

## GET /notification/{user}
```php
<?php
$ch = curl_init("https://api.hyperspace.one/notification/{user}");
curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
curl_setopt($ch, CURLOPT_HTTPHEADER, [
  "Authorization: Bearer xxx",
]);
$res = curl_exec($ch);

if (curl_errno($ch)) {
  die(curl_error($ch));
}

curl_close($ch);
$res = json_decode($res, true);

print_r($res);
```

> The above request returns JSON structured like this:

```json
{
  "success": true,
  "notifications": [
    {
      "title": "Test notification",
      "text": "Test content",
      "deeplink": "",
      "createdAt": {
        "date": "2019-03-09 21:33:20.000000",
        "timezone_type": 3,
        "timezone": "UTC"
      },
      "updatedAt": {
        "date": "2019-03-09 21:33:21.000000",
        "timezone_type": 3,
        "timezone": "UTC"
      },
      "sent": true
    }
  ]
}
```

<aside style="color: white;">Requires user authentication with right <b>send_notifications</b></aside>

This endpoint allows administrators to see notifications that have been sent to a user. The newest notifications are shown first.

### Query parameters

Parameter | Default | Description
--------- | ------- | -----------
user | - | User ID
page | 1 | Page number
per_page | 20 | Elements per page

### Return values

Every notification has these properties:

Parameter | Description
--------- | -----------
title | Title of notification
text | Content of notification
deeplink | App deeplink
createdAt | Notification creation time
updatedAt | Notification modification time (usually send time)
sent | Notification sent status

## POST /notification/{user}
```php
<?php
$ch = curl_init("https://api.hyperspace.one/notification/{user}");
curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
curl_setopt($ch, CURLOPT_POSTFIELDS, http_build_query([
  "title" => "Test notification",
  "text" => "Test content",
]));
curl_setopt($ch, CURLOPT_HTTPHEADER, [
  "Authorization: Bearer xxx",
]);
$res = curl_exec($ch);

if (curl_errno($ch)) {
  die(curl_error($ch));
}

curl_close($ch);
$res = json_decode($res, true);

print_r($res);
```

> The above request returns JSON structured like this:

```json
{
  "success": true
}
```

<aside style="color: white;">Requires user authentication with right <b>send_notifications</b></aside>

This endpoint allows administrators to send a notification to the specified user

### Query parameters

Parameter | Description
--------- | -----------
user | User ID or email address
title | Notification title
text | Notification content
deeplink | **Optional** App deeplink

### Failure codes

Failure Code | Meaning
---------- | -------
1000 | No title specified
1001 | No content specified

## POST /push
```php
<?php
$ch = curl_init("https://api.hyperspace.one/push");
curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
curl_setopt($ch, CURLOPT_POSTFIELDS, http_build_query([
  "token" => "xxx",
]));
curl_setopt($ch, CURLOPT_HTTPHEADER, [
  "Authorization: Bearer xxx",
]);
$res = curl_exec($ch);

if (curl_errno($ch)) {
  die(curl_error($ch));
}

curl_close($ch);
$res = json_decode($res, true);

print_r($res);
```

> The above request returns JSON structured like this:

```json
{
  "success": true
}
```

<aside style="color: white;">Requires user authentication</aside>

Assign push token to user profile

### Query parameters

Parameter | Description
--------- | -----------
token | Push token

### Failure codes

Failure Code | Meaning
---------- | -------
1000 | No token specified

## DELETE /push
```php
<?php
$ch = curl_init("https://api.hyperspace.one/push");
curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
curl_setopt($ch, CURLOPT_CUSTOMREQUEST, "DELETE");
curl_setopt($ch, CURLOPT_POSTFIELDS, http_build_query([
  "token" => "xxx",
]));
curl_setopt($ch, CURLOPT_HTTPHEADER, [
  "Authorization: Bearer xxx",
]);
$res = curl_exec($ch);

if (curl_errno($ch)) {
  die(curl_error($ch));
}

curl_close($ch);
$res = json_decode($res, true);

print_r($res);
```

> The above request returns JSON structured like this:

```json
{
  "success": true
}
```

<aside style="color: white;">Requires user authentication</aside>

Remove push token from user profile

### Query parameters

Parameter | Description
--------- | -----------
token | Push token

### Failure codes

Failure Code | Meaning
---------- | -------
1000 | No token specified
1001 | Token not found

# Devices

## GET /terminal/{device_id}/{rfid_tag}
```php
<?php
$ch = curl_init("https://api.hyperspace.one/terminal/$device_id/$rfid_tag");
curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
curl_setopt($ch, CURLOPT_HTTPHEADER, [
  "X-Gc-Language: en",
]);
$res = curl_exec($ch);

if (curl_errno($ch)) {
  die(curl_error($ch));
}

curl_close($ch);
$res = json_decode($res, true);

print_r($res);
```

> The above request returns JSON structured like this:

```json
{
    "name": "John Doe",
    "color": "green"
}
```

> If the tag is not found, the following response is returned (where `name` is localized based on `X-Gc-Language`):

```json
{
    "name": "Tag not found",
    "color": "red"
}
```

This endpoint returns information about the requested RFID tag.

### Query parameters

Parameter | Description
--------- | -----------
device_id | The UUID of the device requesting
rfid_tag | The ID of the RFID tag scanned

### Return values

Parameter | Description
--------- | -----------
name | Name of ticket owner
color | LED color (age based)

### Failure codes

Failure Code | Meaning
---------- | -------
1000 | Device not found

## GET /terminal/{device_id}/all
```php
<?php
$ch = curl_init("https://api.hyperspace.one/terminal/$device_id/all");
curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
$res = curl_exec($ch);

if (curl_errno($ch)) {
  die(curl_error($ch));
}

curl_close($ch);
$res = json_decode($res, true);

print_r($res);
```

> The above request returns JSON structured like this:

```json
{
    "beep": 1,
    "16000835557E": {
        "name": "John Doe",
        "color": "green"
    },
    "560059DB6FBB": {
        "name": "Max Mustermann",
        "color": "yellow"
    }
}
```

This endpoint returns all tags available for a specific device. Also, it returns config options for the requesting device.

### Query parameters

Parameter | Description
--------- | -----------
device_id | The UUID of the device requesting

### Return values

The return value is a list of tags available and the device options:

Parameter | Description
--------- | -----------
beep | Specifies if the device should beep when RFID scan is finished

### Failure codes

Failure Code | Meaning
---------- | -------
1000 | Device not found

## GET /entry/{hash}
```php
<?php
$ch = curl_init("https://api.hyperspace.one/entry/$hash");
curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
curl_setopt($ch, CURLOPT_HTTPHEADER, [
  "Authorization: Bearer xxx",
]);
$res = curl_exec($ch);

if (curl_errno($ch)) {
  die(curl_error($ch));
}

curl_close($ch);
$res = json_decode($res, true);

print_r($res);
```

> The above request returns JSON structured like this:

```json
{
  "success": true
}
```
<aside style="color: white;">Requires user authentication with right <b>scan_tickets</b></aside>

This endpoint is used to scan tickets at entry. It either returns a success response or a failure if the ticket is invalid or was already scanned. If the scan was successful, it saves the time of the scan and the staff member who has scanned.

### Query parameters

Parameter | Description
--------- | -----------
hash | QR code content / hash of ticket

### Failure codes

Failure Code | Meaning
---------- | -------
1000 | Invalid hash format
1001 | Unknown ticket ID
1002 | Wrong hash
1003 | Already scanned

## GET /entry/{hash}/details
```php
<?php
$ch = curl_init("https://api.hyperspace.one/entry/$hash/details");
curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
curl_setopt($ch, CURLOPT_HTTPHEADER, [
  "Authorization: Bearer xxx",
]);
$res = curl_exec($ch);

if (curl_errno($ch)) {
  die(curl_error($ch));
}

curl_close($ch);
$res = json_decode($res, true);

print_r($res);
```

> The above request returns JSON structured like this:

```json
{
    "product": {
        "id": 4,
        "name": "Early Bird"
    },
    "created_at": {
        "date": "2019-02-02 16:13:59.000000",
        "timezone_type": 3,
        "timezone": "UTC"
    },
    "entry_at": "2019-05-31 11:54:24",
    "entry_by": "John Doe"
}
```
<aside style="color: white;">Requires user authentication with right <b>view_tickets</b></aside>

This endpoint returns details about the ticket scanned and any associated entry.

### Query parameters

Parameter | Description
--------- | -----------
hash | QR code content / hash of ticket

### Return values
Parameter | Description
--------- | -----------
product[] | Ticket product information
created_at | Time of ticket creation
entry_at | **If entry** Date of entry **Otherwise** *null*
entry_by | **If entry** Name of staff member **Otherwise** *null*

### Failure codes

Failure Code | Meaning
---------- | -------
1000 | Invalid hash format
1001 | Unknown ticket ID
1002 | Wrong hash

## DELETE /entry/{hash}
```php
<?php
$ch = curl_init("https://api.hyperspace.one/entry/$hash");
curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
curl_setopt($ch, CURLOPT_CUSTOMREQUEST, "DELETE");
curl_setopt($ch, CURLOPT_HTTPHEADER, [
  "Authorization: Bearer xxx",
]);
$res = curl_exec($ch);

if (curl_errno($ch)) {
  die(curl_error($ch));
}

curl_close($ch);
$res = json_decode($res, true);

print_r($res);
```

> The above request returns JSON structured like this:

```json
{
  "success": true
}
```
<aside style="color: white;">Requires user authentication with right <b>reset_tickets</b></aside>

This endpoint resets any scan of the specified ticket.

### Query parameters

Parameter | Description
--------- | -----------
hash | QR code content / hash of ticket

### Failure codes

Failure Code | Meaning
---------- | -------
1000 | Invalid hash format
1001 | Unknown ticket ID
1002 | Wrong hash

## GET /devices
```php
<?php
$ch = curl_init("https://api.hyperspace.one/devices");
curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
curl_setopt($ch, CURLOPT_HTTPHEADER, [
  "Authorization: Bearer xxx",
]);
$res = curl_exec($ch);

if (curl_errno($ch)) {
  die(curl_error($ch));
}

curl_close($ch);
$res = json_decode($res, true);

print_r($res);
```

> The above request returns JSON structured like this:

```json
{
  "success": true,
  "itemCount": 1,
  "items": [
    {
      "id": "4f0ca3a3-c729-467a-adfd-75ef58029ac3",
      "beep": 1,
      "age_green": 18,
      "created_at": "2019-06-13 04:09:08",
      "updated_at": "2019-06-13 08:01:07"
    }
  ]
}
```

<aside style="color: white;">Requires user authentication with right <b>manage_devices</b></aside>

This endpoint allows administrators to get a paginated list of all devices in the system.

### Query parameters

Parameter | Default | Description
--------- | ------- | -----------
page | 1 | Page number
per_page | 20 | Items to display per page

### Return values

The request returns an `itemCount` which is the number of all devices in system. `items` contains the set of requested devices according to pagination. The devices have these properties:

Parameter | Description
--------- | -----------
id | Device ID (**keep it secret**)
beep | Scan beep activated
age_green | Age required for green LED
created_at | Time of device creation
updated_at | Time of last change

## POST /devices
```php
<?php
$ch = curl_init("https://api.hyperspace.one/devices");
curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
curl_setopt($ch, CURLOPT_HTTPHEADER, [
  "Authorization: Bearer xxx",
]);
curl_setopt($ch, CURLOPT_POSTFIELDS, http_build_query([
  "beep" => true,
]));
$res = curl_exec($ch);

if (curl_errno($ch)) {
  die(curl_error($ch));
}

curl_close($ch);
$res = json_decode($res, true);

print_r($res);
```

> The above request returns JSON structured like this:

```json
{
  "success": true,
  "id": "1f37acb0-8df1-11e9-a016-0b66f9bad036"
}
```

<aside style="color: white;">Requires user authentication with right <b>manage_devices</b></aside>

This endpoint allows administrators to create new devices.

### Query parameters

Parameter | Default | Description
--------- | ------- | -----------
beep | false | Scan beep activated
age_green | 18 | Age required for green LED

### Return values

Parameter | Description
--------- | -----------
id | ID of newly created device (**keep it secret**)

## PUT /devices/{id}
```php
<?php
$ch = curl_init("https://api.hyperspace.one/devices/{id}");
curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
curl_setopt($ch, CURLOPT_POSTFIELDS, http_build_query([
  "beep" => false,
]));
curl_setopt($ch, CURLOPT_HTTPHEADER, [
  "Authorization: Bearer xxx",
]);
curl_setopt($ch, CURLOPT_CUSTOMREQUEST, "PUT");
$res = curl_exec($ch);

if (curl_errno($ch)) {
  die(curl_error($ch));
}

curl_close($ch);
$res = json_decode($res, true);

print_r($res);
```

> The above request returns JSON structured like this:

```json
{
  "success": true
}
```

<aside style="color: white;">Requires user authentication with right <b>manage_devices</b></aside>

This endpoint allows administrators to edit a device.

### Query parameters

Parameter | Default | Description
--------- | ------- | -----------
id | - | Device ID

You can also use the parameters `beep`, and `age_green` to update these properties. Parameters not specified are ignored and not updated.

## DELETE /devices/{id}
```php
<?php
$ch = curl_init("https://api.hyperspace.one/devices/{id}");
curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
curl_setopt($ch, CURLOPT_HTTPHEADER, [
  "Authorization: Bearer xxx",
]);
curl_setopt($ch, CURLOPT_CUSTOMREQUEST, "DELETE");
$res = curl_exec($ch);

if (curl_errno($ch)) {
  die(curl_error($ch));
}

curl_close($ch);
$res = json_decode($res, true);

print_r($res);
```

> The above request returns JSON structured like this:

```json
{
  "success": true
}
```

<aside style="color: white;">Requires user authentication with right <b>manage_devices</b></aside>

This endpoint allows administrators to delete a device.

### Query parameters

Parameter | Default | Description
--------- | ------- | -----------
id | - | Device ID

# Settings

## GET /settings
```php
<?php
$ch = curl_init("https://api.hyperspace.one/settings");
curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
curl_setopt($ch, CURLOPT_HTTPHEADER, [
  "Authorization: Bearer xxx",
]);
$res = curl_exec($ch);

if (curl_errno($ch)) {
  die(curl_error($ch));
}

curl_close($ch);
$res = json_decode($res, true);

print_r($res);
```

> The above request returns JSON structured like this:

```json
{
  "affiliatePointsFactor": "0.2",
  "backendUrl": "https://staging.api.hyperspace.one/",
  "blacklistTime": "10",
  "blacklistTries": "1000",
  "captchaIgnore": "1",
  "currencyCode": "EUR",
  "featuredEvent": "1"
}
```
<aside style="color: white;">Requires user authentication with right <b>settings</b></aside>

This endpoint returns key-value pairs of all system settings.

## PUT /settings
```php
<?php
$ch = curl_init("https://api.hyperspace.one/settings");
curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
curl_setopt($ch, CURLOPT_POSTFIELDS, http_build_query([
  "currencyCode" => "USD",
  "blacklistTime" => 15,
  "unknownOption123" => "test",
]));
curl_setopt($ch, CURLOPT_CUSTOMREQUEST, "PUT");
curl_setopt($ch, CURLOPT_HTTPHEADER, [
  "Authorization: Bearer xxx",
]);
$res = curl_exec($ch);

if (curl_errno($ch)) {
  die(curl_error($ch));
}

curl_close($ch);
$res = json_decode($res, true);

print_r($res);
```

> The above request returns JSON structured like this:

```json
{
  "success": true,
  "set": {
    "currencyCode": "USD",
    "blacklistTime": "15"
  }
}
```
<aside style="color: white;">Requires user authentication with right <b>settings</b></aside>

This endpoint can be used to update settings. It expects as many key-value pairs as you want. Only elements which exists in the `settings` table are updated, there are no elements newly created.

### Return values

Parameter | Description
--------- | -----------
set | All newly set values according to their keys

## POST /faq
```php
<?php
$ch = curl_init("https://api.hyperspace.one/faq");
curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
curl_setopt($ch, CURLOPT_HTTPHEADER, [
  "Authorization: Bearer xxx",
]);
curl_setopt($ch, CURLOPT_POSTFIELDS, http_build_query([
  "question" => "My question",
  "answer" => "My answer",
  "localization" => [
    "de" => [
      "question" => "Meine Frage",
      "answer" => "Meine Antwort"
    ],
  ],
]));
$res = curl_exec($ch);

if (curl_errno($ch)) {
  die(curl_error($ch));
}

curl_close($ch);
$res = json_decode($res, true);

print_r($res);
```

> The above request returns JSON structured like this:

```json
{
  "success": true,
  "id": 1
}
```

<aside style="color: white;">Requires user authentication with right <b>settings</b></aside>

This endpoint allows administrators to create new FAQs.

### Query parameters

Parameter | Default | Description
--------- | ------- | -----------
question | - | Question
answer | - | Answer
localization | [] | Array with localized content (optional)

### Return values

Parameter | Description
--------- | -----------
id | ID of newly created FAQ

### Failure codes

Failure Code | Meaning
---------- | -------
1001 | No question specified
1002 | No answer specified

## PUT /faq/{id}
```php
<?php
$ch = curl_init("https://api.hyperspace.one/faq/{id}");
curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
curl_setopt($ch, CURLOPT_POSTFIELDS, http_build_query([
  "question" => "My question?",
]));
curl_setopt($ch, CURLOPT_HTTPHEADER, [
  "Authorization: Bearer xxx",
]);
curl_setopt($ch, CURLOPT_CUSTOMREQUEST, "PUT");
$res = curl_exec($ch);

if (curl_errno($ch)) {
  die(curl_error($ch));
}

curl_close($ch);
$res = json_decode($res, true);

print_r($res);
```

> The above request returns JSON structured like this:

```json
{
  "success": true
}
```

<aside style="color: white;">Requires user authentication with right <b>settings</b></aside>

This endpoint allows administrators to edit FAQs.

### Query parameters

Parameter | Default | Description
--------- | ------- | -----------
id | - | FAQ ID

You can also use the parameters `question` and `answer` to update these properties. Parameters not specified are ignored and not updated.

If you specify an array named `localization` as in `POST /faq`, the values for the specified languages are updated. Localizations not specified are deleted.

## DELETE /faq/{id}
```php
<?php
$ch = curl_init("https://api.hyperspace.one/faq/{id}");
curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
curl_setopt($ch, CURLOPT_HTTPHEADER, [
  "Authorization: Bearer xxx",
]);
curl_setopt($ch, CURLOPT_CUSTOMREQUEST, "DELETE");
$res = curl_exec($ch);

if (curl_errno($ch)) {
  die(curl_error($ch));
}

curl_close($ch);
$res = json_decode($res, true);

print_r($res);
```

> The above request returns JSON structured like this:

```json
{
  "success": true
}
```

<aside style="color: white;">Requires user authentication with right <b>settings</b></aside>

This endpoint allows administrators to delete a FAQ.

### Query parameters

Parameter | Default | Description
--------- | ------- | -----------
id | - | FAQ ID