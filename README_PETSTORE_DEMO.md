# Petstore Demo — Step-by-step guide for a live walkthrough

Purpose
-------

This file is a concise, step-by-step demo script you can use with the public Swagger Petstore UI (https://petstore.swagger.io) to show a Business Analyst what REST APIs look like in practice. It focuses on the `GET /pet/{petId}` flow but includes additional steps (create/update/delete) so you can demonstrate resource lifecycle, status codes, and request/response shapes.

Audience
--------

Business Analysts, Product Owners, or anyone who wants to see real API requests and responses in an interactive UI.

Goals for the demo (30 minutes max)
- Show how to read an endpoint in Swagger UI
- Demonstrate sending a request and inspecting the response
- Explain common status codes and response shapes
- Show create/read/update/delete (CRUD) for a simple resource

Where to run this
- Open: https://petstore.swagger.io
- You can run the demo in the browser (Swagger UI). If browser calls are blocked by CORS, use the curl examples below.

Quick checklist before you start
- Open the Petstore UI and find the "pet" tag operations (or go directly to https://petstore.swagger.io/#/pet).
- Have a terminal handy (for curl) as a fallback.
- Be ready to explain: resource, endpoint, HTTP method, path params, request body, response body, and status codes.

Demo script — steps and talking points
------------------------------------

1) Introduce the resource and endpoint
   - Say: "We're looking at the Pet resource. We'll get a pet by id with GET /pet/{petId}."
   - Point out the path: `/pet/{petId}` and the HTTP method (GET).

2) Inspect the operation in Swagger UI
   - Expand the GET /pet/{petId} operation.
   - Show the description, parameters (path `id`), and response section.
   - Talking point: "This spec is a contract — it shows what the API expects and returns."

3) Try it out (live request)
   - Click "Try it out".
   - Enter `petId = 1` (or another id shown in examples). Click "Execute".
   - Show the returned status code, response headers, and body.
   - Talking points:
     - Status code 200 means success; 404 would mean not found.
     - Response body is structured JSON with fields such as `id`, `name`, `category`, `status`.

4) Show create (POST /pet) — explain request body and validation
   - Expand POST /pet and click "Try it out".
   - Paste a small JSON body, e.g.:

```
{
  "id": 123456,
  "name": "DemoPet",
  "photoUrls": [],
  "status": "available"
}
```

   - Click Execute. If the API accepts it, you'll get 200 or 201 and the created object.
   - Talking point: This shows how the body is validated and returned.

5) Update (PUT /pet) and confirm with GET
   - Edit the pet (change `status` to `sold`) using PUT /pet. "Try it out" → Execute.
   - Then GET the pet again to show the updated field.

6) Delete (DELETE /pet/{petId})
   - Delete the pet you created. Then GET it to show 404 Not Found.
   - Talking point: Deleting removes the resource; 204 or 200 may be returned depending on the API design.

7) Show error/demo status codes
   - Use endpoints that show status codes (Petstore supports `/pet/{petId}` returning 404 for unknown ids).
   - Explain 400 (bad request), 401/403 (auth), 404 (not found), 500 (server error) in context.

Common talking points for a BA during the demo
-------------------------------------------
- Resource model: fields, types and which are required.
- Success vs error responses and how business logic maps to status codes.
- Validation rules (e.g., required fields, value ranges).
- Idempotency: which methods are safe to retry (GET, DELETE usually safe; POST may create duplicates).
- Pagination/filtering (not in this simple pet demo, but mention for list endpoints).
- Authentication: Petstore example often shows some operations without auth; real APIs usually require tokens/keys.

CORS & network fallback (if Try it out fails)
-------------------------------------------

If the browser blocks execution due to CORS or the demo server is unreachable, use curl to run requests locally. Examples below.

Example curl commands (run in a macOS Terminal)
------------------------------------------------

# Get pet with id 1
curl -i -X GET "https://petstore.swagger.io/v2/pet/1" -H "Accept: application/json"

# Create a pet (POST)
curl -i -X POST "https://petstore.swagger.io/v2/pet" -H "Content-Type: application/json" -d '{"id":123456,"name":"DemoPet","photoUrls":[],"status":"available"}'

# Update a pet (PUT)
curl -i -X PUT "https://petstore.swagger.io/v2/pet" -H "Content-Type: application/json" -d '{"id":123456,"name":"DemoPet","photoUrls":[],"status":"sold"}'

# Delete a pet
curl -i -X DELETE "https://petstore.swagger.io/v2/pet/123456"

Notes and cautions
------------------
- The public Petstore demo may reset or reject some changes; it’s intended for examples.
- Avoid relying on persistence for long-lived demos. Create, show, then delete within the session.
- Real APIs require auth and have rate limits — call these out when comparing to the demo.

Short BA checklist to follow during or after the demo
---------------------------------------------------
1. Can you summarize the endpoint in one sentence? (purpose)
2. Are required inputs and types clear? (path, body, query)
3. Are response fields and error shapes documented?
4. Which status codes are used and what do they mean in business terms?
5. Is the API idempotent for the operations you are showing?

