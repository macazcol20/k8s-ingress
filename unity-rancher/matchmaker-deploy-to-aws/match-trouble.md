kubectl get pods matchmaker-6f554647f-hxgrw -n opr-develop -o jsonpath="{.spec.containers[*].name}"

kubectl exec -it matchmaker-6f554647f-hxgrw  -n opr-develop -- curl -v http://project:10010


## run locally 
docker run -it --rm us-east1-docker.pkg.dev/unity-solutions-tyndall-prd/docker/reflect-matchmaker:develop-latest /bin/sh
