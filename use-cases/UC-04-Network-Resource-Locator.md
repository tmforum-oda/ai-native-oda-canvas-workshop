# UC-04 — Network Resource Locator

## Participant user guide

## 1. Objective

In this exercise, your team will design and validate an AI-Native ODA Component that locates seeded network resources and explains their inventory, administrative, and operational states.

The Component will depend on:

- `resourceinventorymcp` for access to Resource Inventory MCP tools.

You will use this interface to find resources by type, location, state, and stable workshop identifier.

## 2. Use-case boundary

The Component supports Resource Inventory questions about:

- network resources;
- resources of a specified type;
- resources at an Accelerate Asia workshop location;
- available, reserved, and alarm resource states;
- administrative and operational states;
- a resource identified by its stable workshop ID.

It does not provide Product Catalog, Product Inventory, Service Inventory, Service Qualification, or Product Ordering capabilities.

All workshop records are synthetic and prepared specifically for this exercise.

The Orchestrator Agent provides single-turn conversations. Include the relevant entity, identifier, requested fields, and other necessary context in every query. Do not rely on an earlier response or a follow-up such as “Describe it”.

## 3. Identify the dependent function in the AI-Native ODA Canvas Dashboard

### 3.1 Open the AI-Native ODA Canvas Dashboard

Obtain the **AI-Native ODA Canvas Dashboard URL** from the facilitator's presentation or the link pinned in the workshop Teams channel.

> **Workshop certificate notice:** The AI-Native ODA Canvas Dashboard and deployed Orchestrator Agent use workshop-managed self-signed certificates. Your browser may display **Your connection is not private**, **Certificate not trusted**, or a similar warning. Confirm that the complete URL exactly matches the facilitator-provided or pinned workshop URL. Then select the browser's **Advanced** option and choose **Proceed**, **Continue**, or **Accept the risk**. Do not proceed if the hostname differs or the link did not come from the facilitator; ask the facilitator to verify it.

1. Open the AI-Native ODA Canvas Dashboard.
2. Open the **Infrastructure** tab.
3. Select **Components**.
4. If a namespace filter is displayed, select **components** or **All namespaces**.

This use case requires an API from this deployed Component:

| Required interface | Dependent function | Component ID | Deployed Component resource |
|---|---|---|---|
| MCP | Resource Inventory Management | `TMFC012` | `ri-1-resourceinventory` |

### 3.2 Find the MCP dependency under TMFC012

1. In the Components view, find **TMFC012 — Resource Inventory Management**. Its deployed Component resource is `ri-1-resourceinventory`.
2. Open the Component.
3. Confirm that its Component metadata shows ID `TMFC012`.
4. Confirm that its **Status** is **Complete**.
5. Find **Exposed core APIs**.
6. Find the row whose **API Type** is **mcp** and whose **Name** is `resourceinventorymcp`.
7. Record:
   - exposed API **Name**: `resourceinventorymcp`;
   - **API Type**: `mcp`.

Do not select the `TMF639` OpenAPI row. `TMF639` identifies the Resource Inventory API contract; the dependency required by this exercise is the MCP API named `resourceinventorymcp`.

If `resourceinventorymcp` is missing or the Component is not **Complete**, stop and notify the facilitator.

### 3.3 Confirm the dependency value

You should now have:

| Dependent function Component ID | Exposed API Name | API type |
|---|---|---|
| `TMFC012` | `resourceinventorymcp` | `mcp` |

Use the exposed API **Name** exactly as displayed in the AI-Native ODA Canvas Dashboard. Do not use the Component ID, API contract ID, implementation name, display label, or URL as the dependency name.

Do not copy the exposed API URL into `component.yaml`. The AI-Native ODA Canvas resolves the deployed dependency endpoint.

## 4. Download and update component.yaml

### 4.1 Download the template

1. Open the participant template: [`templates/component.yaml`](../templates/component.yaml).
2. Use the GitHub **Download raw file** action to download it.
3. Keep the filename `component.yaml`.
4. Open the downloaded file in a text or code editor.

Do not create a new Component definition from an empty file. Start with the supplied template so that the facilitator-managed sections remain intact.

### 4.2 Update the team-owned fields

Change each of the following fields:

| YAML field | Required change |
|---|---|
| `metadata.name` | Replace `replace-with-team-component-name` with the unique Component resource name assigned or approved by the facilitator. |
| `metadata.labels["oda.tmforum.org/componentName"]` | Use exactly the same Component resource name as `metadata.name`. |
| `spec.componentMetadata.id` | Replace `replace-with-team-component-id` with the unique team Component ID assigned or approved by the facilitator. |
| `spec.componentMetadata.name` | Use the same team Component name used in `metadata.name`. |
| `spec.componentMetadata.description` | Describe the Network Resource Locator and state that it uses the Resource Inventory MCP dependency. |
| `spec.componentMetadata.functionalBlock` | Replace `replace-with-functional-block` with `Production`. |
| `spec.componentMetadata.maintainers[0].name` | Replace `Workshop Team` with the team or maintainer name agreed during the exercise. |
| `spec.componentMetadata.owners[0].name` | Replace `Workshop Team` with the team or owner name agreed during the exercise. |
| `spec.coreFunction.dependentAPIs` | Replace the example dependency with the dependency entry shown in section 4.3. |

Leave these supplied Component metadata values unchanged unless the facilitator instructs otherwise:

- `spec.componentMetadata.version`;
- `spec.componentMetadata.publicationDate`;
- `spec.componentMetadata.status`.

### 4.3 Replace dependentAPIs

Under `spec.coreFunction`, replace the complete example `dependentAPIs` block with:

```yaml
    dependentAPIs:
      - name: resourceinventorymcp
        specification:
          - apiType: mcp
```

Apply these rules:

1. Enter the dependency exactly as shown.
2. Keep the dependency name lowercase and unchanged; matching is case-sensitive.
3. Use `mcp` as its `apiType`.
4. Do not add a dependency URL or credential.
5. Do not declare the `TMF639` OpenAPI interface.

### 4.4 Do not change facilitator-managed fields

Do not change:

- `spec.coreFunction.exposedAPIs`;
- the Orchestrator Agent implementation, path, or port placeholders;
- `spec.eventNotification`;
- `spec.managementFunction`;
- `spec.securityFunction`;
- any gateway, observability, credential, service, health, or runtime setting.

The facilitator will complete the deployment-specific placeholders after reviewing your submission.

### 4.5 Review and submit

Before submitting the file, confirm:

- [ ] The team Component ID and name are correct and consistent.
- [ ] The description identifies the Network Resource Locator use case.
- [ ] `spec.componentMetadata.functionalBlock` is set to `Production`.
- [ ] Owner and maintainer names have been updated.
- [ ] `resourceinventorymcp` is declared with `apiType: mcp`.
- [ ] No dependency URL or credential has been added.
- [ ] Facilitator-managed sections remain unchanged.
- [ ] The YAML indentation and structure are valid.

Submit the completed `component.yaml` through the channel specified by the facilitator. Do not deploy it directly unless the facilitator asks you to do so.

## 5. Open the deployed Orchestrator Agent

The facilitator will review and deploy your `component.yaml`.

After deployment:

1. Wait for the facilitator to confirm that your team's Component is ready.
2. Obtain your team's **Orchestrator Agent URL** from the facilitator in the workshop Teams channel.
3. Alternatively, open your team's Component in the **AI-Native ODA Canvas Dashboard**, find **Exposed core APIs**, locate the row named `orch-agent-ui`, and open its **URL**.
4. If the browser displays a certificate warning, verify the URL and follow the workshop certificate notice in section 3.1.
5. Do not use the Resource Inventory MCP or OpenAPI URL as the Orchestrator Agent URL.

Before running the exercise, confirm that the Orchestrator Agent sidebar shows green indicators for:

- **Component complete**;
- **Component CR readable**;
- **AI Gateway reachable**;
- **Dependencies resolved**;
- **Capabilities available**.

Confirm that `resourceinventorymcp` appears in the dependency selector.

If the facilitator has corrected or redeployed the dependency, select **Refresh Dependent Services** once before continuing.

## 6. Discover the available capabilities

Keep the dependency selection set to **Auto** and run:

> What capabilities are provided by this component?

Expected result:

- `resourceinventorymcp` exposes four Resource Inventory MCP tools;
- the Component reports four capabilities across one dependency;
- the listed tools are `resource_get`, `resource_create`, `resource_update`, and `resource_delete`.

Run:

> What MCP tools are available?

Expected result: the response lists the same four Resource Inventory MCP tools.

## 7. Test Resource Inventory access

Select `resourceinventorymcp` in the dependency selector.

### 7.1 List every network resource

Run:

> Invoke resource_get to list only the name, ID, resource type, resource status, administrative state, operational state and location of every resource.

Expected result:

- **Accelerate Asia Edge Router**, available at Accelerate Asia Primary Site;
- **Accelerate Asia Backup Router**, reserved at Accelerate Asia Secondary Site;
- **Accelerate Asia Fiber Access Node**, in alarm at Accelerate Asia Primary Site;
- the status line identifies `resourceinventorymcp`, API type `mcp`, and capability `resource_get`.

