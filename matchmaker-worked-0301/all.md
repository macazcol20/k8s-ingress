apiVersion: v1
kind: ServiceAccount
metadata:
  annotations:
    meta.helm.sh/release-name: opr
    meta.helm.sh/release-namespace: opr-develop
  creationTimestamp: "2024-01-16T20:14:50Z"
  labels:
    app.kubernetes.io/instance: opr
    app.kubernetes.io/managed-by: Helm
    app.kubernetes.io/name: matchmaker
    helm.sh/chart: reflect-common-chart-1.0.0
  name: matchmaker-sa
  namespace: opr-develop
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  annotations:
    meta.helm.sh/release-name: opr
    meta.helm.sh/release-namespace: opr-develop
  creationTimestamp: "2024-01-16T20:15:00Z"
  finalizers:
  - wrangler.cattle.io/auth-prov-v2-crb
  labels:
    app.kubernetes.io/instance: opr
    app.kubernetes.io/managed-by: Helm
    app.kubernetes.io/name: matchmaker
    helm.sh/chart: reflect-common-chart-1.0.0
  name: matchmaker-rb
  resourceVersion: "130594039"
  uid: 569fa7f8-e7a6-45ea-9cab-c1ca2635e5fd
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: matchmaker-clusterrole
subjects:
- kind: ServiceAccount
  name: matchmaker-sa
  namespace: opr-develop
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  annotations:
    meta.helm.sh/release-name: opr
    meta.helm.sh/release-namespace: opr-develop
  creationTimestamp: "2024-01-16T20:15:00Z"
  finalizers:
  - wrangler.cattle.io/auth-prov-v2-crb
  labels:
    app.kubernetes.io/instance: opr
    app.kubernetes.io/managed-by: Helm
    app.kubernetes.io/name: opr-reflect-common-chart
    helm.sh/chart: reflect-common-chart-1.0.0
  name: netcode-rb
  resourceVersion: "148928737"
  uid: e74b7ef1-530c-4a0a-b7a6-1f67ab9d3184
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: netcode-clusterrole
subjects:
- kind: ServiceAccount
  name: netcode-sa
  namespace: opr-develop
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  annotations:
    meta.helm.sh/release-name: opr
    meta.helm.sh/release-namespace: opr-develop
  creationTimestamp: "2024-01-16T20:14:59Z"
  finalizers:
  - wrangler.cattle.io/auth-prov-v2-crole
  labels:
    app.kubernetes.io/instance: opr
    app.kubernetes.io/managed-by: Helm
    app.kubernetes.io/name: matchmaker
    helm.sh/chart: reflect-common-chart-1.0.0
  name: matchmaker-clusterrole
  resourceVersion: "130594034"
  uid: e22886c0-242d-470a-9eab-6d3164de7a1e
rules:
- apiGroups:
  - ""
  - apps
  - batch
  resources:
  - services
  - jobs
  verbs:
  - create
  - get
  - watch
  - list
  - delete
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  annotations:
    meta.helm.sh/release-name: opr
    meta.helm.sh/release-namespace: opr-develop
  creationTimestamp: "2024-01-16T20:15:00Z"
  finalizers:
  - wrangler.cattle.io/auth-prov-v2-crole
  labels:
    app.kubernetes.io/instance: opr
    app.kubernetes.io/managed-by: Helm
    app.kubernetes.io/name: opr-reflect-common-chart
    helm.sh/chart: reflect-common-chart-1.0.0
  name: netcode-clusterrole
  resourceVersion: "130594036"
  uid: ffc08364-c0a2-4e2f-a4e1-97058a2104e7
rules:
- apiGroups:
  - ""
  resources:
  - services
  - nodes
  - secrets
  verbs:
  - get
  - watch
  - list
