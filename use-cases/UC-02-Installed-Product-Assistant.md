# UC-02 — Installed Product Assistant

## Participant User Guide

## 1. Objective

In this exercise, your team will use the AI-Native Canvas Orchestrator Agent to find seeded installed products and explain their Product Inventory lifecycle state.

You will use:

- `productinventorymcp` — direct invocation of TMF637 Product Inventory MCP tools.

By completing the exercise, your team should be able to explain how natural-language customer, lifecycle, and stable-identifier requests are mapped to a bounded MCP tool contract.

## 2. Use-case boundary

The Component supports Product Inventory questions about:

- installed products;
- products assigned to a workshop customer;
- active, suspended, and terminated product states;
- a product identified by its stable product serial number;
- a product identified by its API-generated ID.

It does not provide Product Catalog, Service Inventory, Resource Inventory, Service Qualification, or Product Ordering capabilities.

Each query sent to the Orchestrator Agent invokes at most one selected dependency capability. The Orchestrator Agent does not chain multiple tools or skills.

## 3. Discover the dependency in the Canvas Dashboard

Before editing `component.yaml`, use the AI-Native Canvas Dashboard to identify the scaffolded ODA Component that owns the required MCP API.

The primary dependency entity for this exercise is:

| Component ID | Scaffolded Component | Deployed Component resource |
|---|---|---|
| `TMFC005` | Product Inventory Management | `pi-1-productinventory` |

`TMFC005` identifies the scaffolded Product Inventory Management Component. `TMF637` identifies its Product Inventory Management OpenAPI contract; it is not the Component identity and is not the dependent API selected for this MVP.

1. Open the AI-Native Canvas Dashboard URL supplied by the facilitator.
2. Open the **Infrastructure** area.
3. Select **Components**.
4. If a namespace filter is displayed, select **components** or **All namespaces**.
5. Find **TMFC005 — Product Inventory Management** in the Components view. The deployed Component resource is `pi-1-productinventory`.
6. Open that Component and confirm that its metadata identifies `TMFC005`.
7. Confirm that the Component **Status** is **Complete**.
8. Locate the **Exposed core APIs** section owned by this Component.
9. Find the row whose **API Type** is **mcp**.
10. Record the exact **Name** and **API Type** shown for that row.

For this use case, you should identify:

| Exposed API name | API type | Purpose |
|---|---|---|
| `productinventorymcp` | `mcp` | Product Inventory MCP tools |

Use the value shown in the **Name** column—not the ID, implementation, URL, Component name, or display label—as the dependent API name in your Component definition. Do not copy the exposed URL into `component.yaml`; the Canvas resolves it after deployment.

Do not select the `TMF637` OpenAPI row for this workshop exercise. Select the MCP API exposed by the `TMFC005` Component.

If the MCP API is missing or the Product Inventory Component is not **Complete**, stop and notify the facilitator.

## 4. Build the dependency declaration

Declare this dependency in the supplied Component template:

```yaml
dependentAPIs:
  - name: productinventorymcp
    specification:
      - apiType: mcp
```

The dependency name is exact and case-sensitive. Do not add an endpoint URL; the Canvas resolves it from the deployed Product Inventory Component.

## 5. Readiness checks

Open the team-specific Orchestrator Agent URL supplied by the facilitator. Before running the exercise, confirm that the sidebar shows green indicators for:

- **Component complete**;
- **Component CR readable**;
- **AI Gateway reachable**;
- **Dependencies resolved**;
- **Capabilities available**.

Confirm that `productinventorymcp` appears in the dependency selector.

If the dependency was recently deployed or corrected, select **Refresh Dependent Services** before continuing.

## 6. Discover the available capabilities

Keep the dependency selection set to **Auto** and run:

> What capabilities are provided by this component?

Expected result:

- the response reports four capabilities across one dependency;
- `productinventorymcp` exposes `product_get`, `product_create`, `product_update`, and `product_delete`.

Run:

> What MCP tools are available?

Expected result: the response lists the four discovered Product Inventory MCP tools.

Run:

> Describe the product_get capability and its parameters

Expected result: the response describes these optional arguments:

- `product_id`;
- `product_serial_number`;
- `customer_id`;
- `status`;
- `offset`;
- `limit`;
- `filter`.

The response must not list a `fields` argument.

## 7. Test Product Inventory access

Select `productinventorymcp` explicitly in the dependency selector.

### 7.1 Find products installed for a customer

Run:

> What products are installed for customer CUST-1001?

Expected result:

| Product | Product serial number | Status |
|---|---|---|
| Fiber Broadband 50 Mbps | `WS-PROD-1001` | active |
| Business Firewall | `WS-PROD-1002` | suspended |

**Legacy Business Broadband** must not be included because it is assigned to `CUST-1002`.

The status line should identify `productinventorymcp`, API type `mcp`, capability `product_get`, and invocation mode.

### 7.2 Find active installed products

Run:

> Which installed products are currently active?

Expected result: only **Fiber Broadband 50 Mbps**, with product serial number `WS-PROD-1001` and status `active`.

The request must route to `product_get`; it must not be classified as unsupported.

### 7.3 Retrieve a product by its stable workshop identifier

Run:

> Describe installed product WS-PROD-1001.

Expected result:

- name: **Fiber Broadband 50 Mbps**;
- customer: `CUST-1001`;
- product serial number: `WS-PROD-1001`;
- status: `active`;
- download speed: `50 Mbps`.

`WS-PROD-1001` is a stable workshop product serial number. It is not the API-generated Product resource ID.

### 7.4 Explain the seeded lifecycle states

Run:

> List every installed product and explain its current lifecycle state using the seeded product information.

Expected result:

- `WS-PROD-1001` is active;
- `WS-PROD-1002` is suspended for a planned policy review;
- `WS-PROD-1003` is terminated after migration to fiber.

The explanation must be grounded in the returned Product Inventory records rather than a generic lifecycle definition.

## 8. Understand the MCP argument mapping

The Product Inventory MCP server exposes semantic arguments that are translated into TMF637 queries:

| User intent | MCP argument | TMF637 request behavior |
|---|---|---|
| Retrieve one resource using its generated API ID | `product_id` | Calls `/product/{id}` |
| Find a stable workshop product | `product_serial_number` | Filters by `productSerialNumber` |
| Find products belonging to a customer | `customer_id` | Filters the nested `customerId` product characteristic |
| Find products in a lifecycle state | `status` | Filters by Product Inventory status |

Run these explicit controls if instructed by the facilitator:

> Invoke product_get with customer_id `CUST-1001` and list each returned product’s name, product serial number and status.

> Invoke product_get with status `active` and list each returned product’s name, product serial number and status.

> Invoke product_get with product_serial_number `WS-PROD-1001` and describe the returned installed product.

The controls should return the same seeded records as the corresponding natural-language questions.

## 9. Run the unsupported-domain control

With `productinventorymcp` selected, run:

> What network resources are available at the Mumbai site?

Expected result: the Orchestrator Agent rejects the request as unsupported because the Component has no Resource Inventory capability.

The query must not be answered using general model knowledge or routed to `product_get`.

## 10. Validate not-found behavior

Run:

> Invoke product_get with product_id `00000000-0000-0000-0000-000000000000`.

Expected result:

- no product is fabricated;
- the response states that no installed product was found;
- the status is `not_found`;
- the response does not report HTTP 405 or `Invalid input`.

This test uses the API-generated identifier argument intentionally. Do not use a workshop product serial number in `product_id`.

## 11. Architectural observations

Record and discuss:

1. Why does `product_id` require an API-generated identifier?
2. Why is `product_serial_number` more appropriate for `WS-PROD-1001`?
3. How does `customer_id` hide the complexity of a nested TMF637 characteristic filter?
4. How does the capability description help the outer Orchestrator Agent select `product_get`?
5. Why should an unknown resource be reported as `not_found` rather than as an invalid method?

## 12. Exercise completion checklist

- [ ] All five readiness indicators are green.
- [ ] `TMFC005 — Product Inventory Management` is identified as the dependency Component.
- [ ] The deployed Product Inventory Component is **Complete** in the Canvas Dashboard.
- [ ] The MCP API name and type were taken from **Exposed core APIs**.
- [ ] `productinventorymcp` is resolved and available for selection.
- [ ] All four MCP tools are discovered.
- [ ] `product_get` exposes the expected semantic arguments.
- [ ] The customer query returns the two products assigned to `CUST-1001`.
- [ ] The active-products query returns only `WS-PROD-1001`.
- [ ] The stable workshop-ID query describes `WS-PROD-1001` correctly.
- [ ] The lifecycle explanation covers active, suspended, and terminated products.
- [ ] The unknown API ID is reported as `not_found`.
- [ ] The Resource Inventory control query is rejected as unsupported.
- [ ] The response status identifies `productinventorymcp`, MCP, and `product_get`.
- [ ] The team can explain the difference between a product serial number and an API-generated ID.

## 13. Troubleshooting

| Symptom | Action |
|---|---|
| The Product Inventory Component or MCP API is missing in the Dashboard | Confirm the namespace filter, then ask the facilitator to verify the deployed dependency Component. |
| The dependency is missing or unresolved | Confirm the dependency name and `apiType` in `component.yaml`, then ask the facilitator to verify Component completion. |
| MCP capability discovery fails | Select **Refresh Dependent Services** once; if the problem remains, provide the visible error to the facilitator. |
| The customer query returns no products | Confirm the updated MCP contract includes `customer_id`; then use the explicit customer argument control. |
| The active-products query is unsupported | Confirm the updated `product_get` description was discovered after refreshing dependencies. |
| `WS-PROD-1001` is treated as `product_id` | Use `product_serial_number` or repeat the validated natural-language query. Workshop IDs are not generated API IDs. |
| An unknown ID returns HTTP 405 | Ask the facilitator to verify that the updated Product Inventory API image is deployed. |
| A follow-up such as `Describe it` is unsupported | Repeat the complete request and include the product serial number explicitly. |
| A response is too long | Ask for one product by serial number or request only the information needed in the answer. |

## 14. Current MVP constraints

- The outer Orchestrator Agent invokes only one dependency capability for each query.
- Outer-agent tool or skill chaining is not supported.
- Previous chat messages are not supplied as conversational context.
- The exercise uses direct MCP access; no Product Inventory A2A dependency is included.
- All workshop records are synthetic.
- Create, update, and delete operations are outside the UC-02 participant exercise.
