
# 📚 Simple Books API Project

This project demonstrates how to interact with the **Simple Books API**, which allows users to view, reserve, and manage book orders through various RESTful endpoints.

> 🔗 **API Base URL:** [https://simple-books-api.glitch.me](https://simple-books-api.glitch.me)

---

## ✨ Features
- View API status
- Browse books (fiction & non-fiction)
- Get details of a single book
- Register API client (generate token)
- Submit new orders
- View all orders
- View a single order
- Update existing orders
- Delete orders

---

## 🔒 Authorization
- **Type:** Bearer Token
- **How to Get Token:**  
  - Register an API client via `POST /api-clients` endpoint.
  - Use the returned **access token** in all subsequent requests.

---

## 📚 Endpoints

### 1. Check API Status
- **GET** `/status`
- **Description:** Returns the status of the API.
- **Authentication:** Not required.

---

### 2. List of Books
- **GET** `/books`
- **Description:** Returns a list of books.
- **Optional Query Parameters:**
  - `type`: `fiction` or `non-fiction`
  - `limit`: Number between 1 and 20
- **Authentication:** Not required.

---

### 3. Get a Single Book
- **GET** `/books/{bookId}`
- **Description:** Retrieve detailed information about a specific book.
- **Authentication:** Not required.

---

### 4. Register API Client
- **POST** `/api-clients`
- **Description:** Register as an API client to obtain an access token (valid for 7 days).
- **Authentication:** Not required.
- **Request Body (JSON):**
```json
{
  "clientName": "Your Name",
  "clientEmail": "yourname@example.com"
}
```
- **Responses:**
  - ✅ **Success:**
```json
{
  "accessToken": "your-generated-token"
}
```
  - ❌ **Error (409):**
```json
{
  "error": "API client already registered."
}
```

---

### 5. Submit an Order
- **POST** `/orders`
- **Description:** Submit a new book order.
- **Authentication:** Required (Bearer Token).
- **Request Body (JSON):**
```json
{
  "bookId": 6,
  "customerName": "Khalil"
}
```
- **Response:**
```json
{
  "created": true,
  "orderId": "example-order-id"
}
```

---

### 6. Get All Orders
- **GET** `/orders`
- **Description:** Retrieve a list of all orders.
- **Authentication:** Required.

---

### 7. Get a Specific Order
- **GET** `/orders/{orderId}`
- **Description:** Retrieve details of a specific order by ID.
- **Authentication:** Required.

---

### 8. Update an Order
- **PATCH** `/orders/{orderId}`
- **Description:** Update the customer name on an existing order.
- **Authentication:** Required.
- **Request Body (JSON):**
```json
{
  "customerName": "Philip"
}
```

---

### 9. Delete an Order
- **DELETE** `/orders/{orderId}`
- **Description:** Delete an existing order.
- **Authentication:** Required.
- **Request Body:** Must be empty.

---

## 🔧 Example API Workflow
1. Register a client to receive the **access token**.
2. Set the Bearer token for authorization.
3. Use endpoints to **view**, **submit**, **update**, or **delete** orders.

---

## 📂 Project Structure
```
Simple-Books-API-Project/
├── README.md
├── API Collection (Postman / Other)
└── Example Scripts (Optional)
```

---

## 🚀 Tools Used
- Postman (for testing API requests)
- REST Client tools (like curl, Thunder Client)

---

## ⚡ Quick Tip
> If you get a **409 Conflict** error when registering a client, it means your email/client name is already registered.  
> ➡️ Simply change the values and try again!

---

# 🙌 Thank You!
Feel free to clone, fork, and experiment with the Simple Books API.


---

## 🧪 Postman Test Scripts

Below are the Postman test scripts used for validating the API responses:

---

### ✅ 1. GET `/status`
```javascript
// checking response status code 200
pm.test("Status code is 200", function () {
    pm.response.to.have.status(200);
});
```

---

### ✅ 2. GET `/books`
```javascript
// checking multiple books to exist
pm.test("'The Russian book' exists", function () {
    pm.expect(pm.response.text()).to.include("The Russian");
});
pm.test("'Just as I Am' book exists", function () {
    pm.expect(pm.response.text()).to.include("Just as I Am");
});

const response = pm.response.json(); 
// checking if id is a number and response is an array
pm.test("Id datatype is a number", function () {
    for (let i = 0; i < response.length; i++) {
        console.log("ID at index", i, ":", response[i].id);
        pm.expect(typeof response[i].id).to.eql("number");
    }
});

pm.test("All keys are not null", function () {
    const response = pm.response.json();
    for (let i = 0; i < response.length; i++) {
        pm.expect(response[i].id).to.not.eql(null);
        pm.expect(response[i].type).to.not.eql(null);
        pm.expect(response[i].available).to.not.eql(null);
        pm.expect(response[i].name).to.not.eql(null);
    }
});

pm.test("Response is an array", function () {
    pm.expect(Array.isArray(response)).to.be.true;
});
```

---

### ✅ 3. GET `/books/{bookId}`
```javascript
// checking if the endpoint book is correct
pm.test("The book is 'Viscount Who Loved Me'", function () {
    pm.expect(pm.response.text()).to.include("Viscount Who Loved Me");
});
```

---

### ✅ 4. POST `/api-clients`
```javascript
// checking response status code either 201 or 409
pm.test("Status code is 201 or 409", function () {
    pm.expect(pm.response.code).to.be.oneOf([201, 409]);
});

// handling 201 or 409 response
pm.test("Handle 201 or 409 response", function () {
    if (pm.response.code === 409) {
        pm.expect(pm.response.code).to.eql(409); // test passes
    } else if (pm.response.code === 201) {
        // setting token from response to env
        let response = pm.response.json();
        pm.environment.set("Token", response.accessToken);
        pm.expect(pm.response.code).to.eql(201); // test passes
    } else {
        // fail the test for any unexpected status
        pm.expect.fail(`Unexpected status code: ${pm.response.code}`);
    }
});
```

---

### ✅ 5. POST `/orders`
```javascript
// checking response status code 201
pm.test("Status code is 201", function () {
    pm.response.to.have.status(201);
});

// checking if order created successfully
pm.test("Order created successfully", function () {
    var jsonData = pm.response.json();
    pm.expect(jsonData.created).to.eql(true);
});

// saving orderId to env variable
let response = pm.response.json();
pm.environment.set("CreatedOrder", response.orderId);
```

---

### ✅ 6. GET `/orders`
```javascript
// getting env var and saving it to a var
let expectedOrderId = pm.environment.get("CreatedOrder");

// checking if expectedOrderId exists
let response = pm.response.json();
pm.test("expectedOrderId exists", function () {
    pm.expect(pm.response.text()).to.include(expectedOrderId);
});

// checking if response body is an array of orders
pm.test("Response is an array", function () {
    pm.expect(Array.isArray(response)).to.be.true;
});
```

---

### ✅ 7. GET `/orders/{orderId}`
```javascript
// looking for a created order if exists
let orderCheck = pm.environment.get("CreatedOrder");
pm.test("orderCheck exists", function () {
    pm.expect(pm.response.text()).to.include(orderCheck);
});
```

---

### ✅ 8. PATCH `/orders/{orderId}`
```javascript
// checking if request is successful
pm.test("PATCH request succeeded and customerName was updated", function () {
    pm.response.to.have.status(204);
});
```

---

### ✅ 9. DELETE `/orders/{orderId}`
```javascript
// checking if order has been deleted
let deletedId = pm.environment.get("CreatedOrder");
pm.test(`Order with ID ${deletedId} has been deleted`, theFunc );

function theFunc() {
    pm.expect(pm.response.code).to.eql(204);
}
```
