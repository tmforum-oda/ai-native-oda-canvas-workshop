# AI-Native ODA Canvas Workshop

## Participant guide

Welcome to the Innovate Asia AI-Native ODA Canvas workshop.

In this workshop, your team will design an AI-Native ODA Component for an assigned telecom use case. You will use the **Canvas Portal** to identify the ODA Components that provide the capabilities you need, declare their exposed MCP or A2A APIs as dependencies in your Component's values, deploy the Component yourself, and validate it through its Orchestrator Agent.

## 1. Your team objective

Your team will:

1. Select a use case or receive one from the facilitator.
2. Find the required dependency Components in the Canvas Portal.
3. Inspect the APIs exposed by those Components.
4. Install the `canvas-orch-agent` chart from the Canvas Portal, completing the `component` values with your Component metadata and dependent APIs.
5. Confirm that your Component is complete and its dependencies are resolved.
6. Use your deployed Orchestrator Agent to demonstrate the assigned use case.
7. Share your Component design and observations with the workshop.

You are not expected to build source code, a Helm chart, an agent runtime, or a CI/CD pipeline. Your deliverable is one reviewed `component` values block, a deployed Component, and a short working demonstration.

## 2. What is provided

The facilitator provides:

- a shared AI-Native ODA Canvas environment with the Canvas Portal;
- team sign-in credentials and a team **release name**, for example `team01`;
- pre-deployed ODA Components exposing the required MCP and A2A APIs;
- synthetic seed data for each use case;
- the `canvas-orch-agent` Helm chart in the portal's `workshop` repository. This is the pre-built Orchestrator Agent with its chat interface; its ODA Component is generated from your values. The portal installs only this chart, and every team deploys its own release of it.

Your team is responsible for:

- interpreting the assigned use case;
- identifying the correct dependency Components and exposed APIs;
- completing, reviewing and installing the `component` values;
- running the supported and unsupported-domain queries in the use-case guide;
- presenting the working result and explaining the Component dependency design.

### Workshop URL notice

**Canvas Portal.** The portal uses plain `http://`. Type the complete `http://` URL provided by the facilitator or pinned in the workshop Teams channel. If your browser changes it to `https://`, the page does not load. The browser may mark the portal **Not secure**, or warn that the site does not support a secure connection and offer **Continue to site**. Both are expected for the verified portal URL.

**Orchestrator Agent.** Your team's agent URL uses `https://` with a workshop-managed self-signed certificate. Your browser may display a warning such as **Your connection is not private** or **Certificate not trusted**. Confirm that the hostname matches the URL opened from your own `orch-agent-ui` row under **APIS → Exposed APIs** in the Canvas Portal. Then use the browser's **Advanced** option and choose **Proceed**, **Continue**, or **Accept the risk**, depending on the browser.

Do not proceed if the hostname differs from these URLs or if you reached the page from an unverified link. Ask the facilitator to confirm the URL.

## 3. Repository contents

- [`use-cases/`](use-cases/) — the detailed participant exercises, queries, expected results, and troubleshooting guidance (linked below).
- [`charts/`](charts/) — the Helm chart repository the Canvas Portal installs from. You do not download or edit anything here; the portal pre-fills the values you complete.

Read this main guide first. After your use case is assigned, follow its individual guide step by step.

## 4. Use cases

| Use case | Team scenario | Functional block | Detailed guide |
|---|---|---|---|
| UC-01 | Broadband Offer Explorer | `CoreCommerce` | [Open guide](use-cases/UC-01-Broadband-Offer-Explorer.md) |
| UC-02 | Installed Product Assistant | `CoreCommerce` | [Open guide](use-cases/UC-02-Installed-Product-Assistant.md) |
| UC-03 | Service Inventory Assistant | `Production` | [Open guide](use-cases/UC-03-Service-Inventory-Assistant.md) |
| UC-04 | Network Resource Locator | `Production` | [Open guide](use-cases/UC-04-Network-Resource-Locator.md) |
| UC-05 | Service Qualification Advisor | `Production` | [Open guide](use-cases/UC-05-Service-Qualification-Advisor.md) |
| UC-06 | Product Order Status Assistant | `CoreCommerce` | [Open guide](use-cases/UC-06-Product-Order-Status-Assistant.md) |

## 5. Dependency catalogue

Use the catalogue below to identify the dependent functions required for your assigned use case. In the Canvas Portal, locate each dependent function by its **Component ID**, open the Component, and inspect the APIs in its **Core Function** group.

An identifier such as `TMFC005` identifies an ODA Component. An identifier such as `TMF637` identifies an API contract. The value used in `dependentAPIs[].name` is the exposed API **Name** shown by the Canvas Portal; it is not the Component ID, API contract ID, implementation name, or URL.

