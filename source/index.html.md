---
title: API Reference

language_tabs: # must be one of https://git.io/vQNgJ
  - shell

toc_footers:
  - <a href='https://www.bitcoin.com'>Bitcoin.com</a>

includes:
  - errors

search: true
---
<img src="images/logo.png" alt="Bitcoin.com">

# Bitcoin Card API Documentation

# Introduction

This is documentation for Bitcoin Card API, REST based API service, which provides various endpoints to Bitcoin.com front end applications. The API allows authorized applications to get user data, create users, their wallet information, card information and various other card user related data. The data provided by the API comes from PostgreSQL compatible database, run on Amazon Web Service, and from card provider APIs (Ternio API and Prysym API). 

We have language bindings in Shell, Ruby, Python, and JavaScript! You can view code examples in the dark area to the right, and you can switch the programming language of the examples with the tabs in the top right.

This example API documentation page was created with [Slate](https://github.com/lord/slate). Feel free to edit it and use it as a base for your own API's documentation.

# Quickstart

The main purpose of Card API is to handle requests from applications requesting backend data. The data at the moment is mostly tied to Bitcoin.com card users. The users are created in the system by creating them in AWS Cognito. After that, front end application (Bitcoin.com) will call Card API with valid token and execute following steps:

1. Call API create user endpoint. 
2. Create user endpoint will create user in PostgreSQL database.
3. API will determine which card provider flow should user follow and follow their flow.
4. User will be setup with card provider (create user, create wallet, signup for card and other necessary steps), or error message will be returned. 

Any application invoking Card API to setup new card user would only need to create user by invoking create user endpoint. All other steps would happen automatically within the API. The other endpoints below allow other applications to retrieve data related to created users. 

# Environment

The Card API is currently running on AWS EC2 instance. There is a load balancer connected to domain <b>https://card.api.bitcoin.com</b> which will be production end point. The two other endpoints <b>dev.card.api.bitcoin.com</b> and <b>qa.card.api.bitcoin.com</b> will be used for Dev/Staging environments. 

# Building

The application is developed in Java/Kotlin programming language and it is using Maven build automation tool for building and deployments. It is also using Spring Framework (java framework for MVC architecture and REST APIs) as a base for all development. Spring Boot is used to run the application in local environment with command specified below. 

To build the application, execute following from command line in root application directory:

<b>mvn clean install</b>

To run the application in development environment, execute:

<b>mvn spring-boot:run</b>

# Deployment

As part of new Bitcoin.com architecture application will be deployed using Docker image. Image will be uploaded to AWS ECR.  

# Authentication

All communication with Card API is done over HTTPS protocol. We provide user with the certificate after which user can connect to the API. 

In order for any client to access Card API endpoints, valid access token has to be obtained from AWS Cognito. Token can be obtained for any user created in Cognito by calling API /login page. Once user is authenticated and valid token obtained, all data for that user can be accessed from API, as well as user info updated.   

Token is parsed and validated by the API and and only after this verification can API be accessed. The access is limited to only that authenticated user. 


If token is invalid, user has globally logged out, or token was never issued by Cognito, Card API will return <b>401</b> code and <b>'Unauthorized'</b> message.


### Header Parameters
Parameter | Description
--------- | -----------
Authorization | Used to pass token value in the format <b>Bearer (token value)</b>


## Authenticate User


```shell
`curl -v -X POST https://card.api.bitcoin.com/v1/login`
```

> The above command returns Cognito token like this:

```json
{
   "access_token":"eyJraWQiOiI1T3U5TGJNMEU1aXJwdDZud3F0WTVZUWFJK0JuMXZZWVZCbVJkVE1GUmNFPSIsImFsZyI6IlJTMjU2In0.eyJzdWIiOiJkYzBlYzk1ZC1kZDQ1LTQzYTgtYmE3Zi02MTk5NWM5N2QwZmMiLCJ0b2tlbl91c2UiOiJhY2Nlc3MiLCJzY29wZSI6Im9wZW5pZCBwcm9maWxlIiwiYXV0aF90aW1lIjoxNTgxMzkzOTgwLCJpc3MiOiJodHRwczpcL1wvY29nbml0by1pZHAudXMtZWFzdC0yLmFtYXpvbmF3cy5jb21cL3VzLWVhc3QtMl9MeGdGd0FOQXIiLCJleHAiOjE1ODEzOTc1ODAsImlhdCI6MTU4MTM5Mzk4MCwidmVyc2lvbiI6MiwianRpIjoiMzRmYTA5YmEtNGJiYS00YmEwLWJjMjktYzQ2ZmQ2YTkwZGIzIiwiY2xpZW50X2lkIjoiN29ka2hpOHQ5dmc0cnRlZmNwNmd2aDZiMG0iLCJ1c2VybmFtZSI6InRlc3RfdXNlcjEifQ.rv-SiBXjkIGYEC-5GDZRJ3_A9xxCWxjMqR8wtAm7gyD0HIAf3joqRyzmfOg_wBByu0_V8sp5WMTfcvME3DghhivxDDUmZjLOcog8s06g6NcpWTgta-fozUBa6ilHC9ioedfPEcYcLTcD2leNinjf0dONpZJOzd6wZR2oYPeXkPJlwNqwvY5UkTXAX1kupiXJ53o-7Lfvn_mF6BjazvjUdlbcYwSgXnol41jW2htiYYYUjQWVx4Wr61uc1WQY0Vv5GVeJ8bjr1oo8iLD7bXP6DAmzIhqrikVtWtZ6pcOHBKa3BLR4Iz7-1fd3KDfqyyCTiT3gVrxutBI9FCfT1iwexw"
   
}
```
Endpoint to login the user. Must be a valid, already created Cognito user. 

<aside class="success">
If succesfull, HTTP code 200 will be returned
</aside>
<aside class="warning">If user is not authorized, HTTP code 401 will be returned.</aside>

### HTTP Request

`POST https://card.api.bitcoin.com/v1/login`

### URL Parameters

Parameter | Description
--------- | -----------
username  | User name of the user to login
password  | Password of the user to login


## Verify Authorization Code for User


```shell
`curl -v -X POST https://card.api.bitcoin.com/v1/verify`
```

Endpoint to verify authorization code sent to user's email. Must be a valid, already created Cognito user.

<aside class="success">
If succesfull, HTTP code 200 will be returned
</aside>
<aside class="warning">If user is not authorized, HTTP code 401 will be returned.</aside>

### HTTP Request

`POST https://card.api.bitcoin.com/v1/verify`

### URL Parameters

Parameter | Description
--------- | -----------
username  | User name of the user to login
code| User code sent to the email 

## Logout User


```shell
`curl -v -X GET https://card.api.bitcoin.com/v1/logout`
```

Endpoint to globally logout user. Cognito can issue multiple tokens for the same user. This endpoint would invalidate all outstanding user tokens. 

<aside class="success">
If succesfull, HTTP code 200 will be returned
</aside>
<aside class="warning">If user is not authorized, HTTP code 400 will be returned.</aside>

### HTTP Request

`GET https://card.api.bitcoin.com/v1/logout`

### URL Parameters

No parameters are passed, except token in Authorization header in format Bearer <token>

## Reset Password


```shell
`curl -v -X POST https://card.api.bitcoin.com/v1/reset-password`
```
Endpoint to reset user's password. This endpoint will send email with reset password code which should be used to reset password on next call. This endpoint does not require token as user might have forgotten the password so no authentication is necessary before the reset.  

<aside class="success">
If succesfull, HTTP code 200 will be returned
</aside>
<aside class="warning">If user is not found, HTTP code 404 will be returned.</aside>

### HTTP Request

`POST https://card.api.bitcoin.com/v1/reset-password`

Parameter | Description
--------- | -----------
usernameOrEmail  | Parameter accepts either username or email so we can reset the password 


## Update Password


```shell
`curl -v -X POST https://card.api.bitcoin.com/v1/update-password/me`
```
Endpoint to update user's password by passing code received in email. 

<aside class="success">
If succesfull, HTTP code 200 will be returned
</aside>
<aside class="warning">If user is not found, HTTP code 404 will be returned.</aside>

### HTTP Request

`POST https://card.api.bitcoin.com/v1/update-password/me`

### URL Parameters

Parameter | Description
--------- | -----------
newPassword  | New user's password
code| User code sent to the email


# Users

## Get All Users

```shell
curl "https://card.api.bitcoin.com/v1/users"
```

> The above command returns JSON structured like this:

```json
[
  {
    "id": 1,
  },
  {
    "id": 2,
  }
]
```

COMING SOON - This endpoint retrieves all users.

### HTTP Request

`GET https://card.api.bitcoin.com/v1/users`

### Query Parameters

<aside class="success">
If succesfull, HTTP code 200 will be returned
</aside>

## Get a Specific User


```shell
`curl -v -X GET https://card.api.bitcoin.com/v1/users/me`
```

> The above command returns JSON structured like this:

```json
{
   "id":1,
   "firstName":"Mehmed",
   "lastName":"XXX",
   "createdAt":"2019-12-16T23:03:41.121+0000",
   "updatedAt":"2020-01-03T15:56:10.302+0000",
   "email":"md@yahoo.com",
   "phoneNumber":"(997) 334-2323",
   "dateOfBirth":"99/99/9999",
   "gender":"M",
   "promotioanlConsent":true,
   "addresStreet":"2208 Flat Branch",
   "addressCity":"Richmond",
   "addressPostalCode":"23233",
   "addressState":"Va",
   "shippingAddresStreet": "2208 Flat Branch Ct.",
   "shippingAddressCity": "Richmond",
   "shippingAddressPostalCode": "23233",
   "shippingAddressState": "Va",
   "shippingAddressCountry": "US",
   "shippingAddresStreet2": "Apt 2",
   "shippingAddressCountry":"USA",
   "defaultCurrencyId":"BCH",
   "socialSecurityNumber":"888-00-8888",
   "active":true, 
   "username":"mduric"
}
```

This endpoint retrieves a specific user.

<aside class="success">
If succesfull, HTTP code 200 will be returned
</aside>
<aside class="warning">If user is not found, HTTP code 404 will be returned.</aside>

### HTTP Request

`GET https://card.api.bitcoin.com/v1/users/me`

### URL Parameters

No parameters necessary. Username will be extracted from passed token and user details returned. 

Both parameters are optional, but one of them has to be passed in order to retrieve valid user. 


##Create User 

```shell
'curl -v -X POST https://card.api.bitcoin.com/v1/users \
   -H "Content-type:application/json" -d \
   "{\"firstName\":\"Joe\", \
   \"lastName\":\"Doe\", \
   \"email\":\"md@yahoo.com\", \
   \"phoneNumber\":\"941-667-2323\", \
   \"dateOfBirth\":\"04/24/1987\", \
   \"gender\":\"M\", \
   \"isActive\":\"true\", \
   \"promotionalConsent\":\"true\", \
   \"addresStreet\":\"1122 Ridgefield Dr.\", \
   \"addresStreet2\":\"Apt. 22\", \
   \"addressCity\":\"Venice\", \
   \"addressPostalCode\":\"34210\", \
   \"addressState\":\"Ohio\", \
   \"addressCountry\":\"US\", \
   \"defaultCurrencyId\":\"BCH\", \
   \"socialSecurityNumber\":\"223-23-2323\", \
   \"username\":\"mduric\", \
   \"password\":\"test123\" \
   }"'
```

This endpoint creates new user in Posgres and Cognito. Postgres requires unique email and and username. Cognito requires unique username.  

<aside class="success">
If succesfull, HTTP code 200 will be returned
</aside>
<aside class="warning">If error, HTTP code 400 (bad request) will be returned.</aside>

### HTTP Request

`POST https://card.api.bitcoin.com/v1/users`

### URL Parameters

Parameter | Description
--------- | -----------
firstName | The first name of the user 
lastName  | The last name of the user 
email     | The email of the user 
phoneNumber | The phone number of the user 
dateOfBirth | The date of birth of the user 
gender | The gender of the user 
isActive | Active or not?
promotionalConsent | User allows promotional material
addressStreet | The street address of the user 
addressStreet2 | Second line for the address
addressCity | The city of the user 
addressPostalCode | The postal code of the user 
addressState | The state of the user 
addressCountry | The country of the user 
shippingAddressStreet | The street address of the user 
shippingAddressStreet2 | Second line for the address
shippingAddressCity | The city of the user 
shippingAddressPostalCode | The postal code of the user 
shippingAddressState | The state of the user 
shippingAddressCountry | The country of the user 
defaultCurrencyId | The currency type of the user 
socialSecurityNumber | The SSN of the user 
username | The user name of the user 
password | The password for the created user 

##Update User

```shell
curl -v -X PUT https://card.api.bitcoin.com/v1/users \
   -H "Content-type:application/json" -d \
   "{ \
      \"id\":\"4\", \
      \"socialSecurityNumber\":\"888-00-8888\", \
      \"email\":\"zzz@yahoo.com\" \
    }"
```

This endpoint update a specific user information.

<aside class="success">
If succesfull, HTTP code 200 will be returned
</aside>
<aside class="warning">If error, HTTP code 400 (bad request) will be returned.</aside>

### HTTP Request

`PUT https://card.api.bitcoin.com/v1/users`

### URL Parameters

Any of the available parameters can be sent for updating.

Parameter | Description
--------- | -----------
firstName | The first name of the user
lastName  | The last name of the user
email     | The email of the user
phoneNumber | The phone number of the user
dateOfBirth | The date of birth of the user
gender | The gender of the user
isActive | Active or not?
promotionalConsent | User allows promotional material
addressStreet | The street address of the user
addressCity | The city of the user
addressPostalCode | The postal code of the user
addressState | The state of the user
addressCountry | The country of the user
defaultCurrencyId | The currency type of the user
socialSecurityNumber | The SSN of the user
userName | The user name of the user



## Delete a Specific User

```shell
curl -v -X DELETE https://card.api.bitcoin.com/v1/users/me 
```
This endpoint deletes a specific user.

### HTTP Request

`DELETE https://card.api.bitcoin.com/v1/users/me`

### URL Parameters

No parameters need to be passed, username will be extracted from token. 

## Get User's Virtual Card


```shell
`curl https://card.api.bitcoin.com/v1/virtual-card`
```

This endpoint retrieves a specific user's virtual card, such as this: 

<img src="images/virtual-card.jfif" alt="Virtual Card">

<aside class="success">
If succesfull, HTTP code 200 will be returned
</aside>
<aside class="warning">If user is not found, HTTP code 404 will be returned.</aside>

### HTTP Request

`GET https://card.api.bitcoin.com/v1/virtual-card`

### URL Parameters

Parameter | Description
--------- | -----------
ID | The ID of the user to retrieve

## Get User Transactions

```shell
curl -v -X GET https://card.api.bitcoin.com/v1/transactions
```
This endpoint retrieves user transactions. Currently it is getting data from card provider (Ternio) user ledger transactions, however, this will be expanded to card transactions too. Eventually, this endpoint will get transactions from both card providers and allow several types of transactions to be retrieved. 

### HTTP Request

`GET https://card.api.bitcoin.com/v1/transactions`


## Upload User Document 

Endpoint to upload user documents. Currently we accept documents in 3 formats: PDF, JPG and PNG.

```shell
curl --location --request POST 'https://card.api.bitcoin.com/v1/files' \
--header 'Content-Type: multipart/form-data; boundary=--------------------------548324088818205658029465' \
--form 'file=@/C:/projects/card.jpg' \
--form 'type=PASSPORT'
```
This endpoint deletes a specific user.

<aside class="success">
If succesfull, HTTP code 200 will be returned
</aside>
<aside class="warning">If not allowed, HTTP code 400 will be returned.</aside>



### HTTP Request

`POST https://card.api.bitcoin.com/v1/files`

### URL Parameters

Parameter | Description
--------- | -----------
file      | The file object to upload. Only accepted file types are PDF, JPG and PNG
type | Type of the file being uploaded


## Retrieve All User Document

Endpoint to retrieve all user documents. File id retrieved will be used to make additional call to that file object. 

```shell
curl --location --request GET 'https://card.api.bitcoin.com/v1/files'
```
This endpoint retrieves user file.

<aside class="success">
If succesfull, HTTP code 200 will be returned
</aside>
<aside class="warning">If not allowed, HTTP code 400 will be returned.</aside>


### HTTP Request

`GET https://card.api.bitcoin.com/v1/files`


## Retrieve User Document

Endpoint to download user documents, by passing file id retrieved in call which retrieved all user files. 

```shell
curl --location --request GET 'https://card.api.bitcoin.com/v1/files/<fileId>'
```
This endpoint retrieves user file.

<aside class="success">
If succesfull, HTTP code 200 will be returned
</aside>
<aside class="warning">If not found, HTTP code 404 will be returned.</aside>


### HTTP Request

`GET https://card.api.bitcoin.com/v1/files/<fileId>`

### URL Parameters

Parameter | Description
--------- | -----------
fileId        | ID of the file to retrieve


API will respond with following <a href="Errors.xlsx">error messages</a> to all requests with missing or wrong parameters. 
