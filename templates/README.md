# Participant Component template

Use `component.yaml` to design your team's AI-Native ODA Component. The facilitator will deploy the reviewed file and provide the Orchestrator Agent URL.

## What your team edits

1. Replace the team Component ID, Component name, description, owner name and maintainer name. Leave the supplied placeholder email values unchanged.
2. Replace `dependentAPIs` with the exact API names and types in your assigned use-case guide.
3. Replace `functionalBlock` with the exact value in your assigned use-case guide: `CoreCommerce` or `Production`.
4. Within participant-declared `dependentAPIs`, use only `mcp` or `a2a`. Names and values are case-sensitive.
5. Do not add dependency URLs; the AI-Native ODA Canvas resolves them.

## What remains facilitator-managed

Do not redesign the exposed UI, implementation names, paths, ports, observability interface, gateway configuration or security function. The facilitator will replace the remaining deployment placeholders after reviewing your submission.

Submit only the completed `component.yaml`. Do not deploy it directly unless the facilitator asks you to do so.
