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

## 3. Identify the dependent function in the Canvas Portal

### 3.1 Open the Canvas Portal

Obtain the **Canvas Portal URL** from the facilitator's presentation or the link pinned in the workshop Teams channel.

> **Workshop URL notice:** The Canvas Portal uses plain `http://`. Type the complete `http://` URL from the facilitator or the workshop Teams channel; if your browser changes it to `https://`, the page does not load. The browser may mark the portal **Not secure**, or warn that the site does not support a secure connection and offer **Continue to site**; both are expected for the verified portal URL. Your Orchestrator Agent uses `https://` with a workshop-managed self-signed certificate, so the browser may display **Your connection is not private**, **Certificate not trusted**, or a similar warning. Confirm that the hostname matches the URL opened from your own `orch-agent-ui` row under **APIS → Exposed APIs** in the Canvas Portal, then select **Advanced** and choose **Proceed**, **Continue**, or **Accept the risk**. Do not proceed if the hostname differs; ask the facilitator to verify it.

1. Open the Canvas Portal and sign in with your team's credentials from the facilitator. On first sign-in you are asked to set a new password.
2. In the left navigation, under **COMPONENTS**, select **Inventory**.
3. If a namespace filter is displayed, select **components** or all namespaces.

This use case requires an API from this deployed Component:

| Required interface | Dependent function | Component ID | Deployed Component resource |
|---|---|---|---|
| MCP | Service Inventory Management | `TMFC008` | `si-1-serviceinventory` |

### 3.2 Find the MCP dependency under TMFC008

1. In **Components → Inventory**, find **TMFC008 — Service Inventory Management**. Its deployed Component resource is `si-1-serviceinventory`.
2. Open the Component by selecting its name.
3. Open the **Raw** tab and confirm that `spec.componentMetadata.id` is `TMFC008`.
4. On the **Overview** tab, confirm that **Status** shows **Complete**.
5. Open the **APIs** tab and find the **Core Function** group.
6. Find the API named `serviceinventorymcp`. The **APIs** tab shows only the API name. To confirm its type, stay in the same browser tab: in the left navigation, under **APIS**, select **Exposed APIs**, set the **All API Types** filter to **MCP**, and type `serviceinventorymcp` in the search box. Check that the row `si-1-serviceinventory-serviceinventorymcp` is listed, then return to **COMPONENTS → Inventory**.
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

Use the exposed API **Name** exactly as displayed in the Canvas Portal. Do not use the Component ID, API contract ID, implementation name, display label, or URL as the dependency name.

Do not copy the exposed API URL into your values. The AI-Native ODA Canvas resolves the deployed dependency endpoint.

## 4. Configure and deploy your Component in the Canvas Portal

Your team deploys its own Orchestrator Agent from the Canvas Portal. The agent is packaged as the Helm chart `canvas-orch-agent`, and its ODA Component definition is generated from that chart's values. You edit only the `component` block of the values. The portal hides and protects every other setting.

### 4.1 Start the installation

1. In the left navigation, under **COMPONENTS**, select **Deploy**.
2. Select **Install Component**.
3. Under **Chart Details**, the portal has already filled in the workshop chart and shows the notice **Chart fixed for this environment**. These fields cannot be changed:

   | Field | Value |
   |---|---|
   | **Repository** | `workshop` |
   | **Chart** | `canvas-orch-agent` |
   | **Chart Version** | The version set by the facilitator |

4. Under **Release Info**, enter:

   | Field | Value |
   |---|---|
   | **Release Name** | Your team's release name from the facilitator, for example `team01`. Use lowercase letters, digits and hyphens. |
   | **Namespace** | `components` |

5. Select **Next: Edit Values & Install →**.

The values editor opens with the `component` block of the chart defaults and the notice **This chart lets you edit only: component**. Every value that starts with `replace-with` is a placeholder. The chart refuses to install until you have replaced all of them.

### 4.2 Update the team-owned fields

Change each of the following fields in the editor:

