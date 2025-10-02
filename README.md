# Install-Prometheus-and-graphana-on-Kubernetes-using-Helm-
## Helm 
Is the package manager for Kubernetes.

It allows us to streamline installation and management of Kubernetes applications.

Helms use a packaging format called as Charts which is basically a collection of yaml manifest files

## Promenthous 

Prometheus is an Open-source systems monitoring and alerting toolkit.

Prometheus collects and stores the metrics as time series data.

It provides out-of-box monitoring capabilities for container orchestration platforms such as Kubernetes.

## Graphana 

Grafana is a multi-platform Open-source analytics and interactive visualization web application.

It provides charts, graphs and alerts for the web when connected to supported data services.

Grafana allows us to query, visualize, alert on and understand our metrics, no matter where they are stored. Some supported data sources in addition to Prometheus are AWS CloudWatch, AzureMonitor, PostgreSQL, Elasticsearch and many more.

### prerequistes :

## 1- install Helm using this link using this link >>  https://helm.sh/docs/intro/install/

download helm package from my device to master node 

  tar -zxvf helm-v3.19.0-rc.1-linux-amd64.tar.gz

  mv linux-amd64/helm /usr/local/bin/helm

  echo $PATH

  export PATH=$PATH:/usr/local/bin

  echo $PATH

  echo 'export PATH=$PATH:/usr/local/bin' >> ~/.bashrc

  source ~/.bashrc

  /usr/local/bin/helm version

   helm version

<img width="1370" height="201" alt="image" src="https://github.com/user-attachments/assets/8b8fc1a0-d303-48a9-b069-c5a0648c9ae1" />

<img width="1592" height="87" alt="image" src="https://github.com/user-attachments/assets/1ef6354e-4eb0-4d1d-a079-30a1fb410bf0" />

<img width="966" height="240" alt="image" src="https://github.com/user-attachments/assets/b8010159-ad89-4789-9917-f731f8772555" />

## 2- Download repositorires for promethous 

helm repo add prometheus-community https://prometheus-community.github.io/helm-charts

helm repo add grafana https://grafana.github.io/helm-charts

helm repo update

<img width="917" height="230" alt="image" src="https://github.com/user-attachments/assets/3848ec8a-8217-4cd5-9f6c-8c0c868b7763" />

## 3- now , we need to install Prometheus using the following command:

helm install prometheus prometheus-community/prometheus -n monitoring 

to skip creating PV , PVC to be start promenthous without issue related to the limitation of vagarnt file  using minimal-values.yml 

helm upgrade prometheus prometheus-community/prometheus -n monitoring -f minimal-values.yaml



<img width="1902" height="907" alt="image" src="https://github.com/user-attachments/assets/6f1157cc-6c7f-4b03-b748-6a6485cba952" />

## 3-Patch the Prometheus service to NodePort as it is locally only .

<img width="1087" height="145" alt="image" src="https://github.com/user-attachments/assets/0771bd3d-cc55-4bd2-b9c9-cf4887486e67" />


kubectl -n monitoring patch svc prometheus-server -p '{"spec": {"type": "NodePort"}}'

<img width="886" height="73" alt="image" src="https://github.com/user-attachments/assets/c3515d69-fbd3-412d-82cf-1e5933858ee1" />


<img width="1908" height="952" alt="image" src="https://github.com/user-attachments/assets/137baf4e-d598-49b0-99d9-7ed225e1fef8" />


choose 315 ID  → Kubernetes cluster monitoring (via Prometheus)


### Graphana using helm 

helm install grafana grafana/grafana \

   -n monitoring  \

  --set adminUser=admin \

  --set adminPassword='admin' \

<img width="1860" height="583" alt="image" src="https://github.com/user-attachments/assets/6ac41ae0-da8e-414b-8ec7-217018761dfa" />

NodePort (accessible outside the cluster)

kubectl -n monitoring patch svc grafana -p '{"spec": {"type": "NodePort"}}'

kubectl get svc grafana -n monitoring

<img width="813" height="116" alt="image" src="https://github.com/user-attachments/assets/2d49a869-b3d2-44ee-a672-6af544a16bc5" />

<img width="1882" height="1022" alt="image" src="https://github.com/user-attachments/assets/d3d5a525-7a76-46c6-b639-4f0a6e3d1e1b" />

<img width="1886" height="922" alt="image" src="https://github.com/user-attachments/assets/2d7a9a9c-aabc-41a2-aa15-b730dbedb785" />

## Integrate graphana with promethous : 

add user name : admin with password admin and change password to be admin123 

 add details of promethous  

<img width="1915" height="943" alt="image" src="https://github.com/user-attachments/assets/f900748b-6be4-48f3-934a-3570ad6433ff" />

<img width="1872" height="936" alt="image" src="https://github.com/user-attachments/assets/5ee3539a-ba57-45e0-8cb8-33cc977d4562" />


Download Json File for specicifc dashboard and import it 


<img width="1886" height="926" alt="image" src="https://github.com/user-attachments/assets/5368c382-f49b-4ddd-bf65-250cbb281a0a" />

  
  ### Issue :

1- we should make sure from Kube-flannel is instalaltion well to create pods well 

[root@master01 ~]# kubectl get pods -n kube-flannel

NAME                    READY   STATUS    RESTARTS   AGE

kube-flannel-ds-9vkk6   1/1     Running   0          28s

kube-flannel-ds-hxnpz   1/1     Running   0          28s

kube-flannel-ds-zsvck   1/1     Running   0          28s

2- related to the limitation of vagrant to create PV and  PVC  cretae yaml file to skip the issue of cresation promethous 

[root@master01 ~]# cat minimal-values.yaml

server:

  persistentVolume:

    enabled: false

alertmanager:

  persistentVolume:

    enabled: false

pushgateway:

  persistentVolume:

    enabled: false


3- i have issue with flannel cni 

<img width="1907" height="401" alt="image" src="https://github.com/user-attachments/assets/7d56654d-72cd-4fe6-a868-51b92781c55e" />
  
kubectl -n kube-flannel edit cm kube-flannel-cfg

and change CIDR to be matched 192.168.0.0/16 netwrk 

<img width="320" height="190" alt="image" src="https://github.com/user-attachments/assets/bd8d7789-2c40-4902-8c4c-dece18c2952c" />

it is working fine after that 

<img width="1918" height="852" alt="image" src="https://github.com/user-attachments/assets/1b876211-c315-4413-b15b-cb246811d81f" />

4- Error reading Prometheus: PGrafana Pod → 192.168.2.4 (on worker02)

Prometheus Pod → 192.168.1.3 (on worker01)ost "http://localhost:9090/api/v1/query": dial tcp 127.0.0.1:9090: connect: connection refused  

when communicte grrapahan with API of promethous i found the pods can not talk to gether as i can not ping from grapahan pod tp promethouds pod 

delete pods and recreate it automially to be in  the same worker to take the same ip subent as belwo : 

<img width="1897" height="412" alt="image" src="https://github.com/user-attachments/assets/9a677f8a-01da-4caf-96d0-8d8304b10a3a" />


<img width="1517" height="561" alt="image" src="https://github.com/user-attachments/assets/3eb71f54-30b2-4061-8ae2-497507205a86" />








