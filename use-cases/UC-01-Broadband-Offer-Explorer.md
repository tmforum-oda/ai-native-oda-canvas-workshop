# UC-01 — Broadband Offer Explorer

## Participant user guide

## 1. Objective

In this exercise, your team will design and validate an AI-Native ODA Component that discovers and explains seeded Product Catalog offerings and prices.

The Component will depend on:

- `productcatalogmcp` for direct access to Product Catalog MCP tools;
- `productcataloga2a` for access to the Product Catalog A2A agent skill.

You will use both interfaces to retrieve Product Catalog information and compare the results.

## 2. Use-case boundary

The Component supports Product Catalog questions about:

- product offerings;
- product specifications;
- product-offering prices;
- catalogues and categories.

It does not provide Product Inventory, Service Inventory, Resource Inventory, Service Qualification, or Product Ordering capabilities.

All workshop records are synthetic and prepared specifically for this exercise.

The Orchestrator Agent provides single-turn conversations. Include the relevant Product Catalog entity, complete identifier, requested fields, and other necessary context in every query. Do not rely on an earlier response or a follow-up such as “Describe it”.

## 3. Identify the dependent functions in the AI-Native ODA Canvas Dashboard

### 3.1 Open the AI-Native ODA Canvas Dashboard

Obtain the **AI-Native ODA Canvas Dashboard URL** from the facilitator's presentation or the link pinned in the workshop Teams channel.

> **Workshop certificate notice:** The AI-Native ODA Canvas Dashboard and deployed Orchestrator Agent use workshop-managed self-signed certificates. Your browser may display **Your connection is not private**, **Certificate not trusted**, or a similar warning. Confirm that the complete URL exactly matches the facilitator-provided or pinned workshop URL. Then select the browser's **Advanced** option and choose **Proceed**, **Continue**, or **Accept the risk**. Do not proceed if the hostname differs or the link did not come from the facilitator; ask the facilitator to verify it.

1. Open the AI-Native ODA Canvas Dashboard.
2. Open the **Infrastructure** tab.
3. Select **Components**.
4. If a namespace filter is displayed, select **components** or **All namespaces**.

This use case requires APIs from two deployed Components:

| Required interface | Dependent function | Component ID | Deployed Component resource |
|---|---|---|---|
| MCP | Product Catalog Management | `TMFC001` | `pc-2-productcatalogmanagement` |
| A2A | Product Catalog Agent | `productagent` | `pa-1-productagent` |

### 3.2 Find the MCP dependency under TMFC001

1. In the Components view, find **TMFC001 — Product Catalog Management**. Its deployed Component resource is `pc-2-productcatalogmanagement`.
2. Open the Component.
3. Confirm that its Component metadata shows ID `TMFC001`.
4. Confirm that its **Status** is **Complete**.
5. Find **Exposed core APIs**.
6. Find the row whose **API Type** is **mcp** and whose **Name** is `productcatalogmcp`.
7. Record:
   - exposed API **Name**: `productcatalogmcp`;
   - **API Type**: `mcp`.

Do not select the `TMF620` OpenAPI row. `TMF620` identifies the Product Catalog API contract; the dependency required by this exercise is the MCP API named `productcatalogmcp`.

If `productcatalogmcp` is missing or the Component is not **Complete**, stop and notify the facilitator.

### 3.3 Find the A2A dependency under productagent

1. Return to the **Components** view.
2. Find **productagent — Product Catalog Agent**. Its deployed Component resource is `pa-1-productagent`.
3. Open the Component.
4. Confirm that its Component metadata shows ID `productagent`.
5. Confirm that its **Status** is **Complete**.
6. Find **Exposed core APIs**.
7. Find the row whose **API Type** is **a2a** and whose **Name** is `productcataloga2a`.
8. Record:
   - exposed API **Name**: `productcataloga2a`;
   - **API Type**: `a2a`.

If `productcataloga2a` is missing or the Product Catalog Agent Component is not **Complete**, stop and notify the facilitator.

### 3.4 Confirm the dependency values

You should now have:

| Dependent function Component ID | Exposed API Name | API type |
|---|---|---|
| `TMFC001` | `productcatalogmcp` | `mcp` |
| `productagent` | `productcataloga2a` | `a2a` |

Use the exposed API **Name** exactly as displayed in the AI-Native ODA Canvas Dashboard. Do not use the Component ID, API contract ID, implementation name, display label, or URL as the dependency name.

Do not copy either exposed API URL into `component.yaml`. The AI-Native ODA Canvas resolves the deployed dependency endpoints.

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
| `spec.componentMetadata.description` | Describe the Broadband Offer Explorer and state that it uses Product Catalog MCP and A2A dependencies. |
| `spec.componentMetadata.functionalBlock` | Replace `replace-with-functional-block` with `CoreCommerce`. |
| `spec.componentMetadata.maintainers[0].name` | Replace `Workshop Team` with the team or maintainer name agreed during the exercise. |
| `spec.componentMetadata.owners[0].name` | Replace `Workshop Team` with the team or owner name agreed during the exercise. |
| `spec.coreFunction.dependentAPIs` | Replace the example dependency with the two dependency entries shown in section 4.3. |

