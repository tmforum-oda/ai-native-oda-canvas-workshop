# UC-05 — Service Qualification Advisor

## Participant User Guide

## 1. Objective

In this exercise, your team will use the AI-Native Canvas Orchestrator Agent to retrieve and explain seeded Service Qualification outcomes.

You will compare two ways of accessing the same domain:

- `servicequalificationmcp` — direct invocation of TMF645 Service Qualification MCP tools;
- `servicequalificationa2a` — delegation to the Service Qualification A2A agent skill.

By completing the exercise, your team should be able to explain the difference between direct MCP tool selection and delegation to a bounded domain agent.

## 2. Use-case boundary

The Component supports questions about persisted Service Qualification requests and seeded outcomes, including:

- completed and pending qualification requests;
- qualified and unqualified outcomes;
- requested bandwidth and workshop location;
- alternatives and reasons recorded in a qualification result;
- qualification records identified by a stable workshop ID;
- previewing the data required to create a qualification request.

The seeded records are reference outcomes prepared for the workshop. The Component does not perform live network feasibility analysis, reserve resources, activate services, or place product orders.

Each query sent to the outer Orchestrator Agent invokes at most one selected dependency. When A2A is selected, the Service Qualification agent may make one internal call to its MCP dependency. The outer Orchestrator Agent does not chain multiple tools or skills.

## 3. Discover the dependencies in the Canvas Dashboard

Before editing `component.yaml`, use the AI-Native Canvas Dashboard to identify the scaffolded ODA Component that owns the required APIs.

The primary dependency entity for this exercise is:

| Component ID | Scaffolded Component | Deployed Component resource |
|---|---|---|
| `TMFC009` | Service Qualification Management | `sq-1-servicequalificationmanagement` |

`TMFC009` identifies the scaffolded Service Qualification Management Component. `TMF645` identifies its Service Qualification OpenAPI contract; it is not the Component identity or the dependent API name used in this exercise.

1. Open the AI-Native Canvas Dashboard URL supplied by the facilitator.
2. Open the **Infrastructure** area.
3. Select **Components**.
4. If a namespace filter is displayed, select **components** or **All namespaces**.
5. Find **TMFC009 — Service Qualification Management**. The deployed Component resource is `sq-1-servicequalificationmanagement`.
6. Open the Component and confirm that its metadata identifies `TMFC009`.
7. Confirm that the Component **Status** is **Complete**.
8. Locate the **Exposed core APIs** section.
9. Find the rows whose **API Type** values are **mcp** and **a2a**.
10. Record the exact **Name** and **API Type** shown for each row.

For this use case, you should identify:

| Exposed API name | API type | Purpose |
|---|---|---|
| `servicequalificationmcp` | `mcp` | Direct Service Qualification MCP tools |
| `servicequalificationa2a` | `a2a` | Service Qualification A2A agent skill |

Use the value shown in the **Name** column—not the ID, implementation, URL, Component name, or display label—as the dependent API name. Do not copy an exposed URL into `component.yaml`; the Canvas resolves it after deployment.

Do not select the `TMF645` OpenAPI row for this workshop exercise. Select the MCP and A2A APIs exposed by the `TMFC009` Component.

If either API is missing or the Service Qualification Component is not **Complete**, stop and notify the facilitator.

## 4. Build the dependency declaration

Declare both dependencies in the supplied Component template:

```yaml
dependentAPIs:
  - name: servicequalificationmcp
    specification:
      - apiType: mcp
  - name: servicequalificationa2a
    specification:
      - apiType: a2a
```

Dependency names are exact and case-sensitive. Do not add endpoint URLs; the Canvas resolves them from the deployed dependency Component.

## 5. Readiness checks

Open the team-specific Orchestrator Agent URL supplied by the facilitator. Confirm that the sidebar shows green indicators for:

- **Component complete**;
- **Component CR readable**;
- **AI Gateway reachable**;
- **Dependencies resolved**;
- **Capabilities available**.

Confirm that both `servicequalificationmcp` and `servicequalificationa2a` appear in the dependency selector.

If a dependency was recently deployed or corrected, select **Refresh Dependent Services** before continuing.

## 6. Discover the available capabilities

Keep the dependency selection set to **Auto** and run:

> What capabilities are provided by this component?

Expected result:

- nine capabilities are reported across two dependencies;
- `servicequalificationmcp` exposes eight MCP tools;
- `servicequalificationa2a` exposes one A2A skill.

Run:

> What MCP tools are available?

Expected MCP tools:

- `check_service_qualification_get`;
- `check_service_qualification_create`;
- `check_service_qualification_update`;
- `check_service_qualification_delete`;
- `query_service_qualification_get`;
- `query_service_qualification_create`;
- `query_service_qualification_update`;
- `query_service_qualification_delete`.

Run:

> What A2A skills are available?

Expected result: **Service Qualification Query**, with skill ID `service-qualification-query`.

The UC-05 baseline is stored as query qualification records. Use `query_service_qualification_get` for direct access to the seeded results.

## 7. Test direct MCP access

Select `servicequalificationmcp` explicitly.

### 7.1 List every seeded qualification result

Run:

> Invoke query_service_qualification_get to list every qualification record

Expected result:

| Workshop ID | Request | Location | Outcome | State |
|---|---|---|---|---|
| `WS-QUAL-1001` | 1 Gbps Fiber Access | Accelerate Asia Primary Site | qualified | `done` |
| `WS-QUAL-1002` | 1 Gbps Fiber Access | Accelerate Asia Secondary Site | unqualified, with a 100 Mbps alternative | `done` |
| `WS-QUAL-1003` | 500 Mbps Fiber Access | Accelerate Asia Primary Site | pending | `inProgress` |

The response status should identify `servicequalificationmcp`, API type `mcp`, capability `query_service_qualification_get`, and invocation mode.

The API-generated record IDs may change whenever the facilitator restores the workshop baseline. Use the stable workshop IDs for the remaining tests.

### 7.2 Retrieve the qualified outcome

Run:

> Invoke query_service_qualification_get with external_id WS-QUAL-1001 and explain the qualification result

Expected result:

- 1 Gbps Fiber Access was requested;
- the location is **Accelerate Asia Primary Site**;
- the outcome is **qualified**;
- the state is `done`.

`WS-QUAL-1001` is stored in `externalIdentifier.id`. It is not an API-generated qualification resource ID.

### 7.3 Retrieve the unqualified outcome and alternative

Run:

> Invoke query_service_qualification_get with external_id WS-QUAL-1002 and explain the outcome, reason and available alternative

Expected result:

- 1 Gbps Fiber Access is unqualified at **Accelerate Asia Secondary Site**;
- available access capacity is limited to 100 Mbps;
- a 100 Mbps Fiber Access service is identified as an alternative;
- the state is `done`.

### 7.4 Retrieve the pending request

Run:

> Invoke query_service_qualification_get with external_id WS-QUAL-1003 and explain its current state

Expected result:

- the request is for 500 Mbps Fiber Access;
- the location is **Accelerate Asia Primary Site**;
- the outcome is pending;
- the state is `inProgress`.

Do not ask a follow-up such as `What about the second result?`. Previous messages are displayed but are not supplied as model context. Every query must be self-contained.

## 8. Compare A2A access

Select `servicequalificationa2a` explicitly.

### 8.1 Inspect the A2A contract

Run:

> Retrieve the agent card for servicequalificationa2a

Expected result: the Agent Card identifies **Service Qualification Agent** and its `service-qualification-query` skill.

Run:

> Describe the service-qualification-query capability and its parameters

Expected result: the response explains that the skill retrieves seeded results or submits a qualification request through one bounded downstream MCP tool call.

### 8.2 List results through the domain agent

Run:

> Invoke service-qualification-query to list every available service qualification result

Expected result: the response contains the same qualified, unqualified-with-alternative, and pending outcomes returned by direct MCP access.

The status should identify `servicequalificationa2a`, API type `a2a`, capability `service-qualification-query`, and invocation mode.

### 8.3 Retrieve one result through the domain agent

Run:

> Invoke service-qualification-query to show and explain qualification result WS-QUAL-1002

Expected result: the A2A agent returns the unqualified 1 Gbps result, its capacity reason, and its 100 Mbps alternative.

## 9. Compare the two approaches

Record your observations:

| Observation | Direct MCP | Service Qualification A2A |
|---|---|---|
| Selected dependency | `servicequalificationmcp` | `servicequalificationa2a` |
| Interface type | MCP | A2A |
| Outer capability | `query_service_qualification_get` | `service-qualification-query` |
| Tool selection | Performed by the outer Orchestrator Agent | Performed inside the Service Qualification agent |
| Outer dependency calls per query | One | One |
| Internal MCP calls | None | At most one |
| Response grounded in persisted qualification data | Yes | Yes |

Discuss:

1. When is direct MCP tool selection preferable?
2. When is delegation to a domain A2A agent preferable?
3. Which execution details are visible in the Orchestrator Agent status line?
4. Did both paths return consistent seeded outcomes?

