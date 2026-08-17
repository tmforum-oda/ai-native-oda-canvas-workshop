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
| A2A | Product Order Capture and Validation | `TMFC002` | `po-1-productordercaptureandvalidation` |

### 3.2 Find the A2A dependency under TMFC002

1. In the Components view, find **TMFC002 — Product Order Capture and Validation**. Its deployed Component resource is `po-1-productordercaptureandvalidation`.
2. Open the Component.
3. Confirm that its Component metadata shows ID `TMFC002`.
4. Confirm that its **Status** is **Complete**.
5. Find **Exposed core APIs**.
6. Find the row whose **API Type** is **a2a** and whose **Name** is `productordera2a`.
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
| `spec.componentMetadata.description` | Describe the Product Order Status Assistant and state that it uses the Product Order A2A dependency. |
| `spec.componentMetadata.functionalBlock` | Replace `replace-with-functional-block` with `CoreCommerce`. |
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
      - name: productordera2a
        specification:
          - apiType: a2a
```

Apply these rules:

1. Enter the dependency exactly as shown.
2. Keep the dependency name lowercase and unchanged; matching is case-sensitive.
3. Use `a2a` as its `apiType`.
4. Do not add a dependency URL or credential.
5. Do not declare `productorderingmcp`.
6. Do not declare the `TMF622` OpenAPI interface.

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
- [ ] The description identifies the Product Order Status Assistant use case.
- [ ] `spec.componentMetadata.functionalBlock` is set to `CoreCommerce`.
- [ ] Owner and maintainer names have been updated.
- [ ] `productordera2a` is declared with `apiType: a2a`.
- [ ] `productorderingmcp` and `TMF622` are not declared.
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
5. Do not use the Product Order A2A, MCP, or OpenAPI URL as the Orchestrator Agent URL.

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

- [ ] The AI-Native ODA Canvas Dashboard URL was obtained from the facilitator or pinned Teams message.
- [ ] `TMFC002 — Product Order Capture and Validation` was used to find `productordera2a`.
- [ ] The dependent Component reports **Complete** in the AI-Native ODA Canvas Dashboard.
- [ ] The template `component.yaml` was downloaded from this repository.
- [ ] All required team-owned fields were updated.
- [ ] `spec.componentMetadata.functionalBlock` is set to `CoreCommerce`.
- [ ] Only `productordera2a` is declared, with `apiType: a2a`.
- [ ] The completed `component.yaml` was submitted to the facilitator.
- [ ] The deployed Orchestrator Agent URL was obtained from the facilitator or the `orch-agent-ui` row.
- [ ] All five readiness indicators are green.
- [ ] One Product Order A2A skill is discovered.
- [ ] All three seeded Product Orders are returned.
- [ ] `PO-1001` is completed, `PO-1002` is `inProgress`, and `PO-1003` is held.
- [ ] The CUST-1001 query returns `PO-1001` and `PO-1002` only.
- [ ] The Resource Inventory control query is rejected as unsupported.

## 11. Troubleshooting

| Symptom | Action |
|---|---|
| The AI-Native ODA Canvas Dashboard cannot be opened | Use the AI-Native ODA Canvas Dashboard URL shown by the facilitator or pinned in the workshop Teams channel. If it still fails, notify the facilitator. |
| The browser reports that the certificate is not trusted | Confirm that the complete hostname matches the facilitator-provided AI-Native ODA Canvas Dashboard or Orchestrator Agent URL. Use **Advanced** and proceed only for that verified workshop URL. |
| `TMFC002` or `productordera2a` is missing | Confirm the namespace filter, then ask the facilitator to verify the Product Order Capture and Validation Component. |
| The dependency is unresolved | Confirm the exact dependency name and `apiType` in `component.yaml`, then ask the facilitator to verify the dependent Component. |
| The team's Orchestrator Agent URL is unknown | Ask the facilitator for the team-specific URL or locate `orch-agent-ui` under the team's Component in the AI-Native ODA Canvas Dashboard. |
| The Product Order A2A skill is unavailable | Select **Refresh Dependent Services** once; if the problem remains, provide the visible error to the facilitator. |
| An order is not found | Confirm that the complete external ID, including the `PO-` prefix, is included in the same query. |
| A follow-up such as “Why is it held?” is unsupported | Repeat the complete request and include the Product Order external ID explicitly. |
| An unexpected dependency appears | Confirm that the Component declares only `productordera2a`; do not add `productorderingmcp` or `TMF622`. |
