# UC-06 — Product Order Status Assistant

## Participant User Guide

## 1. Objective

In this exercise, your team will use the AI-Native Canvas Orchestrator Agent to retrieve and explain seeded Product Order lifecycle states through an A2A dependency.

You will use:

- `productordera2a` — the Product Order A2A agent and its bounded status-query skill.

The Product Order agent owns its internal dependency on `productorderingmcp`. Your Component must not declare that MCP API directly.

## 2. Use-case boundary

The Component supports read-only Product Order status questions about:

- all seeded Product Orders;
- completed, in-progress and held orders;
- one order identified by its stable external ID;
- the product offering and customer described by an order;
- the reason recorded for a held order.

The A2A skill does not create, update, delete or cancel Product Orders. It does not activate products or services.

Each outer-agent query invokes one A2A skill. The Product Order agent may then invoke one read-only Product Ordering MCP tool internally.

## 3. Discover the dependency in the Canvas Dashboard

Before editing `component.yaml`, use the AI-Native Canvas Dashboard to identify the scaffolded ODA Component that owns the required A2A API.

| Component ID | Scaffolded Component | Deployed Component resource |
|---|---|---|
| `TMFC002` | Product Order Capture and Validation | `po-1-productordercaptureandvalidation` |

`TMFC002` identifies the scaffolded Component. `TMF622` identifies its Product Ordering OpenAPI contract; it is not the dependent API name used in this exercise.

1. Open the AI-Native Canvas Dashboard URL supplied by the facilitator.
2. Open **Infrastructure**, then select **Components**.
3. Select the **components** namespace or **All namespaces** if a namespace filter is displayed.
4. Find **TMFC002 — Product Order Capture and Validation**.
5. Open `po-1-productordercaptureandvalidation` and confirm its status is **Complete**.
6. Locate **Exposed core APIs**.
7. Find the row whose **API Type** is **a2a**.
8. Record the exact API **Name** and **API Type**.

You should identify:

| Exposed API name | API type | Purpose |
|---|---|---|
| `productordera2a` | `a2a` | Product Order status-query agent |

Use the exact value in the **Name** column. Do not copy the API ID, implementation, URL, Component name or display label into `dependentAPIs`.

Do not select the `TMF622` OpenAPI row or declare `productorderingmcp`. If the A2A API is missing or the Component is not **Complete**, notify the facilitator.

## 4. Build the dependency declaration

Declare only this dependency in the supplied Component template:

```yaml
dependentAPIs:
  - name: productordera2a
    specification:
      - apiType: a2a
```

Do not add an endpoint URL. The Canvas resolves the deployed A2A endpoint.

## 5. Readiness checks

Open the team-specific Orchestrator Agent URL and confirm that the sidebar shows green indicators for:

- **Component complete**;
- **Component CR readable**;
- **AI Gateway reachable**;
- **Dependencies resolved**;
- **Capabilities available**.

Confirm that `productordera2a` appears in the dependency selector. If the dependency was recently updated, select **Refresh Dependent Services** once.

## 6. Discover the available capability

Keep the dependency selection set to **Auto** and run:

> What dependencies are available?

Expected result: `productordera2a` is available as an A2A dependency.

Run:

> What capabilities are provided by this component?

Expected result: one capability, **Product Order Status Query**.

Run:

> What A2A skills are available?

Expected result: skill ID `product-order-status-query` exposed by `productordera2a`.

Run:

> Retrieve the agent card for productordera2a

Expected result: the Agent Card identifies **Product Order Agent** and its status-query skill.

## 7. Test Product Order status access

Select `productordera2a` explicitly.

### 7.1 List every seeded Product Order

Run:

> Invoke product-order-status-query to list every product order. Return only the external ID, product offering, customer ID, order state and description.

Expected result:

| External ID | Product offering | Customer | State |
|---|---|---|---|
| `PO-1001` | Fiber Offering 50 Mbps | `CUST-1001` | completed |
| `PO-1002` | Business Firewall Solution | `CUST-1001` | `inProgress` |
| `PO-1003` | Business MPLS Network | `CUST-1002` | held |

The status line should identify `productordera2a`, API type `a2a`, capability `product-order-status-query`, and invocation mode.

### 7.2 Retrieve the completed order

Run:

> Invoke product-order-status-query to describe the status of product order PO-1001

Expected result:

- state: `completed`;
- product offering: Fiber Offering 50 Mbps;
- customer: `CUST-1001`;
- completion date: `2026-07-03T16:45:00Z`.

`PO-1001` is a stable workshop external ID. It is not the API-generated Product Order resource ID.

### 7.3 Find the in-progress order