| Use case | Dependent function | Dependent function Component ID | API contract reference | Exposed API Name | API type |
|---|---|---|---|---|---|
| UC-01 | Product Catalog Management | `TMFC001` | `TMF620` | `productcatalogmcp` | `mcp` |
| UC-01 | Product Catalog Agent | `productagent` | — | `productcataloga2a` | `a2a` |
| UC-02 | Product Inventory Management | `TMFC005` | `TMF637` | `productinventorymcp` | `mcp` |
| UC-03 | Service Inventory Management | `TMFC008` | `TMF638` | `serviceinventorymcp` | `mcp` |
| UC-04 | Resource Inventory Management | `TMFC012` | `TMF639` | `resourceinventorymcp` | `mcp` |
| UC-05 | Service Qualification Management | `TMFC009` | `TMF645` | `servicequalificationmcp` | `mcp` |
| UC-05 | Service Qualification Management | `TMFC009` | `TMF645` | `servicequalificationa2a` | `a2a` |
| UC-06 | Product Order Capture and Validation | `TMFC002` | `TMF622` | `productordera2a` | `a2a` |

## 6. Find your dependencies in the Canvas Portal

Before installing:

1. Open the Canvas Portal using the `http://` URL provided by the facilitator or pinned in the workshop Teams channel, and sign in with your team credentials. On first sign-in you are asked to set a new password.
2. In the left navigation, under **COMPONENTS**, select **Inventory**.
3. Find the owning Component, for example the Component with ID `TMFC005`. The use-case guide gives its deployed resource name.
4. Open the Component and confirm on the **Overview** tab that its status is **Complete**.
5. Open the **APIs** tab and find the required API **Name** in the **Core Function** group. The tab shows only the name, not the API type.
6. To confirm the type, stay in the same browser tab: in the left navigation, under **APIS**, select **Exposed APIs**, set the **All API Types** filter to **MCP** or **A2A**, and search for the API name. The row is named `<owning Component resource>-<API name>`, for example `pi-1-productinventory-productinventorymcp`. Check the name and type against the dependency catalogue and your use-case guide.

Copy the exposed API **Name** exactly. Dependency names are case-sensitive. Do not copy an API URL into your values; the AI-Native ODA Canvas resolves the deployed endpoint.

The individual use-case guide gives the complete navigation and validation steps for that scenario.

## 7. Configure and deploy your Component

1. Under **COMPONENTS**, select **Deploy**, then **Install Component**.
2. The repository (`workshop`), chart (`canvas-orch-agent`) and version are already filled in and cannot be changed. You can still browse the charts of linked repositories under **Deploy → Helm Repositories**.
3. Enter your team's **Release Name** (for example `team01`) and namespace `components`, then select **Next: Edit Values & Install →**.
4. The editor shows only the `component` block. Replace every `replace-with-…` placeholder with the participant-owned values described in the field rules below:
   - a short `component.name` and your team's `component.id`;
   - a concise use-case `description`;
   - team `owner` and `maintainer` names;
   - the `functionalBlock` value specified by your use-case guide;
   - the `dependentAPIs` entries specified by your use-case guide.

A dependent API entry has this form:

```yaml
  dependentAPIs:
    - name: productinventorymcp
      apiType: mcp
```

For a use case with two dependencies, add two entries. Use only the API names and types shown in the catalogue and confirmed in the Canvas Portal.

| Field | Rule |
|---|---|
| `component.name` | Lowercase letters, digits and hyphens. Your Component is named `<release name>-<component.name>`; together with the `-orchagent` suffix it must be at most 63 characters. |
| `component.id` | The team Component ID agreed with the facilitator, for example `TEAM01-UC02`. |
| `component.description` | One sentence naming your use case and its dependencies. |
| `component.functionalBlock` | The value from your use-case guide: `CoreCommerce` or `Production`. |
| `component.maintainer.name`, `component.owner.name` | Your team or person names. |
| `component.dependentAPIs` | One `name` + `apiType` entry per exposed API in your use-case guide. |

Leave `component.version` and `component.publicationDate` unchanged.

The chart turns each dependency into the ODA Component's `spec.coreFunction.dependentAPIs`, which you can inspect on your Component's **Raw** tab after installation:

```yaml
spec:
  coreFunction:
    dependentAPIs:
      - name: "productinventorymcp"
        specification:
          - apiType: "mcp"
```

The portal and chart reject:

- any value still starting with `replace-with`;
- any top-level key other than `component`;
- unknown keys inside `component`, for example a misspelled `dependentAPI`;
- `apiType` values other than `mcp` and `a2a`;
- dependency names with upper-case letters, spaces or URLs;
- the same dependency declared twice;
- a release name and `component.name` that together are too long.

