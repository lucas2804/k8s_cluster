### Google Cloud K8s

#### I - Setup

- 1) Setup project
```bash
gcloud projects create lucskylab
gcloud projects list
gcloud config set project lucskylab
# Enable billing then create cluster  
gcloud container clusters create lucskylab --num-nodes 3
gcloud beta compute --project "lucdocker" ssh --zone "us-central1-a" "gke-lucdocker01-default-pool-b59c3f82-2qhh"

# test kubia
kubectl run kubia --image=luksa/kubia --port=8080 --generator=run/v1
kubectl expose rc kubia --type=LoadBalancer --name kubia-http

gcloud compute firewall-rules create kubia-svc-rule --allow=tcp:30123
```

#### NodePort

- **Note before you can access a NodePort, you need to configure Google Cloud's firewalls**

```yaml
apiVersion: v1
kind: Service
metadata:
  name: kubia-nodeport
spec:
  type: NodePort
  ports:
    - port: 80
  targetPort: 8080
  nodePort: 30123
  selector:
    app: kubia
```

```bash
# NodePort can be accessed vi External IP of Nodes as: 35.232.116.97 35.225.151.158 35.194.50.26
# curl http://35.232.116.97:30123
# - **Note before you can access a NodePort, you need to configure Google Cloud's firewalls**
gke-lucskylab01-default-pool-01cedace-7w7h   Ready    <none>   5h8m   v1.13.7-gke.24   10.128.0.4    35.232.116.97    
gke-lucskylab01-default-pool-01cedace-cg9w   Ready    <none>   5h8m   v1.13.7-gke.24   10.128.0.2    35.225.151.158 
gke-lucskylab01-default-pool-01cedace-z6jl   Ready    <none>   5h8m   v1.13.7-gke.24   10.128.0.3    35.194.50.26

gcloud compute firewall-rules create kubia-svc-rule --allow=tcp:30123    

gcloud compute firewall-rules create kubia-svc-rule-31795 --allow=tcp:31795
curl http://35.225.151.158:30123                                              
This is v1 running in pod kubia-6b646f577b-5qv6m

gcloud compute firewall-rules create kubia-svc-rule-30500 --allow=tcp:30500

30500

```



```bash
 gcloud projects create lucskylab
 gcloud projects list
 gcloud config set project lucskylab
 gcloud container clusters create lucskylab --num-nodes 3
 gcloud beta compute --project "lucdocker" ssh --zone "us-central1-a" "gke-lucdocker01-default-pool-b59c3f82-2qhh"
 mkdir -p registry/{images,certs,auth}
 openssl req -x509 -nodes -days 10000 -newkey rsa:2048 -keyout cert.key -out cert.crt
 sudo docker run --entrypoint htpasswd registry:2 -Bbn skylab skylab2019 > registry/auth/htpasswd
 gcloud compute scp  gke-lucdocker-default-pool-caa9989e-08c5:/home/lucskylab/cert.crt* ./
 ke nodes gke-lucdocker-default-pool-caa9989e-08c5
 # -> Change label node: master

 openssl genrsa -out tls.key 2048
 openssl req -new -x509 -key tls.key -out tls.cert -days 360 -subj /CN=kubia.example.com
 kubectl create secret tls tls-secret --cert=tls.cert --key=tls.key

 gcloud compute scp  ./registry gke-lucskylab01-default-pool-01cedace-7w7h:/home/lucskylab/
```