| Values field | Required change |
|---|---|
| `component.name` | A short lowercase name for your Component, for example `service-inventory-assistant`. Use only letters, digits and hyphens. Your deployed Component is named `<release name>-<component.name>`, for example `team01-service-inventory-assistant`. |
| `component.id` | The unique team Component ID assigned or approved by the facilitator, for example `TEAM01-UC03`. |
| `component.description` | Describe the Service Inventory Assistant and the dependencies it uses. |
| `component.functionalBlock` | `Production` |
| `component.maintainer.name` | The team or maintainer name agreed during the exercise. |
| `component.owner.name` | The team or owner name agreed during the exercise. |
| `component.dependentAPIs` | Replace the placeholder entry with the dependency entry shown in section 4.3. |

Leave `component.version` and `component.publicationDate` unchanged unless the facilitator instructs otherwise.

### 4.3 Set dependentAPIs

Replace the complete placeholder `dependentAPIs` list inside the `component` block with:

```yaml
  dependentAPIs:
    - name: serviceinventorymcp
      apiType: mcp
```

Apply these rules:

1. Enter the dependency exactly as shown.
2. Keep the dependency name lowercase and unchanged; matching is case-sensitive.
3. Use `mcp` as the `apiType` of `serviceinventorymcp`.
4. Do not add a URL or credential to any dependency.
5. Do not declare the `TMF638` OpenAPI interface.

Your completed values should look like this, with your own team values:

```yaml
component:
  name: service-inventory-assistant
  id: TEAM01-UC03
  version: "0.1.0"
  description: Service Inventory Assistant using the Service Inventory MCP dependency
  functionalBlock: Production
  publicationDate: "2026-07-23T00:00:00Z"
  maintainer:
    name: Team 01
    email: workshop@example.invalid
  owner:
    name: Team 01
    email: workshop@example.invalid
  dependentAPIs:
    - name: serviceinventorymcp
      apiType: mcp
```

The chart renders each entry as an ODA `dependentAPIs` item (`name` with `specification[].apiType`) in the generated Component.

### 4.4 Do not add anything else

- Do not add any top-level key other than `component`, such as `image`, `canvas`, `credentials` or `otel`. The portal rejects it.
- Do not add keys inside `component` other than those shown above. The chart rejects unknown keys.
- Do not add an Orchestrator Agent path, port, implementation, gateway, observability, credential or runtime setting. The chart supplies these.

### 4.5 Review and install

Before installing, confirm:

- [ ] The release name is the one assigned to your team.
- [ ] The namespace is `components`.
- [ ] `component.name` and `component.id` are correct and unique to your team.
- [ ] The description identifies the Service Inventory Assistant use case.
- [ ] `component.functionalBlock` is set to `Production`.
- [ ] Owner and maintainer names have been updated.
- [ ] No value starting with `replace-with` remains.
- [ ] `serviceinventorymcp` is declared with `apiType: mcp`.
- [ ] No dependency URL or credential has been added.
- [ ] The editor contains only the `component` block.
- [ ] The YAML indentation is valid (two spaces per level, no tabs).

Select **Install Release**. Installation normally takes one to three minutes. Keep the dialog open until the success message appears.

If the installation is rejected, read the message, correct the values, and select **Install Release** again:

| Message contains | Correction |
|---|---|
| `values don't meet the specifications of the schema(s)` | The named field is wrong. Check the spelling of keys (`dependentAPIs`, `apiType`), use lowercase dependency names, use `mcp` or `a2a` as `apiType`, and use one of the listed functional blocks. |
| `is still the placeholder` | Replace the named `replace-with-…` value with the value from this guide. |
| `only allows editing the following values: component` | Remove every top-level key except `component`. |
| `already exists in namespace` | The release name is taken. Confirm your team's release name with the facilitator. |
| `release name + component.name is too long` | Shorten `component.name`. |
| `declares … more than once` | Remove the duplicate dependency entry. |

### 4.6 Confirm the deployment

1. Under **COMPONENTS**, select **Inventory** and find your Component in the **Outside TMF Catalog** section, for example `team01-service-inventory-assistant`. Team Components are listed there because they are not TMF catalogue Components.
2. Open it and confirm on the **Overview** tab that **Status** shows **Complete**. This can take a minute after installation.
3. Open the **Raw** tab and confirm that `spec.coreFunction.dependentAPIs` contains exactly the dependency from section 4.3.
4. Under **APIs**, select **Dependent APIs**, find the entry belonging to your Component, and confirm that it is **Resolved** (ready).

To correct your values after installation, use **COMPONENTS → Lifecycle → Upgrade**. Select your own release. The repository and chart are already fixed to `workshop` and `canvas-orch-agent`. The editor opens with your deployed values. Edit them and select **Upgrade Release**. Never upgrade or uninstall another team's release.

