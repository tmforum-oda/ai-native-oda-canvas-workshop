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

## 3. Identify the dependent functions in the Canvas Portal

### 3.1 Open the Canvas Portal

Obtain the **Canvas Portal URL** from the facilitator's presentation or the link pinned in the workshop Teams channel.

> **Workshop URL notice:** The Canvas Portal uses plain `http://`. Type the complete `http://` URL from the facilitator or the workshop Teams channel; if your browser changes it to `https://`, the page does not load. The browser may mark the portal **Not secure**, or warn that the site does not support a secure connection and offer **Continue to site**; both are expected for the verified portal URL. Your Orchestrator Agent uses `https://` with a workshop-managed self-signed certificate, so the browser may display **Your connection is not private**, **Certificate not trusted**, or a similar warning. Confirm that the hostname matches the URL opened from your own `orch-agent-ui` row under **APIS → Exposed APIs** in the Canvas Portal, then select **Advanced** and choose **Proceed**, **Continue**, or **Accept the risk**. Do not proceed if the hostname differs; ask the facilitator to verify it.

1. Open the Canvas Portal and sign in with your team's credentials from the facilitator. On first sign-in you are asked to set a new password.
2. In the left navigation, under **COMPONENTS**, select **Inventory**.
3. If a namespace filter is displayed, select **components** or all namespaces.

This use case requires APIs from two deployed Components:

| Required interface | Dependent function | Component ID | Deployed Component resource |
|---|---|---|---|
| MCP | Product Catalog Management | `TMFC001` | `pc-2-productcatalogmanagement` |
| A2A | Product Catalog Agent | `productagent` | `pa-1-productagent` |

### 3.2 Find the MCP dependency under TMFC001

1. In **Components → Inventory**, find **TMFC001 — Product Catalog Management**. Its deployed Component resource is `pc-2-productcatalogmanagement`.
2. Open the Component by selecting its name.
3. Open the **Raw** tab and confirm that `spec.componentMetadata.id` is `TMFC001`.
4. On the **Overview** tab, confirm that **Status** shows **Complete**.
5. Open the **APIs** tab and find the **Core Function** group.
6. Find the API named `productcatalogmcp`. The **APIs** tab shows only the API name. To confirm its type, stay in the same browser tab: in the left navigation, under **APIS**, select **Exposed APIs**, set the **All API Types** filter to **MCP**, and type `productcatalogmcp` in the search box. Check that the row `pc-2-productcatalogmanagement-productcatalogmcp` is listed, then return to **COMPONENTS → Inventory**.
7. Record:
   - exposed API **Name**: `productcatalogmcp`;
   - **API Type**: `mcp`.

Do not select the `TMF620` OpenAPI row. `TMF620` identifies the Product Catalog API contract; the dependency required by this exercise is the MCP API named `productcatalogmcp`.

If `productcatalogmcp` is missing or the Component is not **Complete**, stop and notify the facilitator.

### 3.3 Find the A2A dependency under productagent

1. Return to **Components → Inventory**.
2. Find **productagent — Product Catalog Agent** in the **Outside TMF Catalog** section. Its deployed Component resource is `pa-1-productagent`.
3. Open the Component by selecting its name.
4. Open the **Raw** tab and confirm that `spec.componentMetadata.id` is `productagent`.
5. On the **Overview** tab, confirm that **Status** shows **Complete**.
6. Open the **APIs** tab and find the **Core Function** group.
7. Find the API named `productcataloga2a`. The **APIs** tab shows only the API name. To confirm its type, stay in the same browser tab: in the left navigation, under **APIS**, select **Exposed APIs**, set the **All API Types** filter to **A2A**, and type `productcataloga2a` in the search box. Check that the row `pa-1-productagent-productcataloga2a` is listed, then return to **COMPONENTS → Inventory**.
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

Use the exposed API **Name** exactly as displayed in the Canvas Portal. Do not use the Component ID, API contract ID, implementation name, display label, or URL as the dependency name.

