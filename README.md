# azure-devops-CI-pipeline


apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: solarwinds
  namespace: argocd
  finalizers:
    - resources-finalizer.argocd.argoproj.io
spec:
  project: monitoring
  destination:
    server: https://kubernetes.default.svc
    namespace: monitoring
  source:
    repoURL: wfcuprimaryregistry.azurecr.io/helm
    chart: swo-k8s-collector
    targetRevision: 3.4.0
    helm:
      valuesObject:
        otel:
          endpoint: otel.collector.na-01.cloud.solarwinds.com:443
          image:
            repository: wfcuprimaryregistry.azurecr.io/tools/solarwinds/swi-opentelemetry-collector
            tag: latest
        init_images:
          swi_endpoint_check:
            repository: wfcuprimaryregistry.azurecr.io/tools/fullstorydev/grpcurl
            tag: v1.8.9
            pullPolicy: IfNotPresent
        cluster:
          name: aks-wfcu-nonprod-cus
          uid: 5d1a8a9f-a0f1-4984-aa28-c52296800707
        swoagent:
          enabled: true
          image:
            repository: wfcuprimaryregistry.azurecr.io/tools/solarwinds/swo-agent
            tag: v2.8.85

  syncPolicy:
    syncOptions:
      - ServerSideApply=true
      - CreateNamespace=true
    automated:
      prune: true
      selfHeal: true
