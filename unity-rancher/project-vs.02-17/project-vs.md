## Verify the Istio Routes:
istioctl proxy-config routes $(kubectl get pod -l istio=sidecar -o jsonpath='{.items[0].metadata.name}') -n opr-develop

## Confirm GRPC Service is Reachable:
grpcurl -v develop.opr.s3gis.be:10010 list

nc -zv develop.opr.s3gis.be 10010

## Ensure the Matchmaker Service Can Connect:
kubectl exec -it matchmaker-<pod-name> -n opr-develop -- curl -v http://project:10010

kubectl -n opr-develop logs deployment/project

# Check if port 10010 is open
nc -zv project 10010  
# Test gRPC service internally
grpcurl -v project:10010 list  


## troubleshoot the grpc
kubectl -n istio-system logs deploy/istio-ingressgateway --tail=50 -f
kubectl -n istio-system get svc istio-ingressgateway -o yaml | grep 10010


## I have to edit  istio-ingressgateway to add port 10010
kubectl -n istio-system edit svc istio-ingressgateway


- name: grpc
  port: 10010
  targetPort: 10010
  protocol: TCP