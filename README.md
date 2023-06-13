## Elasticsearch

### Criando e configurando as vm's
1) Crie 3 vm usando a distribuição [debian](https://www.debian.org/CD/http-ftp/) com os seguintes nomes:
- elasticmaster01
- elasticmaster02
- elasticmaster03

2) Em cada uma das vm instale os pré-requisitos abaixo:
* sudo
```
apt update
apt install sudo
sudo usermod -aG sudo [NOME_DO_USUARIO]
```
* ansible (Necessário para roda o playbook)
```
apt install ansible
```

* vim
```
sudo apt install vim
```

3) Abra o arquivo sources.list e comente todas as linhas que tiverem a palavra "CD-ROM"
```
vim /etc/apt/sources.list
```

4) Obtenha o ip de cada vm
```
ip addr show
```

5) Configure o arquivo de hosts para cada vm
```
[DIGITE_O_IP] elasticmaster01
[DIGITE_O_IP] elasticmaster02
[DIGITE_O_IP] elasticmaster03
```

6) Verifique se as configurações estão funcionado
```
ping elasticmaster01
ping elasticmaster02
ping elasticmaster03
```

### Criando e configurando os arquivos de configuração do elasticsearch para cada vm

1) Arquivo "hosts.ini"
* Crie o arquivo
```
vim /home/[NOME_DO_USUARIO]/hosts.ini
```
* Adicione o texto abaixo
```
[meuservidores]
localhost ansible_connection=local
```

2) Arquivo "elasticsearch.yml"
* Crie o arquivo
```
vim /home/[NOME_DO_USUARIO]/elasticsearch.yml
```
* Adicione o texto abaixo
```
cluster.name: clr_elasticsearch
node.name: elasticmaster01
path.data: /var/lib/elasticsearch
path.logs: /var/log/elasticsearch
network.host: 0.0.0.0
http.port: 9200
discovery.seed_hosts: ["127.0.0.1", "192.168.254.142", "192.168.254.143"]
cluster.initial_master_nodes: ["elasticmaster01", "elasticmaster02", "elasticmaster03"]
```

2) Arquivo "playbook_elasticsearch.yml"
* Crie o arquivo
```
vim /home/[NOME_DO_USUARIO]/playbook_elasticsearch.yml
```
* Adicione o texto abaixo
```
---
- name: Instalar e configurar o Elasticsearch
  hosts: localhost
  become: true

  tasks:
    - name: Instalar pacote gnupg
      apt:
        name: gnupg
        state: present
  
    - name: Importar chave GPG do Elasticsearch
      apt_key:
        url: https://artifacts.elastic.co/GPG-KEY-elasticsearch
        state: present

    - name: Adicionar repositório do Elasticsearch
      apt_repository:
        repo: deb https://artifacts.elastic.co/packages/7.x/apt stable main
        state: present
        filename: "elastic-7.x.list"
        update_cache: yes

    - name: Atualizar cache do APT
      apt:
        update_cache: yes

    - name: Instalar o Elasticsearch
      apt:
        name: elasticsearch
        state: present

    - name: Copiar arquivo de configuração
      template:
        src: elasticsearch.yml
        dest: /etc/elasticsearch/elasticsearch.yml
        owner: root
        group: root
        mode: 0644
      notify: Reiniciar serviço Elasticsearch

  handlers:
    - name: Reiniciar serviço Elasticsearch
      service:
        name: elasticsearch
        state: restarted
```


- [logstash](https://github.com/thiagoautran/logstash.elasticsearch.kibana.playbook/blob/main/v2/exemplo/logstash/README.md)
- [elasticsearch_master_01](https://github.com/thiagoautran/logstash.elasticsearch.kibana.playbook/blob/main/v2/exemplo/elastic_mater_01/README.md)
- [elasticsearch_master_02](https://github.com/thiagoautran/logstash.elasticsearch.kibana.playbook/blob/main/v2/exemplo/elastic_mater_02/README.md)
- [elasticsearch_master_03](https://github.com/thiagoautran/logstash.elasticsearch.kibana.playbook/blob/main/v2/exemplo/elastic_mater_03/README.md)
- [kibana](https://github.com/thiagoautran/logstash.elasticsearch.kibana.playbook/blob/main/v2/exemplo/kibana/README.md)