Leave these supplied Component metadata values unchanged unless the facilitator instructs otherwise:

- `spec.componentMetadata.version`;
- `spec.componentMetadata.publicationDate`;
- `spec.componentMetadata.status`.

### 4.3 Replace dependentAPIs

Under `spec.coreFunction`, replace the complete example `dependentAPIs` block with:

```yaml
    dependentAPIs:
      - name: productcatalogmcp
        specification:
          - apiType: mcp
      - name: productcataloga2a
        specification:
          - apiType: a2a
```

Apply these rules:

1. Enter both dependencies exactly as shown.
2. Keep the dependency names lowercase and unchanged; matching is case-sensitive.
3. Use `mcp` for `productcatalogmcp`.
4. Use `a2a` for `productcataloga2a`.
5. Use one `dependentAPIs` entry for each exposed API.
6. Do not add a URL to either dependency.
7. Do not declare the `TMF620` OpenAPI interface.

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
- [ ] The description identifies the Broadband Offer Explorer use case.
- [ ] `spec.componentMetadata.functionalBlock` is set to `CoreCommerce`.
- [ ] Owner and maintainer names have been updated.
- [ ] `productcatalogmcp` is declared with `apiType: mcp`.
- [ ] `productcataloga2a` is declared with `apiType: a2a`.
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
5. Do not use the MCP or A2A dependency URL as the Orchestrator Agent URL.

Before running the exercise, confirm that the Orchestrator Agent sidebar shows green indicators for:

- **Component complete**;
- **Component CR readable**;
- **AI Gateway reachable**;
- **Dependencies resolved**;
- **Capabilities available**.

Confirm that both `productcatalogmcp` and `productcataloga2a` appear in the dependency selector.

If the facilitator has corrected or redeployed a dependency, select **Refresh Dependent Services** once before continuing.

## 6. Discover the available capabilities

Keep the dependency selection set to **Auto** and run:

> What capabilities are provided by this component?

Expected result:

- the response groups capabilities by dependency;
- `productcatalogmcp` exposes 20 Product Catalog MCP tools;
- `productcataloga2a` exposes one Product Catalog A2A skill;
- the Component reports 21 capabilities across two dependencies.

Run:

> What MCP tools are available?

Expected result: the response lists the Product Catalog MCP tools, including `product_offering_get` and `product_offering_price_get`.

Run:

> What A2A skills are available?

Expected result: the response lists the Product Catalog query skill exposed by `productcataloga2a`.

Run:

> Retrieve the agent card for productcataloga2a

Expected result: the Agent Card identifies **Product Agent** and its Product Catalog query skill.

## 7. Test direct MCP access

Select `productcatalogmcp` in the dependency selector.

### 7.1 List the product offerings and retain the Fiber ID

Run:

> Invoke product_offering_get to list the name, ID, lifecycle status and category of every product offering in the catalog.

Expected result:

- the response includes **Fiber Broadband 50 Mbps**;
- the status line identifies `productcatalogmcp`, API type `mcp`, and capability `product_offering_get`;
- the response includes a generated API ID for each offering.

In the response:

1. Find the entry named **Fiber Broadband 50 Mbps**.
2. Copy the value displayed beside its **ID**.
3. Keep this ID for the suggested question in section 7.2.

The ID is generated by the Product Catalog API and may change whenever the workshop seed data is reloaded.

### 7.2 Retrieve the Fiber offering by ID

Use this suggested question:

> Invoke product_offering_get with product_offering_id `<FIBER-OFFERING-ID>` and describe the offering.

Before submitting it:

1. Take the ID copied from the **Fiber Broadband 50 Mbps** entry in the response to section 7.1.
2. In the suggested question above, replace the complete placeholder `<FIBER-OFFERING-ID>`, including the angle brackets, with that copied ID.
3. Submit the complete updated question.

For example, if section 7.1 returned ID `12345678-1234-1234-1234-123456789abc`, submit:

> Invoke product_offering_get with product_offering_id `12345678-1234-1234-1234-123456789abc` and describe the offering.

Expected result: the response describes only **Fiber Broadband 50 Mbps** and the status line identifies `productcatalogmcp`, API type `mcp`, and capability `product_offering_get`.

Do not submit the placeholder unchanged. Do not use a follow-up such as “Describe it”; each query must contain the Product Catalog entity and its complete ID.

### 7.3 List product-offering prices

Run:

> Invoke product_offering_price_get to list every product offering price, including its name, amount, currency, recurring charge period and lifecycle status.

Expected result:

