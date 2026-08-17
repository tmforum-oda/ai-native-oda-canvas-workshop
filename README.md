# AI-Native ODA Canvas Workshop

## Participant guide

Welcome to the Accelerate Asia AI-Native ODA Canvas workshop.

In this workshop, your team will design an AI-Native ODA Component for an assigned telecom use case. You will use the AI-Native ODA Canvas Dashboard to identify the ODA Components that provide the capabilities you need, declare their exposed MCP or A2A APIs as dependencies, and validate the resulting Component through a deployed Orchestrator Agent.

## 1. Your team objective

Your team will:

1. Select a use case or receive one from the facilitator.
2. Find the required dependency Components in the AI-Native ODA Canvas Dashboard.
3. Inspect the APIs exposed by those Components.
4. Complete the supplied `component.yaml` template with the correct Component metadata and dependent APIs.
5. Submit the completed file to the facilitator for deployment.
6. Use the deployed Orchestrator Agent to demonstrate the assigned use case.
7. Share your Component design and observations with the workshop.

You are not expected to build source code, a Helm chart, an agent runtime, or a CI/CD pipeline. Your deliverable is one reviewed `component.yaml` and a short working demonstration.

## 2. What is provided

The facilitator provides:

- a shared AI-Native ODA Canvas environment;
- pre-deployed ODA Components exposing the required MCP and A2A APIs;
- synthetic seed data for each use case;
- a pre-built Orchestrator Agent and chat interface;
- a participant Component template;
- deployment of your submitted Component;
- the URL of your team's deployed Orchestrator Agent.

Your team is responsible for:

- interpreting the assigned use case;
- identifying the correct dependency Components and exposed APIs;
- completing and reviewing the participant Component definition;
- running the supported and unsupported-domain queries in the use-case guide;
- presenting the working result and explaining the Component dependency design.

### Workshop certificate notice

The **AI-Native ODA Canvas Dashboard URL** and the deployed **Orchestrator Agent URL** use workshop-managed self-signed certificates. Your browser may display a certificate warning such as **Your connection is not private** or **Certificate not trusted**.

This warning is expected for the workshop URLs. Before proceeding, confirm that the complete URL exactly matches the URL provided by the facilitator or pinned in the workshop Teams channel. Then use the browser's **Advanced** option and choose **Proceed**, **Continue**, or **Accept the risk**, depending on the browser.

Do not proceed if the hostname is different from the facilitator-provided URL or if you reached the warning from an unverified link. Ask the facilitator to confirm the URL.

## 3. Repository contents

- [`templates/component.yaml`](templates/component.yaml) — the Component definition your team will complete.
- [`templates/README.md`](templates/README.md) — concise instructions for editing the template.
- [`use-cases/`](use-cases/) — the detailed participant exercises, queries, expected results, and troubleshooting guidance.

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

Use the catalogue below to identify the dependent functions required for your assigned use case. In the AI-Native ODA Canvas Dashboard, locate each dependent function by its **Component ID**, open the Component, and inspect its **Exposed core APIs**.

An identifier such as `TMFC005` identifies an ODA Component. An identifier such as `TMF637` identifies an API contract. The value used in `dependentAPIs.name` is the exposed API **Name** shown by the AI-Native ODA Canvas Dashboard; it is not the Component ID, API contract ID, implementation name, or URL.

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


## 6. Find your dependencies in the AI-Native ODA Canvas Dashboard

Before editing the template:

1. Open the AI-Native ODA Canvas Dashboard using the URL provided by the facilitator or pinned in the workshop Teams channel.
2. Open **Infrastructure** tab and select **Components**.
3. Find the owning Component by its Component ID, for example `TMFC005`.
4. Open the Component and confirm that its status is **Complete**.
5. Find the required entry under **Exposed core APIs**.
6. Check the exposed API **Name** and **API Type** against the dependency catalogue and your use-case guide.

Copy the exposed API **Name** exactly. Dependency names are case-sensitive. Do not copy an API URL into `component.yaml`; the AI-Native ODA Canvas resolves the deployed endpoint.

The individual use-case guide identifies the deployed Component resource and gives the complete navigation and validation steps for that scenario.

## 7. Complete your Component definition

Open [`templates/component.yaml`](templates/component.yaml), use GitHub's **Download raw file** action, and edit only the participant-owned values described in [`templates/README.md`](templates/README.md):

- your team's unique Component ID and name;
- a concise use-case description;
- team owner and maintainer names;
- the `functionalBlock` value specified by your use-case guide;
- the `dependentAPIs` entries specified by your use-case guide.

A dependent API entry has this form:

```yaml
    dependentAPIs:
      - name: productinventorymcp
        specification:
          - apiType: mcp
```

For a use case with two dependencies, add two entries. Use only the API names and types shown in the catalogue and confirmed in the AI-Native ODA Canvas Dashboard.

Do not change facilitator-managed workload, exposed UI, service, security, observability, health, credential, path, port, or implementation settings.

Before submission, confirm:

- [ ] The Component ID and name are unique to your team.
- [ ] The description clearly states the assigned use-case boundary.
- [ ] The `functionalBlock` value matches the assigned use-case guide.
- [ ] Every dependency was verified against its owning Component in the AI-Native ODA Canvas Dashboard.
- [ ] Every dependent API name and type is exact.
- [ ] No URL, token, password, certificate, or other credential is embedded.
- [ ] Only the participant-owned sections of the template were changed.
- [ ] The YAML structure and indentation are valid.

Submit the completed `component.yaml` through the channel specified by the facilitator. Do not deploy it directly unless asked.

## 8. Validate the deployed Component

After deployment, the facilitator will give your team its Orchestrator Agent URL.

1. Open the URL and confirm that the sidebar readiness indicators are green.
2. Confirm that the dependencies declared by your team are available.
3. Ask what capabilities are provided by the Component.
4. Run the supported queries in your assigned use-case guide.
5. If your use case uses both MCP and A2A, follow the guide to compare them.
6. Run an unrelated telecom-domain query to confirm that the Component remains within its declared use-case boundary. For example, an Inventory Component could be asked an unrelated Ordering or Qualification question.
7. Record the results required by the use-case guide and prepare your demonstration.

If a dependency is unavailable, first recheck its exact name and API type. If the definition is correct, ask the facilitator to inspect the deployed dependency Component.

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

- [ ] The Component reports **Complete** in the AI-Native ODA Canvas Dashboard.
- [ ] The Orchestrator Agent readiness indicators are green.
- [ ] The declared dependencies and capabilities are available.
- [ ] The supported use-case queries return the expected seeded information.
- [ ] The stable or API-generated identifier specified in the use-case guide retrieves the correct record.
- [ ] An unrelated telecom-domain query is rejected as unsupported.
- [ ] The team can explain why each dependent API belongs in its Component definition.
- [ ] The team can explain the difference between the owning Component ID, API contract ID, exposed API Name, and API type.

## 11. Getting help

If anything is unclear or does not match the guide:

1. Recheck the owning Component ID, exposed API Name, and API type in the AI-Native ODA Canvas Dashboard.
2. Compare your `dependentAPIs` entries with the assigned use-case guide.
3. Ask the facilitator or post the question in the workshop Teams channel.

Do not add URLs, credentials, or undeclared dependencies as a workaround.
