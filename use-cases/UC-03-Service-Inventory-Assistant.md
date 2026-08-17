# UC-03 — Service Inventory Assistant

## Participant user guide

## 1. Objective

In this exercise, your team will design and validate an AI-Native ODA Component that finds seeded service instances and explains their Service Inventory lifecycle and enabled states.

The Component will depend on:

- `serviceinventorymcp` for access to Service Inventory MCP tools.

You will use this interface to retrieve services by customer, lifecycle state, and stable workshop identifier.

## 2. Use-case boundary

The Component supports Service Inventory questions about:

- service instances;
- services assigned to a workshop customer;
- active, inactive, and terminated service states;
- whether a service is enabled;
- a service identified by its stable workshop ID.

It does not provide Product Catalog, Product Inventory, Resource Inventory, Service Qualification, or Product Ordering capabilities.

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
| MCP | Service Inventory Management | `TMFC008` | `si-1-serviceinventory` |

### 3.2 Find the MCP dependency under TMFC008

1. In the Components view, find **TMFC008 — Service Inventory Management**. Its deployed Component resource is `si-1-serviceinventory`.
2. Open the Component.
3. Confirm that its Component metadata shows ID `TMFC008`.
4. Confirm that its **Status** is **Complete**.
5. Find **Exposed core APIs**.
6. Find the row whose **API Type** is **mcp** and whose **Name** is `serviceinventorymcp`.
7. Record:
   - exposed API **Name**: `serviceinventorymcp`;
   - **API Type**: `mcp`.

Do not select the `TMF638` OpenAPI row. `TMF638` identifies the Service Inventory API contract; the dependency required by this exercise is the MCP API named `serviceinventorymcp`.

If `serviceinventorymcp` is missing or the Component is not **Complete**, stop and notify the facilitator.

### 3.3 Confirm the dependency value

You should now have:

| Dependent function Component ID | Exposed API Name | API type |
|---|---|---|
| `TMFC008` | `serviceinventorymcp` | `mcp` |

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
| `spec.componentMetadata.description` | Describe the Service Inventory Assistant and state that it uses the Service Inventory MCP dependency. |
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
      - name: serviceinventorymcp
        specification:
          - apiType: mcp
