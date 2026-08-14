# UC-04 — Network Resource Locator

## Participant User Guide

## 1. Objective

In this exercise, your team will use the AI-Native Canvas Orchestrator Agent to locate seeded network resources and explain their inventory, administrative, and operational states.

You will use:

- `resourceinventorymcp` — direct invocation of TMF639 Resource Inventory MCP tools.

By completing the exercise, your team should be able to explain how natural-language resource-type, location, state, and stable-identifier requests are mapped to a bounded MCP tool contract.

## 2. Use-case boundary

The Component supports Resource Inventory questions about:

- network resources;
- resources of a specified type or category;
- resources at an exact workshop location;
- available, reserved, and alarm resource states;
- administrative and operational state;
- a resource identified by its stable workshop ID;
- a resource identified by its API-generated ID.

It does not provide Product Catalog, Product Inventory, Service Inventory, Service Qualification, or Product Ordering capabilities.

Each query sent to the Orchestrator Agent invokes at most one selected dependency capability. The Orchestrator Agent does not chain multiple tools or skills.

## 3. Discover the dependency in the Canvas Dashboard

Before editing `component.yaml`, use the AI-Native Canvas Dashboard to identify the scaffolded ODA Component that owns the required MCP API.

The primary dependency entity for this exercise is:

| Component ID | Scaffolded Component | Deployed Component resource |
|---|---|---|
| `TMFC012` | Resource Inventory Management | `ri-1-resourceinventory` |

`TMFC012` identifies the scaffolded Resource Inventory Management Component. `TMF639` identifies its Resource Inventory Management OpenAPI contract; it is not the Component identity and is not the dependent API selected for this MVP.

1. Open the AI-Native Canvas Dashboard URL supplied by the facilitator.
2. Open the **Infrastructure** area.
3. Select **Components**.
4. If a namespace filter is displayed, select **components** or **All namespaces**.
5. Find **TMFC012 — Resource Inventory Management** in the Components view. The deployed Component resource is `ri-1-resourceinventory`.
6. Open that Component and confirm that its metadata identifies `TMFC012`.
7. Confirm that the Component **Status** is **Complete**.
8. Locate the **Exposed core APIs** section owned by this Component.
9. Find the row whose **API Type** is **mcp**.
10. Record the exact **Name** and **API Type** shown for that row.

For this use case, you should identify:

| Exposed API name | API type | Purpose |
|---|---|---|
| `resourceinventorymcp` | `mcp` | Resource Inventory MCP tools |

Use the value shown in the **Name** column—not the ID, implementation, URL, Component name, or display label—as the dependent API name in your Component definition. Do not copy the exposed URL into `component.yaml`; the Canvas resolves it after deployment.

Do not select the `TMF639` OpenAPI row for this workshop exercise. Select the MCP API exposed by the `TMFC012` Component.

If the MCP API is missing or the Resource Inventory Component is not **Complete**, stop and notify the facilitator.

## 4. Build the dependency declaration

Declare this dependency in the supplied Component template:

```yaml
dependentAPIs:
  - name: resourceinventorymcp
    specification:
      - apiType: mcp
```

The dependency name is exact and case-sensitive. Do not add an endpoint URL; the Canvas resolves it from the deployed Resource Inventory Component.

## 5. Readiness checks

Open the team-specific Orchestrator Agent URL supplied by the facilitator. Before running the exercise, confirm that the sidebar shows green indicators for:

- **Component complete**;
- **Component CR readable**;
- **AI Gateway reachable**;
- **Dependencies resolved**;
- **Capabilities available**.

Confirm that `resourceinventorymcp` appears in the dependency selector.

If the dependency was recently deployed or corrected, select **Refresh Dependent Services** before continuing.

## 6. Discover the available capabilities

Keep the dependency selection set to **Auto** and run:

> What capabilities are provided by this component?

Expected result:

- the response reports four capabilities across one dependency;
- `resourceinventorymcp` exposes `resource_get`, `resource_create`, `resource_update`, and `resource_delete`.

Run:

> What MCP tools are available?

Expected result: the response lists the four discovered Resource Inventory MCP tools.

Run:

> Describe the resource_get capability and its parameters

Expected result: the response describes these optional arguments:

- `resource_id`;
- `workshop_id`;
- `resource_name`;
- `resource_type`;
- `location_id`;
- `resource_status`;
- `offset`;
- `limit`.

The response must not list `fields` or a generic `filter` argument. `resource_status` accepts one defined TMF639 status; it does not accept synthetic values such as `all`, `any`, or a comma-separated status list.

## 7. Test Resource Inventory access

