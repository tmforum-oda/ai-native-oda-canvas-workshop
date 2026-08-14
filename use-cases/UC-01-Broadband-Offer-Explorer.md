# UC-01 — Broadband Offer Explorer

## Participant User Guide

## 1. Objective

In this exercise, your team will use the AI-Native Canvas Orchestrator Agent to discover and explain seeded Product Catalog offerings and prices.

You will compare two ways of accessing the same domain:

- `productcatalogmcp` — direct invocation of Product Catalog MCP tools;
- `productcataloga2a` — invocation of the Product Catalog A2A agent skill.

By completing the exercise, your team should be able to explain the difference between direct MCP tool use and delegation to an A2A agent.

## 2. Use-case boundary

The Component supports Product Catalog questions about:

- product offerings;
- product specifications;
- product-offering prices;
- catalogues and categories.

It does not provide Product Inventory, Service Inventory, Resource Inventory, Service Qualification, or Product Ordering capabilities.

Each query sent to the outer Orchestrator Agent invokes at most one selected dependency. The Orchestrator Agent does not chain multiple tools or skills.

## 3. Discover the dependencies in the Canvas Dashboard

Before editing `component.yaml`, use the AI-Native Canvas Dashboard to identify the scaffolded ODA Component that owns the required APIs.

The primary dependency entity for this exercise is:

| Component ID | Scaffolded Component | Deployed Component resource |
|---|---|---|
| `TMFC001` | Product Catalog Management | `pc-2-productcatalogmanagement` |
| `productagent` | Product Catalog Agent | `pa-1-productagent` |

`TMFC001` identifies the scaffolded Product Catalog Management Component. `TMF620` identifies its Product Catalog Management OpenAPI contract; 
`productagent` is an agent scaffolded into a Component definition that depends on MCP interface exposed by `TMFC001`.

1. Open the AI-Native Canvas Dashboard URL supplied by the facilitator.
2. Open the **Infrastructure** area.
3. Select **Components**.
4. If a namespace filter is displayed, select **components** or **All namespaces**.
5. Find **TMFC001 — Product Catalog Management** in the Components view. The deployed Component resource is `pc-2-productcatalogmanagement`.
6. Open that Component and confirm that its metadata identifies `TMFC001`.
7. Confirm that the Component **Status** is **Complete**.
8. Locate the **Exposed core APIs** section owned by this Component.
9. FInd **productagent - Product Catalog Agent** in the Components view. The deployed Component resource is `pa-1-productagent`.
10. Find the rows with these API types:
   - **mcp**;
   - **a2a**.
11. Record the exact **Name** and **API Type** shown for each row.

For this use case, you should identify:

| Exposed API name | API type | Purpose |
|---|---|---|
| `productcatalogmcp` | `mcp` | Direct Product Catalog MCP tools |
| `productcataloga2a` | `a2a` | Product Catalog A2A agent skill |

Use the value shown in the **Name** column—not the ID, implementation, URL, Component name, or display label—as the dependent API name in your Component definition. Do not copy the exposed URL into `component.yaml`; the Canvas resolves it after deployment.

Do not select the `TMF620` OpenAPI row for this workshop exercise. Select the MCP and A2A APIs exposed by the `TMFC001` Component.

If either API is missing or the Product Catalog Component is not **Complete**, stop and notify the facilitator.

## 4. Build the dependency declaration

Declare these dependencies in the supplied Component template:

```yaml
dependentAPIs:
  - name: productcatalogmcp
    specification:
      - apiType: mcp
  - name: productcataloga2a
    specification:
      - apiType: a2a
```

Dependency names are exact and case-sensitive. Do not add endpoint URLs; the Canvas resolves them from the deployed dependency Components.

## 5. Readiness checks

Open the team-specific Orchestrator Agent URL supplied by the facilitator. Before running the exercise, confirm that the sidebar shows green indicators for:

- **Component complete**;
- **Component CR readable**;
- **AI Gateway reachable**;
- **Dependencies resolved**;
- **Capabilities available**.

Confirm that both `productcatalogmcp` and `productcataloga2a` appear in the dependency selector.

If a dependency was recently deployed or corrected, select **Refresh Dependent Services** before continuing.

## 6. Discover the available capabilities

Keep the dependency selection set to **Auto** and run:

> What capabilities are provided by this component?

Expected result:

- the response groups capabilities by dependency;
- `productcatalogmcp` exposes Product Catalog MCP tools;
- `productcataloga2a` exposes the Product Catalog A2A skill.

Run:

> What MCP tools are available?

Expected result: the response lists the discovered Product Catalog MCP tools, including the offering and price retrieval tools.

Run:

> What A2A skills are available?

Expected result: the response lists the Product Catalog query skill exposed by `productcataloga2a`.

## 7. Test direct MCP access

Select `productcatalogmcp` explicitly in the dependency selector.

### 7.1 List the product offerings

Run:

> Invoke product_offering_get to list the name, ID, lifecycle status and category of every product offering in the catalog.

