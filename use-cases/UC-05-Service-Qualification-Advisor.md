# UC-05 — Service Qualification Advisor

## Participant user guide

## 1. Objective

In this exercise, your team will design and validate an AI-Native ODA Component that retrieves and explains seeded Service Qualification outcomes.

The Component will depend on:

- `servicequalificationmcp` for direct access to Service Qualification MCP tools;
- `servicequalificationa2a` for access to the Service Qualification A2A agent skill.

You will use both interfaces to retrieve qualification results and compare the returned information.

## 2. Use-case boundary

The Component supports Service Qualification questions about:

- completed and pending qualification requests;
- qualified and unqualified outcomes;
- requested bandwidth and workshop location;
- alternatives and reasons recorded in qualification results;
- a qualification record identified by its stable external ID.

The seeded records are reference outcomes prepared for the workshop. The Component does not perform live network feasibility analysis, reserve resources, activate services, or place product orders.

All workshop records are synthetic and prepared specifically for this exercise.

The Orchestrator Agent provides single-turn conversations. Include the qualification entity, complete external ID, requested fields, and other necessary context in every query. Do not rely on an earlier response or a follow-up such as “Explain that result”.

## 3. Identify the dependent functions in the AI-Native ODA Canvas Dashboard

### 3.1 Open the AI-Native ODA Canvas Dashboard

Obtain the **AI-Native ODA Canvas Dashboard URL** from the facilitator's presentation or the link pinned in the workshop Teams channel.

> **Workshop certificate notice:** The AI-Native ODA Canvas Dashboard and deployed Orchestrator Agent use workshop-managed self-signed certificates. Your browser may display **Your connection is not private**, **Certificate not trusted**, or a similar warning. Confirm that the complete URL exactly matches the facilitator-provided or pinned workshop URL. Then select the browser's **Advanced** option and choose **Proceed**, **Continue**, or **Accept the risk**. Do not proceed if the hostname differs or the link did not come from the facilitator; ask the facilitator to verify it.

1. Open the AI-Native ODA Canvas Dashboard.
2. Open the **Infrastructure** tab.
3. Select **Components**.
4. If a namespace filter is displayed, select **components** or **All namespaces**.

This use case requires two APIs from the same deployed Component:

| Required interface | Dependent function | Component ID | Deployed Component resource |
|---|---|---|---|
| MCP | Service Qualification Management | `TMFC009` | `sq-1-servicequalificationmanagement` |
| A2A | Service Qualification Management | `TMFC009` | `sq-1-servicequalificationmanagement` |

### 3.2 Find the MCP and A2A dependencies under TMFC009

1. In the Components view, find **TMFC009 — Service Qualification Management**. Its deployed Component resource is `sq-1-servicequalificationmanagement`.
2. Open the Component.
3. Confirm that its Component metadata shows ID `TMFC009`.
4. Confirm that its **Status** is **Complete**.
5. Find **Exposed core APIs**.
6. Find the row whose **API Type** is **mcp** and whose **Name** is `servicequalificationmcp`.
7. Record:
   - exposed API **Name**: `servicequalificationmcp`;
   - **API Type**: `mcp`.
8. In the same **Exposed core APIs** section, find the row whose **API Type** is **a2a** and whose **Name** is `servicequalificationa2a`.
9. Record:
   - exposed API **Name**: `servicequalificationa2a`;
   - **API Type**: `a2a`.

Do not select the `TMF645` OpenAPI row. `TMF645` identifies the Service Qualification API contract; the dependencies required by this exercise are the MCP and A2A APIs named above.

If either required API is missing or the Component is not **Complete**, stop and notify the facilitator.

### 3.3 Confirm the dependency values

You should now have:

| Dependent function Component ID | Exposed API Name | API type |
|---|---|---|
| `TMFC009` | `servicequalificationmcp` | `mcp` |
| `TMFC009` | `servicequalificationa2a` | `a2a` |

