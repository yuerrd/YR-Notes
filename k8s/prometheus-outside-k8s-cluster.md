### prometheus集群外监控k8s

1. 创建k8s账号

   **role**

   ```yaml
   apiVersion: rbac.authorization.k8s.io/v1
   kind: ClusterRole
   metadata:
     name: prometheus-k8s
   rules:
   - apiGroups:
     - ""
     resources:
     - nodes
     - services
     - endpoints
     - pods
     - nodes/proxy
     verbs:
     - get
     - list
     - watch
   - apiGroups:
     - ""
     resources:
     - configmaps
     - nodes/metrics
     verbs:
     - get
   - nonResourceURLs:
     - /metrics
     verbs:
     - get
   ```

   **RoleBinding**

   ```yaml
   apiVersion: rbac.authorization.k8s.io/v1
   kind: ClusterRoleBinding
   metadata:
     name: prometheus-k8s
   roleRef:
     apiGroup: rbac.authorization.k8s.io
     kind: ClusterRole
     name: prometheus-k8s
   subjects:
     - kind: ServiceAccount
       name: prometheus-k8s
       namespace: default
   ```

   **serviceAccount**

   ```yaml
   apiVersion: v1
   kind: ServiceAccount
   metadata:
     name: prometheus-k8s
   ```

2. 获取token

   ```sh
   kubectl get secret
   prometheus-k8s-token-fcdp6   kubernetes.io/service-account-token   3      5d
   kubectl  get secret prometheus-k8s-token-fcdp6  -o jsonpath={.data.token} | base64 -d
   ```

3. 配置prometheus.yml

   ```yaml
    - job_name: 'kubernetes-pods'
       kubernetes_sd_configs:
       - role: pod
         api_server: https://192.168.31.16:6443
         tls_config:
           ca_file: /etc/kubernetes/ca.crt
         bearer_token_file: /etc/kubernetes/token
       relabel_configs:
       - action: keep
         regex: true
         source_labels:
         - __meta_kubernetes_pod_annotation_prometheus_io_scrape
       - action: replace
         regex: (.+)
         source_labels:
         - __meta_kubernetes_pod_annotation_prometheus_io_path
         target_label: __metrics_path__
       - action: replace
         regex: ([^:]+)(?::\d+)?;(\d+)
         replacement: $1:$2
         source_labels:
         - __address__
         - __meta_kubernetes_pod_annotation_prometheus_io_port
         target_label: __address__
       - action: labelmap
         regex: __meta_kubernetes_pod_label_(.+)
       - action: replace
         source_labels:
         - __meta_kubernetes_namespace
         target_label: kubernetes_namespace
       - action: replace
         source_labels:
         - __meta_kubernetes_pod_name
         target_label: kubernetes_pod_name
   ```

4. pod添加annotations

   ```yaml
   metadata:
     labels:
       app: spring-boot-example
     annotations:
       prometheus.io/path: /actuator/prometheus     #prometheus访问的接口地址
       prometheus.io/port: "8080"                   #服务的端口号
       prometheus.io/scrape: "true"                 #是否开启prometheus监控
   ```

5. docker-compose.yml

   ```yaml
   version: "3"
   services:
     prometheus:
       container_name: prometheus
       image: prom/prometheus:master
       user: root
       volumes:
         - ./prometheus.yml:/etc/prometheus/prometheus.yml
         - ./data:/prometheus
         - ./kubernetes/ca.crt:/etc/kubernetes/ca.crt
         - ./kubernetes/token:/etc/kubernetes/token
       ports:
         - "9090:9090"
       restart: on-failure
   ```

