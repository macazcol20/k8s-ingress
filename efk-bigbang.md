## Installation steps
https://repo1.dso.mil/big-bang/product/packages/eck-operator
https://repo1.dso.mil/big-bang/product/packages/elasticsearch-kibana
https://repo1.dso.mil/big-bang/product/packages/fluentbit

```sh
git clone https://repo1.dso.mil/big-bang/product/packages/eck-operator.git
git clone https://repo1.dso.mil/big-bang/product/packages/elasticsearch-kibana.git
git clone https://repo1.dso.mil/big-bang/product/packages/fluentbit.git
```

## create secret
```sh
k create ns eck-operator
k create ns elasticsearch-kibana
k create ns fluentbit
k -n eck-operator apply -f private-registry.yaml
k -n elasticsearch-kibana apply -f private-registry.yaml
k -n fluentbit apply -f private-registry.yaml
```

## update directory structure 

```sh
mv eck-operator io
cd io 
mv chart eck-operator
mv eck-operator ../
cd ..
rm -rf io
```

```sh
mv elasticsearch-kibana io
cd io 
mv chart elasticsearch-kibana
mv elasticsearch-kibana ../
cd ..
rm -rf io
```

```sh
mv fluentbit io
cd io 
mv chart fluentbit
mv fluentbit ../
cd ..
rm -rf io
```

## install the chart

```sh
helm -n eck-operator install eck-operator eck-operator/
k -n eck-operator get po 
k -n eck-operator get svc
``` 
```sh
helm -n elasticsearch-kibana install elasticsearch-kibana elasticsearch-kibana/
k -n elasticsearch-kibana get po 
k -n elasticsearch-kibana get svc
```

```sh
helm -n fluentbit install fluentbit fluentbit/
k -n fluentbit get po 
k -n fluentbit get svc
```

domain: kapistiogroup.com


## chark
```sh
kubectl -n elasticsearch-kibana logs elasticsearch-kibana-es-data-0 -c elastic-internal-init-filesystem
kubectl -n elasticsearch-kibana logs elasticsearch-kibana-es-master-0 -c elastic-internal-init-filesystem
kubectl -n elasticsearch-kibana logs elasticsearch-kibana-es-data-0 -c elastic-internal-suspend
kubectl -n elasticsearch-kibana logs elasticsearch-kibana-es-master-0 -c elastic-internal-suspend
```

## for storage issue
```sh
sudo sysctl -w vm.max_map_count=262144
vm.max_map_count=262144
sudo sysctl -p
```