## 10. Test mutation safety

Select `servicequalificationmcp` and run:

> Preview the parameters required to create a query service qualification request

Expected result: the Orchestrator Agent displays a preview and does not invoke the dependency.

Then run:

> Invoke query_service_qualification_create to create a service qualification request

Expected result:

- the Orchestrator Agent displays an invocation preview;
- the status is `confirmation_required`;
- **Confirm Invocation** and **Cancel** controls appear;
- selecting **Cancel** performs no operation.

Do not confirm placeholder arguments. An executable create request requires a facilitator-provided payload containing a valid `@type` and `searchCriteria`.

Repeat the safety check with `servicequalificationa2a` selected:

> Invoke service-qualification-query to create a service qualification request

Expected result: an A2A mutation-intent preview requiring confirmation. Select **Cancel** unless the facilitator has explicitly provided a synthetic payload and instructed you to create it.

## 11. Run the unsupported-domain control

Return the dependency selector to **Auto** and run:

> Activate the qualified service on the network

Expected result:

- the request is rejected as unsupported;
- the response status is `unsupported`;
- no qualification, inventory, ordering, or activation operation is invoked;
- no activation result is fabricated.

Service activation is outside the capabilities declared by this Component.

## 12. Architectural observations

Record and discuss:

1. Why is a persisted qualification outcome different from a live feasibility decision?
2. Why is `external_id` appropriate for `WS-QUAL-1001`?
3. Why should API-generated identifiers not be hard-coded into participant instructions?
4. What responsibility remains with the outer Orchestrator Agent when A2A is selected?
5. What responsibility moves into the Service Qualification A2A agent?
6. Why must create, update, and delete operations require confirmation?
7. Why does a successful qualification not authorize service activation?

## 13. Exercise completion checklist

- [ ] All five readiness indicators are green.
- [ ] `TMFC009 — Service Qualification Management` is identified as the dependency Component.
- [ ] The deployed Service Qualification Component is **Complete** in the Canvas Dashboard.
- [ ] The MCP and A2A names were taken from **Exposed core APIs**.
- [ ] Both dependencies resolve and are available for selection.
- [ ] Eight MCP tools and one A2A skill are discovered.
- [ ] The complete list contains all three workshop qualification records.
- [ ] `WS-QUAL-1001` is reported as qualified at the Primary Site.
- [ ] `WS-QUAL-1002` is reported as unqualified with its reason and alternative.
- [ ] `WS-QUAL-1003` is reported as pending and `inProgress`.
- [ ] Direct MCP and A2A access return consistent outcomes.
- [ ] Mutation requests display confirmation controls and cancellation performs no operation.
- [ ] The activation control query is rejected as unsupported.
- [ ] The team can explain the MCP and A2A execution differences.

## 14. Troubleshooting

| Symptom | Action |
|---|---|
| The Service Qualification Component or an expected API is missing in the Dashboard | Confirm the namespace filter, then ask the facilitator to verify the deployed dependency Component. |
| A dependency is missing or unresolved | Confirm the exact dependency name and `apiType` in `component.yaml`, then ask the facilitator to verify Component completion. |
| MCP tools or the A2A skill are unavailable | Select **Refresh Dependent Services** once; if the problem remains, provide the visible error to the facilitator. |
| The complete-list query returns no records | Ask the facilitator to verify that the UC-05 seed baseline was loaded for release `sq-1`. |
| A stable workshop ID returns no result | Use `external_id`, not `qualification_id`, with `WS-QUAL-1001`, `WS-QUAL-1002`, or `WS-QUAL-1003`. |
| An A2A invocation returns HTTP 404 | Ask the facilitator to verify that the current Service Qualification A2A image and base invocation route are deployed. |
| A create preview contains placeholder values | Do not confirm it. Use only a complete synthetic payload supplied by the facilitator. |
| A follow-up question is unsupported | Repeat the complete request and include the stable workshop ID. |
| Auto selects an unexpected dependency | Select `servicequalificationmcp` or `servicequalificationa2a` explicitly and repeat the request. |
| A response is too long | Request one qualification by `external_id` or ask only for the required fields. |

## 15. Current MVP constraints

- The qualification results are persisted workshop reference data, not live feasibility decisions.
- The outer Orchestrator Agent invokes only one dependency capability for each query.
- The Service Qualification A2A agent may make at most one internal MCP tool call.
- Outer-agent tool or skill chaining is not supported.
- Previous chat messages are not supplied as conversational context.
- Mutation previews are not executed until explicitly confirmed.
- The workshop uses synthetic data only.