---
apiVersion: v1
data:
  ASPNETCORE_URLS: http://+:8090
  OTEL_EXPORTER_OTLP_ENDPOINT: http://apm-server-apm-http:8200
  OTEL_EXPORTER_OTLP_HEADERS: Authorization=Bearer 4113wNO7AFYO336Yn3vj2yvP
  OTEL_RESOURCE_ATTRIBUTES: deployment.environment=production
  OTEL_SERVICE_NAME: matchmaker
  REFLECT_COMPONENT: matchmaker
  REFLECT_ENVIRONMENT: Production
  REFLECTMULTIPLAYER_ClusterController__AllocationPool__PoolSize: '0'
  REFLECTMULTIPLAYER_ClusterController__KubeApi__CertsSecretName: matchmaker-reflect-tls
  REFLECTMULTIPLAYER_ClusterController__KubeApi__Images__Init: >-
    us-east1-docker.pkg.dev/unity-solutions-tyndall-prd/docker/reflect-netcode-init:develop-latest
  REFLECTMULTIPLAYER_ClusterController__KubeApi__Images__Netcode: >-
    us-east1-docker.pkg.dev/unity-solutions-tyndall-prd/docker/reflect-netcode:develop-latest
  REFLECTMULTIPLAYER_ClusterController__KubeApi__KubeNamespace: opr-develop
  REFLECTMULTIPLAYER_ClusterController__KubeApi__KubeNodepool: 'kubernetes.io/os : linux'
  REFLECTMULTIPLAYER_ClusterController__KubeApi__MatchMakerEndpoint: matchmaker
  REFLECTMULTIPLAYER_ClusterController__KubeApi__Namespace: default
  REFLECTMULTIPLAYER_ClusterController__KubeApi__NetcodeServerAddress: develop.s3gis.be
  REFLECTMULTIPLAYER_Mumble__OwnerManagerAclGroupName: OprOwnerManager
  # REFLECTMULTIPLAYER_Mumble__RestApiUrl: http://mumble-rest.opr-develop.svc.cluster.local:8082
  REFLECTMULTIPLAYER_ProjectServer__Address: http://project:10010
  REFLECTMULTIPLAYER_ProjectServer__HttpAddress: https://develop.s3gis.be
  REFLECTMULTIPLAYER_Prometheus__Enabled: 'true'
  # REFLECTMULTIPLAYER_Sentry__Environment: gcp-test
  REFLECTMULTIPLAYER_Serilog__MinimumLevel__Default: Debug
  URLS: http://+:8090
kind: ConfigMap
metadata:
  annotations:
    meta.helm.sh/release-name: opr
    meta.helm.sh/release-namespace: opr-develop
  creationTimestamp: '2024-01-16T20:14:56Z'
  labels:
    app.kubernetes.io/instance: opr
    app.kubernetes.io/managed-by: Helm
    app.kubernetes.io/name: matchmaker
    helm.sh/chart: reflect-common-chart-1.0.0
  managedFields:
    - apiVersion: v1
      fieldsType: FieldsV1
      fieldsV1:
        f:data:
          f:REFLECTMULTIPLAYER_Serilog__MinimumLevel__Default: {}
      manager: rancher
      operation: Update
      time: '2024-03-04T16:56:54Z'
    - apiVersion: v1
      fieldsType: FieldsV1
      fieldsV1:
        f:data:
          .: {}
          f:ASPNETCORE_URLS: {}
          f:OTEL_EXPORTER_OTLP_ENDPOINT: {}
          f:OTEL_EXPORTER_OTLP_HEADERS: {}
          f:OTEL_RESOURCE_ATTRIBUTES: {}
          f:OTEL_SERVICE_NAME: {}
          f:REFLECT_COMPONENT: {}
          f:REFLECT_ENVIRONMENT: {}
          f:REFLECTMULTIPLAYER_ClusterController__AllocationPool__PoolSize: {}
          f:REFLECTMULTIPLAYER_ClusterController__KubeApi__CertsSecretName: {}
          f:REFLECTMULTIPLAYER_ClusterController__KubeApi__Images__Init: {}
          f:REFLECTMULTIPLAYER_ClusterController__KubeApi__Images__Netcode: {}
          f:REFLECTMULTIPLAYER_ClusterController__KubeApi__KubeNamespace: {}
          f:REFLECTMULTIPLAYER_ClusterController__KubeApi__KubeNodepool: {}
          f:REFLECTMULTIPLAYER_ClusterController__KubeApi__MatchMakerEndpoint: {}
          f:REFLECTMULTIPLAYER_ClusterController__KubeApi__Namespace: {}
          f:REFLECTMULTIPLAYER_ClusterController__KubeApi__NetcodeServerAddress: {}
          f:REFLECTMULTIPLAYER_Mumble__OwnerManagerAclGroupName: {}
          f:REFLECTMULTIPLAYER_Mumble__RestApiUrl: {}
          f:REFLECTMULTIPLAYER_ProjectServer__Address: {}
          f:REFLECTMULTIPLAYER_ProjectServer__HttpAddress: {}
          f:REFLECTMULTIPLAYER_Prometheus__Enabled: {}
          f:REFLECTMULTIPLAYER_Sentry__Environment: {}
          f:URLS: {}
        f:metadata:
          f:annotations:
            .: {}
            f:meta.helm.sh/release-name: {}
            f:meta.helm.sh/release-namespace: {}
          f:labels:
            .: {}
            f:app.kubernetes.io/instance: {}
            f:app.kubernetes.io/managed-by: {}
            f:app.kubernetes.io/name: {}
            f:helm.sh/chart: {}
      manager: helm
      operation: Update
      time: '2024-03-28T16:26:49Z'
  name: matchmaker-config
  namespace: opr-develop
  resourceVersion: '193319503'
  uid: d733c8bc-7129-480d-ae7a-1fdbeb6566b8
