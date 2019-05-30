---
title: GroundControl API - Spacion GmbH

language_tabs: # must be one of https://git.io/vQNgJ
  - php

toc_footers:
  - <a href='https://my.hyperspace.one/contact'>Imprint</a>

search: false
---

# Introduction

GroundControl API is the backend of GroundControl by Spacion GmbH. This documentation covers all available API actions, from authentication to endpoints. GroundControl API is a RESTful API.

We use `https://api.hyperspace.one` as an example URL. This URL has to be replaced by your URL where GroundControl API is hosted.

We provide example codes in PHP. You can view code examples in the dark area to the right, and you can switch the programming language of the examples with the tabs in the top right.

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

In case of a successful request, you will get a JSON element `success` containing `true`. The HTTP status code will be `200`.

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

> The above command returns JSON structured like this:

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

### Query Parameters

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

> The above command returns JSON structured like this:

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

### Query Parameters

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

<aside>Additional values are possible based on your system configuration</aside>

## GET /products

TODO

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

> The above command returns JSON structured like this:

```json
{
  "success": true,
  "faq": [
    {
      "question":"WHERE CAN YOU PARK?",
      "answer":"At our PreParty in Music Dome there is free parking."
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
TODO

# Users

## PUT /register
TODO

## PUT /password
TODO

## POST /password/email
TODO

## POST /password/check
TODO

## POST /password/reset
TODO

## GET /users
TODO

## GET /user/{id}
TODO

## PUT /user/{id}
TODO

## DELETE /user/{id}
TODO

## POST /user
TODO

# Preregistrations

## POST /preregister
TODO

## GET /preregister/confirm/{token}
TODO

## PUT /preregister
TODO

## GET /preregistrations
TODO

## PUT /preregistrations/{id}
TODO

## POST /preregistrations/{id}
TODO

## DELETE /preregistrations/{id}
TODO

# Events

## GET /events
TODO

## GET /events/{slug}
TODO

# Galleries

## GET /galleries
TODO

## GET /galleries/{slug}
TODO

## POST /galleries
TODO

## PUT /galleries/{slug}
TODO

## DELETE /galleries/{slug}
TODO

## GET /images
TODO

## POST /images/upload
TODO

## POST /images
TODO

## PUT /images/{id}
TODO

## DELETE /images/{id}
TODO

# Orders

## POST /order
TODO

## GET /order/{order_id}/{token}
TODO

## GET /orders
TODO

## ANY /order/{order_id}/{token}/ipn/{payment_method}
TODO

## POST /checkout/paypal
TODO

# Payment

## GET /ideal/banks
TODO

# Tickets

## GET /tickets
TODO

## POST /ticket/{id}/personalize
TODO

## GET /ticket/{id}/pdf/{token}
TODO

# Ranks

## GET /ranks
TODO

## GET /achievements
TODO

## POST /achievement
TODO

# Promoter

## GET /promoter/orders
TODO

# Affiliate

## GET /affiliate
TODO

## POST /affiliate
TODO

## GET /reflinks
TODO

## PUT /affiliate/{hash}
TODO

## DELETE /affiliate/{hash}
TODO

## GET /affiliate/orders
TODO

## POST /affiliate/click/{hash}
TODO

# Notifications

## GET /notification/{user}
TODO

## POST /notification/{user}
TODO

## POST /push
TODO

## DELETE /push
TODO

# Terminals

## GET /terminal/{device_id}/{rfid_tag}
TODO

## GET /entry/{hash}
TODO

## GET /entry/{hash}/details
TODO

## DELETE /entry/{hash}
TODO

# Settings

## GET /settings
TODO

## PUT /settings
TODO