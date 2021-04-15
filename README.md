# ansible-microk8s

Ansible galaxy. Role for Microk8s on Ubuntu
### Problem with certs 


#### Quick fix for Chrome

> Chrome is now always blocking self-signed certificates. It can be turned off with chrome://flags/#allow-insecure-localhost (change to Enable) [see source](https://github.com/ubuntu/microk8s/issues/945#issuecomment-593843714)

#### Reconfigure certs generation (optional)

> This worked. In this exact order.
```bash
cd $HOME/kubernetes/
mkdir certs
# Generate Certs
openssl req -nodes -newkey rsa:2048 -keyout certs/dashboard.key -out certs/dashboard.csr -subj "/C=/ST=/L=/O=/OU=/CN=kubernetes-dashboard"
openssl x509 -req -sha256 -days 365 -in certs/dashboard.csr -signkey certs/dashboard.key -out certs/dashboard.crt

kubectl create --edit -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.0.0-beta5/aio/deploy/recommended.yaml

# ESC :wq until you get to the deployment
# Modify the appropriate section for the dashboard-args
          args:
            - --tls-cert-file=/dashboard.crt
            - --tls-key-file=/dashboard.key
            #- --auto-generate-certificates

# Add certs to dashboard
kubectl delete secret kubernetes-dashboard-certs --namespace=kubernetes-dashboard
kubectl create secret generic kubernetes-dashboard-certs --from-file=$HOME/kubernetes/certs -n kubernetes-dashboard
```
[see source](https://github.com/kubernetes/dashboard/issues/2995#issuecomment-551309479)