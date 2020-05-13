# **Warehouse API**

Warehouse API is a service that provides easy and understandable working with warehouses and courier services throughout the Europe.

In this document you will find the technical information regarding API work and methods.

## API access
API can be accessed through the following test endpoint: 

> `https://warehouse-api-test.azurewebsites.net/api`

The endpoint is protected by the **authentication token**. The personalizes token is provided to each client via 
`x-client-id` header

### Message signing
 For the message signing the HMAC-SHA1 is used
- Hash key (e.g. HMAC secret) will be provided personally
- A **signature** should be indicated in the `x-signature` header

# Creating a new outbound request
To create a new request (order)  `POST /outbounds` you need following details

**Header:**

```text
accept: application/json
content-type: application/json
x-client-id: <a guid, provided specially for you>
x-signature: <a HMAC signature you get using `HMAC sercret`, provided specially for you>
```
**Body** should include all fields listed below:

```json 
// this is an example of the request
{  
    "orderNumber":"5", /*Each order number should be different*/
    "product":{  
        "name":"Product Name",
        "quantity":2,
        "price":41.5
    },
    "additionalProducts" : [
        {
            "name":"Product Name-2",
            "quantity":2,
            "price":41.5
        }
    ],
    "cashOnDelivery": 85.00,
    "receiver":{  
        "firstName":"Test",
        "lastName":"Receiver",
        "phoneNumber":"123456789",
        "emailAddress": "test@test.com",
        "nationalID": "XXXXXXXXXX",
        "houseNumber":"122",
        "addressText":"Some street",
        "addressAdditionalInfo":"apt. 35",
        "city": "Venice",
        "country":"IT",
        "zipCode":"30123"
    },
    "comment": "some text (optional)"
}
```
 **Important!**

All fields are required, except `additionalProducts,` `receiver.lastName,` `receiver.emailAddress,` `receiver.houseNumber,` `receiver.nationalID`

`cashOnDelivery` field is added later with purpose to override calculations of COD, based on `price` field of products. That is, when COD amount is specified in `cashOnDelivery` field and **is greater then zero**, amounts in `price` are ignored.

`additionalProducts` field is optional. When you need to send more than one product, First product you need to pass via `product` node and other products in `additionalProducts` array.

`nationalID` field is used to be included in invoices, which are required in some cases (e.g. sending orders to Spanish islands).


**Responses:**
 
**200 OK** 

Status means that order has been created. In response you are getting the Tracking number of the order registered in WAPI system

```json
{
    "trackingNumber": "WH0000000024"
}
```

_Any status except 200 OK should be considered as an error:_

**401 Unauthorized** 

 `x-client-id` is missing or is wrong

**400 Bad Request**  

Signature or request data is not valid

**500 Internal Server Error** 

 For internal errors
```
{
    "Error": "<SOME ERROR DESCRIPTION>"
}
```


## API Methods
 ### [Creating A New Outbound Request (e.g. new order)](https://github.com/wapiee/Creating-a-new-outbound-request.git)
 ### [Preferred Delivery Date and Time parameters](https://github.com/wapiee/Preferred-Delivery-Date-and-Time-parameters.git)
 ### [Getting Product's Remained Amount](https://github.com/wapiee/Getting-Product-s-Remained-Amount.git)
 ### [Getting Product's Remainded Amount v.2 (detailed version)](https://github.com/wapiee/Getting-product-remainded-amount-v.2.git)
 ### [Update an Order Through the API](https://github.com/wapiee/Update-an-order-through-the-API.git)