---
apiVersion: v1
data:
  REFLECTMULTIPLAYER_ConnectionStrings__MatchmakerDatabase: >-
    SG9zdD1vcHItcG9zdGdyZXNxbDtEYXRhYmFzZT1NYXRjaG1ha2VyO1VzZXJuYW1lPW1hdGNobWFrZXJfc2VydmljZTtQYXNzd29yZD1yZWZsZWN0
  REFLECTMULTIPLAYER_Mumble__RestApiPassword: bXVybXVyLXJlc3Qtc2VjcmV0LWtleQ==
  REFLECTMULTIPLAYER_Mumble__RestApiUsername: cmVmbGVjdA==
kind: Secret
metadata:
  annotations:
    meta.helm.sh/release-name: opr
    meta.helm.sh/release-namespace: opr-develop
  creationTimestamp: '2024-01-16T20:14:52Z'
  labels:
    app.kubernetes.io/instance: opr
    app.kubernetes.io/managed-by: Helm
    app.kubernetes.io/name: matchmaker
    helm.sh/chart: reflect-common-chart-1.0.0
  managedFields:
    - apiVersion: v1
      fieldsType: FieldsV1
      fieldsV1:
        f:data:
          .: {}
          f:REFLECTMULTIPLAYER_ConnectionStrings__MatchmakerDatabase: {}
        f:metadata:
          f:annotations:
            .: {}
            f:meta.helm.sh/release-name: {}
            f:meta.helm.sh/release-namespace: {}
          f:labels:
            .: {}
            f:app.kubernetes.io/instance: {}
            f:app.kubernetes.io/managed-by: {}
            f:app.kubernetes.io/name: {}
            f:helm.sh/chart: {}
        f:type: {}
      manager: helm
      operation: Update
      time: '2024-01-16T20:14:52Z'
    - apiVersion: v1
      fieldsType: FieldsV1
      fieldsV1:
        f:data:
          f:REFLECTMULTIPLAYER_Mumble__RestApiPassword: {}
          f:REFLECTMULTIPLAYER_Mumble__RestApiUsername: {}
      manager: rancher
      operation: Update
      time: '2024-03-03T00:52:28Z'
  name: matchmaker-secrets
  namespace: opr-develop
