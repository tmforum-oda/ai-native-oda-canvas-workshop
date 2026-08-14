# UC-03 — Service Inventory Assistant

## Participant User Guide

## 1. Objective

In this exercise, your team will use the AI-Native Canvas Orchestrator Agent to find seeded service instances and explain their lifecycle and enabled states.

You will use:

- `serviceinventorymcp` — direct invocation of TMF638 Service Inventory MCP tools.

By completing the exercise, your team should be able to explain how natural-language customer, lifecycle-state, and stable-identifier requests are mapped to a bounded MCP tool contract.

## 2. Use-case boundary

The Component supports Service Inventory questions about:

- service instances;
- services assigned to a workshop customer;
- active, inactive, and terminated service states;
- whether a service is enabled;
- a service identified by its stable workshop ID;
- a service identified by its API-generated ID.

It does not provide Product Catalog, Product Inventory, Resource Inventory, Service Qualification, or Product Ordering capabilities.

Each query sent to the Orchestrator Agent invokes at most one selected dependency capability. The Orchestrator Agent does not chain multiple tools or skills.

## 3. Discover the dependency in the Canvas Dashboard

Before editing `component.yaml`, use the AI-Native Canvas Dashboard to identify the scaffolded ODA Component that owns the required MCP API.

The primary dependency entity for this exercise is:

| Component ID | Scaffolded Component | Deployed Component resource |
|---|---|---|
| `TMFC008` | Service Inventory Management | `si-1-serviceinventory` |

`TMFC008` identifies the scaffolded Service Inventory Management Component. `TMF638` identifies its Service Inventory Management OpenAPI contract; it is not the Component identity and is not the dependent API selected for this MVP.

1. Open the AI-Native Canvas Dashboard URL supplied by the facilitator.
2. Open the **Infrastructure** area.
3. Select **Components**.
4. If a namespace filter is displayed, select **components** or **All namespaces**.
5. Find **TMFC008 — Service Inventory Management** in the Components view. The deployed Component resource is `si-1-serviceinventory`.
6. Open that Component and confirm that its metadata identifies `TMFC008`.
7. Confirm that the Component **Status** is **Complete**.
8. Locate the **Exposed core APIs** section owned by this Component.
9. Find the row whose **API Type** is **mcp**.
10. Record the exact **Name** and **API Type** shown for that row.

For this use case, you should identify:

| Exposed API name | API type | Purpose |
|---|---|---|
| `serviceinventorymcp` | `mcp` | Service Inventory MCP tools |

Use the value shown in the **Name** column—not the ID, implementation, URL, Component name, or display label—as the dependent API name in your Component definition. Do not copy the exposed URL into `component.yaml`; the Canvas resolves it after deployment.

Do not select the `TMF638` OpenAPI row for this workshop exercise. Select the MCP API exposed by the `TMFC008` Component.

If the MCP API is missing or the Service Inventory Component is not **Complete**, stop and notify the facilitator.

## 4. Build the dependency declaration

Declare this dependency in the supplied Component template:

```yaml
dependentAPIs:
  - name: serviceinventorymcp
    specification:
      - apiType: mcp
```

The dependency name is exact and case-sensitive. Do not add an endpoint URL; the Canvas resolves it from the deployed Service Inventory Component.

## 5. Readiness checks

Open the team-specific Orchestrator Agent URL supplied by the facilitator. Before running the exercise, confirm that the sidebar shows green indicators for:

- **Component complete**;
- **Component CR readable**;
- **AI Gateway reachable**;
- **Dependencies resolved**;
- **Capabilities available**.

Confirm that `serviceinventorymcp` appears in the dependency selector.

If the dependency was recently deployed or corrected, select **Refresh Dependent Services** before continuing.

## 6. Discover the available capabilities

Keep the dependency selection set to **Auto** and run:

> What capabilities are provided by this component?

Expected result:

- the response reports four capabilities across one dependency;
- `serviceinventorymcp` exposes `service_get`, `service_create`, `service_update`, and `service_delete`.

