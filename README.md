# helm-charts
helm-charts

### Exemplo de README.md para `values.yaml`

```markdown
# Configurações do Helm Chart

Este documento detalha as configurações disponíveis no `values.yaml` para o Helm Chart. 

## Valores Globais

```yaml
global:
  appName: mfe-analyzes
  namespace: qstudio
  image:
    repository: pmspool.meudominio.com/docker-local/sandbox/frontends/mfe-analyzes
    tag: d1.0.0-20230310-122040
    pullPolicy: Always
```

Estes valores são usados em várias partes do chart e ajudam a minimizar a repetição.

## Deployment do Kubernetes

Configurações específicas para o Deployment do Kubernetes.

```yaml
deployment:
  replicaCount: 1
  image:
    repository: {{ .Values.global.image.repository }}
    tag: {{ .Values.global.image.tag }}
    pullPolicy: {{ .Values.global.image.pullPolicy }}
  resources:
    limits:
      cpu: 500m
      memory: 1Gi
    requests:
      cpu: 100m
      memory: 256Mi
  podLabels:
    app: {{ .Values.global.appName }}
  podAnnotations:
    example-annotation: "valor"
  securityContext: 
    runAsUser: 1000
  livenessProbe:
    httpGet:
      path: /health
      port: http
    initialDelaySeconds: 10
    periodSeconds: 5
  readinessProbe:
    httpGet:
      path: /ready
      port: http
    initialDelaySeconds: 5
    periodSeconds: 5
  nodeSelector:
    disktype: ssd
  affinity:
    podAntiAffinity:
      # Exemplo de afinidade
  tolerations:
    - key: "key"
      operator: "Exists"
      effect: "NoExecute"
      tolerationSeconds: 6000
  imagePullSecrets:
    - name: jfrog-registry
  volumeMounts:
    - name: config-volume
      mountPath: /etc/config
  volumes:
    - name: config-volume
      configMap:
        name: my-config-map
```

## Configurações do Kubernetes Service

```yaml
service:
  type: NodePort
  port: 80
  targetPort: http
  protocol: TCP
  name: http
```

Define o tipo de serviço, portas e protocolo.

## Istio VirtualService

Configurações para o Istio VirtualService.

```yaml
virtualService:
  name: "custom-virtual-service"
  namespace: "default"
  gateways: []
  hosts: []
  http:
    - match: []
      rewrite: {}
      route: []
      corsPolicy: {}
  tls: []
  tcp: []
  exportTo: ["*"]
  # Configurações específicas do seu exemplo
  specific:
    name: "filtros-vs-ms"
    gateways: ["API-gateway"]
    hosts: ["api-qstudio-dev.meudominio.com"]
    http:
      - name: "filtros-ms"
        match:
          - uri:
              prefix: "/filtros-ms/"
        rewrite:
          uri: "/"
        route:
          - destination:
              host: "filtros-ms"
              port:
                number: 8080
        corsPolicy:
          allowCredentials: true
          allowHeaders: ["*"]
          allowMethods: ["POST", "OPTIONS", "GET", "PUT", "DELETE"]
          allowOrigins:
            - exact: "https://qstudio-dev.meudominio.com"
            - exact: "http://localhost:9000"
          exposeHeaders: ["Content-Length", "Content-Range"]
          maxAge: "10m"

```

Estas configurações incluem regras de roteamento e políticas CORS.
```