---
apiVersion: v1
data:
  job.yml: |
    apiVersion: batch/v1
    kind: Job
    metadata:
      name: netcode-job-{{ Ident }}
      namespace: {{ Namespace }}
    spec:
      template:
        metadata:
          labels:
            app: netcode-job-{{ Ident }}
        spec:
          restartPolicy: Never
          nodeSelector:
            {{ NodeSelector }}
          serviceAccountName: netcode-sa
          volumes:
          - name: netcode-config
            emptyDir: {}
          initContainers:
            - name: netcode-init
              image: {{ InitImage }}
              imagePullPolicy: "Always"
              volumeMounts:
              - name: netcode-config
                mountPath: /etc/reflect
              env:
                  - name: REFLECT_NETCODE_NODE_NAME
                    valueFrom:
                      fieldRef:
                        fieldPath: spec.nodeName
                  - name: CERTS_SECRET_NAME
                    value: {{ CertsSecretName }}
                  - name: REFLECT_SENTRY_DSN
                    value: {{ SentryDSN }}
                  - name: NAMESPACE
                    value: {{ Namespace }}
          imagePullSecrets:
            - name: regcred-gcp
          containers:
            - name: netcode-server
              image: {{ NetcodeImage }}
              imagePullPolicy: "Always"
              ports:
                - containerPort: 30303
                  name: mlapi
                  protocol: UDP
              env:
                  - name: REFLECT_NETCODE_NODE_NAME
                    valueFrom:
                      fieldRef:
                        fieldPath: spec.nodeName
                  - name: REFLECT_NETCODE_POD_NAME
                    valueFrom:
                      fieldRef:
                        fieldPath: metadata.name
                  - name: MATCHMAKER_ENABLE
                    value: "true"
                  - name: MATCHMAKER_HOST
                    value: {{ MatchMakerEndpoint }}
                  - name: MATCHMAKER_PORT
                    value: "10042"
                  - name: REFLECT_NETCODE_IP
                    value: {{ NetcodeServerAddress }}
                  - name: REFLECT_SENTRY_DSN
                    value: {{ SentryDSN }}
              volumeMounts:
              - name: netcode-config
                mountPath: /etc/reflect
              resources:
                requests:
                  memory: "400Mi"
                  cpu: "2000m"
                limits:
                  memory: "800Mi"
                  cpu: "2000m"
  service.yml: |
    apiVersion: v1
    kind: Service
    metadata:
      name: netcode-svc-{{ Ident }}
      namespace: {{ Namespace }}
      labels:
        app: netcode-svc-{{ Ident }}
        reflectComponent: netcode
    spec:
      type: NodePort
      selector:
        app: netcode-job-{{ Ident }}
      ports:
        - port: 30303
          targetPort: mlapi
          name: mlapi
          protocol: UDP
kind: ConfigMap
metadata:
  annotations:
    meta.helm.sh/release-name: opr
    meta.helm.sh/release-namespace: opr-develop
  creationTimestamp: '2024-01-15T18:42:03Z'
  labels:
    app.kubernetes.io/managed-by: Helm
  managedFields:
    - apiVersion: v1
      fieldsType: FieldsV1
      fieldsV1:
        f:data:
          .: {}
          f:job.yml: {}
          f:service.yml: {}
        f:metadata:
          f:annotations:
            .: {}
            f:meta.helm.sh/release-name: {}
            f:meta.helm.sh/release-namespace: {}
          f:labels:
            .: {}
            f:app.kubernetes.io/managed-by: {}
      manager: helm
      operation: Update
      time: '2024-02-07T16:55:51Z'
  name: netcode-manifests
  namespace: opr-develop
  resourceVersion: '149706419'
  uid: 077fdaa9-3f87-4f55-a7f7-8b630ddffce0
