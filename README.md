#The Ultimate DevOps Project: CI/CD Pipeline with Docker, Kubernetes, ArgoCD & GitHub Actions !

from flask import Flask, jsonify
import datetime, socket

app = Flask(__name__)

@app.route('/api/v1/info')
def info():
    return jsonify({
        'time': datetime.datetime.now().strftime('%Y-%m-%d %H:%M:%S'),
        'hostname': socket.gethostname(),
        'env': 'dev'
    })

@app.route('/api/v1/healthz')
def health():
    return jsonify({'status': 'up'}), 200

if __name__ == '__main__':
    app.run(host="0.0.0.0", port=5001)

**The above code is for Python APP**

Where we are importing flask and jsonify 

Flask is the engine that handles web request and jsonify is the helper that converts python data into json format 

import datetime, socket-> we are importing date time and socket module these are used to fetch current time and name of the host where code is running

app = Flask(__name__) -> intilaizing application

@app.route(‘/api/v1/info’),  @app.route(‘/api/v1/healthz') are 2 end points 

@app.route(‘/api/v1/info’) -> prints the time and hostname 

@app.route(‘/api/v1/healthz’) -> which shows the status up and status code 200, we will use this endpoint to define liveness and readiness probes 
when we start creating kubernetes manifest and helm charts 

app.run(host="0.0.0.0", port=5000) -> this tells the app to listens the connections from any network interface not only internal, 
its very critical for docker and kubernetes

We need  flask to run this python application for that we will define requirements.txt file and specify the flask version, 
which we will install while running python application

flask==3.0.3
Make sure python is installed on our machine, by default all modern day OS comes with python3 —version

Switch to directory python-app

Create python virtual environment(api) to test the application safely

Python3 -m venv api -> this creates the directory called api

Now activate the virtual environment by using source command
source api/bin/activate 

<img width="1068" height="222" alt="image" src="https://github.com/user-attachments/assets/85bd502c-5e21-454a-a5be-d56952d82f22" />

<img width="650" height="330" alt="image" src="https://github.com/user-attachments/assets/b4360fc0-ccae-4258-aeee-8309119b13fb" />


Now install the flask application
pip3 install -r requirements.txt 

<img width="2104" height="821" alt="image" src="https://github.com/user-attachments/assets/08dcdb7b-d773-4664-a11d-c744997d2c70" />

<img width="1713" height="269" alt="image" src="https://github.com/user-attachments/assets/8ca71142-ea84-4627-8d2b-3b6ccc5c0468" />

<img width="1996" height="418" alt="image" src="https://github.com/user-attachments/assets/891433b4-1dc5-4583-a729-4ec60ff04d7a" />


We need to access it using end points -> http://localhost:5000/api/v1/info 
<img width="1410" height="352" alt="image" src="https://github.com/user-attachments/assets/6471bece-eaf7-43b2-a9d4-96c0135faacf" />

But this works only on my machine. That’s where docker comes

Docker helps us to bundle code, dependencies and run time env  to a docker image and it runs anywhere on your machine

FROM python:3.9.6-slim
COPY requirements.txt /tmp
RUN pip install -r /tmp/requirments.txt
COPY ./src/ /src
EXPOSE 5001
CMD ["python" “/src/app.py"]

docker build -t python-app:v1 .

<img width="1868" height="682" alt="image" src="https://github.com/user-attachments/assets/934611a0-3f24-4a83-9e42-fb9b4738faca" />

<img width="2009" height="660" alt="image" src="https://github.com/user-attachments/assets/c765a057-8fe1-4cfd-a7b0-8e3a112df2ac" />

<img width="1911" height="334" alt="image" src="https://github.com/user-attachments/assets/9c482ac6-04a0-4a8f-b10f-20f16e98a826" />

(api) jyothsna@Jyothsnas-MacBook-Air python-app % docker run --name python-app -dp 5001:5001 python-app:v1 
88a401d5a19bffb3163cfeec47926c05a06376716419e5aa32d447c946baa8ed

<img width="1599" height="562" alt="image" src="https://github.com/user-attachments/assets/79c2c798-09ea-4853-b09d-59775fa74155" />

<img width="1588" height="374" alt="image" src="https://github.com/user-attachments/assets/42af3df6-dcbd-471c-bdab-4bc6c6b0ed9a" />

<img width="2107" height="359" alt="image" src="https://github.com/user-attachments/assets/97e8fb57-ce46-4770-9fed-b0ddc29249f5" />


Now push the container image to docker hub as our app is working 
In order to push the image to docker hub we need to login to the docker hub account using docker login
Hub.docker.com-> docker profile-> account settings-> personal access token-> create a token->generate a token
dckr_pat_54S_woEmTOVNLiruKr36CCmlswQ
docker login -u jyothsnav

<img width="1566" height="1177" alt="image" src="https://github.com/user-attachments/assets/f72f26c9-f821-480d-85bf-a66dd14b9fb8" />

<img width="1144" height="276" alt="image" src="https://github.com/user-attachments/assets/c662a954-1495-441e-bfca-33102770494c" />

Now push the local image to docker hub for that we need to tag the image with username

<img width="1917" height="688" alt="image" src="https://github.com/user-attachments/assets/77884b6b-7d02-4c69-afb6-4d6275647268" />

docker push jyothsnav/python-app:v1

<img width="1469" height="354" alt="image" src="https://github.com/user-attachments/assets/ef15bdce-213a-4abf-a636-d65b130e1fb3" />

<img width="2556" height="1193" alt="image" src="https://github.com/user-attachments/assets/fd5d6c3d-13c9-41d3-a35c-a53003383dd5" />

Now lets try to pull the image to local from docker hub
For that first delete the existing image at local
docker rmi jyothsnav/python-app:v1

<img width="1649" height="595" alt="image" src="https://github.com/user-attachments/assets/21d4aaa9-f2b8-4fa5-8e6f-665e463758bb" />

Now do docker pull command
docker pull jyothsnav/python-app:v1 

<img width="1149" height="204" alt="image" src="https://github.com/user-attachments/assets/3de762d1-6f88-49aa-b311-2c2f51209265" />

Now push all the local changes to GitHub repo

<img width="1150" height="604" alt="image" src="https://github.com/user-attachments/assets/e8350526-60b9-481f-85b1-39a629ec439f" />

<img width="1669" height="1345" alt="image" src="https://github.com/user-attachments/assets/d17b68e3-7e96-475f-8272-f63f929cba1d" />

<img width="1038" height="339" alt="image" src="https://github.com/user-attachments/assets/6386143f-c3a9-45a7-a3e9-dd307d4657ce" />

The application image is ready and pushed to docker hub 

Lets move to kubernetes orchestration engine
We will use kind to build kubernetes cluster

https://kind.sigs.k8s.io/docs/user/quick-start/
https://kind.sigs.k8s.io/docs/user/quick-start/#installation

jyothsna@Jyothsnas-MacBook-Air cicd-project % # For Intel Macs
[ $(uname -m) = x86_64 ] && curl -Lo ./kind https://kind.sigs.k8s.io/dl/v0.31.0/kind-darwin-amd64
# For M1 / ARM Macs
[ $(uname -m) = arm64 ] && curl -Lo ./kind https://kind.sigs.k8s.io/dl/v0.31.0/kind-darwin-arm64
chmod +x ./kind
sudo mv ./kind /usr/local/bin/kind
zsh: command not found: #
zsh: command not found: #
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100    98  100    98    0     0    609      0 --:--:-- --:--:-- --:--:--   612
  0     0    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0
100 10.0M  100 10.0M    0     0  14.8M      0 --:--:-- --:--:-- --:--:-- 14.8M

<img width="964" height="98" alt="image" src="https://github.com/user-attachments/assets/d3979f04-9546-4f65-86e8-b52cb61e8df4" />

Let us create kind-config.yml file which will be used to build our kubernetes cluster. As kind runs in docker we must map our port 80 from the host to the kind node container 

<img width="482" height="258" alt="image" src="https://github.com/user-attachments/assets/fac470b2-2954-4a36-b53f-9bca12336b93" />

Lets create a kind cluster
Kind create cluster —config kind-config.yml

<img width="1325" height="415" alt="image" src="https://github.com/user-attachments/assets/64d1016f-c154-49bf-ae33-b1235502e80a" />

<img width="1596" height="278" alt="image" src="https://github.com/user-attachments/assets/c5f557f4-43f6-481f-915a-ae03ed488818" />

To access the cluster we have to install kubectl tool and configure it
https://kubernetes.io/docs/tasks/tools/install-kubectl-linux/

<img width="930" height="152" alt="image" src="https://github.com/user-attachments/assets/12366c9d-1547-442c-9eee-6adf07a53962" />

Now we will set cluster context to kind 
->kubectl cluster-info —context kind-kind  , once done we are able to run kubectl commands
<img width="964" height="104" alt="image" src="https://github.com/user-attachments/assets/c3fef9c8-2ee0-4aae-914e-f4b771dbf014" />

Now go to GitHub repo and create a new repo to host the infrastructure resources like manifest, helm charts etc
https://github.com/Jyothsna-99/infra-repo

<img width="2214" height="1245" alt="image" src="https://github.com/user-attachments/assets/96409606-b213-4b49-aed8-e8721c760b47" />

jyothsna@Jyothsnas-MacBook-Air cicd-project % git clone https://github.com/Jyothsna-99/infra-repo.git
Cloning into 'infra-repo'...
warning: You appear to have cloned an empty repository.

https://kubernetes.io/docs/concepts/workloads/controllers/deployment/

apiVersion: apps/v1
kind: Deployment
metadata:
  name: python-app
  labels:
    app: python-app
spec:
  replicas: 1
  selector:
    matchLabels:
      app: python-app
  template:
    metadata:
      labels:
        app: python-app
    spec:
      containers:
      - name: python-app
        image: jyothsnav/python-app:v1 —> here the image and tag we have pushed to docker hub 
        ports:
        - containerPort: 5001 —-> here the port that we have used for docker image to create

https://kubernetes.io/docs/concepts/services-networking/service/

apiVersion: v1
kind: Service
metadata:
  name: python-app-service
spec:
  selector:
    app.kubernetes.io/name: python-app —> the name we have used in deployment file to match the labels 
  ports:
    - protocol: TCP
      port: 8080
      targetPort: 5001


<img width="1469" height="435" alt="image" src="https://github.com/user-attachments/assets/0fc25547-5663-4395-903f-9445451ff894" />

<img width="1170" height="254" alt="image" src="https://github.com/user-attachments/assets/10cd6edb-34c1-41f9-9e20-b3385063eedf" />

<img width="1274" height="162" alt="image" src="https://github.com/user-attachments/assets/f3a207ca-6642-470e-8bf8-104bfb9af442" />

<img width="1332" height="317" alt="image" src="https://github.com/user-attachments/assets/1e814868-ecd6-4c18-bd2d-b3be978c393f" />

Kubectl rollout restart deploy python-app -> used to restart the deployment file if there is any pod failure 
As the service type is cluster ip we can use port forward to access the application
kubectl port-forward svc/python-app-service 8080:8080
Mapping hostport 8080 to container port 8080

For now the service is internal which is good for testing
To make it access for externally we can use traffic gateway api

Gateway-api was not installed by default in kubernetes distribution, we must apply standard CRD’s  

<img width="2007" height="402" alt="image" src="https://github.com/user-attachments/assets/c0d56ce7-5228-46cc-bb60-d0fa12b00a07" />

https://gateway-api.sigs.k8s.io/guides/getting-started/

kubectl apply --server-side -f https://github.com/kubernetes-sigs/gateway-api/releases/download/v1.5.0/standard-install.yaml

<img width="1067" height="688" alt="image" src="https://github.com/user-attachments/assets/2dd33800-525f-4698-9c4e-b4a773743519" />

now install the traefik gateway on cluster
Traefik is a modern cloud native Ingress controller that supports gateway api and simplifies routing
We will define http route that routes traffic to our service so that we can access flask api externally 
Through domain or load balancer 
https://doc.traefik.io/traefik/getting-started/kubernetes/

We can use helm to install treafik 

curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-4
chmod 700 get_helm.sh
./get_helm.sh

https://helm.sh/docs/intro/install/
Password : ******

<img width="2058" height="457" alt="image" src="https://github.com/user-attachments/assets/20e9cb84-b613-4b37-8fab-ad1720374827" />

helm repo add traefik https://traefik.github.io/charts
helm repo update

<img width="1580" height="210" alt="image" src="https://github.com/user-attachments/assets/ee93336b-969b-4095-9c4a-0ead734eb07c" />


Now install traefik using helm install command, 

helm install traefik traefik/traefik \
  --namespace traefik --create-namespace \
  --skip-crds \
  --set providers.kubernetesGateway.enabled=true \
  --set service.type=NodePort \
  --set ports.web.nodePort=30080 \
  --set ports.websecure.nodePort=30443

<img width="1288" height="124" alt="image" src="https://github.com/user-attachments/assets/775a1336-8eed-45b2-be72-932fcb42f700" />

Here we are creating namespace for traefik
As we have already deployed gateway-api crds so we are skipping it 
Setting the service type to node port  so it will receive traffic from client port mapping to access it externally with port number 30080
Once the traffic is installed we need to make sure traefik has recognised the gateway api 
kubectl get gatewayclass -> to check if gateway class is admitted 

<img width="1193" height="119" alt="image" src="https://github.com/user-attachments/assets/c7e81bde-4fef-4539-8a24-c183ce2c608c" />

Now we are ready to apply our gateway  and create http route
https://gateway-api.sigs.k8s.io/guides/http-routing/

apiVersion: gateway.networking.k8s.io/v1
kind: HTTPRoute
metadata:
  name: python-app-route
spec:
  parentRefs:
  - name: traefik-gateway
    namespace: traefik
  hostnames:
  - "python-app.test.com"
  rules:
  - matches:
    - path:
        type: PathPrefix
        value: /
    backendRefs:
    - name: python-app-service
 port: 8080

<img width="760" height="441" alt="image" src="https://github.com/user-attachments/assets/8c5534e9-2ef0-4216-8c95-69bc8381ae03" />

<img width="1216" height="339" alt="image" src="https://github.com/user-attachments/assets/a6914bff-ba53-4e2e-a1a6-08ec3ac50a5d" />

<img width="1487" height="755" alt="image" src="https://github.com/user-attachments/assets/8d15b8c1-dc11-466d-826d-8a83cccecc5f" />

<img width="1481" height="197" alt="image" src="https://github.com/user-attachments/assets/abfc5b6b-8b66-4953-a56c-6f7005a1fc98" />

<img width="1486" height="511" alt="image" src="https://github.com/user-attachments/assets/d2b8061e-3e80-4582-ab6d-96c2f0f9ec40" />

Change it from same to all

<img width="654" height="474" alt="image" src="https://github.com/user-attachments/assets/d32e74fb-3c35-45ef-8a87-9cebfb1e83cc" />

Now delete the httproute and create it again

<img width="1422" height="281" alt="image" src="https://github.com/user-attachments/assets/9080dc08-12c6-45eb-bafc-4f6e6ef5a37b" />

<img width="1538" height="764" alt="image" src="https://github.com/user-attachments/assets/94a12cff-9692-4108-afd2-31e3c056f40e" />

Now the status is true and it is accepted . So we should be able to access our application using http route

<img width="1129" height="115" alt="image" src="https://github.com/user-attachments/assets/9f93a278-a50f-4cdb-b4ce-2e4efd871d9a" />

<img width="2025" height="1063" alt="image" src="https://github.com/user-attachments/assets/6c6a5387-f4bc-4f6b-aa58-adbf6c1694e0" />

This will not work because it don’t has any dns record 
sudo vim /etc/hosts

<img width="943" height="382" alt="image" src="https://github.com/user-attachments/assets/615fc1e6-fe4c-4926-9c4f-c6511c7998cb" />

<img width="963" height="391" alt="image" src="https://github.com/user-attachments/assets/eb4b2cdc-84a5-40c4-b85d-fcd355ef53df" />

We are able to access our backend using http route 

<img width="1496" height="317" alt="image" src="https://github.com/user-attachments/assets/ba06cd46-4010-49bf-ba53-10d758a059e6" />

As project grows managing multiple yaml files become complex hence we will use helm 
Helm is package manager for kubernetes
We will create helm package for our python app which templatize our deployment, service and http route files
Create directory called helm-charts 
Navigate to it
Helm create python-app -> to create a directory structure along with common files and directories used in our chart

<img width="1495" height="1418" alt="image" src="https://github.com/user-attachments/assets/99fdd139-7131-4bae-a895-5b83ee354c5a" />

We need to cutomize the default helm chart, so that we can deploy python-api application on kubernetes

<img width="1277" height="690" alt="image" src="https://github.com/user-attachments/assets/f0cce572-b718-4d3b-a58d-188d04427109" />

Update the values.yml file

<img width="560" height="1121" alt="image" src="https://github.com/user-attachments/assets/7b873cbf-6b20-43d8-a569-d355b948e82c" />

<img width="1404" height="321" alt="image" src="https://github.com/user-attachments/assets/b5618873-b4e8-43a8-b7d4-733e8533ae3a" />

Now install the application using helm
helm install python-app python-app
Here first python-app is name we have given to use 
Second python-app is chart directory 

<img width="1684" height="459" alt="image" src="https://github.com/user-attachments/assets/59b8770b-2688-403f-a1cc-b0f1930a8e26" />

<img width="1310" height="672" alt="image" src="https://github.com/user-attachments/assets/8886f5a0-17d4-46d6-9b34-1a44abe1c656" />

<img width="1207" height="958" alt="image" src="https://github.com/user-attachments/assets/5512ad55-f209-4716-a67d-9f43a5cb00cf" />

<img width="1416" height="889" alt="image" src="https://github.com/user-attachments/assets/94beb1cf-88f1-49fd-8364-f4ffd0137d5a" />

Now setup argocd gitops tool for CD 
Instead of manually applying yaml files or helm charts, argo cd can automatically sync the kubernetes workloads based on what’s defined on git repository

Install argocd on our cluster using helm and expose its UI using traefik ingress
We will have understanding of how to use traefik http route and ingress 
Then we will create an argocd application to deploy our python flask api from the custom helm charts 

helm repo add argo https://argoproj.github.io/argo-helm
->helm repo update argo

<img width="1465" height="216" alt="image" src="https://github.com/user-attachments/assets/5c95a13f-9784-4f18-ae1f-75739de22a72" />

jyothsna@Jyothsnas-MacBook-Air helm-charts % helm install argocd argo/argo-cd --namespace argocd --create-namespace \
> --set server.extraArgs={—insecure}

What does --set server.extraArgs={--insecure} do in ArgoCD
The --set server.extraArgs={--insecure} flag configures the Argo CD server deployment to start with the --insecure command-line argument.

Purpose
This disables Argo CD's built-in TLS, switching the server from HTTPS (port 8443) to plain HTTP (port 8080). It prevents protocol conflicts when an ingress controller, load balancer, or reverse proxy handles TLS termination instead.

Common Use Cases
Development setups or local testing without TLS.
Ingress setups (e.g., NGINX, Traefik) that terminate TLS externally.
Avoiding redirect loops from double TLS handling.
Cloud load balancers like AWS ALB that manage encryption. 
Effects
Service port changes to 80→8080 (HTTP).
No self-signed certificate generation.
gRPC uses cleartext HTTP/2 (h2c).
Access UI via http://<argocd-server>:8080 (CLI: argocd login --insecure). 
Note: Never expose insecure Argo CD directly to the internet—always use a TLS proxy in production.

<img width="1217" height="320" alt="image" src="https://github.com/user-attachments/assets/0d11367f-e0f6-4a50-97f9-473cd67fefcd" />

<img width="1493" height="272" alt="image" src="https://github.com/user-attachments/assets/482aae0f-4bd9-4709-9800-973c7d9b6e55" />

—>CrashLoopBackOff: Often due to missing ServiceAccount bindings, OOM, or invalid configs. Restart after fixes: kubectl rollout restart deployment argocd-applicationset-controller -n argocd

—>However, argocd-repo-server shows two pods: the old one in "Unknown" status (stuck transition) and a new one starting. This indicates a rollout but incomplete cleanup, common during manual fixes or partial Helm ops.

kubectl scale deployment argocd-repo-server --replicas=0 -n argocd

kubectl delete pod argocd-repo-server-85bfb6c45d-whgpz -n argocd  # Old Unknown pod
kubectl scale deployment argocd-repo-server --replicas=1 -n argocd

<img width="1314" height="282" alt="image" src="https://github.com/user-attachments/assets/cb63bb4f-54c7-42d4-9cc6-6efd5f7fae35" />

<img width="1703" height="319" alt="image" src="https://github.com/user-attachments/assets/b2414810-97f4-4972-8605-cd9389561e11" />

Here argo-server used to access our application
Expose the argocd service using ingress

https://kubernetes.io/docs/concepts/services-networking/ingress/

apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: argocd-server-ingress
  namespace: argocd
  annotations:
     traefik.ingress.kubernetes.io/router.entrypoints: web
spec:
  rules:
  - host: "argocd.test.com"
    http:
      paths:
      - pathType: Prefix
        path: /
        backend:
          service:
            name: argocd-server
            port:
              number: 80

jyothsna@Jyothsnas-MacBook-Air argocd % kubectl apply -f argo-ingress.yml
ingress.networking.k8s.io/argocd-server-ingress created

<img width="1157" height="214" alt="image" src="https://github.com/user-attachments/assets/e15bfccc-656f-45b7-bfcb-33b8dae90f83" />

-> sudo vi /etc/hosts

<img width="920" height="469" alt="image" src="https://github.com/user-attachments/assets/9ce8c0e4-1a63-451c-9d78-6e78895e0cf2" />

—>http://argocd.test.com

<img width="2375" height="1030" alt="image" src="https://github.com/user-attachments/assets/691c6943-5099-4fc7-93dd-7be9782ca9c1" />

kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d

Now create an application on argocd to deploy python-flask api from our custom helm-chart

First connect our GitHub repo 

<img width="1518" height="889" alt="image" src="https://github.com/user-attachments/assets/a662c28a-5e66-4c03-acf9-b2454c5e32ab" />

Give the GitHub repo link for infra-repo and connect

<img width="2400" height="540" alt="image" src="https://github.com/user-attachments/assets/3b2758e4-fef9-4cdd-8ebb-d0cbe7ba03cf" />

Now create an application

<img width="1494" height="960" alt="image" src="https://github.com/user-attachments/assets/4506f058-5799-424b-aca9-5ba5ce7bf431" />

<img width="2062" height="1277" alt="image" src="https://github.com/user-attachments/assets/8d911c1e-af4f-4f2d-a764-18a407483450" />

<img width="1236" height="593" alt="image" src="https://github.com/user-attachments/assets/385cb738-eaa1-4773-8a8c-f93947322285" />

Now if we make any changes to helm chart , argocd will automcatically pull those changes and deployed 

<img width="2559" height="973" alt="image" src="https://github.com/user-attachments/assets/06236360-1339-42af-9a82-7dbe2b7212ef" />

those changes to kubernetes cluster

<img width="1766" height="471" alt="image" src="https://github.com/user-attachments/assets/f6e5f69e-192a-4c48-804a-6820f6f9365c" />


But if we make any changes to our application those changes will not reflect because image which we build currently not have those new features 

Now automate the entire pipeline using GitHub actions
GitHub actions help us define automated workflows directly in our repository

Create a directory .github/workflows in our python-app repo

<img width="591" height="678" alt="image" src="https://github.com/user-attachments/assets/2a9ab545-7080-43c7-bc24-3f4be41b8c78" />

<img width="1650" height="1417" alt="image" src="https://github.com/user-attachments/assets/f245e2a1-184a-4343-8903-69d56280d622" />

<img width="1660" height="1245" alt="image" src="https://github.com/user-attachments/assets/c7cdac00-9d4e-4f70-bc3c-3e8cd9d2bde9" />

INFRA_REPO_TOKEN

Go to GitHub profile->settings->developer settings-> PAT -> tokens classic

<img width="2550" height="1180" alt="image" src="https://github.com/user-attachments/assets/65c87299-b563-4efa-a187-678f90e1665a" />

<img width="1826" height="1401" alt="image" src="https://github.com/user-attachments/assets/1c1af2ee-1600-49a8-b5f8-90bd4eed0d79" />

We need to change the tag of values.yml with latest docker tag 

<img width="1206" height="978" alt="image" src="https://github.com/user-attachments/assets/390d3279-de7d-4b4b-9ae7-cebd59800a9f" />

<img width="2469" height="1273" alt="image" src="https://github.com/user-attachments/assets/cc36c1e1-ede0-4d4f-906f-0f1927ffaca0" />

<img width="961" height="219" alt="image" src="https://github.com/user-attachments/assets/954325d9-2a31-4002-a83a-82e707310940" />

Here we can see latest changes env:dev was added which we have modified recently in python src file

<img width="1229" height="1248" alt="image" src="https://github.com/user-attachments/assets/3587e828-2cb8-4984-9af3-973ebb7caa6a" />

<img width="1461" height="378" alt="image" src="https://github.com/user-attachments/assets/3dd6450e-11c3-4292-9509-6de2014cbdcd" />

Now we have automated the entire process of building the image , pushing the image to docker hub and automatically argocd sync those changes and deploying those changes to the cluster 

Now we have to test the automatic update of image tag 
As we have made the changes in cicd yml file
Push the changes to python-repo in GitHub 

<img width="1047" height="635" alt="image" src="https://github.com/user-attachments/assets/bfab7b07-112e-4c63-b245-5e06537084f0" />

<img width="1728" height="988" alt="image" src="https://github.com/user-attachments/assets/bff726f8-8ecb-430e-a493-1cfe5144bfd2" />

<img width="1722" height="1205" alt="image" src="https://github.com/user-attachments/assets/3645fddd-f230-413c-a62a-32e574955784" />

<img width="1674" height="1153" alt="image" src="https://github.com/user-attachments/assets/90521084-0dba-4a41-b5d2-38c5aecb57e8" />

<img width="998" height="140" alt="image" src="https://github.com/user-attachments/assets/60c4552b-b5a2-4db8-8a96-1b5f86d99c47" />

<img width="2560" height="1213" alt="image" src="https://github.com/user-attachments/assets/6e05bc8f-573d-4be3-9c8e-d87b41866a16" />

<img width="1448" height="364" alt="image" src="https://github.com/user-attachments/assets/c1a43c5c-4197-43bd-9232-a215ac513794" />


First we have build simple python flask api application 
We containerised it using docker
Deployed it to our kubernetes cluster 
We exposed it using traefik gateway api
We created our custom helm chart and enabled gitops using argocd
We automated entire process using GitHub actions





