Run:

> Invoke product-order-status-query to list only product orders currently in progress. Return their external ID, offering, state and expected completion date.

Expected result: only `PO-1002`, for **Business Firewall Solution**, in state `inProgress`, with expected completion date `2026-08-15T18:00:00Z`.

### 7.4 Explain the held order

Run:

> Invoke product-order-status-query to explain why product order PO-1003 is held

Expected result: the Business MPLS order is held pending confirmation of site access.

### 7.5 Find orders for one customer

Run:

> Invoke product-order-status-query to list every product order for customer CUST-1001. Return only the external ID, offering and state.

Expected result:

- `PO-1001`, Fiber Offering 50 Mbps, completed;
- `PO-1002`, Business Firewall Solution, `inProgress`;
- `PO-1003` is excluded because it belongs to `CUST-1002`.

The Product Order agent retrieves the applicable order records with one MCP call and grounds the final answer in that result.

## 8. Validate an unknown order

Run:

> Invoke product-order-status-query to retrieve product order PO-9999

Expected result: the response states that the result set is empty or that no matching Product Order was found. It must not fabricate an order or confuse `PO-9999` with an API-generated resource ID.

## 9. Test the mutation guardrail

Run:

> Cancel product order PO-1001

Then run:

> Invoke product-order-status-query to cancel product order PO-1001

Expected result for both requests:

- the request is rejected as unsupported;
- no cancellation request is created;
- no Product Order state changes;
- the status-query skill does not select a mutating MCP tool.

This UC-06 A2A skill is intentionally read-only even though the underlying Product Ordering Component exposes other operations.

## 10. Run unsupported-domain controls

Run:

> What network resources are in alarm at the Accelerate Asia Primary Site?

Run:

> Who is the prime minister of India?

Expected result: both requests are rejected as unsupported. The Component must not answer from another domain or from general model knowledge.

## 11. Architectural observations

Record and discuss:

1. Why does the participant Component declare only `productordera2a`?
2. Where is the internal `productorderingmcp` dependency owned and invoked?
3. How does the outer response status identify the selected A2A dependency and skill?
4. Why is `PO-1001` passed as an external ID rather than an API-generated ID?
5. Why must a status-focused skill reject cancellation requests?
6. How does the one-internal-tool-call limit keep delegation bounded?

## 12. Exercise completion checklist

- [ ] All five readiness indicators are green.
- [ ] `TMFC002 — Product Order Capture and Validation` is identified as the dependency Component.
- [ ] The deployed Component is **Complete** in the Canvas Dashboard.
- [ ] The A2A name and type were taken from **Exposed core APIs**.
- [ ] Only `productordera2a` is declared by the participant Component.
- [ ] The Product Order Agent Card and skill are discovered.
- [ ] All three seeded Product Orders are returned.
- [ ] `PO-1001` is reported as completed.
- [ ] `PO-1002` is reported as `inProgress`.
- [ ] `PO-1003` is reported as held with the correct reason.
- [ ] The unknown order produces no fabricated result.
- [ ] Both cancellation requests are rejected.
- [ ] Cross-domain and general-knowledge requests are rejected.
- [ ] The team can explain the outer A2A and internal MCP relationship.

## 13. Troubleshooting

| Symptom | Action |
|---|---|
| The Product Ordering Component or A2A API is missing in the Dashboard | Confirm the namespace filter, then ask the facilitator to verify the deployed dependency Component. |
| The dependency is unresolved | Confirm the exact name `productordera2a` and `apiType: a2a` in `component.yaml`. |
| The Agent Card or skill cannot be discovered | Select **Refresh Dependent Services** once, then give the visible error to the facilitator. |
| An A2A invocation returns HTTP 404 | Ask the facilitator to verify that the current Product Order A2A image and base invocation route are deployed. |
| The complete list contains no orders | Ask the facilitator to verify the UC-06 Product Ordering seed baseline for release `po-1`. |
| A stable order ID returns no result | Include the complete ID, such as `PO-1001`, in a self-contained request. |
| A follow-up such as `Why is it held?` is unsupported | Repeat the complete request and include `PO-1003`. |
| A cancellation request is rejected | This is expected; the workshop skill is status-only. |
| A response is too long | Request one order by external ID or ask for only the required fields. |

## 14. Current MVP constraints

- The outer Orchestrator Agent invokes one A2A capability for each query.
- The Product Order agent may make at most one internal MCP call.
- The Product Order A2A skill is status-focused and does not invoke mutation tools.
- Outer-agent tool or skill chaining is not supported.
- Previous chat messages are not supplied as conversational context.
- The workshop uses synthetic data only.