Expected result:

- the response includes **Fiber Broadband 50 Mbps**;
- the status line identifies `productcatalogmcp`, API type `mcp`, and capability `product_offering_get`;
- the response includes the generated API ID of the fiber offering.

Copy the API ID returned for **Fiber Broadband 50 Mbps**. The ID may change when the workshop seed data is reloaded.

### 7.2 Inspect the fiber offering

Replace `<FIBER-OFFERING-ID>` with the ID copied from the previous response, then run:

> Invoke product_offering_get with product_offering_id `<FIBER-OFFERING-ID>` and describe the offering.

Expected result: the response describes the 50 Mbps fiber offering and identifies the direct MCP capability in the status line.

Do not ask a follow-up such as `Describe it`. The MVP displays previous messages but does not supply them as model context. Every query must be self-contained.

### 7.3 List product-offering prices

Run:

> Invoke product_offering_price_get to list every product offering price, including its name, amount, currency, recurring charge period and lifecycle status.

Expected result: the response includes **Fiber Broadband 50 Mbps - Monthly Fee** and identifies `product_offering_price_get` in the status line.

## 8. Compare A2A access

Select `productcataloga2a` explicitly in the dependency selector.

### 8.1 List the product offerings through A2A

Run:

> List the name, ID, lifecycle status and category of every product offering in the catalog.

Expected result:

- the response includes **Fiber Broadband 50 Mbps**;
- the status line identifies `productcataloga2a`, API type `a2a`, and the Product Catalog query skill.

### 8.2 List prices through A2A

Run:

> List every product offering price, including its name, amount, currency, recurring charge period and lifecycle status.

Expected result: the A2A agent returns the available prices, including the fiber monthly fee.

## 9. Run the unsupported-domain control

With either Product Catalog dependency selected, run:

> What service instances are active for customer CUST-1001?

Expected result: the Orchestrator Agent rejects the request as unsupported because the Component has no Service Inventory capability.

The query must not be answered using general model knowledge or routed to an unrelated Product Catalog operation.

## 10. Compare the two approaches

Record your observations using the following table:

| Observation | Direct MCP | Product Catalog A2A |
|---|---|---|
| Selected dependency | `productcatalogmcp` | `productcataloga2a` |
| Interface type | MCP | A2A |
| Outer capability | Specific MCP tool | Product Catalog agent skill |
| Tool selection | Performed by the outer Orchestrator Agent | Performed inside the Product Catalog agent |
| Outer dependency calls per query | One | One |
| Response grounded in dependency data | Yes | Yes |

Discuss:

1. When is direct selection of an MCP tool preferable?
2. When is delegation to a domain A2A agent preferable?
3. What information is visible in the response status line?
4. Did both approaches return consistent seeded data?

## 11. Exercise completion checklist

- [ ] All five readiness indicators are green.
- [ ] `TMFC001 — Product Catalog Management` is identified as the dependency Component.
- [ ] `productagent - Product Catalog Agent` is identified as the dependency Component.
- [ ] The deployed Product Catalog Component is **Complete** in the Canvas Dashboard.
- [ ] The exposed API names and types were taken from **Exposed core APIs**.
- [ ] Both dependencies are resolved and available for selection.
- [ ] MCP tools and the A2A skill are discovered.
- [ ] The complete offering list includes **Fiber Broadband 50 Mbps**.
- [ ] The fiber offering is retrieved using its generated API ID.
- [ ] The price list includes the fiber monthly fee.
- [ ] The MCP response identifies `productcatalogmcp`.
- [ ] The A2A response identifies `productcataloga2a`.
- [ ] The Service Inventory control query is rejected as unsupported.
- [ ] The team can explain the MCP and A2A execution differences.

## 12. Troubleshooting

| Symptom | Action |
|---|---|
| The Product Catalog Component or an expected API is missing in the Dashboard | Confirm the namespace filter, then ask the facilitator to verify the deployed dependency Component. |
| A dependency is missing or unresolved | Confirm the dependency name and `apiType` in `component.yaml`, then ask the facilitator to verify Component completion. |
| MCP tools or the A2A skill are unavailable | Select **Refresh Dependent Services** once; if the problem remains, provide the visible error to the facilitator. |
| A broadband keyword query returns no result | Use the complete-list query in this guide, then retrieve the required offering with its generated API ID. |
| A follow-up such as `Describe it` is unsupported | Repeat the complete request and include the offering ID explicitly. |
| Auto selects an unexpected dependency | Select `productcatalogmcp` or `productcataloga2a` explicitly and repeat the query. |
| A response is too long | Request only the specific fields shown in this guide or retrieve one resource by ID. |

## 13. Current MVP constraints

- The outer Orchestrator Agent invokes only one dependency capability for each query.
- Outer-agent tool or skill chaining is not supported.
- Previous chat messages are not supplied as conversational context.
- Keyword filtering follows the behavior of the dependent Product Catalog implementation and may not perform partial-text matching.
- The workshop uses synthetic data only.