Everything else (image, exposed UI, service, security, observability, health, credential, path, port and implementation settings) is managed by the facilitator. The portal rejects any value outside the `component` block.

Before selecting **Install Release**, confirm:

- [ ] The release name is the one assigned to your team, and the namespace is `components`.
- [ ] `component.name` and `component.id` are unique to your team.
- [ ] No value starting with `replace-with` remains.
- [ ] The description clearly states the assigned use-case boundary.
- [ ] The `functionalBlock` value matches the assigned use-case guide.
- [ ] Every dependency was verified against its owning Component in the Canvas Portal.
- [ ] Every dependent API name and type is exact.
- [ ] No URL, token, password, certificate, or other credential is embedded.
- [ ] The editor contains only the `component` block, with valid YAML indentation.

If the portal rejects the installation, the message names the field to correct. See the correction table in section 4.5 of your use-case guide.

To change your values later, use **COMPONENTS → Lifecycle → Upgrade** on **your own** release. Never upgrade or uninstall another team's release.

## 8. Validate the deployed Component

1. Under **COMPONENTS → Inventory**, open your Component (`<release name>-<component.name>`, for example `team01-installed-product-assistant`) in the **Outside TMF Catalog** section and confirm that its status is **Complete**.
2. Under **APIs → Dependent APIs**, confirm that every dependency declared by your team is **Resolved**.
3. Under **APIS → Exposed APIs**, search for your release name, find your `<release name>-<component.name>-orch-agent-ui` row, and select the **Test API endpoint** icon in its **Actions** column. Your Orchestrator Agent opens in a new browser tab.
4. Confirm that the agent's sidebar readiness indicators are green.
5. Ask what capabilities are provided by the Component.
6. Run the supported queries in your assigned use-case guide.
7. If your use case uses both MCP and A2A, follow the guide to compare them.
8. Run an unrelated telecom-domain query to confirm that the Component remains within its declared use-case boundary. For example, an Inventory Component could be asked an unrelated Ordering or Qualification question.
9. Record the results required by the use-case guide and prepare your demonstration.

If a dependency is unresolved, first recheck its exact name and API type in your values and correct them with **Lifecycle → Upgrade**. If the values are correct, ask the facilitator to inspect the deployed dependency Component.

## 9. The chat is single-turn

The Orchestrator Agent treats every submitted query as a new, independent request. Earlier messages may remain visible in the chat window, but they are not supplied as conversational context for the next query.

Make every query self-contained:

- name the domain entity you want to query;
- include the complete workshop identifier when retrieving a specific record;
- state the fields or explanation you want returned;
- do not rely on follow-ups such as “describe it,” “what is its status?” or “show the previous result.”

For example, use:

> Describe installed product WS-PROD-1001 and include its lifecycle state.

Do not use:

> What is its lifecycle state?

## 10. Completion checklist

Your team is ready to demonstrate when:

- [ ] Your release is installed and your Component reports **Complete** in the Canvas Portal.
- [ ] Every declared dependency is **Resolved** under **APIs → Dependent APIs**.
- [ ] The Orchestrator Agent readiness indicators are green.
- [ ] The declared dependencies and capabilities are available.
- [ ] The supported use-case queries return the expected seeded information.
- [ ] The stable or API-generated identifier specified in the use-case guide retrieves the correct record.
- [ ] An unrelated telecom-domain query is rejected as unsupported.
- [ ] The team can explain why each dependent API belongs in its Component definition.
- [ ] The team can explain how the `component` values became the ODA Component (see the **Raw** tab).
- [ ] The team can explain the difference between the owning Component ID, API contract ID, exposed API Name, and API type.

## 11. Getting help

If anything is unclear or does not match the guide:

1. Recheck the owning Component ID, exposed API Name, and API type in the Canvas Portal.
2. Compare your `dependentAPIs` entries with the assigned use-case guide.
3. Check your Component's **Events** tab in the Canvas Portal.
4. Ask the facilitator or post the question in the workshop Teams channel.

Do not add URLs, credentials, other values keys, or undeclared dependencies as a workaround.

## 12. Remove your release

When the facilitator announces that the exercise is over, remove your own release. This deletes your Component, its Orchestrator Agent and its dependency registrations; the pre-deployed dependency Components are not affected.

1. In the Canvas Portal, under **COMPONENTS**, select **Lifecycle**, then **Uninstall**.
2. In **Uninstall Deployed Components**, select **only your own release**, for example `team01`. Never select another team's release or a pre-deployed dependency Component.
3. Select **Review Removal**, check that the list contains only your release, and select **Uninstall Selected**.
4. Under **COMPONENTS → Inventory**, confirm that your Component is no longer listed. Removal can take a minute.