## 5. Open the deployed Orchestrator Agent

1. In the left navigation, under **APIS**, select **Exposed APIs**.
2. In the search box, type your team's release name, for example `team01`.
3. Find the row named `<release name>-<component.name>-orch-agent-ui`, for example `team01-service-inventory-assistant-orch-agent-ui`, and confirm that its **Status** is **LIVE**.
4. In the **Actions** column of that row, select the **Test API endpoint** icon. Your Orchestrator Agent opens in a new browser tab.
5. If the row is missing or not yet **LIVE**, wait a minute and refresh the page. If it is still missing, ask the facilitator.
6. If the browser displays a certificate warning, verify the URL and follow the workshop URL notice in section 3.1.
7. Do not open the MCP dependency rows as the Orchestrator Agent. Do not open another team's `orch-agent-ui` row; every team's Component exposes an API with this name.

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

- [ ] The Canvas Portal URL was obtained from the facilitator or pinned Teams message.
- [ ] `TMFC008 — Service Inventory Management` was used to find `serviceinventorymcp`.
- [ ] The dependent Component reports **Complete** in the Canvas Portal.
- [ ] The `canvas-orch-agent` chart from the `workshop` repository was installed as the team's release in namespace `components`.
- [ ] All required team-owned fields were updated.
- [ ] `component.functionalBlock` is set to `Production`.
- [ ] `serviceinventorymcp` is declared with `apiType: mcp`.
- [ ] Every declared dependency is **Resolved** under **APIs → Dependent APIs**.
- [ ] The Orchestrator Agent was opened with **Test API endpoint** on the team's own `orch-agent-ui` row under **APIS → Exposed APIs**.
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
| The installation is rejected | Follow the correction table in section 4.5. |
| The Component does not reach **Complete** | Open its **Events** tab, then ask the facilitator. Do not reinstall under a different release name. |
| The Canvas Portal cannot be opened | Use the Canvas Portal URL shown by the facilitator or pinned in the workshop Teams channel. If it still fails, notify the facilitator. |
| The Canvas Portal page does not load or keeps loading | Type the complete `http://` portal URL. If the browser switches to `https://`, allow the HTTP connection for that verified URL (for example **Continue to site**). |
| The browser reports that the Orchestrator Agent certificate is not trusted | Confirm that the hostname matches the URL opened from your own `orch-agent-ui` row under **APIS → Exposed APIs**. Use **Advanced** and proceed only for that verified URL. |
| `TMFC008` or `serviceinventorymcp` is missing | Confirm the namespace filter, then ask the facilitator to verify the Service Inventory Management Component. |
| The dependency is unresolved | Confirm the exact dependency name and `apiType` in `component.dependentAPIs`, correct them with **Components → Lifecycle → Upgrade**, then ask the facilitator to verify the dependent Component if the problem remains. |
| The team's Orchestrator Agent URL is unknown | Under **APIS → Exposed APIs**, search for your release name and select **Test API endpoint** on your `orch-agent-ui` row. If the row is missing, ask the facilitator. |
| Service Inventory tools are unavailable | Select **Refresh Dependent Services** once; if the problem remains, provide the visible error to the facilitator. |
| A stable identifier query returns no record | Confirm that `WS-SVC-1001` was supplied as `workshop_id`, not `service_id`. |
| A follow-up such as “Describe it” is unsupported | Repeat the complete request and include the workshop ID explicitly. |
| A response is too long | Request only the fields shown in this guide or retrieve one service by its workshop ID. |

## 12. Remove your release

Remove your release only when the facilitator announces that the exercise is over. Removal deletes your Component, its Orchestrator Agent and its dependency registrations; the pre-deployed dependency Components are not affected.

1. In the Canvas Portal, under **COMPONENTS**, select **Lifecycle**.
2. Select **Uninstall**. The **Uninstall Deployed Components** dialog lists the releases in the `components` namespace.
3. Select **only your own release**, for example `team01`. Never select another team's release or a pre-deployed dependency Component.
4. Select **Review Removal**, check that the list contains only your release, and select **Uninstall Selected**.
5. Under **COMPONENTS → Inventory**, confirm that your Component, for example `team01-…`, is no longer listed. Removal can take a minute.
