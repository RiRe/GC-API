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

> The above request returns JSON structured like this:

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
<aside>Requires correct iDEAL API token</aside>

This endpoint returns the banks available for use with iDEAL. The results are cached for performance reasons.

Parameter | Description
--------- | -----------
code | Bank code, needs to be sent when doing payment
name | Name of the bank

### Failure codes

Failure Code | Meaning
---------- | -------
1000 | API fail ( can be caused by wrong API token)

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