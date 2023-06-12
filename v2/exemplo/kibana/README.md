### Instalar os pré-requisitos do linux
1) sudo
> apt update<br>
> apt install sudo<br>
> sudo usermod -aG sudo [NOME_DO_USUARIO]

2) ansible (Necessário para roda o playbook)
> apt install ansible

3) vim
> sudo apt install vim<br>
> vim /etc/apt/sources.list (Comentar as linhas CD-ROM)

### Criar os arquivos de configuração
> vim /home/[NOME_DO_USUARIO]/hosts.ini<br>
> vim /home/[NOME_DO_USUARIO]/kibana.yml<br>
> vim /home/[NOME_DO_USUARIO]/playbook_kibana.yml

### Alterar os campos no arquivo kibana.yml
> elasticsearch.hosts

### Executar o playbook como root
> ansible-playbook -i /home/[NOME_DO_USUARIO]/hosts.ini /home/[NOME_DO_USUARIO]/playbook_kibana.yml

### Criar arquivo de inicialização automatico
> vim /etc/init.d/start_kibana.sh