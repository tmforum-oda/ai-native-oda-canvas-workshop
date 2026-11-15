# Workshop Helm chart repository

This folder is a static Helm chart repository for the workshop Orchestrator
Agent chart, `canvas-orch-agent`. The Canvas Portal registers it as the
`workshop` repository and pins its chart, so teams install it from the portal
without adding a repository.

Repository URL (Helm reads `index.yaml` and the archives through
raw.githubusercontent.com; the index uses relative archive paths, so the same
files work from any branch):

```
https://raw.githubusercontent.com/tmforum-oda/ai-native-oda-canvas-workshop/main/charts
```

Command-line use:

```bash
helm repo add workshop https://raw.githubusercontent.com/tmforum-oda/ai-native-oda-canvas-workshop/main/charts
helm show values workshop/canvas-orch-agent
```

The chart's `component` values are placeholders (`replace-with-…`). The chart
refuses to install until every placeholder has been replaced with the values
from your use-case guide.

Do not edit these files by hand. They are generated from the chart source with
`thin-agent/scripts/package-chart.sh "" <this folder>`; bump the chart version
for every change so earlier versions stay installable.