Run:

> What MCP tools are available?

Expected result: the response lists the four discovered Service Inventory MCP tools.

Run:

> Describe the service_get capability and its parameters

Expected result: the response describes these optional arguments:

- `service_id`;
- `workshop_id`;
- `customer_id`;
- `state`;
- `offset`;
- `limit`.

The response must not list a `fields` argument. The `state` argument accepts defined TMF638 lifecycle values; it does not accept synthetic values such as `all` or `any`.

## 7. Test Service Inventory access

Select `serviceinventorymcp` explicitly in the dependency selector.

### 7.1 List all service instances

Run:

> Invoke service_get to list only the name, ID, state, enabled status, and customer ID of every service

Expected result:

| Service | Customer | State | Enabled |
|---|---|---|---|
| Fiber Access Service | `CUST-1001` | active | true |
| Managed Firewall Service | `CUST-1001` | inactive | false |
| Legacy Copper Access Service | `CUST-1002` | terminated | false |

The status line should identify `serviceinventorymcp`, API type `mcp`, capability `service_get`, and invocation mode.

Copy the API-generated ID returned for **Fiber Access Service**. The ID may change when the workshop seed data is reloaded.

### 7.2 Find services for a customer

Run:

> What service instances are available for customer CUST-1001? Return only the service name, ID and lifecycle state.

Expected result:

- **Fiber Access Service** in state `active`;
- **Managed Firewall Service** in state `inactive`;
- **Legacy Copper Access Service** is not included because it belongs to `CUST-1002`.

### 7.3 Find active services

Run:

> Which service instances are currently active? Return only their name, ID and customer ID.

Expected result: only **Fiber Access Service** for `CUST-1001`.

The request must route to `service_get`; it must not be classified as unsupported.

### 7.4 Retrieve a service by its stable workshop identifier

Run:

> List details of service where workshop ID is WS-SVC-1001.

Expected result:

- name: **Fiber Access Service**;
- customer: `CUST-1001`;
- workshop ID: `WS-SVC-1001`;
- state: `active`;
- enabled: `true`;
- bandwidth: `50 Mbps`.

`WS-SVC-1001` is a stable workshop identifier stored in a service characteristic. It is not the API-generated Service resource ID.

### 7.5 Retrieve a service by its API-generated ID

Replace `<FIBER-SERVICE-ID>` with the ID copied in section 7.1, then run:

> Invoke service_get with service_id `<FIBER-SERVICE-ID>` and describe the result

Expected result: the response describes **Fiber Access Service** and includes its active state, enabled status, customer, characteristics, and service specification.

Do not ask a follow-up such as `Describe it`. The MVP displays previous messages but does not supply them as model context. Every query must be self-contained.

### 7.6 Summarize lifecycle and enabled state

Run:

> Summarize the lifecycle state and enabled status of every service instance.

Expected result:

- **Fiber Access Service** — active and enabled;
- **Managed Firewall Service** — inactive and not enabled;
- **Legacy Copper Access Service** — terminated and not enabled.

The explanation must be grounded in the returned Service Inventory records rather than a generic lifecycle definition.

## 8. Understand the MCP argument mapping

The Service Inventory MCP server exposes semantic arguments that are translated into TMF638 queries:

| User intent | MCP argument | TMF638 request behavior |
|---|---|---|
| Retrieve one resource using its generated API ID | `service_id` | Calls `/service/{id}` |
| Find a stable workshop service | `workshop_id` | Filters the nested `workshopId` service characteristic |
| Find services belonging to a customer | `customer_id` | Filters the nested `customerId` service characteristic |
| Find services in one lifecycle state | `state` | Filters by the TMF638 service state |
| List services in every lifecycle state | omit `state` | Retrieves the complete service list |

Run these explicit controls if instructed by the facilitator:

> Invoke service_get with customer_id `CUST-1001` and list each returned service's name, state and enabled status.

> Invoke service_get with state `active` and list each returned service's name, ID and customer ID.

> Invoke service_get with workshop_id `WS-SVC-1001` and describe the returned service instance.

