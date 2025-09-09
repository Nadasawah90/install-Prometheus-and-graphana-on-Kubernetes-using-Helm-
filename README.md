# install-Prometheus-and-graphana-on-Kubernetes-using-Helm-
## Helm 
is the package manager for Kubernetes.
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
We can create our own dashboards or use the existing ones provided by Grafana. We can personalize the dashboards as per our requirements.
+-------------------+       +-------------------+
| Node Exporter     |       | Node Exporter     |
| (on Worker Node1) |  ...  | (on Worker NodeN) |
+---------+---------+       +---------+---------+
          |                           |
          +---------------------------+
                      |
                      v
             +-----------------+
             |                 |
             | Prometheus      |
             |   Server        |
             | (scrapes data)  |
             +---+---------+---+
                 |         |
     ------------+         +------------
     |                                |
     v                                v
+------------+                 +--------------+
| Kube-State |                 | Pushgateway  |
|  Metrics   |                 | (batch jobs) |
+------------+                 +--------------+

                 |
                 v
        +------------------+
        |   Alertmanager   |
        | (sends alerts)   |
        +------------------+

                 |
                 v
        +------------------+
        |   Notification   |
        | Email / Slack    |
        +------------------+

                 |
                 v
        +------------------+
        |     Grafana      |
        | (dashboards)     |
        +------------------+

### prerequistes :

1- install Helm using this link using this link >>  https://helm.sh/docs/intro/install/

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

2- Download repositorires for promethous 

helm repo add prometheus-community https://prometheus-community.github.io/helm-charts

helm repo add grafana https://grafana.github.io/helm-charts

helm repo update

<img width="917" height="230" alt="image" src="https://github.com/user-attachments/assets/3848ec8a-8217-4cd5-9f6c-8c0c868b7763" />

now , we need to install Prometheus using the following command:

helm install prometheus prometheus-community/prometheus

<img width="1902" height="907" alt="image" src="https://github.com/user-attachments/assets/6f1157cc-6c7f-4b03-b748-6a6485cba952" />

We can expose the prometheus-server service to the internet using nodeport but the GUI provided by prometheus is not as good as the one provided by Grafana. We can use the following command for the same:

kubectl expose service prometheus-server — type=NodePort — target-port=9090 — name=prometheus-server-ext

minikube service prometheus-server-ext