### 7.2 Find all router resources

Run:

> List every router resource at the Accelerate Asia workshop locations across all resource statuses. Return only their names, IDs, resource statuses and locations.

Expected result:

- Accelerate Asia Edge Router, available, Accelerate Asia Primary Site;
- Accelerate Asia Backup Router, reserved, Accelerate Asia Secondary Site.

The words **across all resource statuses** are important: both the available and reserved routers must be included.

### 7.3 Identify resources that are not available

Run:

> List every network resource and identify only those whose resource status is not available. Include their name, workshop ID, status and the reason where available.

Expected result:

- Accelerate Asia Backup Router, `WS-RES-1002`, reserved, reason **Workshop resilience exercise**;
- Accelerate Asia Fiber Access Node, `WS-RES-1003`, alarm, reason **Simulated uplink fault**.

### 7.4 Retrieve a resource by its stable workshop identifier

Run:

> Invoke resource_get with workshop_id WS-RES-1001 and describe the returned resource.

Expected result: the response describes **Accelerate Asia Edge Router**, its available state, Accelerate Asia Primary Site location, and 100 Gbps capacity.

Use `workshop_id` for `WS-RES-1001`. Do not place this stable workshop value in the `resource_id` argument.

## 8. Run the unsupported-domain control

With `resourceinventorymcp` selected, run:

> What is the status of product order PO-1001?

Expected result: the request is rejected as unsupported because Product Ordering is outside this Component's declared Resource Inventory domain.

## 9. Record your observations

Record and discuss:

1. Which Component owns `resourceinventorymcp`?
2. Which exposed API Name and API type did your Component declare?
3. Which fields distinguish inventory, administrative, and operational state?
4. Which argument holds the stable identifier `WS-RES-1001`?
5. Why was the Product Ordering control query rejected?

## 10. Exercise completion checklist

- [ ] The AI-Native ODA Canvas Dashboard URL was obtained from the facilitator or pinned Teams message.
- [ ] `TMFC012 — Resource Inventory Management` was used to find `resourceinventorymcp`.
- [ ] The dependent Component reports **Complete** in the AI-Native ODA Canvas Dashboard.
- [ ] The template `component.yaml` was downloaded from this repository.
- [ ] All required team-owned fields were updated.
- [ ] `spec.componentMetadata.functionalBlock` is set to `Production`.
- [ ] `resourceinventorymcp` is declared with `apiType: mcp`.
- [ ] The completed `component.yaml` was submitted to the facilitator.
- [ ] The deployed Orchestrator Agent URL was obtained from the facilitator or the `orch-agent-ui` row.
- [ ] All five readiness indicators are green.
- [ ] Four Resource Inventory MCP tools are discovered.
- [ ] All three seeded resources are returned.
- [ ] Both router records are returned across all statuses.
- [ ] The non-available result contains the reserved router and alarmed access node.
- [ ] The stable identifier is supplied as `workshop_id`.
- [ ] The Product Ordering control query is rejected as unsupported.

## 11. Troubleshooting

| Symptom | Action |
|---|---|
| The AI-Native ODA Canvas Dashboard cannot be opened | Use the AI-Native ODA Canvas Dashboard URL shown by the facilitator or pinned in the workshop Teams channel. If it still fails, notify the facilitator. |
| The browser reports that the certificate is not trusted | Confirm that the complete hostname matches the facilitator-provided AI-Native ODA Canvas Dashboard or Orchestrator Agent URL. Use **Advanced** and proceed only for that verified workshop URL. |
| `TMFC012` or `resourceinventorymcp` is missing | Confirm the namespace filter, then ask the facilitator to verify the Resource Inventory Management Component. |
| The dependency is unresolved | Confirm the exact dependency name and `apiType` in `component.yaml`, then ask the facilitator to verify the dependent Component. |
| The team's Orchestrator Agent URL is unknown | Ask the facilitator for the team-specific URL or locate `orch-agent-ui` under the team's Component in the AI-Native ODA Canvas Dashboard. |
| Resource Inventory tools are unavailable | Select **Refresh Dependent Services** once; if the problem remains, provide the visible error to the facilitator. |
| Only the available router is returned | Repeat the complete section 7.2 query and include **across all resource statuses**. |
| A status query fails during argument generation | Use **status is not available** as shown in section 7.3; do not submit multiple enum values as one comma-separated value. |
| A stable identifier query returns no record | Confirm that `WS-RES-1001` was supplied as `workshop_id`, not `resource_id`. |
| A follow-up such as “Describe it” is unsupported | Repeat the complete request and include the workshop ID explicitly. |
