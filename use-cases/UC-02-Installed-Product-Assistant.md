# UC-02 — Installed Product Assistant

## Participant user guide

## 1. Objective

In this exercise, your team will design and validate an AI-Native ODA Component that finds seeded installed products and explains their Product Inventory lifecycle state.

The Component will depend on:

- `productinventorymcp` for access to Product Inventory MCP tools.

You will use this interface to retrieve installed products by customer, lifecycle state, and stable workshop identifier.

## 2. Use-case boundary

The Component supports Product Inventory questions about:

- installed products;
- products assigned to a workshop customer;
- active, suspended, and terminated product states;
- the product-offering reference recorded against an installed product;
- a product identified by its stable product serial number.

It does not provide Product Catalog, Service Inventory, Resource Inventory, Service Qualification, or Product Ordering capabilities.

All workshop records are synthetic and prepared specifically for this exercise.

The Orchestrator Agent provides single-turn conversations. Include the relevant entity, identifier, requested fields, and other necessary context in every query. Do not rely on an earlier response or a follow-up such as “Describe it”.

## 3. Identify the dependent function in the Canvas Portal

### 3.1 Open the Canvas Portal

Obtain the **Canvas Portal URL** from the facilitator's presentation or the link pinned in the workshop Teams channel.

> **Workshop URL notice:** The Canvas Portal uses plain `http://`. Type the complete `http://` URL from the facilitator or the workshop Teams channel; if your browser changes it to `https://`, the page does not load. The browser may mark the portal **Not secure**, or warn that the site does not support a secure connection and offer **Continue to site**; both are expected for the verified portal URL. Your Orchestrator Agent uses `https://` with a workshop-managed self-signed certificate, so the browser may display **Your connection is not private**, **Certificate not trusted**, or a similar warning. Confirm that the hostname matches the `orch-agent-ui` URL shown for your own Component in the Canvas Portal, then select **Advanced** and choose **Proceed**, **Continue**, or **Accept the risk**. Do not proceed if the hostname differs; ask the facilitator to verify it.

1. Open the Canvas Portal and sign in with your team's credentials from the facilitator. On first sign-in you are asked to set a new password.
2. In the left navigation, under **COMPONENTS**, select **Inventory**.
3. If a namespace filter is displayed, select **components** or all namespaces.

This use case requires an API from this deployed Component:

| Required interface | Dependent function | Component ID | Deployed Component resource |
|---|---|---|---|
| MCP | Product Inventory Management | `TMFC005` | `pi-1-productinventory` |

### 3.2 Find the MCP dependency under TMFC005

1. In **Components → Inventory**, find **TMFC005 — Product Inventory Management**. Its deployed Component resource is `pi-1-productinventory`.
2. Open the Component by selecting its name.
3. Open the **Raw** tab and confirm that `spec.componentMetadata.id` is `TMFC005`.
4. On the **Overview** tab, confirm that **Status** shows **Complete**.
5. Open the **APIs** tab and find the **Core Function** group.
6. Find the API named `productinventorymcp`. To confirm its type, open **APIs → Exposed APIs** in a second browser tab, set the API type filter to **MCP**, and check that `productinventorymcp` is listed.
7. Record:
   - exposed API **Name**: `productinventorymcp`;
   - **API Type**: `mcp`.

Do not select the `TMF637` OpenAPI row. `TMF637` identifies the Product Inventory API contract; the dependency required by this exercise is the MCP API named `productinventorymcp`.

If `productinventorymcp` is missing or the Component is not **Complete**, stop and notify the facilitator.

### 3.3 Confirm the dependency value

You should now have:

| Dependent function Component ID | Exposed API Name | API type |
|---|---|---|
| `TMFC005` | `productinventorymcp` | `mcp` |

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
| `component.name` | A short lowercase name for your Component, for example `installed-product-assistant`. Use only letters, digits and hyphens. Your deployed Component is named `<release name>-<component.name>`, for example `team01-installed-product-assistant`. |
| `component.id` | The unique team Component ID assigned or approved by the facilitator, for example `TEAM01-UC02`. |
| `component.description` | Describe the Installed Product Assistant and the dependencies it uses. |
| `component.functionalBlock` | `CoreCommerce` |
| `component.maintainer.name` | The team or maintainer name agreed during the exercise. |
| `component.owner.name` | The team or owner name agreed during the exercise. |
| `component.dependentAPIs` | Replace the placeholder entry with the dependency entry shown in section 4.3. |

Leave `component.version` and `component.publicationDate` unchanged unless the facilitator instructs otherwise.

### 4.3 Set dependentAPIs

Replace the complete placeholder `dependentAPIs` list inside the `component` block with:

```yaml
  dependentAPIs:
    - name: productinventorymcp
      apiType: mcp
```

