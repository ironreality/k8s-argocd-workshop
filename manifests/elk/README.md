# ELK in Kubernetes

These are some Kubernetes manifests to help you stand up an ELK (Elasticsearch, Logstash, and Kibana (with Grafana too)) inside your Kubernetes cluster.

More documentation coming soon...


... Yes I know I'm missing Logstash, Filebeat, etc.  Will be adding those in soon. Stay tuned!

## Usage
Apply all the things - `kubectl apply -f ./` 

## Services
Append the following to your cluster endpoint to access each part 
* `/elk/elasticsearch` - Kibana data node (port 9200) endpoint 
* `/elk/kibana` - Kibana web UI
* `/elk/grafana` - Grafana web UI

example (assuming your using [KIAB](https://github.com/nickmaccarthy/kube-in-a-box) ), you can access Kibana at `http://kiab.local/elk/kibana` in your web browser on your local machine.  

## Requirements 
* Kubernetes (obsiously) - check out [KIAB](https://github.com/nickmaccarthy/kube-in-a-box) to get a cluster up and running quickly
* Needs an ingress controller such as [Traefik](http://traefik.io) (built into KIAB)  