Select `resourceinventorymcp` explicitly in the dependency selector.

### 7.1 List all network resources

Run:

> Invoke resource_get to list only the name, ID, resource type, resource status, administrative state, operational state and location of every resource

Expected result:

| Resource | Workshop ID | Type | Resource status | Administrative state | Operational state | Location |
|---|---|---|---|---|---|---|
| Accelerate Asia Edge Router | `WS-RES-1001` | EdgeRouter | available | unlocked | enable | Accelerate Asia Primary Site |
| Accelerate Asia Backup Router | `WS-RES-1002` | EdgeRouter | reserved | locked | disable | Accelerate Asia Secondary Site |
| Accelerate Asia Fiber Access Node | `WS-RES-1003` | OpticalLineTerminal | alarm | unlocked | disable | Accelerate Asia Primary Site |

The status line should identify `resourceinventorymcp`, API type `mcp`, capability `resource_get`, and invocation mode.

Copy the API-generated ID returned for **Accelerate Asia Edge Router**. The ID may change when the workshop seed data is reloaded.

### 7.2 Find all router resources

Run this exact query:

> List every router resource at the Accelerate Asia workshop locations across all resource statuses. Return only its name, ID, resource status and location.

Expected result:

- **Accelerate Asia Edge Router** in status `available`;
- **Accelerate Asia Backup Router** in status `reserved`;
- **Accelerate Asia Fiber Access Node** is not included because it is an optical access resource.

The words **across all resource statuses** are intentional. They distinguish complete router discovery from a request for only resources whose exact TMF639 status is `available`.

### 7.3 Identify resources that are not available

Run:

> List every network resource and identify only those whose resource status is not available. Include their name, workshop ID, status and the reason where available.

Expected result:

- **Accelerate Asia Backup Router**, `WS-RES-1002`, status `reserved`, held for the workshop resilience exercise;
- **Accelerate Asia Fiber Access Node**, `WS-RES-1003`, status `alarm`, with a simulated uplink fault;
- **Accelerate Asia Edge Router** is not included because its status is `available`.

The Orchestrator Agent retrieves the complete applicable result and grounds the exclusion in the returned records. A scalar `resource_status` filter cannot represent multiple values or an exclusion.

### 7.4 Retrieve a resource by its stable workshop identifier

Run:

> Invoke resource_get with workshop_id WS-RES-1001 and describe the returned resource

Expected result:

- name: **Accelerate Asia Edge Router**;
- workshop ID: `WS-RES-1001`;
- category: `Router`;
- resource type: `EdgeRouter`;
- resource status: `available`;
- administrative state: `unlocked`;
- operational state: `enable`;
- capacity: `100 Gbps`;
- location: **Accelerate Asia Primary Site**.

`WS-RES-1001` is a stable workshop identifier stored in a resource characteristic. It is not the API-generated Resource ID.

### 7.5 Retrieve a resource by its API-generated ID

Replace `<EDGE-ROUTER-API-ID>` with the ID copied in section 7.1, then run:

> Invoke resource_get with resource_id `<EDGE-ROUTER-API-ID>` and describe the result

Expected result: the response describes the same Accelerate Asia Edge Router record.

Do not submit the placeholder text literally. Do not ask a follow-up such as `Describe it`. The MVP displays previous messages but does not supply them as model context, so every query must be self-contained.

## 8. Test explicit semantic filters

The Resource Inventory MCP server exposes semantic arguments that are translated into TMF639 queries:

| User intent | MCP argument | TMF639 request behavior |
|---|---|---|
| Retrieve one resource using its generated API ID | `resource_id` | Calls `/resource/{id}` |
| Find a stable workshop resource | `workshop_id` | Filters the nested `workshopId` resource characteristic |
| Find an exact named resource | `resource_name` | Filters by the complete resource name |
| Find a resource type or category | `resource_type` | Filters by TMF639 `category` |
| Find resources at one exact location ID | `location_id` | Filters by TMF639 `place.id` |
| Find resources in one inventory state | `resource_status` | Filters by TMF639 `resourceStatus` |
| List resources across every inventory state | omit `resource_status` | Retrieves resources without a status filter |

Run these explicit controls if instructed by the facilitator:

> Invoke resource_get with resource_type Router and list each returned resource's name, resource status and location.

Expected result: the two router resources.

> Invoke resource_get with location_id WS-LOC-MUM-001 and list each returned resource's name, type and resource status.

Expected result: the Edge Router and Fiber Access Node at the Accelerate Asia Primary Site.

> Invoke resource_get with resource_status reserved and return each resource's name, workshop ID and location.

Expected result: only the Accelerate Asia Backup Router.

