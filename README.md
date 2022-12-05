# Merchant Guide

A vey short guide for the merchant who want to connect their shops into our
payment system.

## Login

Go to https://zion.ae/login/ and login with your username and password.

![login](resources/login-page.png)

## Switch Shop

From the sidebar just below the shop name, you can select the "switch account"
button. Then you will see a list of all your shops. Select the shop that you
like to work with.

![login](resources/switch-shop.png)

## Get the Shop ID

Select the "Settings" button from the sidebar, and on opened page go to the
second tab which is "Shop settings".

![login](resources/shop-id.png)

## Set up callback URL

In the "Shop settings" page you can also configure your callback URL. If you
leave this field empty, you will not receive any notifications.

![login](resources/callback.png)

## Get the API key

If you want to have accesses to your account from the code, You are going to
need an API key. This is really important to you keep this key very safe and
never share this with anyone because anyone with this API key is have the full
control over your account.

From the sidebar select "Account" button and in the "Account settings" tab you
are going to see your API key.

![login](resources/api-key.png)

## Generate an order

You can use your shop ID and your API key to generate an order. You can replace
your credentials in the following sample:

```shell
curl --request POST \
  --url https://zion.ae/self/shops/<YOUR-SHOP-ID-HERE>/generate-order/ \
  --header 'Authorization: Token <YOUR-API-KEY-HERE>' \
  --header 'Content-Type: application/json' \
  --data '{
    "amount": <AMOUNT-IN-USDT>
  }'
```

for example the final request will be like this:

```shell
curl --request POST \
  --url https://zion.ae/self/shops/9f043cb2-a006-4c44-9ba8-1f4468c178c2/generate-order/ \
  --header 'Authorization: Token 263f33390ddc255edc1f64a3f88a972b9cc1a684' \
  --header 'Content-Type: application/json' \
  --data '{
      "amount": 100
  }'
```

if everything was okay, and you made the correct request the server will send
you a response like this:

```json
{
  "id": "1d55680e-aac8-466d-8e7c-b27ba1e12bf0",
  "reference": "HMAXHQYS",
  "fiat_amount": 100.000000
}
```

here the most important data is the ID, and you are going to use this to
redirect your customer to our website.

## Generate payment page URL

After you generate an order and received an ID then you can create a URL like
this:

```shell
https://zion.ae/payment/?id=<YOUR-ORDER-ID>
```

and redirect your customer to this URL. Users are going to see a payment page
with your shop name and your order amount.

![login](resources/payment-page.png)

On this page they can choose from the list of available coins, and after
transaction verification you are going to receive the money

## Verify order

After verification of the order, our server will send a POST request to your
shop callback URL. The payload or the body of the request is like this:

```json
{
  "id": "1d55680e-aac8-466d-8e7c-b27ba1e12bf0",
  "reference": "HMAXHQYS",
  "fiat_amount": 100.000000
}
```

NOTE: Reviving this request does not guarantee that the order is completed
successfully, you have to verify the order after receiving this webhook. To
verify your order you can use this request:

```shell
curl --request GET \
  --url https://zion.ae/self/orders/<YOUR-ORDER-ID>/ \
  --header 'Authorization: Token <YOUR-API-KEY>
```

and you will receive a response like this:

```json
{
  "id": "f4afe31f-fd1f-40d4-a1ed-38ff06308dc9",
  "reference": "Z0Y1O42U",
  "fiat_amount": "100.000000",
  "status": "Expired",
  "created_at": "2022-11-29T14:40:23.546918Z"
}
```

here the status is very important, you should only accept an order if its status
is "Filled" otherwise it means the order is failed, and you have not received
any money. 