---
apiVersion: apps/v1
kind: Deployment
metadata:
  annotations:
    deployment.kubernetes.io/revision: '18'
    meta.helm.sh/release-name: opr
    meta.helm.sh/release-namespace: opr-develop
  creationTimestamp: '2024-01-19T00:35:28Z'
  generation: 40
  labels:
    app.kubernetes.io/instance: opr
    app.kubernetes.io/managed-by: Helm
    app.kubernetes.io/name: matchmaker
    helm.sh/chart: reflect-common-chart-1.0.0
  managedFields:
    - apiVersion: apps/v1
      fieldsType: FieldsV1
      fieldsV1:
        f:metadata:
          f:annotations:
            .: {}
            f:meta.helm.sh/release-name: {}
            f:meta.helm.sh/release-namespace: {}
          f:labels:
            .: {}
            f:app.kubernetes.io/instance: {}
            f:app.kubernetes.io/managed-by: {}
            f:app.kubernetes.io/name: {}
            f:helm.sh/chart: {}
        f:spec:
          f:progressDeadlineSeconds: {}
          f:replicas: {}
          f:revisionHistoryLimit: {}
          f:selector: {}
          f:strategy:
            f:rollingUpdate:
              .: {}
              f:maxSurge: {}
              f:maxUnavailable: {}
            f:type: {}
          f:template:
            f:metadata:
              f:annotations:
                .: {}
                f:checksum/config: {}
                f:checksum/externalSecrets: {}
                f:checksum/secrets: {}
              f:labels:
                .: {}
                f:app.kubernetes.io/instance: {}
                f:app.kubernetes.io/name: {}
            f:spec:
              f:containers:
                k:{"name":"matchmaker"}:
                  .: {}
                  f:env:
                    .: {}
                    k:{"name":"REFLECT_POD_NAME"}:
                      .: {}
                      f:name: {}
                      f:valueFrom:
                        .: {}
                        f:fieldRef: {}
                  f:envFrom: {}
                  f:imagePullPolicy: {}
                  f:livenessProbe:
                    .: {}
                    f:exec:
                      .: {}
                      f:command: {}
                    f:failureThreshold: {}
                    f:periodSeconds: {}
                    f:successThreshold: {}
                    f:timeoutSeconds: {}
                  f:name: {}
                  f:ports:
                    .: {}
                    k:{"containerPort":80,"protocol":"TCP"}:
                      .: {}
                      f:containerPort: {}
                      f:name: {}
                      f:protocol: {}
                    k:{"containerPort":1234,"protocol":"TCP"}:
                      .: {}
                      f:containerPort: {}
                      f:name: {}
                      f:protocol: {}
                    k:{"containerPort":10042,"protocol":"TCP"}:
                      .: {}
                      f:containerPort: {}
                      f:name: {}
                      f:protocol: {}
                  f:readinessProbe:
                    .: {}
                    f:exec:
                      .: {}
                      f:command: {}
                    f:failureThreshold: {}
                    f:periodSeconds: {}
                    f:successThreshold: {}
                    f:timeoutSeconds: {}
                  f:resources:
                    .: {}
                    f:limits:
                      .: {}
                      f:cpu: {}
                      f:memory: {}
                    f:requests:
                      .: {}
                      f:cpu: {}
                      f:memory: {}
                  f:securityContext: {}
                  f:terminationMessagePath: {}
                  f:terminationMessagePolicy: {}
                  f:volumeMounts:
                    .: {}
                    k:{"mountPath":"/app/netcode-manifests"}:
                      .: {}
                      f:mountPath: {}
                      f:name: {}
                      f:readOnly: {}
              f:dnsPolicy: {}
              f:imagePullSecrets:
                .: {}
                k:{"name":"regcred-gcp"}: {}
              f:restartPolicy: {}
              f:schedulerName: {}
              f:securityContext: {}
              f:serviceAccount: {}
              f:serviceAccountName: {}
              f:terminationGracePeriodSeconds: {}
              f:volumes:
                .: {}
                k:{"name":"cloudsql-credentials"}:
                  .: {}
                  f:name: {}
                  f:secret:
                    .: {}
                    f:defaultMode: {}
                    f:secretName: {}
                k:{"name":"netcode-manifests"}:
                  .: {}
                  f:configMap:
                    .: {}
                    f:defaultMode: {}
                    f:name: {}
                  f:name: {}
      manager: helm
      operation: Update
      time: '2024-03-28T16:26:58Z'
    - apiVersion: apps/v1
      fieldsType: FieldsV1
      fieldsV1:
        f:metadata:
          f:annotations:
            f:field.cattle.io/publicEndpoints: {}
        f:spec:
          f:template:
            f:metadata:
              f:namespace: {}
            f:spec:
              f:containers:
                k:{"name":"matchmaker"}:
                  f:image: {}
      manager: rancher
      operation: Update
      time: '2025-02-05T20:42:27Z'
    - apiVersion: apps/v1
      fieldsType: FieldsV1
      fieldsV1:
        f:spec:
          f:template:
            f:metadata:
              f:annotations:
                f:kubectl.kubernetes.io/restartedAt: {}
      manager: k9s
      operation: Update
      time: '2025-02-05T21:33:12Z'
    - apiVersion: apps/v1
      fieldsType: FieldsV1
      fieldsV1:
        f:metadata:
          f:annotations:
            f:deployment.kubernetes.io/revision: {}
        f:status:
          f:availableReplicas: {}
          f:conditions:
            .: {}
            k:{"type":"Available"}:
              .: {}
              f:lastTransitionTime: {}
              f:lastUpdateTime: {}
              f:message: {}
              f:reason: {}
              f:status: {}
              f:type: {}
            k:{"type":"Progressing"}:
              .: {}
              f:lastTransitionTime: {}
              f:lastUpdateTime: {}
              f:message: {}
              f:reason: {}
              f:status: {}
              f:type: {}
          f:observedGeneration: {}
          f:readyReplicas: {}
          f:replicas: {}
          f:updatedReplicas: {}
      manager: kube-controller-manager
      operation: Update
      subresource: status
      time: '2025-02-05T21:33:24Z'
  name: matchmaker
  namespace: opr-develop
  resourceVersion: '406710693'
  uid: 15d89297-9621-4272-8dcb-9d95778d869d
