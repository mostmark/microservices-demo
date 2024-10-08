# Microservices Demo Application

This is the [Online Boutique](https://github.com/GoogleCloudPlatform/microservices-demo/) microservices demo application modified to run on OpenShift with some customisation to render nice in the OpenShift graphical console (runtime icons and connection arrows has been added).

To deploy the application:

```
oc new-project ms-demo
oc apply -f https://raw.githubusercontent.com/mostmark/microservices-demo/main/application.yaml

```

![OpenShift Console](./images/ms-demo-screenshot1.png)

To clean up:

```
oc delete all --all -n ms-demo
oc delete project ms-demo

```

To deploy the application using OpenShift GitOps (Argo CD):

```yaml
oc apply -f - <<EOF
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: ms-demo
  namespace: openshift-gitops
spec:
  destination:
    name: ''
    namespace: ms-demo
    server: 'https://kubernetes.default.svc'
  source:
    path: kubernetes-manifests
    repoURL: 'https://github.com/mostmark/microservices-demo.git'
    targetRevision: HEAD
  project: default
  syncPolicy:
    automated:
      selfHeal: true
EOF

```

To clean up:

```
oc patch application ms-demo  -p '{"metadata": {"finalizers": ["resources-finalizer.argocd.argoproj.io"]}}' --type merge -n openshift-gitops
oc delete application ms-demo -n openshift-gitops

```

