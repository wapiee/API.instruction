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

## API Methods
 ### [Creating A New Outbound Request (e.g. new order)](https://github.com/wapiee/Creating-a-new-outbound-request.git) 
 ### [Preferred Delivery Date and Time parameters](https://github.com/wapiee/Preferred-Delivery-Date-and-Time-parameters.git)
 ### [Getting Product's Remained Amount](https://github.com/wapiee/Getting-Product-s-Remained-Amount.git)
 ### [Getting Product's Remainded Amount v.2 (detailed version)](https://github.com/wapiee/Getting-product-remainded-amount-v.2.git)
 ### [Update an Order Through the API](https://github.com/wapiee/Update-an-order-through-the-API.git)