spec:
  progressDeadlineSeconds: 600
  replicas: 1
  revisionHistoryLimit: 10
  selector:
    matchLabels:
      app.kubernetes.io/instance: opr
      app.kubernetes.io/name: matchmaker
  strategy:
    rollingUpdate:
      maxSurge: 25%
      maxUnavailable: 25%
    type: RollingUpdate
  template:
    metadata:
      annotations:
        checksum/config: b8677dbde48ced43986ba512f074d0577e421b8f78d1d93a60e6211b18020f78
        checksum/externalSecrets: 5acf3ff77b4420677b5923071f303facaba7a9273a346284a667a275df325146
        checksum/secrets: 6bf08e4cae2f2379312d45da04f1eb2a7c08ba9fd7d3d96f27b734d14a2629bd
        kubectl.kubernetes.io/restartedAt: '2025-02-05T16:33:12-05:00'
      creationTimestamp: null
      labels:
        app.kubernetes.io/instance: opr
        app.kubernetes.io/name: matchmaker
      namespace: opr-develop
    spec:
      containers:
        - env:
            - name: REFLECT_POD_NAME
              valueFrom:
                fieldRef:
                  apiVersion: v1
                  fieldPath: metadata.name
          envFrom:
            - secretRef:
                name: matchmaker-secrets
            - configMapRef:
                name: matchmaker-config
          image: >-
            us-east1-docker.pkg.dev/unity-solutions-tyndall-prd/docker/reflect-matchmaker:develop-latest
          imagePullPolicy: Always
          livenessProbe:
            exec:
              command:
                - /bin/grpc_health_probe
                - '-addr=:10042'
            failureThreshold: 3
            periodSeconds: 10
            successThreshold: 1
            timeoutSeconds: 1
          name: matchmaker
          ports:
            - containerPort: 10042
              name: grpc
              protocol: TCP
            - containerPort: 80
              name: http
              protocol: TCP
            - containerPort: 1234
              name: prom-publisher
              protocol: TCP
          readinessProbe:
            exec:
              command:
                - /bin/grpc_health_probe
                - '-addr=:10042'
            failureThreshold: 3
            periodSeconds: 10
            successThreshold: 1
            timeoutSeconds: 1
          resources:
            limits:
              cpu: 500m
              memory: 1000Mi
            requests:
              cpu: 500m
              memory: 1000Mi
          securityContext: {}
          terminationMessagePath: /dev/termination-log
          terminationMessagePolicy: File
          volumeMounts:
            - mountPath: /app/netcode-manifests
              name: netcode-manifests
              readOnly: true
      dnsPolicy: ClusterFirst
      imagePullSecrets:
        - name: regcred-gcp
      restartPolicy: Always
      schedulerName: default-scheduler
      securityContext: {}
      serviceAccount: matchmaker-sa
      serviceAccountName: matchmaker-sa
      terminationGracePeriodSeconds: 30
      volumes:
        - name: cloudsql-credentials
          secret:
            defaultMode: 420
            secretName: cloudsql
        - configMap:
            defaultMode: 420
            name: netcode-manifests
          name: netcode-manifests