- the response includes **Fiber Broadband 50 Mbps - Monthly Fee**;
- the response includes its amount, currency, recurring charge period, and lifecycle status;
- the status line identifies `productcatalogmcp`, API type `mcp`, and capability `product_offering_price_get`.

## 8. Compare A2A access

Select `productcataloga2a` in the dependency selector.

### 8.1 List the product offerings through A2A

Run:

> List the name, ID, lifecycle status and category of every product offering in the catalog.

Expected result:

- the response includes **Fiber Broadband 50 Mbps**;
- the status line identifies `productcataloga2a`, API type `a2a`, and the Product Catalog query skill.

### 8.2 List prices through A2A

Run:

> List every product offering price, including its name, amount, currency, recurring charge period and lifecycle status.

Expected result:

- the response includes the same seeded Product Catalog prices returned through MCP;
- the status line identifies `productcataloga2a` and API type `a2a`.

## 9. Run the unsupported-domain control

With either Product Catalog dependency selected, run:

> What service instances are active for customer CUST-1001?

Expected result: the request is rejected as unsupported because Service Inventory is outside this Component's declared Product Catalog domain.

## 10. Compare the two interfaces

Record your observations:

| Observation | Direct MCP | Product Catalog A2A |
|---|---|---|
| Dependency | `productcatalogmcp` | `productcataloga2a` |
| API type | MCP | A2A |
| Interface used | Product Catalog MCP tool | Product Catalog agent skill |
| Seeded offerings returned | Record your result | Record your result |
| Seeded prices returned | Record your result | Record your result |

Discuss:

1. Did both interfaces return the same seeded offerings and prices?
2. Which Component owns each exposed API?
3. Which exposed API Name and API type did your Component declare for each interface?
4. What information in the response confirms which interface was used?

## 11. Exercise completion checklist

- [ ] The AI-Native ODA Canvas Dashboard URL was obtained from the facilitator or pinned Teams message.
- [ ] `TMFC001 — Product Catalog Management` was used to find `productcatalogmcp`.
- [ ] `productagent — Product Catalog Agent` was used to find `productcataloga2a`.
- [ ] Both dependency Components report **Complete** in the AI-Native ODA Canvas Dashboard.
- [ ] The template `component.yaml` was downloaded from this repository.
- [ ] All required team-owned fields were updated.
- [ ] `spec.componentMetadata.functionalBlock` is set to `CoreCommerce`.
- [ ] Both dependent API declarations are correct.
- [ ] The completed `component.yaml` was submitted to the facilitator.
- [ ] The deployed Orchestrator Agent URL was obtained from the facilitator or the `orch-agent-ui` row.
- [ ] All five readiness indicators are green.
- [ ] MCP tools, the A2A skill, and the Product Catalog Agent Card are discovered.
- [ ] The offering list includes **Fiber Broadband 50 Mbps**.
- [ ] The Fiber offering is retrieved using the generated API ID copied from the list response.
- [ ] The price list includes the Fiber monthly fee and its requested price fields.
- [ ] The MCP response identifies `productcatalogmcp`.
- [ ] The A2A response identifies `productcataloga2a`.
- [ ] The Service Inventory control query is rejected as unsupported.
- [ ] The team can explain the two dependency declarations.

## 12. Troubleshooting

| Symptom | Action |
|---|---|
| The AI-Native ODA Canvas Dashboard cannot be opened | Use the AI-Native ODA Canvas Dashboard URL shown by the facilitator or pinned in the workshop Teams channel. If it still fails, notify the facilitator. |
| The browser reports that the certificate is not trusted | Confirm that the complete hostname matches the facilitator-provided AI-Native ODA Canvas Dashboard or Orchestrator Agent URL. Use **Advanced** and proceed only for that verified workshop URL. |
| `TMFC001` or `productcatalogmcp` is missing | Confirm the namespace filter, then ask the facilitator to verify the Product Catalog Management Component. |
| `productagent` or `productcataloga2a` is missing | Return to the Components view and locate the Product Catalog Agent separately. If it is still missing, notify the facilitator. |
| A dependency is unresolved | Confirm the exact dependency name and `apiType` in `component.yaml`, then ask the facilitator to verify the dependent Component. |
| The team's Orchestrator Agent URL is unknown | Ask the facilitator for the team-specific URL or locate `orch-agent-ui` under the team's Component in the AI-Native ODA Canvas Dashboard. |
| MCP tools or the A2A skill are unavailable | Select **Refresh Dependent Services** once; if the problem remains, provide the visible error to the facilitator. |
| The Fiber ID placeholder is still in the question | Return to section 7.1, copy the ID beside **Fiber Broadband 50 Mbps**, and replace the entire placeholder before submitting. |
| A broadband keyword query returns no result | Use the complete-list query in section 7.1, then retrieve the Fiber offering using its generated API ID. |
| A follow-up such as “Describe it” is unsupported | Repeat the complete request and include the offering ID explicitly. |
| A response is too long | Request only the fields shown in this guide or retrieve one offering by ID. |