The controls should return the same seeded records as the corresponding natural-language questions.

## 9. Run the unsupported-domain control

With `serviceinventorymcp` selected, run:

> What product offering prices are available?

Expected result: the Orchestrator Agent rejects the request as unsupported because the Component has no Product Catalog capability.

The query must not be answered using general model knowledge or routed to `service_get`.

## 10. Validate not-found behavior

Run:

> Invoke service_get with service_id `00000000-0000-0000-0000-000000000000`.

Expected result:

- no service is fabricated;
- the response states that no service instance was found;
- the status is `not_found`;
- the response does not report HTTP 405 or `Invalid input`.

This test uses the API-generated identifier argument intentionally. Do not use a workshop ID in `service_id`.

## 11. Architectural observations

Record and discuss:

1. Why does `service_id` require an API-generated identifier?
2. Why is `workshop_id` more appropriate for `WS-SVC-1001`?
3. How does `customer_id` hide the complexity of a nested TMF638 characteristic filter?
4. Why must `state` be omitted, rather than set to `all`, when the complete inventory is required?
5. How do lifecycle state and enabled status describe different aspects of a service?
6. Why should an unknown resource be reported as `not_found` rather than as an invalid method?

## 12. Exercise completion checklist

- [ ] All five readiness indicators are green.
- [ ] `TMFC008 — Service Inventory Management` is identified as the dependency Component.
- [ ] The deployed Service Inventory Component is **Complete** in the Canvas Dashboard.
- [ ] The MCP API name and type were taken from **Exposed core APIs**.
- [ ] `serviceinventorymcp` is resolved and available for selection.
- [ ] All four MCP tools are discovered.
- [ ] `service_get` exposes the expected semantic arguments.
- [ ] The complete list contains all three workshop services.
- [ ] The customer query returns the two services assigned to `CUST-1001`.
- [ ] The active-state query returns only `WS-SVC-1001`.
- [ ] The stable workshop-ID query describes `WS-SVC-1001` correctly.
- [ ] The lifecycle summary covers active, inactive, and terminated services.
- [ ] The unknown API ID is reported as `not_found`.
- [ ] The Product Catalog control query is rejected as unsupported.
- [ ] The response status identifies `serviceinventorymcp`, MCP, and `service_get`.
- [ ] The team can explain the difference between a workshop ID and an API-generated ID.

## 13. Troubleshooting

| Symptom | Action |
|---|---|
| The Service Inventory Component or MCP API is missing in the Dashboard | Confirm the namespace filter, then ask the facilitator to verify the deployed dependency Component. |
| The dependency is missing or unresolved | Confirm the dependency name and `apiType` in `component.yaml`, then ask the facilitator to verify Component completion. |
| MCP capability discovery fails | Select **Refresh Dependent Services** once; if the problem remains, provide the visible error to the facilitator. |
| A complete-list or lifecycle-summary query returns no records | Repeat the validated prompt and ensure no `state` value such as `all` or `any` was generated. Use the explicit no-argument list control if required. |
| The customer query returns no services | Confirm the updated MCP contract includes `customer_id`; then use the explicit customer argument control. |
| `WS-SVC-1001` is treated as `service_id` | Use `workshop_id` or repeat the validated natural-language query. Workshop IDs are not generated API IDs. |
| An unknown ID returns HTTP 405 | Ask the facilitator to verify that the updated Service Inventory API image is deployed. |
| A follow-up such as `Describe it` is unsupported | Repeat the complete request and include the workshop ID or generated service ID explicitly. |
| A response is too long | Ask for one service by identifier or request only the information needed in the answer. |

## 14. Current MVP constraints

- The outer Orchestrator Agent invokes only one dependency capability for each query.
- Outer-agent tool or skill chaining is not supported.
- Previous chat messages are not supplied as conversational context.
- The exercise uses direct MCP access; no Service Inventory A2A dependency is included.
- All workshop records are synthetic.
- Create, update, and delete operations are outside the UC-03 participant exercise.
