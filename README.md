# Elasticsearch

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
cluster.name: [NOME_DO_CLUSTER]
node.name: [NOME_DA_VM]
path.data: /var/lib/elasticsearch
path.logs: /var/log/elasticsearch
network.host: 0.0.0.0
http.port: 9200
discovery.seed_hosts: ["[IP_VM_ELASTICMASTER01]", "[IP_VM_ELASTICMASTER02]", "[IP_VM_ELASTICMASTER03]"]
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

3) Instalando elasticsearch
```
ansible-playbook -i /home/[NOME_DO_USUARIO]/hosts.ini /home/[NOME_DO_USUARIO]/playbook_elasticsearch.yml
```

4) Arquivo "start_elasticsearch.sh"
* Crie o arquivo
```
vim /etc/init.d/start_elasticsearch.sh
```
* Adicione o texto abaixo
```
sudo systemctl enable elasticsearch
```

# Kibana
1) Crie 1 vm usando a distribuição [debian](https://www.debian.org/CD/http-ftp/) com os seguintes nome:
- kibana

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

### Criando e configurando os arquivos de configuração do kibana para cada vm

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

2) Arquivo "kibana.yml"
* Crie o arquivo
```
vim /home/[NOME_DO_USUARIO]/kibana.yml
```
* Adicione o texto abaixo
```
server.port: 5601
server.host: "0.0.0.0"
elasticsearch.hosts: ["http://[IP_VM_ELASTICMASTER01]:9200"]
```

2) Arquivo "playbook_kibana.yml"
* Crie o arquivo
```
vim /home/[NOME_DO_USUARIO]/playbook_kibana.yml
```
* Adicione o texto abaixo
```
---
- name: Instalar e configurar o Kibana
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

    - name: Instalar o Kibana
      apt:
        name: kibana
        state: present

    - name: Copiar arquivo de configuração
      template:
        src: kibana.yml
        dest: /etc/kibana/kibana.yml
        owner: root
        group: root
        mode: 0644
      notify: Reiniciar serviço Kibana

  handlers:
    - name: Reiniciar serviço Kibana
      service:
        name: kibana
        state: restarted
```

3) Instalando kibana
```
ansible-playbook -i /home/[NOME_DO_USUARIO]/hosts.ini /home/[NOME_DO_USUARIO]/playbook_kibana.yml
```

4) Arquivo "start_kibana.sh"
* Crie o arquivo
```
vim /etc/init.d/start_kibana.sh
```
* Adicione o texto abaixo
```
sudo systemctl enable kibana
```

5) Consultando o elasticsearch
![kibana_01](https://github.com/thiagoautran/logstash.elasticsearch.kibana.playbook/blob/main/files/kibana_01.png)
![kibana_02](https://github.com/thiagoautran/logstash.elasticsearch.kibana.playbook/blob/main/files/kibana_02.png)

# Logstash
1) Crie 1 vm usando a distribuição [debian](https://www.debian.org/CD/http-ftp/) com os seguintes nome:
- logstash

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

### Criando e configurando os arquivos de configuração do kibana para cada vm

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

2) Arquivo "logstash.conf"
* Crie o arquivo
```
vim /home/[NOME_DO_USUARIO]/logstash.conf
```
* Adicione o texto abaixo
```
input {
  file {
    type => "log"
    path => "/path/to/logs/*"
    start_position => "beginning"
  }
}

filter {
}

output {
  elasticsearch {
    hosts => ["[IP_VM_ELASTICMASTER01]:9200"]
    index => "logs-%{+YYYY.MM.dd}"
  }
  stdout {}
}
```

2) Arquivo "playbook_logstash.yml"
* Crie o arquivo
```
vim /home/[NOME_DO_USUARIO]/playbook_logstash.yml
```
* Adicione o texto abaixo
```
---
- name: Instalar e configurar o Logstash
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

    - name: Instalar o Logstash
      apt:
        name: logstash
        state: present

    - name: Copiar arquivo de configuração
      template:
        src: logstash.conf
        dest: /etc/logstash/conf.d/logstash.conf
        owner: root
        group: root
        mode: 0644
      notify: Reiniciar serviço Logstash

  handlers:
    - name: Reiniciar serviço Logstash
      service:
        name: logstash
        state: restarted
```

3) Instalando kibana
```
ansible-playbook -i /home/[NOME_DO_USUARIO]/hosts.ini /home/[NOME_DO_USUARIO]/playbook_logstash.yml
```

4) Arquivo "start_logstash.sh"
* Crie o arquivo
```
vim /etc/init.d/start_logstash.sh
```
* Adicione o texto abaixo
```
sudo systemctl enable logstash
```
