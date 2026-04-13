# BA inputs template & OpenAPI snippet

This file contains two practical artifacts you can copy and use:

- A minimal OpenAPI 3.0 snippet for a BA example: "Create Order" (POST /orders) plus GET /orders/{orderId}. Paste this into https://editor.swagger.io to inspect and try it.
- A BA input collection template (markdown table + CSV) you can use to collect endpoint details from stakeholders.

---

## A — Minimal OpenAPI 3.0 snippet (Orders example)

Paste the YAML below into the left panel of https://editor.swagger.io (File → Paste or File → Import Paste Raw). This shows a small contract for creating and retrieving an Order.

```yaml
openapi: 3.0.1
info:
  title: Orders API (example)
  version: '1.0.0'
  description: Minimal example for BA demo — create and fetch an order
servers:
  - url: https://api.example.com
paths:
  /orders:
    post:
      summary: Create an order
      tags: [orders]
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/OrderCreate'
            example:
              customerId: "cust_123"
              items:
                - productId: "prod_1"
                  quantity: 2
              total: 49.98
      responses:
        '201':
          description: Order created
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/Order'
        '400':
          description: Validation error
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/Error'
  /orders/{orderId}:
    get:
      summary: Get order by id
      tags: [orders]
      parameters:
        - name: orderId
          in: path
          required: true
          schema:
            type: string
      responses:
        '200':
          description: Order retrieved
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/Order'
        '404':
          description: Not found
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/Error'
components:
  schemas:
    OrderCreate:
      type: object
      required: [customerId, items, total]
      properties:
        customerId:
          type: string
        items:
          type: array
          items:
            type: object
            properties:
              productId:
                type: string
              quantity:
                type: integer
        total:
          type: number
          format: float
    Order:
      allOf:
        - $ref: '#/components/schemas/OrderCreate'
        - type: object
          properties:
            id:
              type: string
            status:
              type: string
              enum: [created, paid, shipped, cancelled]
    Error:
      type: object
      properties:
        error:
          type: object
          properties:
            code:
              type: string
            message:
              type: string

```

Notes about the snippet
- The snippet models the BA example: POST /orders creates an order (201) and GET /orders/{orderId} retrieves it (200). It includes minimal schemas and an example to paste into the editor.
- In a real project you’ll extend schemas (address, billing, items details), security (BearerAuth), and add examples for error responses.

---

## B — BA input collection template

Use the table below to collect endpoint details from stakeholders. Paste into a doc or spreadsheet, duplicate rows per endpoint.

Markdown table template

| Endpoint | Method | One-line purpose | Path (example) | Parameters (path/query/header/body) | Request example (JSON) | Success response example (JSON) | Error examples (code + JSON) | Acceptance criteria (Given/When/Then) | Notes |
|---|---|---|---|---|---|---|---|---|---|
| Create order | POST | Create a new order | /orders | body: OrderCreate (required) | {"customerId":"cust_123","items":[{"productId":"prod_1","quantity":2}],"total":49.98} | {"id":"order_1","customerId":"cust_123","items":[...],"total":49.98,"status":"created"} | 400: {"error":{"code":"INVALID_INPUT","message":"total must be > 0"}} | Given valid payload..., When POST /orders, Then 201 with order and id | Requires auth; no duplicate orders for same request id |

CSV template (copy-paste into spreadsheet)

"Endpoint","Method","One-line purpose","Path","Parameters","Request example","Success response example","Error examples","Acceptance criteria","Notes"
"Create order","POST","Create a new order","/orders","body: OrderCreate (required)","{""customerId"":""cust_123"",""items"": [{""productId"":""prod_1"",""quantity"":2}],""total"":49.98}","{""id"":""order_1"",""customerId"":""cust_123"",""items"":[...],""total"":49.98,""status"":""created""}","400: {""error"": {""code"": ""INVALID_INPUT"", ""message"": ""total must be > 0""}}","Given valid payload...,When POST /orders,Then 201 with order and id","Requires auth"

How to use this template
- Copy the Markdown table into your product doc or paste the CSV into Excel/Google Sheets.
- Fill one row per endpoint. Provide at least one happy-path request/response and one error example.
- Add acceptance criteria that are testable (Given/When/Then). Developers will use these to create the OpenAPI spec and tests.

---

If you want, I can:
- convert your filled template rows into an OpenAPI YAML (one endpoint or whole set), or
- create a ready-to-share spreadsheet file and attach it to this repo.

File created: BA templates file with OpenAPI snippet and stakeholder input template.
