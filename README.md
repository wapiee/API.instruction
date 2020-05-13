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
# Preferred Delivery Date and Time parameters
In the Create Order request now can be indicated the preferred Delivery Date and Time in the following format:
 

```json
{
   ...
   "preferredDeliveryDate": "YYYY-MM-DD",
   "preferredDeliveryDayPart": "morning" | "evening"
}
```
 **Important!**

- Both fields are not required. If the date is indicated in incorrect format, or if the `preferredDeliveryDayPart` will have any other value than `morning` or `evening` these values will be ignored. 

- If in order there is `preferredDeliveryDate` indicated then the order will be sent to the partner not less than 2 days before that date, so that delivery does not happen before the correct date.

- ##  Tracking orders
 
To track the order you should know the tracking (WH) number of the order and should send the below mentioned API request:

> `POST /tracking`

Example:
```text
accept: application/json
content-type: application/json
x-client-id: <a guid, provided specially for you>
x-signature: <a HMAC signature you get using `HMAC sercret`, provided specially for you>
```
```json 
{  
    "trackingNumbers":[  
        "WH0000000024"
    ]
}
```

**Responses:**

 **200 OK**

```json
{
    "statuses": [
        {
            "trackingNumber": "WH0000000024",
            "deliveryStatus": "Pending",
            "deliveryStatusText": "Order is being processed",
            "troubleStatus": "IsAbsent"
        }
    ]
}
```
_Any status except 200 OK should be considered as an error._

**401 Unauthorized** - when x-client-id is missing or is wrong

**400 Bad Request** - when signature or request data is not valid

**500 Internal Server Error** - for internal errors
```
{
    "Error": "<SOME ERROR DESCRIPTION>"
}
```

##Possible statuses:

- `Pending` - the status indicates that the outbound order has been just created and still not passed to the Partner;
- `OnHold` - the status may appear, when delivery date is far in the future and the order will stay on hold on our side for some days, to prevent delivery earlier, than it is expected;
- `Error` - the status appears when during the order's processing happens a technical error. Status may appear only before the order has been sent for delivery;
- `WaybillNotCreated` - the status means that there are some questions regarding the delivery address. This is the temporary status, which after solving all address related questions will be changed to the status InTransit;
- `AssignedToPartner` - the status means that the order has been passed to our partners in the destination country for processing;
- `Dispatched` - the status indicates that the outbound order has been passed to the carrier services;
- `InTransit` - the status indicates that the order, is on it's way to the customer;
- `Delivered` - parcel has been delivered to the customer;
- `Returning` - the order is on it's way back to the initial warehouse;
- `ReturnedToSender` - indicates that products was returned to the initial warehouse and remainders are added back to the stock;
- `Cancelled` - parcel's processing is cancelled;
- `OutOfStock` - order cannot be processed, because of lack of goods in the warehouse. It will be processed right after goods are present on stock (delivered new or returned);
- `Lost` - due to some reasons, partners may report this status;
- `Damaged` - the status may occur if the parcel has been damaged on the way to the customer.


Possible trouble statuses (appear when order has some problems with delivery):
- `IsAbsent1` - indicates, that a recipient was not at the delivery address during first delivery attempt;
- `IsAbsent` - indicates, that a recipient was not at the delivery address during following delivery attempts;
- `CannotLocateConsignee1` - indicates, that a courier is not able to find where to deliver the package during first delivery attempt;
- `CannotLocateConsignee` - indicates, that a courier is not able to find where to deliver the package during following delivery attempts;
- `IsRefused` - indicates, that a recipient refused to receive the package (pay for it).


 **Important!**

Please note, that you should sign up request as you send it. That is, that payloads `{"TrackingNumbers":["12346126348721"]}` and `{"trackingNumbers": [ "12346126348721" ]}` will have differnet signatures.
The same belongs to line endings and tab spaces in a payload.


## API Methods
 ### [Creating A New Outbound Request (e.g. new order)](https://github.com/wapiee/Creating-a-new-outbound-request.git)
 ### [Preferred Delivery Date and Time parameters](https://github.com/wapiee/Preferred-Delivery-Date-and-Time-parameters.git)
 ### [Getting Product's Remained Amount](https://github.com/wapiee/Getting-Product-s-Remained-Amount.git)
 ### [Getting Product's Remainded Amount v.2 (detailed version)](https://github.com/wapiee/Getting-product-remainded-amount-v.2.git)
 ### [Update an Order Through the API](https://github.com/wapiee/Update-an-order-through-the-API.git)