Do not copy either exposed API URL into your values. The AI-Native ODA Canvas resolves the deployed dependency endpoints.

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
| `component.name` | A short lowercase name for your Component, for example `offer-explorer`. Use only letters, digits and hyphens. Your deployed Component is named `<release name>-<component.name>`, for example `team01-offer-explorer`. |
| `component.id` | The unique team Component ID assigned or approved by the facilitator, for example `TEAM01-UC01`. |
| `component.description` | Describe the Broadband Offer Explorer and the dependencies it uses. |
| `component.functionalBlock` | `CoreCommerce` |
| `component.maintainer.name` | The team or maintainer name agreed during the exercise. |
| `component.owner.name` | The team or owner name agreed during the exercise. |
| `component.dependentAPIs` | Replace the placeholder entry with both dependency entries shown in section 4.3. |

Leave `component.version` and `component.publicationDate` unchanged unless the facilitator instructs otherwise.

### 4.3 Set dependentAPIs

Replace the complete placeholder `dependentAPIs` list inside the `component` block with:

```yaml
  dependentAPIs:
    - name: productcatalogmcp
      apiType: mcp
    - name: productcataloga2a
      apiType: a2a
```

Apply these rules:

1. Enter both dependencies exactly as shown.
2. Keep the dependency names lowercase and unchanged; matching is case-sensitive.
3. Use `mcp` as the `apiType` of `productcatalogmcp`.
4. Use `a2a` as the `apiType` of `productcataloga2a`.
5. Use one `dependentAPIs` entry for each exposed API.
6. Do not add a URL or credential to any dependency.
7. Do not declare the `TMF620` OpenAPI interface.

Your completed values should look like this, with your own team values:

```yaml
component:
  name: offer-explorer
  id: TEAM01-UC01
  version: "0.1.0"
  description: Broadband Offer Explorer using the Product Catalog MCP and A2A dependencies
  functionalBlock: CoreCommerce
  publicationDate: "2026-07-23T00:00:00Z"
  maintainer:
    name: Team 01
    email: workshop@example.invalid
  owner:
    name: Team 01
    email: workshop@example.invalid
  dependentAPIs:
    - name: productcatalogmcp
      apiType: mcp
    - name: productcataloga2a
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
- [ ] The description identifies the Broadband Offer Explorer use case.
- [ ] `component.functionalBlock` is set to `CoreCommerce`.
- [ ] Owner and maintainer names have been updated.
- [ ] No value starting with `replace-with` remains.
- [ ] `productcatalogmcp` is declared with `apiType: mcp`.
- [ ] `productcataloga2a` is declared with `apiType: a2a`.
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

1. Under **COMPONENTS**, select **Inventory** and find your Component in the **Outside TMF Catalog** section, for example `team01-offer-explorer`. Team Components are listed there because they are not TMF catalogue Components.
2. Open it and confirm on the **Overview** tab that **Status** shows **Complete**. This can take a minute after installation.
3. Open the **Raw** tab and confirm that `spec.coreFunction.dependentAPIs` contains exactly the dependencies from section 4.3.
4. Under **APIs**, select **Dependent APIs**, find the entries belonging to your Component, and confirm that each is **Resolved** (ready).

To correct your values after installation, use **COMPONENTS → Lifecycle → Upgrade**. Select your own release. The repository and chart are already fixed to `workshop` and `canvas-orch-agent`. The editor opens with your deployed values. Edit them and select **Upgrade Release**. Never upgrade or uninstall another team's release.

## 5. Open the deployed Orchestrator Agent

1. In the left navigation, under **APIS**, select **Exposed APIs**.
2. In the search box, type your team's release name, for example `team01`.
3. Find the row named `<release name>-<component.name>-orch-agent-ui`, for example `team01-offer-explorer-orch-agent-ui`, and confirm that its **Status** is **LIVE**.
4. In the **Actions** column of that row, select the **Test API endpoint** icon. Your Orchestrator Agent opens in a new browser tab.
5. If the row is missing or not yet **LIVE**, wait a minute and refresh the page. If it is still missing, ask the facilitator.
6. If the browser displays a certificate warning, verify the URL and follow the workshop URL notice in section 3.1.
7. Do not open the MCP or A2A dependency rows as the Orchestrator Agent. Do not open another team's `orch-agent-ui` row; every team's Component exposes an API with this name.

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

- [ ] The Canvas Portal URL was obtained from the facilitator or pinned Teams message.
- [ ] `TMFC001 — Product Catalog Management` was used to find `productcatalogmcp`.
- [ ] `productagent — Product Catalog Agent` was used to find `productcataloga2a`.
- [ ] Both dependency Components report **Complete** in the Canvas Portal.
- [ ] The `canvas-orch-agent` chart from the `workshop` repository was installed as the team's release in namespace `components`.
- [ ] All required team-owned fields were updated.
- [ ] `component.functionalBlock` is set to `CoreCommerce`.
- [ ] Both dependent API declarations are correct.
- [ ] Every declared dependency is **Resolved** under **APIs → Dependent APIs**.
- [ ] The Orchestrator Agent was opened with **Test API endpoint** on the team's own `orch-agent-ui` row under **APIS → Exposed APIs**.
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
| The installation is rejected | Follow the correction table in section 4.5. |
| The Component does not reach **Complete** | Open its **Events** tab, then ask the facilitator. Do not reinstall under a different release name. |
| The Canvas Portal cannot be opened | Use the Canvas Portal URL shown by the facilitator or pinned in the workshop Teams channel. If it still fails, notify the facilitator. |
| The Canvas Portal page does not load or keeps loading | Type the complete `http://` portal URL. If the browser switches to `https://`, allow the HTTP connection for that verified URL (for example **Continue to site**). |
| The browser reports that the Orchestrator Agent certificate is not trusted | Confirm that the hostname matches the URL opened from your own `orch-agent-ui` row under **APIS → Exposed APIs**. Use **Advanced** and proceed only for that verified URL. |
| `TMFC001` or `productcatalogmcp` is missing | Confirm the namespace filter, then ask the facilitator to verify the Product Catalog Management Component. |
| `productagent` or `productcataloga2a` is missing | Return to **Components → Inventory** and locate the Product Catalog Agent separately. If it is still missing, notify the facilitator. |
| A dependency is unresolved | Confirm the exact dependency name and `apiType` in `component.dependentAPIs`, correct them with **Components → Lifecycle → Upgrade**, then ask the facilitator to verify the dependent Component if the problem remains. |
| The team's Orchestrator Agent URL is unknown | Under **APIS → Exposed APIs**, search for your release name and select **Test API endpoint** on your `orch-agent-ui` row. If the row is missing, ask the facilitator. |
| MCP tools or the A2A skill are unavailable | Select **Refresh Dependent Services** once; if the problem remains, provide the visible error to the facilitator. |
| The Fiber ID placeholder is still in the question | Return to section 7.1, copy the ID beside **Fiber Broadband 50 Mbps**, and replace the entire placeholder before submitting. |
| A broadband keyword query returns no result | Use the complete-list query in section 7.1, then retrieve the Fiber offering using its generated API ID. |
| A follow-up such as “Describe it” is unsupported | Repeat the complete request and include the offering ID explicitly. |
| A response is too long | Request only the fields shown in this guide or retrieve one offering by ID. |

## 12. Remove your release

Remove your release only when the facilitator announces that the exercise is over. Removal deletes your Component, its Orchestrator Agent and its dependency registrations; the pre-deployed dependency Components are not affected.

1. In the Canvas Portal, under **COMPONENTS**, select **Lifecycle**.
2. Select **Uninstall**. The **Uninstall Deployed Components** dialog lists the releases in the `components` namespace.
3. Select **only your own release**, for example `team01`. Never select another team's release or a pre-deployed dependency Component.
4. Select **Review Removal**, check that the list contains only your release, and select **Uninstall Selected**.
5. Under **COMPONENTS → Inventory**, confirm that your Component, for example `team01-…`, is no longer listed. Removal can take a minute.
