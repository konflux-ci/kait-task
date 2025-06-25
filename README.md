# kait-task
A Tekton Task to call out to compatible AI agents to triage Konflux CI Failures

A `Role` and `RoleBinding` to the `ServiceAccount` used is required to allow getting taskrun logs:

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  name: ai-triager
rules:
- apiGroups: [""]
  resources: ["pods"]
  verbs: ["get", "list"]
- apiGroups: [""]
  resources: ["pods/log"]
  verbs: ["get"]
---
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: ai-triager
subjects:
- kind: ServiceAccount
  name: appstudio-pipeline
roleRef:
  kind: Role
  name: ai-triager
  apiGroup: rbac.authorization.k8s.io
```

A secret is required to enabled `comment_on_change_request`:

```
apiVersion: v1
kind: Secret
metadata:
  name: ai-triager
stringData:
  tangerine_api_url: ""
  tangerine_api_token: ""
  git_provider_api_token: ""
  git_provider_api_url: ""
```
---
## Recommended prompt pattern for applying PoC KAIT to your project.
Prompt assumes the user has collected error logs from failed Konflux run.

```
prompt_summarize_template: |
  Take the provided konflux log and create a list of the errors contained in it
  <error_log>
```

__In the event there is more than one error provided, use the following prompt.__
```
prompt_clarify_template: |
  Which error was the root cause?
```

__In the event no explanation has been provided, use the following prompt.__
```
prompt_solution_template: |
  How do I resolve this error?  <-- results may vary
```
