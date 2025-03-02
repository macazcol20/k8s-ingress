kubectl get pods matchmaker-6f554647f-hxgrw -n opr-develop -o jsonpath="{.spec.containers[*].name}"

kubectl exec -it matchmaker-6f554647f-hxgrw  -n opr-develop -- curl -v http://project:10010


## run locally 
docker run -it --rm us-east1-docker.pkg.dev/unity-solutions-tyndall-prd/docker/reflect-matchmaker:develop-latest /bin/sh


## get the sg
```sh
aws ec2 describe-security-groups --group-ids <your-sg-id>
```

## get the envoy config and verify matchmaker
```sh
kubectl -n opr-develop get configmap envoy-config -o yaml
#  it looks like matchmaker grpc is not defined in the envoy config... is it required?

if so then it shoult look something like this

```yaml
apiVersion: v1
data:
  envoy.yaml: |
    admin:
      access_log_path: /tmp/admin_access.log
      address:
        socket_address: { address: 0.0.0.0, port_value: 9901 }

    static_resources:
      listeners:
        - name: listener_0
          address:
            socket_address: { address: 0.0.0.0, port_value: 8080 }
          filter_chains:
            - filters:
                - name: envoy.http_connection_manager
                  config:
                    codec_type: auto
                    stat_prefix: ingress_http
                    route_config:
                      name: local_route
                      virtual_hosts:
                        - name: local_service
                          domains: ["*"]
                          routes:
                            - match: { prefix: "/" }
                              route:
                                cluster: reflect-project
                                max_grpc_timeout: 0s
                            - match: { prefix: "/matchmaker" }
                              route:
                                cluster: matchmaker-grpc
                                max_grpc_timeout: 0s
                          cors:
                            allow_origin_string_match:
                            - safe_regex:
                                google_re2: {}
                                regex: \*
                            allow_methods: GET, PUT, DELETE, POST, OPTIONS
                            allow_headers: authorization,x-grpc-web,grpc-timeout,keep-alive,user-agent,cache-control,content-type,content-transfer-encoding,x-accept-content-transfer-encoding,x-accept-response-streaming,x-user-agent,x-reflect-appid
                            max_age: "1728000"
                            expose_headers: grpc-status,grpc-message
                    http_filters:
                      - name: envoy.grpc_web
                      - name: envoy.cors
                      - name: envoy.router
      clusters:
        - name: project
          connect_timeout: 0.25s
          type: logical_dns
          http2_protocol_options: {}
          lb_policy: round_robin
          hosts: [{ socket_address: { address: project, port_value: 10010 }}]
        - name: matchmaker-grpc
          connect_timeout: 0.25s
          type: logical_dns
          http2_protocol_options: {}
          lb_policy: round_robin
          hosts: [{ socket_address: { address: matchmaker, port_value: 10042 }}]
```

## run locally 
docker run -it --rm us-east1-docker.pkg.dev/unity-solutions-tyndall-prd/docker/reflect-matchmaker:develop-latest /bin/sh


## get the envoy config and verify matchmaker
```sh
kubectl -n opr-develop get configmap envoy-config -o yaml
```

## Check if Envoy is working:
```sh
kubectl -n opr-develop exec -it project-d878b8ff7-jssx2 -- curl -v localhost:8080
```

## Check If Envoy is Properly Forwarding gRPC
```sh
kubectl -n opr-develop exec -it project-d878b8ff7-jssx2 -- curl -v project:10010
```
 
  - note:  curl project:10010 test successfully connected to the project service, but it failed to write the response because it's binary output.

## Verify That Envoy Has the Right Upstream
```sh
kubectl -n opr-develop exec -it project-d878b8ff7-jssx2 -- curl -v http://localhost:9901/clusters
```

## check Matchmaker  connection to Project
```sh
kubectl -n opr-develop exec -it matchmaker-576ddc56cf-6njgk -- curl -v project:10010
```


kubectl run -it --rm debug --image=fullstorydev/grpcurl:latest-alpine --restart=Never -- sh
    docker pull fullstorydev/grpcurl:latest