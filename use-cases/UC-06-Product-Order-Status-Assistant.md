# UC-06 — Product Order Status Assistant

## Participant user guide

## 1. Objective

In this exercise, your team will design and validate an AI-Native ODA Component that retrieves and explains seeded Product Order lifecycle states.

The Component will depend on:

- `productordera2a` for access to the Product Order A2A status-query skill.

You will use this interface to retrieve orders by state, customer, and stable external ID.

## 2. Use-case boundary

The Component supports Product Order questions about:

- all seeded Product Orders;
- completed, in-progress, and held orders;
- an order identified by its stable external ID;
- the product offering and customer described by an order;
- the reason recorded for a held order.

It does not provide Product Catalog, Product Inventory, Service Inventory, Resource Inventory, or Service Qualification capabilities. The Product Order interface used in this exercise provides order-status queries; it is not used to create, update, delete, or cancel an order.

All workshop records are synthetic and prepared specifically for this exercise.

The Orchestrator Agent provides single-turn conversations. Include the Product Order entity, complete external ID, requested fields, and other necessary context in every query. Do not rely on an earlier response or a follow-up such as “Why is it held?”.

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
| A2A | Product Order Capture and Validation | `TMFC002` | `po-1-productordercaptureandvalidation` |

### 3.2 Find the A2A dependency under TMFC002

1. In **Components → Inventory**, find **TMFC002 — Product Order Capture and Validation**. Its deployed Component resource is `po-1-productordercaptureandvalidation`.
2. Open the Component by selecting its name.
3. Open the **Raw** tab and confirm that `spec.componentMetadata.id` is `TMFC002`.
4. On the **Overview** tab, confirm that **Status** shows **Complete**.
5. Open the **APIs** tab and find the **Core Function** group.
6. Find the API named `productordera2a`. The **APIs** tab shows only the API name. To confirm its type, stay in the same browser tab: in the left navigation, under **APIS**, select **Exposed APIs**, set the **All API Types** filter to **A2A**, and type `productordera2a` in the search box. Check that the row `po-1-productordercaptureandvalidation-productordera2a` is listed, then return to **COMPONENTS → Inventory**.
7. Record:
   - exposed API **Name**: `productordera2a`;
   - **API Type**: `a2a`.

Do not select the `TMF622` OpenAPI row. `TMF622` identifies the Product Ordering API contract; the dependency required by this exercise is the A2A API named `productordera2a`.

Do not select or declare `productorderingmcp` for this use case. If `productordera2a` is missing or the Component is not **Complete**, stop and notify the facilitator.

### 3.3 Confirm the dependency value

You should now have:

| Dependent function Component ID | Exposed API Name | API type |
|---|---|---|
| `TMFC002` | `productordera2a` | `a2a` |

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
| `component.name` | A short lowercase name for your Component, for example `order-status-assistant`. Use only letters, digits and hyphens. Your deployed Component is named `<release name>-<component.name>`, for example `team01-order-status-assistant`. |
| `component.id` | The unique team Component ID assigned or approved by the facilitator, for example `TEAM01-UC06`. |
| `component.description` | Describe the Product Order Status Assistant and the dependencies it uses. |
| `component.functionalBlock` | `CoreCommerce` |
| `component.maintainer.name` | The team or maintainer name agreed during the exercise. |
| `component.owner.name` | The team or owner name agreed during the exercise. |
| `component.dependentAPIs` | Replace the placeholder entry with the dependency entry shown in section 4.3. |

Leave `component.version` and `component.publicationDate` unchanged unless the facilitator instructs otherwise.

### 4.3 Set dependentAPIs

Replace the complete placeholder `dependentAPIs` list inside the `component` block with:

```yaml
  dependentAPIs:
    - name: productordera2a
      apiType: a2a
```

Apply these rules:

1. Enter the dependency exactly as shown.
2. Keep the dependency name lowercase and unchanged; matching is case-sensitive.
3. Use `a2a` as the `apiType` of `productordera2a`.
4. Do not add a URL or credential to any dependency.
5. Do not declare the `TMF622` OpenAPI interface.

Your completed values should look like this, with your own team values:

