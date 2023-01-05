# Zabbix + Kubernetes

# Prepare SSL certificates
\# mkdir -p $HOME/.minikube/certs\
\# cp domain.pem $HOME/.minikube/certs/domain.pem\
\# minikube start --embed-certs

## Create Service Account
\# kubectl -n default create serviceaccount zabbix-service-account\
\# kubectl create clusterrolebinding zabbix-service-account-bind --clusterrole=cluster-admin --serviceaccount=default:zabbix-service-account

## Create the token
\# nano api-token.yaml

```
apiVersion: v1
kind: Secret
metadata:
  name: oke-kubeconfig-sa-token
  namespace: default
  annotations:
    kubernetes.io/service-account.name: zabbix-service-account
type: kubernetes.io/service-account-token
```

\# kubectl apply -f api-token.yaml\
\# kubectl -n kube-system get secret oke-kubeconfig-sa-token -o jsonpath='{.data.token}' | base64 --decode

## Static route
Depends on your enviroment, in my case I have to create a static route to be able to reach the new subnet where the API is deployed.
![image](https://user-images.githubusercontent.com/19838800/210895567-00cf4d99-2548-40ea-af5d-4402063b5d4c.png)

## Finally in Zabbix

The output is the token that you will need to set in Zabbix.
![image](https://user-images.githubusercontent.com/19838800/210896138-af50a584-c8cf-4ede-ba33-5e3bc0e252ad.png)

No Zabbix agent is needed. All is performed throught HTTPS.
![image](https://user-images.githubusercontent.com/19838800/210896370-aee8f7ae-16da-414a-81cf-dd71b9786c20.png)