```

Apply these rules:

1. Enter the dependency exactly as shown.
2. Keep the dependency name lowercase and unchanged; matching is case-sensitive.
3. Use `mcp` as its `apiType`.
4. Do not add a dependency URL or credential.
5. Do not declare the `TMF638` OpenAPI interface.

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
- [ ] The description identifies the Service Inventory Assistant use case.
- [ ] `spec.componentMetadata.functionalBlock` is set to `Production`.
- [ ] Owner and maintainer names have been updated.
- [ ] `serviceinventorymcp` is declared with `apiType: mcp`.
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
5. Do not use the Service Inventory MCP or OpenAPI URL as the Orchestrator Agent URL.

Before running the exercise, confirm that the Orchestrator Agent sidebar shows green indicators for:

- **Component complete**;
- **Component CR readable**;
- **AI Gateway reachable**;
- **Dependencies resolved**;
- **Capabilities available**.

Confirm that `serviceinventorymcp` appears in the dependency selector.

If the facilitator has corrected or redeployed the dependency, select **Refresh Dependent Services** once before continuing.

## 6. Discover the available capabilities

Keep the dependency selection set to **Auto** and run:

> What capabilities are provided by this component?

Expected result:

- `serviceinventorymcp` exposes four Service Inventory MCP tools;
- the Component reports four capabilities across one dependency;
- the listed tools are `service_get`, `service_create`, `service_update`, and `service_delete`.

Run:

> What MCP tools are available?

Expected result: the response lists the same four Service Inventory MCP tools.

## 7. Test Service Inventory access

Select `serviceinventorymcp` in the dependency selector.

### 7.1 List every service instance

Run:

> Invoke service_get to list only the name, ID, state, enabled status and customer ID of every service.

Expected result:

- **Fiber Access Service** is active, enabled, and assigned to `CUST-1001`;
- **Managed Firewall Service** is inactive, not enabled, and assigned to `CUST-1001`;
- **Legacy Copper Access Service** is terminated, not enabled, and assigned to `CUST-1002`;
- the status line identifies `serviceinventorymcp`, API type `mcp`, and capability `service_get`.

### 7.2 Find service instances for a customer

Run:

> What service instances are available for customer CUST-1001? Return only the service name, ID and lifecycle state.

Expected result: **Fiber Access Service** and **Managed Firewall Service** are returned; the CUST-1002 service is not returned.

### 7.3 Find active service instances

Run:

> Which service instances are currently active? Return only their name, ID and customer ID.

Expected result: only **Fiber Access Service** for `CUST-1001` is returned.

### 7.4 Retrieve a service by its stable workshop identifier

Run:

> Invoke service_get with workshop_id WS-SVC-1001 and describe the returned service instance.

Expected result: the response describes **Fiber Access Service**, state active, enabled status true, customer `CUST-1001`, and bandwidth 50 Mbps.

Use `workshop_id` for `WS-SVC-1001`. Do not place this stable workshop value in the `service_id` argument.

### 7.5 Summarize service states

Run:

> Summarize the lifecycle state and enabled status of every service instance.

Expected result:

- Fiber Access Service: active and enabled;
- Managed Firewall Service: inactive and not enabled;
- Legacy Copper Access Service: terminated and not enabled.

## 8. Run the unsupported-domain control

With `serviceinventorymcp` selected, run:

> What product offering prices are available?

Expected result: the request is rejected as unsupported because Product Catalog is outside this Component's declared Service Inventory domain.

## 9. Record your observations

Record and discuss:

1. Which Component owns `serviceinventorymcp`?
2. Which exposed API Name and API type did your Component declare?
3. Which argument holds the stable identifier `WS-SVC-1001`?
4. How do lifecycle state and enabled status differ in the seeded records?
5. Why was the Product Catalog control query rejected?

## 10. Exercise completion checklist

- [ ] The AI-Native ODA Canvas Dashboard URL was obtained from the facilitator or pinned Teams message.
- [ ] `TMFC008 — Service Inventory Management` was used to find `serviceinventorymcp`.
- [ ] The dependent Component reports **Complete** in the AI-Native ODA Canvas Dashboard.
- [ ] The template `component.yaml` was downloaded from this repository.
- [ ] All required team-owned fields were updated.
- [ ] `spec.componentMetadata.functionalBlock` is set to `Production`.
- [ ] `serviceinventorymcp` is declared with `apiType: mcp`.
- [ ] The completed `component.yaml` was submitted to the facilitator.
- [ ] The deployed Orchestrator Agent URL was obtained from the facilitator or the `orch-agent-ui` row.
- [ ] All five readiness indicators are green.
- [ ] Four Service Inventory MCP tools are discovered.
- [ ] All three seeded service records and their states are returned.
- [ ] The CUST-1001 query returns the Fiber and Firewall services only.
- [ ] The active-service query returns only Fiber Access Service.
- [ ] The stable identifier is supplied as `workshop_id`.
- [ ] The Product Catalog control query is rejected as unsupported.

## 11. Troubleshooting

| Symptom | Action |
|---|---|
| The AI-Native ODA Canvas Dashboard cannot be opened | Use the AI-Native ODA Canvas Dashboard URL shown by the facilitator or pinned in the workshop Teams channel. If it still fails, notify the facilitator. |
| The browser reports that the certificate is not trusted | Confirm that the complete hostname matches the facilitator-provided AI-Native ODA Canvas Dashboard or Orchestrator Agent URL. Use **Advanced** and proceed only for that verified workshop URL. |
| `TMFC008` or `serviceinventorymcp` is missing | Confirm the namespace filter, then ask the facilitator to verify the Service Inventory Management Component. |
| The dependency is unresolved | Confirm the exact dependency name and `apiType` in `component.yaml`, then ask the facilitator to verify the dependent Component. |
| The team's Orchestrator Agent URL is unknown | Ask the facilitator for the team-specific URL or locate `orch-agent-ui` under the team's Component in the AI-Native ODA Canvas Dashboard. |
| Service Inventory tools are unavailable | Select **Refresh Dependent Services** once; if the problem remains, provide the visible error to the facilitator. |
| A stable identifier query returns no record | Confirm that `WS-SVC-1001` was supplied as `workshop_id`, not `service_id`. |
| A follow-up such as “Describe it” is unsupported | Repeat the complete request and include the workshop ID explicitly. |
| A response is too long | Request only the fields shown in this guide or retrieve one service by its workshop ID. |
