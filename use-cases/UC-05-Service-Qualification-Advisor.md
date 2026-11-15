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

## 3. Identify the dependent functions in the Canvas Portal

### 3.1 Open the Canvas Portal

Obtain the **Canvas Portal URL** from the facilitator's presentation or the link pinned in the workshop Teams channel.

> **Workshop URL notice:** The Canvas Portal uses plain `http://`. Type the complete `http://` URL from the facilitator or the workshop Teams channel; if your browser changes it to `https://`, the page does not load. The browser may mark the portal **Not secure**, or warn that the site does not support a secure connection and offer **Continue to site**; both are expected for the verified portal URL. Your Orchestrator Agent uses `https://` with a workshop-managed self-signed certificate, so the browser may display **Your connection is not private**, **Certificate not trusted**, or a similar warning. Confirm that the hostname matches the URL opened from your own `orch-agent-ui` row under **APIS → Exposed APIs** in the Canvas Portal, then select **Advanced** and choose **Proceed**, **Continue**, or **Accept the risk**. Do not proceed if the hostname differs; ask the facilitator to verify it.

1. Open the Canvas Portal and sign in with your team's credentials from the facilitator. On first sign-in you are asked to set a new password.
2. In the left navigation, under **COMPONENTS**, select **Inventory**.
3. If a namespace filter is displayed, select **components** or all namespaces.

This use case requires two APIs from the same deployed Component:

| Required interface | Dependent function | Component ID | Deployed Component resource |
|---|---|---|---|
| MCP | Service Qualification Management | `TMFC009` | `sq-1-servicequalificationmanagement` |
| A2A | Service Qualification Management | `TMFC009` | `sq-1-servicequalificationmanagement` |

### 3.2 Find the MCP and A2A dependencies under TMFC009

1. In **Components → Inventory**, find **TMFC009 — Service Qualification Management**. Its deployed Component resource is `sq-1-servicequalificationmanagement`.
2. Open the Component by selecting its name.
3. Open the **Raw** tab and confirm that `spec.componentMetadata.id` is `TMFC009`.
4. On the **Overview** tab, confirm that **Status** shows **Complete**.
5. Open the **APIs** tab and find the **Core Function** group.
6. Find the API named `servicequalificationmcp`. The **APIs** tab shows only the API name. To confirm its type, stay in the same browser tab: in the left navigation, under **APIS**, select **Exposed APIs**, set the **All API Types** filter to **MCP**, and type `servicequalificationmcp` in the search box. Check that the row `sq-1-servicequalificationmanagement-servicequalificationmcp` is listed, then return to **COMPONENTS → Inventory**.
7. Record:
   - exposed API **Name**: `servicequalificationmcp`;
   - **API Type**: `mcp`.
8. In the same **Core Function** group, find the API named `servicequalificationa2a`. The **APIs** tab shows only the API name. To confirm its type, stay in the same browser tab: in the left navigation, under **APIS**, select **Exposed APIs**, set the **All API Types** filter to **A2A**, and type `servicequalificationa2a` in the search box. Check that the row `sq-1-servicequalificationmanagement-servicequalificationa2a` is listed, then return to **COMPONENTS → Inventory**.
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

Use each exposed API **Name** exactly as displayed in the Canvas Portal. Do not use the Component ID, API contract ID, implementation name, display label, or URL as the dependency name.

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
| `component.name` | A short lowercase name for your Component, for example `qualification-advisor`. Use only letters, digits and hyphens. Your deployed Component is named `<release name>-<component.name>`, for example `team01-qualification-advisor`. |
| `component.id` | The unique team Component ID assigned or approved by the facilitator, for example `TEAM01-UC05`. |
| `component.description` | Describe the Service Qualification Advisor and the dependencies it uses. |
| `component.functionalBlock` | `Production` |
| `component.maintainer.name` | The team or maintainer name agreed during the exercise. |
| `component.owner.name` | The team or owner name agreed during the exercise. |
| `component.dependentAPIs` | Replace the placeholder entry with both dependency entries shown in section 4.3. |

Leave `component.version` and `component.publicationDate` unchanged unless the facilitator instructs otherwise.

### 4.3 Set dependentAPIs

Replace the complete placeholder `dependentAPIs` list inside the `component` block with:

```yaml
  dependentAPIs:
    - name: servicequalificationmcp
      apiType: mcp
    - name: servicequalificationa2a
      apiType: a2a
```

Apply these rules:

1. Enter both dependencies exactly as shown.
2. Keep the dependency names lowercase and unchanged; matching is case-sensitive.
3. Use `mcp` as the `apiType` of `servicequalificationmcp`.
4. Use `a2a` as the `apiType` of `servicequalificationa2a`.
5. Use one `dependentAPIs` entry for each exposed API.
6. Do not add a URL or credential to any dependency.
7. Do not declare the `TMF645` OpenAPI interface.

Your completed values should look like this, with your own team values:

