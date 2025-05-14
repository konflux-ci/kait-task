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