Apply these rules:

1. Enter the dependency exactly as shown.
2. Keep the dependency name lowercase and unchanged; matching is case-sensitive.
3. Use `mcp` as the `apiType` of `productinventorymcp`.
4. Do not add a URL or credential to any dependency.
5. Do not declare the `TMF637` OpenAPI interface.

Your completed values should look like this, with your own team values:

```yaml
component:
  name: installed-product-assistant
  id: TEAM01-UC02
  version: "0.1.0"
  description: Installed Product Assistant using the Product Inventory MCP dependency
  functionalBlock: CoreCommerce
  publicationDate: "2026-07-23T00:00:00Z"
  maintainer:
    name: Team 01
    email: workshop@example.invalid
  owner:
    name: Team 01
    email: workshop@example.invalid
  dependentAPIs:
    - name: productinventorymcp
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
- [ ] The description identifies the Installed Product Assistant use case.
- [ ] `component.functionalBlock` is set to `CoreCommerce`.
- [ ] Owner and maintainer names have been updated.
- [ ] No value starting with `replace-with` remains.
- [ ] `productinventorymcp` is declared with `apiType: mcp`.
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

1. Under **COMPONENTS**, select **Inventory** and find your Component in the **Outside TMF Catalog** section, for example `team01-installed-product-assistant`. Team Components are listed there because they are not TMF catalogue Components.
2. Open it and confirm on the **Overview** tab that **Status** shows **Complete**. This can take a minute after installation.
3. Open the **Raw** tab and confirm that `spec.coreFunction.dependentAPIs` contains exactly the dependency from section 4.3.
4. Under **APIs**, select **Dependent APIs**, find the entry belonging to your Component, and confirm that it is **Resolved** (ready).

To correct your values after installation, use **COMPONENTS → Lifecycle → Upgrade**. Select your own release. The repository and chart are already fixed to `workshop` and `canvas-orch-agent`. The editor opens with your deployed values. Edit them and select **Upgrade Release**. Never upgrade or uninstall another team's release.

## 5. Open the deployed Orchestrator Agent

1. Under **COMPONENTS**, select **Inventory** and open your team's Component, for example `team01-installed-product-assistant`.
2. Open the **APIs** tab. Under **Core Function**, find `orch-agent-ui` and open its **URL**.
3. If no URL is shown yet, wait a minute and refresh the page. If it is still missing, ask the facilitator.
4. If the browser displays a certificate warning, verify the URL and follow the workshop URL notice in section 3.1.
5. Do not use an MCP dependency URL as the Orchestrator Agent URL. Do not use another team's `orch-agent-ui`; every team's Component exposes an API with this name.

Before running the exercise, confirm that the Orchestrator Agent sidebar shows green indicators for:

- **Component complete**;
- **Component CR readable**;
- **AI Gateway reachable**;
- **Dependencies resolved**;
- **Capabilities available**.

Confirm that `productinventorymcp` appears in the dependency selector.

If the facilitator has corrected or redeployed the dependency, select **Refresh Dependent Services** once before continuing.

## 6. Discover the available capabilities

Keep the dependency selection set to **Auto** and run:

> What capabilities are provided by this component?

Expected result:

- `productinventorymcp` exposes four Product Inventory MCP tools;
- the Component reports four capabilities across one dependency;
- the listed tools are `product_get`, `product_create`, `product_update`, and `product_delete`.

Run:

> What MCP tools are available?

Expected result: the response lists the same four Product Inventory MCP tools.

## 7. Test Product Inventory access

Select `productinventorymcp` in the dependency selector.

### 7.1 Find installed products for a customer

Run:

> What products are installed for customer CUST-1001? Return only their name, product serial number, status and product offering.

Expected result:

- **Fiber Broadband 50 Mbps**, serial number `WS-PROD-1001`, state active;
- **Business Firewall**, serial number `WS-PROD-1002`, state suspended;
- both records belong to `CUST-1001` and the response does not include the CUST-1002 product;
- the status line identifies `productinventorymcp`, API type `mcp`, and capability `product_get`.

### 7.2 Find active installed products

Run:

> Which installed products are currently active? Return only their name, product serial number and customer ID.

Expected result: only **Fiber Broadband 50 Mbps**, serial number `WS-PROD-1001`, for `CUST-1001` is returned.

### 7.3 Retrieve a product by its stable workshop identifier

Run:

> Invoke product_get with product_serial_number WS-PROD-1001 and describe the returned installed product.

Expected result: the response describes **Fiber Broadband 50 Mbps**, its active state, customer `CUST-1001`, 50 Mbps characteristic, and related Fiber product offering or specification.

Use `product_serial_number` for `WS-PROD-1001`. Do not place this stable workshop value in the `product_id` argument.

### 7.4 Explain all seeded lifecycle states

Run:

> List every installed product and explain its current lifecycle state using the seeded product information.

Expected result:

- Fiber Broadband 50 Mbps is active;
- Business Firewall is suspended;
- Legacy Business Broadband is terminated.

## 8. Run the unsupported-domain control

With `productinventorymcp` selected, run:

> What network resources are available at the Accelerate Asia Primary Site?

Expected result: the request is rejected as unsupported because Resource Inventory is outside this Component's declared Product Inventory domain.

## 9. Record your observations

Record and discuss:

1. Which Component owns `productinventorymcp`?
2. Which exposed API Name and API type did your Component declare?
3. Which argument holds the stable identifier `WS-PROD-1001`?
4. What information in the response confirms that the Product Inventory MCP dependency was used?
5. Why was the Resource Inventory control query rejected?

## 10. Exercise completion checklist

- [ ] The Canvas Portal URL was obtained from the facilitator or pinned Teams message.
- [ ] `TMFC005 — Product Inventory Management` was used to find `productinventorymcp`.
- [ ] The dependent Component reports **Complete** in the Canvas Portal.
- [ ] The `canvas-orch-agent` chart from the `workshop` repository was installed as the team's release in namespace `components`.
- [ ] All required team-owned fields were updated.
- [ ] `component.functionalBlock` is set to `CoreCommerce`.
- [ ] `productinventorymcp` is declared with `apiType: mcp`.
- [ ] Every declared dependency is **Resolved** under **APIs → Dependent APIs**.
- [ ] The deployed Orchestrator Agent URL was opened from the `orch-agent-ui` API of the team's Component.
- [ ] All five readiness indicators are green.
- [ ] Four Product Inventory MCP tools are discovered.
- [ ] The CUST-1001 response contains the Fiber and Firewall products only.
- [ ] The active-product query returns only `WS-PROD-1001`.
- [ ] The stable identifier is supplied as `product_serial_number`.
- [ ] All three seeded lifecycle states are returned.
- [ ] The Resource Inventory control query is rejected as unsupported.

## 11. Troubleshooting

| Symptom | Action |
|---|---|
| The installation is rejected | Follow the correction table in section 4.5. |
| The Component does not reach **Complete** | Open its **Events** tab, then ask the facilitator. Do not reinstall under a different release name. |
| The Canvas Portal cannot be opened | Use the Canvas Portal URL shown by the facilitator or pinned in the workshop Teams channel. If it still fails, notify the facilitator. |
| The Canvas Portal page does not load or keeps loading | Type the complete `http://` portal URL. If the browser switches to `https://`, allow the HTTP connection for that verified URL (for example **Continue to site**). |
| The browser reports that the Orchestrator Agent certificate is not trusted | Confirm that the hostname matches the `orch-agent-ui` URL of your own Component in the Canvas Portal. Use **Advanced** and proceed only for that verified URL. |
| `TMFC005` or `productinventorymcp` is missing | Confirm the namespace filter, then ask the facilitator to verify the Product Inventory Management Component. |
| The dependency is unresolved | Confirm the exact dependency name and `apiType` in `component.dependentAPIs`, correct them with **Components → Lifecycle → Upgrade**, then ask the facilitator to verify the dependent Component if the problem remains. |
| The team's Orchestrator Agent URL is unknown | Open the team's Component in **Components → Inventory**, then the **APIs** tab, and use the `orch-agent-ui` URL. If it is missing, ask the facilitator. |
| Product Inventory tools are unavailable | Select **Refresh Dependent Services** once; if the problem remains, provide the visible error to the facilitator. |
| A stable identifier query returns no record | Confirm that `WS-PROD-1001` was supplied as `product_serial_number`, not `product_id`. |
| A follow-up such as “Describe it” is unsupported | Repeat the complete request and include the product serial number explicitly. |
| A response is too long | Request only the fields shown in this guide or retrieve one product by its product serial number. |

## 12. Remove your release

Remove your release only when the facilitator announces that the exercise is over. Removal deletes your Component, its Orchestrator Agent and its dependency registrations; the pre-deployed dependency Components are not affected.

1. In the Canvas Portal, under **COMPONENTS**, select **Lifecycle**.
2. Select **Uninstall**. The **Uninstall Deployed Components** dialog lists the releases in the `components` namespace.
3. Select **only your own release**, for example `team01`. Never select another team's release or a pre-deployed dependency Component.
4. Select **Review Removal**, check that the list contains only your release, and select **Uninstall Selected**.
5. Under **COMPONENTS → Inventory**, confirm that your Component, for example `team01-…`, is no longer listed. Removal can take a minute.