```yaml
component:
  name: qualification-advisor
  id: TEAM01-UC05
  version: "0.1.0"
  description: Service Qualification Advisor using the Service Qualification MCP and A2A dependencies
  functionalBlock: Production
  publicationDate: "2026-07-23T00:00:00Z"
  maintainer:
    name: Team 01
    email: workshop@example.invalid
  owner:
    name: Team 01
    email: workshop@example.invalid
  dependentAPIs:
    - name: servicequalificationmcp
      apiType: mcp
    - name: servicequalificationa2a
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
- [ ] The description identifies the Service Qualification Advisor use case.
- [ ] `component.functionalBlock` is set to `Production`.
- [ ] Owner and maintainer names have been updated.
- [ ] No value starting with `replace-with` remains.
- [ ] `servicequalificationmcp` is declared with `apiType: mcp`.
- [ ] `servicequalificationa2a` is declared with `apiType: a2a`.
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

1. Under **COMPONENTS**, select **Inventory** and find your Component in the **Outside TMF Catalog** section, for example `team01-qualification-advisor`. Team Components are listed there because they are not TMF catalogue Components.
2. Open it and confirm on the **Overview** tab that **Status** shows **Complete**. This can take a minute after installation.
3. Open the **Raw** tab and confirm that `spec.coreFunction.dependentAPIs` contains exactly the dependencies from section 4.3.
4. Under **APIs**, select **Dependent APIs**, find the entries belonging to your Component, and confirm that each is **Resolved** (ready).

To correct your values after installation, use **COMPONENTS → Lifecycle → Upgrade**. Select your own release. The repository and chart are already fixed to `workshop` and `canvas-orch-agent`. The editor opens with your deployed values. Edit them and select **Upgrade Release**. Never upgrade or uninstall another team's release.

## 5. Open the deployed Orchestrator Agent

1. In the left navigation, under **APIS**, select **Exposed APIs**.
2. In the search box, type your team's release name, for example `team01`.
3. Find the row named `<release name>-<component.name>-orch-agent-ui`, for example `team01-qualification-advisor-orch-agent-ui`, and confirm that its **Status** is **LIVE**.
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

- [ ] The Canvas Portal URL was obtained from the facilitator or pinned Teams message.
- [ ] `TMFC009 — Service Qualification Management` was used to find both dependencies.
- [ ] The dependent Component reports **Complete** in the Canvas Portal.
- [ ] The `canvas-orch-agent` chart from the `workshop` repository was installed as the team's release in namespace `components`.
- [ ] All required team-owned fields were updated.
- [ ] `component.functionalBlock` is set to `Production`.
- [ ] `servicequalificationmcp` and `servicequalificationa2a` are declared with the correct API types.
- [ ] Every declared dependency is **Resolved** under **APIs → Dependent APIs**.
- [ ] The Orchestrator Agent was opened with **Test API endpoint** on the team's own `orch-agent-ui` row under **APIS → Exposed APIs**.
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
| The installation is rejected | Follow the correction table in section 4.5. |
| The Component does not reach **Complete** | Open its **Events** tab, then ask the facilitator. Do not reinstall under a different release name. |
| The Canvas Portal cannot be opened | Use the Canvas Portal URL shown by the facilitator or pinned in the workshop Teams channel. If it still fails, notify the facilitator. |
| The Canvas Portal page does not load or keeps loading | Type the complete `http://` portal URL. If the browser switches to `https://`, allow the HTTP connection for that verified URL (for example **Continue to site**). |
| The browser reports that the Orchestrator Agent certificate is not trusted | Confirm that the hostname matches the URL opened from your own `orch-agent-ui` row under **APIS → Exposed APIs**. Use **Advanced** and proceed only for that verified URL. |
| `TMFC009` or an expected API is missing | Confirm the namespace filter, then ask the facilitator to verify the Service Qualification Management Component. |
| A dependency is unresolved | Confirm both exact dependency names and API types in `component.dependentAPIs`, correct them with **Components → Lifecycle → Upgrade**, then ask the facilitator to verify the dependent Component if the problem remains. |
| The team's Orchestrator Agent URL is unknown | Under **APIS → Exposed APIs**, search for your release name and select **Test API endpoint** on your `orch-agent-ui` row. If the row is missing, ask the facilitator. |
| MCP tools or the A2A skill are unavailable | Select **Refresh Dependent Services** once; if the problem remains, provide the visible error to the facilitator. |
| A stable identifier query returns no record | Confirm that the complete `WS-QUAL-100x` value was supplied and that direct MCP requests use `external_id`. |
| A follow-up such as “Explain that result” is unsupported | Repeat the complete request and include the qualification external ID explicitly. |
| The two results are difficult to compare | Request the same record and fields through each explicitly selected dependency. |

## 12. Remove your release

Remove your release only when the facilitator announces that the exercise is over. Removal deletes your Component, its Orchestrator Agent and its dependency registrations; the pre-deployed dependency Components are not affected.

1. In the Canvas Portal, under **COMPONENTS**, select **Lifecycle**.
2. Select **Uninstall**. The **Uninstall Deployed Components** dialog lists the releases in the `components` namespace.
3. Select **only your own release**, for example `team01`. Never select another team's release or a pre-deployed dependency Component.
4. Select **Review Removal**, check that the list contains only your release, and select **Uninstall Selected**.
5. Under **COMPONENTS → Inventory**, confirm that your Component, for example `team01-…`, is no longer listed. Removal can take a minute.