> Invoke resource_get with resource_status alarm and explain why each returned resource is in alarm.

Expected result: only the Accelerate Asia Fiber Access Node, with its simulated uplink fault.

## 9. Run the unsupported-domain control

With `resourceinventorymcp` selected, run:

> What is the status of product order PO-1001?

Expected result:

- the Orchestrator Agent rejects the request as unsupported;
- the response status is `unsupported`;
- `resource_get` is not invoked;
- no Resource Inventory result is fabricated for `PO-1001`.

The Component has no Product Ordering capability, and the query must not be answered using general model knowledge.

## 10. Validate not-found behavior

Run:

> Invoke resource_get with resource_id `00000000-0000-0000-0000-000000000000`.

Expected result:

- no resource is fabricated;
- the response states that no resource was found;
- the status is `not_found`;
- the response does not report HTTP 405 or `Invalid input`.

This test uses the API-generated identifier argument intentionally. Do not use a workshop ID in `resource_id`.

## 11. Architectural observations

Record and discuss:

1. Why does `resource_id` require an API-generated identifier?
2. Why is `workshop_id` more appropriate for `WS-RES-1001`?
3. How do `resource_type` and `location_id` hide TMF639 query details from the outer agent?
4. Why must `resource_status` be omitted when the request covers all statuses or excludes one status?
5. How are resource status, administrative state, operational state, and usage state different?
6. Why must an ID argument originate in the user's request rather than capability examples?
7. Why should an unknown resource be reported as `not_found` rather than as an invalid method?

## 12. Exercise completion checklist

- [ ] All five readiness indicators are green.
- [ ] `TMFC012 — Resource Inventory Management` is identified as the dependency Component.
- [ ] The deployed Resource Inventory Component is **Complete** in the Canvas Dashboard.
- [ ] The MCP API name and type were taken from **Exposed core APIs**.
- [ ] `resourceinventorymcp` is resolved and available for selection.
- [ ] All four MCP tools are discovered.
- [ ] `resource_get` exposes the expected semantic arguments.
- [ ] The complete list contains all three workshop resources.
- [ ] The router query returns both router resources across all statuses.
- [ ] The non-available query returns the reserved and alarm resources.
- [ ] The stable workshop-ID query describes `WS-RES-1001` correctly.
- [ ] The generated-ID query retrieves the same Edge Router record.
- [ ] The unknown API ID is reported as `not_found`.
- [ ] The Product Ordering control query is rejected as unsupported.
- [ ] The response status identifies `resourceinventorymcp`, MCP, and `resource_get`.
- [ ] The team can explain the difference between a workshop ID and an API-generated ID.

## 13. Troubleshooting

| Symptom | Action |
|---|---|
| The Resource Inventory Component or MCP API is missing in the Dashboard | Confirm the namespace filter, then ask the facilitator to verify the deployed dependency Component. |
| The dependency is missing or unresolved | Confirm the dependency name and `apiType` in `component.yaml`, then ask the facilitator to verify Component completion. |
| MCP capability discovery fails | Select **Refresh Dependent Services** once; if the problem remains, provide the visible error to the facilitator. |
| A router query returns only the available Edge Router | Use the exact query in section 7.2 and include **across all resource statuses**. |
| A multi-status or exclusion query reports invalid arguments | Use the exact query in section 7.3; do not provide a comma-separated value to `resource_status`. |
| A location-name query returns no resources | Use the complete-list query, or supply one exact location ID through `location_id`. |
| `WS-RES-1001` is treated as `resource_id` | Use `workshop_id` or repeat the validated stable-identifier query. Workshop IDs are not generated API IDs. |
| The literal placeholder `<EDGE-ROUTER-API-ID>` returns `not_found` | Replace it with the generated ID returned by the complete-list query. |
| An unknown ID returns HTTP 405 | Ask the facilitator to verify that the updated Resource Inventory API image is deployed. |
| A follow-up such as `Describe it` is unsupported | Repeat the complete request and include the workshop ID or generated resource ID explicitly. |
| A response is too long | Ask for one resource by identifier or request only the information needed in the answer. |

## 14. Current MVP constraints

- The outer Orchestrator Agent invokes only one dependency capability for each query.
- Outer-agent tool or skill chaining is not supported.
- Previous chat messages are not supplied as conversational context.
- The exercise uses direct MCP access; no Resource Inventory A2A dependency is included.
- `resource_status` accepts one exact status; multi-status and exclusion questions require an unfiltered retrieval followed by grounded response selection.
- All workshop records are synthetic.
- Create, update, and delete operations are outside the UC-04 participant exercise.