```yaml
component:
  name: order-status-assistant
  id: TEAM01-UC06
  version: "0.1.0"
  description: Product Order Status Assistant using the Product Order A2A dependency
  functionalBlock: CoreCommerce
  publicationDate: "2026-07-23T00:00:00Z"
  maintainer:
    name: Team 01
    email: workshop@example.invalid
  owner:
    name: Team 01
    email: workshop@example.invalid
  dependentAPIs:
    - name: productordera2a
      apiType: a2a
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
- [ ] The description identifies the Product Order Status Assistant use case.
- [ ] `component.functionalBlock` is set to `CoreCommerce`.
- [ ] Owner and maintainer names have been updated.
- [ ] No value starting with `replace-with` remains.
- [ ] `productordera2a` is declared with `apiType: a2a`.
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

1. Under **COMPONENTS**, select **Inventory** and find your Component in the **Outside TMF Catalog** section, for example `team01-order-status-assistant`. Team Components are listed there because they are not TMF catalogue Components.
2. Open it and confirm on the **Overview** tab that **Status** shows **Complete**. This can take a minute after installation.
3. Open the **Raw** tab and confirm that `spec.coreFunction.dependentAPIs` contains exactly the dependency from section 4.3.
4. Under **APIs**, select **Dependent APIs**, find the entry belonging to your Component, and confirm that it is **Resolved** (ready).

To correct your values after installation, use **COMPONENTS → Lifecycle → Upgrade**. Select your own release. The repository and chart are already fixed to `workshop` and `canvas-orch-agent`. The editor opens with your deployed values. Edit them and select **Upgrade Release**. Never upgrade or uninstall another team's release.

## 5. Open the deployed Orchestrator Agent

1. In the left navigation, under **APIS**, select **Exposed APIs**.
2. In the search box, type your team's release name, for example `team01`.
3. Find the row named `<release name>-<component.name>-orch-agent-ui`, for example `team01-order-status-assistant-orch-agent-ui`, and confirm that its **Status** is **LIVE**.
4. In the **Actions** column of that row, select the **Test API endpoint** icon. Your Orchestrator Agent opens in a new browser tab.
5. If the row is missing or not yet **LIVE**, wait a minute and refresh the page. If it is still missing, ask the facilitator.
6. If the browser displays a certificate warning, verify the URL and follow the workshop URL notice in section 3.1.
7. Do not open the A2A dependency rows as the Orchestrator Agent. Do not open another team's `orch-agent-ui` row; every team's Component exposes an API with this name.

Before running the exercise, confirm that the Orchestrator Agent sidebar shows green indicators for:

- **Component complete**;
- **Component CR readable**;
- **AI Gateway reachable**;
- **Dependencies resolved**;
- **Capabilities available**.

Confirm that `productordera2a` appears in the dependency selector and that it is the only declared domain dependency for this use case.

If the facilitator has corrected or redeployed the dependency, select **Refresh Dependent Services** once before continuing.

## 6. Discover the available capability

Keep the dependency selection set to **Auto** and run:

> What dependencies are available?

Expected result: `productordera2a` is available as an A2A dependency.

Run:

> What capabilities are provided by this component?

Expected result: the Component reports one capability across one dependency.

Run:

> What A2A skills are available?

Expected result: the response lists **Product Order Status Query**, skill ID `product-order-status-query`, under `productordera2a`.

Run:

> Retrieve the agent card for productordera2a

Expected result: the Agent Card identifies the Product Order Agent and its `product-order-status-query` skill.

## 7. Test Product Order status access

Select `productordera2a` in the dependency selector.

### 7.1 List every seeded Product Order

Run:

> Invoke product-order-status-query to list every product order. Return only the external ID, product offering, customer ID, order state and description.

Expected result:

- `PO-1001`, Fiber Offering 50 Mbps, `CUST-1001`, completed;
- `PO-1002`, Business Firewall Solution, `CUST-1001`, state `inProgress`;
- `PO-1003`, Business MPLS Network, `CUST-1002`, held;
- the status line identifies `productordera2a`, API type `a2a`, and capability `product-order-status-query`.

### 7.2 Describe the completed order

Run:

> Invoke product-order-status-query to describe the status of product order PO-1001.

Expected result: the response describes the completed Fiber order and its completion information.

### 7.3 Find the order in state inProgress

Run:

> Invoke product-order-status-query to list only product orders currently in state inProgress. Return their external ID, offering, state and expected completion date.

Expected result: only `PO-1002` is returned, with expected completion date `2026-08-15T18:00:00Z`.

### 7.4 Explain the held order

Run:

> Invoke product-order-status-query to explain why product order PO-1003 is held.

Expected result: the response explains that `PO-1003` is held pending site access.

### 7.5 Find orders for one customer

Run:

> Invoke product-order-status-query to list every product order for customer CUST-1001. Return only the external ID, offering and state.

Expected result: only `PO-1001` and `PO-1002` are returned.

## 8. Run the unsupported-domain control

With `productordera2a` selected, run:

> What network resources are in alarm at the Accelerate Asia Primary Site?

Expected result: the request is rejected as unsupported because Resource Inventory is outside this Component's declared Product Ordering domain.

## 9. Record your observations

Record and discuss:

1. Which Component owns `productordera2a`?
2. Which exposed API Name and API type did your Component declare?
3. Which A2A skill answered the Product Order queries?
4. How did the seeded data distinguish completed, in-progress, and held orders?
5. Why was the Resource Inventory control query rejected?

## 10. Exercise completion checklist

- [ ] The Canvas Portal URL was obtained from the facilitator or pinned Teams message.
- [ ] `TMFC002 — Product Order Capture and Validation` was used to find `productordera2a`.
- [ ] The dependent Component reports **Complete** in the Canvas Portal.
- [ ] The `canvas-orch-agent` chart from the `workshop` repository was installed as the team's release in namespace `components`.
- [ ] All required team-owned fields were updated.
- [ ] `component.functionalBlock` is set to `CoreCommerce`.
- [ ] Only `productordera2a` is declared, with `apiType: a2a`.
- [ ] Every declared dependency is **Resolved** under **APIs → Dependent APIs**.
- [ ] The Orchestrator Agent was opened with **Test API endpoint** on the team's own `orch-agent-ui` row under **APIS → Exposed APIs**.
- [ ] All five readiness indicators are green.
- [ ] One Product Order A2A skill is discovered.
- [ ] All three seeded Product Orders are returned.
- [ ] `PO-1001` is completed, `PO-1002` is `inProgress`, and `PO-1003` is held.
- [ ] The CUST-1001 query returns `PO-1001` and `PO-1002` only.
- [ ] The Resource Inventory control query is rejected as unsupported.

## 11. Troubleshooting

| Symptom | Action |
|---|---|
| The installation is rejected | Follow the correction table in section 4.5. |
| The Component does not reach **Complete** | Open its **Events** tab, then ask the facilitator. Do not reinstall under a different release name. |
| The Canvas Portal cannot be opened | Use the Canvas Portal URL shown by the facilitator or pinned in the workshop Teams channel. If it still fails, notify the facilitator. |
| The Canvas Portal page does not load or keeps loading | Type the complete `http://` portal URL. If the browser switches to `https://`, allow the HTTP connection for that verified URL (for example **Continue to site**). |
| The browser reports that the Orchestrator Agent certificate is not trusted | Confirm that the hostname matches the URL opened from your own `orch-agent-ui` row under **APIS → Exposed APIs**. Use **Advanced** and proceed only for that verified URL. |
| `TMFC002` or `productordera2a` is missing | Confirm the namespace filter, then ask the facilitator to verify the Product Order Capture and Validation Component. |
| The dependency is unresolved | Confirm the exact dependency name and `apiType` in `component.dependentAPIs`, correct them with **Components → Lifecycle → Upgrade**, then ask the facilitator to verify the dependent Component if the problem remains. |
| The team's Orchestrator Agent URL is unknown | Under **APIS → Exposed APIs**, search for your release name and select **Test API endpoint** on your `orch-agent-ui` row. If the row is missing, ask the facilitator. |
| The Product Order A2A skill is unavailable | Select **Refresh Dependent Services** once; if the problem remains, provide the visible error to the facilitator. |
| An order is not found | Confirm that the complete external ID, including the `PO-` prefix, is included in the same query. |
| A follow-up such as “Why is it held?” is unsupported | Repeat the complete request and include the Product Order external ID explicitly. |
| An unexpected dependency appears | Confirm that the Component declares only `productordera2a`; do not add `productorderingmcp` or `TMF622`. |

## 12. Remove your release

Remove your release only when the facilitator announces that the exercise is over. Removal deletes your Component, its Orchestrator Agent and its dependency registrations; the pre-deployed dependency Components are not affected.

1. In the Canvas Portal, under **COMPONENTS**, select **Lifecycle**.
2. Select **Uninstall**. The **Uninstall Deployed Components** dialog lists the releases in the `components` namespace.
3. Select **only your own release**, for example `team01`. Never select another team's release or a pre-deployed dependency Component.
4. Select **Review Removal**, check that the list contains only your release, and select **Uninstall Selected**.
5. Under **COMPONENTS → Inventory**, confirm that your Component, for example `team01-…`, is no longer listed. Removal can take a minute.
