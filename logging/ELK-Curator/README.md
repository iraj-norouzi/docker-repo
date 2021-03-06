# Bringup ELK Curator stack

    docker stack deploy -c docker-stack-es-curator.yml elk

The above command would launch below containers in docker swarm
Elasticsearch, Logstash, Kibana, X-Pack, Curator, NGINX


This stack exposes the following ports:
    5044: Logstash Beats input.
    5601: Kibana with default X-Pack credentials (user: elastic, password: changeme)
    9200: Elasticsearch with default X-Pack credentials (user: elastic, password: changeme)
    
# Update the Logstash configuration    
The Logstash configuration is stored in config/logstash/logstash.conf
It is configured to receive data by a Beats client on port 5044 without filters. 
You might want to configure this file as per yours requirements

# Update the Curator configuration
The Curator configuration is stored in config/curator/curator.yml and config/curator/actions.yml.
NOTE: Curator is configured to purge documents from logstash-* index that are older than 30 days. You might want to configure 
these files according yours needs.

# NGINX as Elasticsearch proxy
We need DNS Round Robin in ( endpoint_mode: dnsrr ) Swarm service configuration for Elasticsearch to achieve scaling on unicast messages.
Docker can't publish on ingress network (VIP is required). So an NGINX included in front of Elasticsearch as a proxy.

# scale the ELK Stack
This Docker Stack is easly scalable. To scale logstash service, use the below command

    docker service scale elk_logstash=3
follow the same for other services that you want to scale

Elasticsearch data is stored in elasticsearch_data volume on ES node. Before scaling elasticsearch instance, make sure you have enough free nodes. You cannot have two instance of elasticsearch on the same node due to same data volume conflict.