Use each exposed API **Name** exactly as displayed in the AI-Native ODA Canvas Dashboard. Do not use the Component ID, API contract ID, implementation name, display label, or URL as the dependency name.

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
| `spec.componentMetadata.description` | Describe the Service Qualification Advisor and state that it uses Service Qualification MCP and A2A dependencies. |
| `spec.componentMetadata.functionalBlock` | Replace `replace-with-functional-block` with `Production`. |
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
      - name: servicequalificationmcp
        specification:
          - apiType: mcp
      - name: servicequalificationa2a
        specification:
          - apiType: a2a
```

Apply these rules:

1. Enter both dependencies exactly as shown.
2. Keep the dependency names lowercase and unchanged; matching is case-sensitive.
3. Use `mcp` for `servicequalificationmcp`.
4. Use `a2a` for `servicequalificationa2a`.
5. Use one `dependentAPIs` entry for each exposed API.
6. Do not add a URL or credential to either dependency.
7. Do not declare the `TMF645` OpenAPI interface.

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
- [ ] The description identifies the Service Qualification Advisor use case.
- [ ] `spec.componentMetadata.functionalBlock` is set to `Production`.
- [ ] Owner and maintainer names have been updated.
- [ ] `servicequalificationmcp` is declared with `apiType: mcp`.
- [ ] `servicequalificationa2a` is declared with `apiType: a2a`.
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
5. Do not use the Service Qualification MCP, A2A, or OpenAPI URL as the Orchestrator Agent URL.

Before running the exercise, confirm that the Orchestrator Agent sidebar shows green indicators for:

- **Component complete**;
- **Component CR readable**;
- **AI Gateway reachable**;
- **Dependencies resolved**;
- **Capabilities available**.

Confirm that both `servicequalificationmcp` and `servicequalificationa2a` appear in the dependency selector.

If the facilitator has corrected or redeployed a dependency, select **Refresh Dependent Services** once before continuing.

## 6. Discover the available capabilities

Keep the dependency selection set to **Auto** and run:

> What capabilities are provided by this component?

Expected result:

- `servicequalificationmcp` exposes eight Service Qualification MCP tools;
- `servicequalificationa2a` exposes one Service Qualification A2A skill;
- the Component reports nine capabilities across two dependencies.

Run:

> What MCP tools are available?

Expected result: the response lists these eight tools:

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

Expected result: the response lists **Service Qualification Query**, skill ID `service-qualification-query`, under `servicequalificationa2a`.

Run:

> Retrieve the agent card for servicequalificationa2a

Expected result: the Agent Card identifies the Service Qualification agent and its `service-qualification-query` skill.

## 7. Test direct MCP access

Select `servicequalificationmcp` in the dependency selector.

### 7.1 List every seeded qualification result

Run:

> Invoke query_service_qualification_get to list every qualification record.

Expected result: three records are returned—one qualified, one unqualified with an alternative, and one pending. The status line identifies `servicequalificationmcp`, API type `mcp`, and capability `query_service_qualification_get`.

### 7.2 Retrieve the qualified outcome

Run:

> Invoke query_service_qualification_get with external_id WS-QUAL-1001 and explain the qualification result.

Expected result: 1 Gbps Fiber Access at **Accelerate Asia Primary Site** is qualified and its task state is `done`.

### 7.3 Retrieve the unqualified outcome and alternative

Run:

> Invoke query_service_qualification_get with external_id WS-QUAL-1002 and explain the outcome, reason and available alternative.

Expected result: 1 Gbps Fiber Access at **Accelerate Asia Secondary Site** is unqualified because access capacity is limited to 100 Mbps; **100 Mbps Fiber Access** is presented as the alternative.

### 7.4 Retrieve the pending request

Run:

> Invoke query_service_qualification_get with external_id WS-QUAL-1003 and explain its current state.

Expected result: the 500 Mbps request at Accelerate Asia Primary Site has a pending qualification outcome and task state `inProgress`.

Use `external_id` for the stable workshop IDs `WS-QUAL-1001`, `WS-QUAL-1002`, and `WS-QUAL-1003`.

## 8. Compare A2A access

Select `servicequalificationa2a` in the dependency selector.

### 8.1 List qualification results through A2A

Run:

> Invoke service-qualification-query to list every available service qualification result.

Expected result: the response contains the same three seeded outcomes—qualified, unqualified with an alternative, and pending. The status line identifies `servicequalificationa2a`, API type `a2a`, and capability `service-qualification-query`.

### 8.2 Retrieve one qualification result through A2A

Run:

> Invoke service-qualification-query to show and explain qualification result WS-QUAL-1002.

Expected result: the response explains the unqualified outcome, the 100 Mbps capacity reason, and the 100 Mbps Fiber Access alternative.

## 9. Run the unsupported-domain control

With either Service Qualification dependency selected, run:

> Activate the qualified service on the network.

Expected result: the request is rejected as unsupported because service activation is outside this Component's declared Service Qualification domain.

## 10. Compare the two interfaces

Record your observations:

| Observation | Direct MCP | Service Qualification A2A |
|---|---|---|
| Dependency | `servicequalificationmcp` | `servicequalificationa2a` |
| API type | MCP | A2A |
| Interface used | Service Qualification MCP tool | Service Qualification agent skill |
| Seeded outcomes returned | Record your result | Record your result |
| WS-QUAL-1002 explanation | Record your result | Record your result |

Discuss:

1. Did both interfaces return the same seeded outcomes?
2. Which Component owns both exposed APIs?
3. Which exposed API Name and API type did your Component declare for each interface?
4. What information in the response confirms which interface was used?

## 11. Exercise completion checklist

- [ ] The AI-Native ODA Canvas Dashboard URL was obtained from the facilitator or pinned Teams message.
- [ ] `TMFC009 — Service Qualification Management` was used to find both dependencies.
- [ ] The dependent Component reports **Complete** in the AI-Native ODA Canvas Dashboard.
- [ ] The template `component.yaml` was downloaded from this repository.
- [ ] All required team-owned fields were updated.
- [ ] `spec.componentMetadata.functionalBlock` is set to `Production`.
- [ ] `servicequalificationmcp` and `servicequalificationa2a` are declared with the correct API types.
- [ ] The completed `component.yaml` was submitted to the facilitator.
- [ ] The deployed Orchestrator Agent URL was obtained from the facilitator or the `orch-agent-ui` row.
- [ ] All five readiness indicators are green.
- [ ] Eight MCP tools and one A2A skill are discovered.
- [ ] All three seeded qualification outcomes are returned.
- [ ] `WS-QUAL-1002` returns the capacity reason and 100 Mbps alternative through both interfaces.
- [ ] Stable workshop IDs are supplied as `external_id` for direct MCP queries.
- [ ] The activation control query is rejected as unsupported.
- [ ] The team can explain the two dependency declarations.

## 12. Troubleshooting

| Symptom | Action |
|---|---|
| The AI-Native ODA Canvas Dashboard cannot be opened | Use the AI-Native ODA Canvas Dashboard URL shown by the facilitator or pinned in the workshop Teams channel. If it still fails, notify the facilitator. |
| The browser reports that the certificate is not trusted | Confirm that the complete hostname matches the facilitator-provided AI-Native ODA Canvas Dashboard or Orchestrator Agent URL. Use **Advanced** and proceed only for that verified workshop URL. |
| `TMFC009` or an expected API is missing | Confirm the namespace filter, then ask the facilitator to verify the Service Qualification Management Component. |
| A dependency is unresolved | Confirm both exact dependency names and API types in `component.yaml`, then ask the facilitator to verify the dependent Component. |
| The team's Orchestrator Agent URL is unknown | Ask the facilitator for the team-specific URL or locate `orch-agent-ui` under the team's Component in the AI-Native ODA Canvas Dashboard. |
| MCP tools or the A2A skill are unavailable | Select **Refresh Dependent Services** once; if the problem remains, provide the visible error to the facilitator. |
| A stable identifier query returns no record | Confirm that the complete `WS-QUAL-100x` value was supplied and that direct MCP requests use `external_id`. |
| A follow-up such as “Explain that result” is unsupported | Repeat the complete request and include the qualification external ID explicitly. |
| The two results are difficult to compare | Request the same record and fields through each explicitly selected dependency. |