status:
  availableReplicas: 1
  conditions:
    - lastTransitionTime: '2025-02-05T20:50:49Z'
      lastUpdateTime: '2025-02-05T20:50:49Z'
      message: Deployment has minimum availability.
      reason: MinimumReplicasAvailable
      status: 'True'
      type: Available
    - lastTransitionTime: '2024-01-19T00:35:29Z'
      lastUpdateTime: '2025-02-05T21:33:24Z'
      message: ReplicaSet "matchmaker-5bc975fc74" has successfully progressed.
      reason: NewReplicaSetAvailable
      status: 'True'
      type: Progressing
  observedGeneration: 40
  readyReplicas: 1
  replicas: 1
  updatedReplicas: 1
---
apiVersion: v1
kind: Service
metadata:
  annotations:
    meta.helm.sh/release-name: opr
    meta.helm.sh/release-namespace: opr-develop
  creationTimestamp: '2024-01-16T20:15:02Z'
  labels:
    app.kubernetes.io/instance: opr
    app.kubernetes.io/managed-by: Helm
    app.kubernetes.io/name: matchmaker
    helm.sh/chart: reflect-common-chart-1.0.0
  managedFields:
    - apiVersion: v1
      fieldsType: FieldsV1
      fieldsV1:
        f:metadata:
          f:annotations:
            .: {}
            f:meta.helm.sh/release-name: {}
            f:meta.helm.sh/release-namespace: {}
          f:labels:
            .: {}
            f:app.kubernetes.io/instance: {}
            f:app.kubernetes.io/managed-by: {}
            f:app.kubernetes.io/name: {}
            f:helm.sh/chart: {}
        f:spec:
          f:internalTrafficPolicy: {}
          f:ports:
            .: {}
            k:{"port":80,"protocol":"TCP"}:
              .: {}
              f:name: {}
              f:port: {}
              f:protocol: {}
              f:targetPort: {}
            k:{"port":1234,"protocol":"TCP"}:
              .: {}
              f:name: {}
              f:port: {}
              f:protocol: {}
              f:targetPort: {}
            k:{"port":10042,"protocol":"TCP"}:
              .: {}
              f:name: {}
              f:port: {}
              f:protocol: {}
              f:targetPort: {}
          f:selector: {}
          f:sessionAffinity: {}
          f:type: {}
      manager: helm
      operation: Update
      time: '2024-03-26T19:15:11Z'
  name: matchmaker
  namespace: opr-develop
  resourceVersion: '191643267'
  uid: f70f9130-8c8c-43d5-b37c-49e1bd5c15ac
spec:
  internalTrafficPolicy: Cluster
  ipFamilies:
    - IPv4
  ipFamilyPolicy: SingleStack
  ports:
    - name: http
      port: 80
      protocol: TCP
      targetPort: 80
    - name: prom-publisher
      port: 1234
      protocol: TCP
      targetPort: 1234
    - name: grpc
      port: 10042
      protocol: TCP
      targetPort: 10042
  selector:
    app.kubernetes.io/instance: opr
    app.kubernetes.io/name: matchmaker
  sessionAffinity: None
  type: ClusterIP
status:
  loadBalancer: {}
