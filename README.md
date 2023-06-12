## Elasticsearch

1) Crie 3 vm usando a distribuição [debian](https://www.debian.org/CD/http-ftp/) com os seguintes nomes:
- elastic_master_01
- elastic_master_02
- elastic_master_03

2) Em cada uma das vm instale os pré-requisitos abaixo:
* sudo
```
> apt update<br>
> apt install sudo<br>
> sudo usermod -aG sudo [NOME_DO_USUARIO]
```
* ansible (Necessário para roda o playbook)
> apt install ansible

- [logstash](https://github.com/thiagoautran/logstash.elasticsearch.kibana.playbook/blob/main/v2/exemplo/logstash/README.md)
- [elasticsearch_master_01](https://github.com/thiagoautran/logstash.elasticsearch.kibana.playbook/blob/main/v2/exemplo/elastic_mater_01/README.md)
- [elasticsearch_master_02](https://github.com/thiagoautran/logstash.elasticsearch.kibana.playbook/blob/main/v2/exemplo/elastic_mater_02/README.md)
- [elasticsearch_master_03](https://github.com/thiagoautran/logstash.elasticsearch.kibana.playbook/blob/main/v2/exemplo/elastic_mater_03/README.md)
- [kibana](https://github.com/thiagoautran/logstash.elasticsearch.kibana.playbook/blob/main/v2/exemplo/kibana/README.md)