# Jenkins

Pré requisito:

|Tool    |Link|
|-------------|-----------|
|`Vagrant`| https://releases.hashicorp.com/vagrant/2.2.19/vagrant_2.2.19_x86_64.msi
|`VirtualBox`| https://download.virtualbox.org/virtualbox/6.1.30/VirtualBox-6.1.30-148432-Win.exe


# Preparação ambiente
```
# Subindo o ambiente virtualizado
    > cd .\Jenkins\install
    > vagrant plugin install vagrant-disksize (permite alteração de tamanho do disco à ser criado)
    > vagrant up
    > vagrant reload
    > vagrant ssh
        $ ps -ef | grep -i mysql (Verificando se o MySQL esta rodando)
        $ mysql -u devops -p (Senha mestre) 
            show databases
        $ mysql -u devops_dev -p (Senha mestre) 
            show databases
    > vagrant ssh -c 'sudo cat /var/lib/jenkins/secrets/initialAdminPassword' (anotar!)

# Acessar: 192.168.33.10:8080
    "Install suggested plugins"

# Credenciais
    Nome de usuário: alura
    Senha: mestre123
    Nome completo: Jenkins Alura
    Email: aluno@alura.com.br
```
<br />

# Criar par de chaves VM Jenkins
    > cd .\Jenkins\install
    > vagrant ssh
        $ cp -R /vagrant/jenkins-todo-list ~
        $ ssh-keygen -t rsa -b 4096 -C "fabio.kerber@gmail.com" (conta Github) (Enter 3x)
        $ cat ~/.ssh/id_rsa.pub (anotar chave pública!)
        $ cat ~/.ssh/id_rsa (anotar chave privada!)
        $ git config --global user.name "fabiokerber"
        $ git config --global user.email "fabio.kerber@gmail.com"

# Configurar a chave no github
<kbd>
    <img src="https://github.com/fabiokerber/Jenkins/blob/main/img/020220220913.jpg">
</kbd>
<br />
<br />
<kbd>
    <img src="https://github.com/fabiokerber/Jenkins/blob/main/img/020220220914.jpg">
</kbd>
<br />
<br />
<kbd>
    <img src="https://github.com/fabiokerber/Jenkins/blob/main/img/020220220916.jpg">
</kbd>
<br />
<br />
<kbd>
    <img src="https://github.com/fabiokerber/Jenkins/blob/main/img/020220220920.jpg"><br>
    alura-jenkins<br>
    "Colar chave pública"<br>
</kbd>
<br />
<br />

# Testar chave pública
        $ ssh -T git@github.com (yes)

# Versionar código e primeiro commit
        $ cd ~/jenkins-todo-list/
        $ git init
        $ git add .
        $ git commit -m "Meu primeiro commit"
        $ git log

# Criar um repositório no github: jenkins-todo-list e envio ao Github
<kbd>
    <img src="https://github.com/fabiokerber/Jenkins/blob/main/img/020220220929.jpg">
    jenkins-todo-list
</kbd>
<br />
<br />

        $ cd ~/jenkins-todo-list/ && git remote add origin git@github.com:fabiokerber/jenkins-todo-list.git
        $ cd ~/jenkins-todo-list/ && git push -u origin master

# Adicionar Credencial ao Jenkins
<kbd>
    <img src="https://github.com/fabiokerber/Jenkins/blob/main/img/020220220841.jpg">
</kbd>
<br />
<br />
<kbd>
    <img src="https://github.com/fabiokerber/Jenkins/blob/main/img/020220220945.jpg">
    Kind: SSH Username with private key (necessário para interagir com repositório Git)<br>
    Scope: Global<br>
    ID: github-ssh<br>
    Description: github-ssh<br>
    Username: git (sempre "git" pois a conexão será realizada por chave SSH)<br>
    Private Key:<br>
    - Enter directly: "Colar chave privada"<br>
    Passphrase: (não necessário pois chave criada sem passphrase)<br>
</kbd>
<br />
<br />

# Alterar/Verificar fuso horário
<kbd>
    <img src="https://github.com/fabiokerber/Jenkins/blob/main/img/020220221125.jpg"><br>
    Acessar configurações do usuário.
</kbd>
<br />
<br />
<kbd>
    <img src="https://github.com/fabiokerber/Jenkins/blob/main/img/020220221126.jpg">
</kbd>
<br />
<br />

# Novo Job Jenkins
<kbd>
    <img src="https://github.com/fabiokerber/Jenkins/blob/main/img/020220220952.jpg">
</kbd>
<br />
<br />
<kbd>
    <img src="https://github.com/fabiokerber/Jenkins/blob/main/img/020220220953.jpg">
    jenkins-todo-list-principal<br>
    (a maioria das interações com o banco será realizada a partir deste job)
</kbd>
<br />
<br />
<kbd>
    <img src="https://github.com/fabiokerber/Jenkins/blob/main/img/020220221108.jpg">
</kbd>
<br />
<br />
<kbd>
    <img src="https://github.com/fabiokerber/Jenkins/blob/main/img/020220221110.jpg">
</kbd>
<br />
<br />
<kbd>
    <img src="https://github.com/fabiokerber/Jenkins/blob/main/img/020220221115.jpg"><br>
    Consultar frequentemente o repositório se há alguma alteração a cada minuto
    (aumentar tempo em prd = "crontabguru")<br>
    * * * * *<br>
    --------------<br>
    Marcar Deletar Workspace antes do projeto iniciar.<br>
    Necessário para evitar que haja alguma "sujeira".<br>
    Novo diretório dentro do Jenkins.<br>
</kbd>
<br />
<br />
<kbd>
    <img src="https://github.com/fabiokerber/Jenkins/blob/main/img/020220221121.jpg"><br>
    Job realizado com sucesso
</kbd>
<br />
<br />
<kbd>
    <img src="https://github.com/fabiokerber/Jenkins/blob/main/img/020220221123.jpg">
</kbd>
<br />
<br />

# Habilitar Daemon do Docker para que seja controlado remotamente (não vem habilitado por padrão)
Serviço será inicializado sendo "exposto" na porta 2376 desta VM, então o Jenkins vai poder controlar esse docker remotamente (de qualquer máquina da rede).<br>
O plugin do Docker dentro do Jenkins vai poder executar os comandos para inserir toda a aplicação dentro de um container.<br>
```
    $ sudo mkdir -p /etc/systemd/system/docker.service.d/
    $ sudo cp /vagrant/configs/override.conf /etc/systemd/system/docker.service.d/
    $ sudo systemctl daemon-reload
    $ sudo systemctl restart docker.service
```

# Instalação do plugin Docker
Com a instalação deste plugin o Jenkins pode gerenciar o Docker em qualquer servidor da rede, mas neste caso irá gerenciar localmente.<br>
<kbd>
    <img src="https://github.com/fabiokerber/Jenkins/blob/main/img/030220220919.jpg">
</kbd>
<br />
<br />
<kbd>
    <img src="https://github.com/fabiokerber/Jenkins/blob/main/img/030220220920.jpg">
</kbd>
<br />
<br />

# Configurar o plugin Docker
<kbd>
    <img src="https://github.com/fabiokerber/Jenkins/blob/main/img/030220220927.jpg">
</kbd>
<br />
<br />
<kbd>
    <img src="https://github.com/fabiokerber/Jenkins/blob/main/img/030220220929.jpg">
    Selecionar docker.<br>
    Será listado todos os plugins instalados que podem ser gerenciados pelo Jenkins.<br>
</kbd>
<br />
<br />
<kbd>
    <img src="https://github.com/fabiokerber/Jenkins/blob/main/img/030220220932.jpg">
    Inserido o endereço de localhost e teste realizado com sucesso.<br>
</kbd>
<br />
<br />

# Ajuste de Job para Build da imagem Docker
    Neste job podem ser adicionadas quantas etapas for necessário.
    Etapa 1 - Checagem rápida se o Dockerfile está de acordo com as convenções de estrutura do arquivo. Linter do Dockerfile.
    Etapa 2 - Build da imagem docker.
    Etapa 3 - Execução do Job.
<kbd>
    <img src="https://github.com/fabiokerber/Jenkins/blob/main/img/030220220940.jpg"><br>
</kbd>
<br />
<br />
<kbd>
    <img src="https://github.com/fabiokerber/Jenkins/blob/main/img/030220220940.jpg"><br>
</kbd>
<br />
<br />
<kbd>
    <img src="https://github.com/fabiokerber/Jenkins/blob/main/img/030220220944.jpg"><br>
</kbd>
<br />
<br />
<kbd>
    <img src="https://github.com/fabiokerber/Jenkins/blob/main/img/030220220945.jpg"><br>
    Etapa 1.
</kbd>
<br />
<br />
<kbd>
    <img src="https://github.com/fabiokerber/Jenkins/blob/main/img/030220220948.jpg"><br>
    Etapa 1. Irá executar um container para checar o Dockerfile
</kbd>
<br />
<br />
<kbd>
    <img src="https://github.com/fabiokerber/Jenkins/blob/main/img/030220220953.jpg"><br>
    Etapa 2. Fará o build da Imagem conforme o Dockerfile contido na raiz.<br>
    ./ (Dockerfile está contido na raiz do repositório)
</kbd>
<br />
<br />
<kbd>
    <img src="https://github.com/fabiokerber/Jenkins/blob/main/img/030220221003.jpg"><br>
    Etapa 3.<br>
    Nesta etapa será clonado o repositório do GitHub para o Jenkins local.<br>
    Ler o Dockerfile, construir a imagem e vai registrar localmente.
</kbd>
<br />
<br />
<kbd>
    <img src="https://github.com/fabiokerber/Jenkins/blob/main/img/030220221012.jpg"><br>
    Imagem "buildada".
</kbd>
<br />
<br />

# Instalar plugin Config File Provider
<kbd>
    <img src="https://github.com/fabiokerber/Jenkins/blob/main/img/030220221438.jpg"><br>
</kbd>
<br />
<br />
<kbd>
    <img src="https://github.com/fabiokerber/Jenkins/blob/main/img/030220221444.jpg"><br>
</kbd>
<br />
<br />
<kbd>
    <img src="https://github.com/fabiokerber/Jenkins/blob/main/img/030220221451.jpg"><br>
</kbd>
<br />
<br />

# Configurar plugin Config File Provider
<kbd>
    <img src="https://github.com/fabiokerber/Jenkins/blob/main/img/030220221453.jpg"><br>
</kbd>
<br />
<br />
<kbd>
    <img src="https://github.com/fabiokerber/Jenkins/blob/main/img/030220221454.jpg"><br>
</kbd>
<br />
<br />
<kbd>
    <img src="https://github.com/fabiokerber/Jenkins/blob/main/img/030220221455.jpg"><br>
</kbd>
<br />
<br />
<kbd>
    <img src="https://github.com/fabiokerber/Jenkins/blob/main/img/030220221457.jpg"><br>
    [config]<br>
    # Secret configuration<br>
    SECRET_KEY = 'r*5ltfzw-61ksdm41fuul8+hxs$86yo9%k1%k=(!@=-wv4qtyv'<br>
    # conf<br>
    DEBUG=True<br>
    # Database<br>
    DB_NAME = "todo_dev"<br>
    DB_USER = "devops_dev"<br>
    DB_PASSWORD = "mestre"<br>
    DB_HOST = "localhost"<br>
    DB_PORT = "3306"<br>
</kbd>
<br />
<br />
<kbd>
    <img src="https://github.com/fabiokerber/Jenkins/blob/main/img/030220221454.jpg"><br>
</kbd>
<br />
<br />
<kbd>
    <img src="https://github.com/fabiokerber/Jenkins/blob/main/img/030220221500.jpg"><br>
    [config]<br>
    # Secret configuration<br>
    SECRET_KEY = 'r*5ltfzw-61ksdm41fuul8+hxs$86yo9%k1%k=(!@=-wv4qtyv'<br>
    # conf<br>
    DEBUG=True<br>
    # Database<br>
    DB_NAME = "todo"<br>
    DB_USER = "devops"<br>
    DB_PASSWORD = "mestre"<br>
    DB_HOST = "localhost"<br>
    DB_PORT = "3306"<br>
</kbd>
<br />
<br />

# Configurar o Job para DEV
<kbd>
    <img src="https://github.com/fabiokerber/Jenkins/blob/main/img/030220221608.jpg"><br>
</kbd>
<br />
<br />
<kbd>
    <img src="https://github.com/fabiokerber/Jenkins/blob/main/img/030220221612.jpg"><br>
    Adicionar mais um step que rodará um container na porta 82 que será de DEV.<br>
    #!/bin/sh
    # Subindo o container de teste
    docker run -d -p 82:8000 -v /var/run/mysqld/mysqld.sock:/var/run/mysqld/mysqld.sock -v /var/lib/jenkins/workspace/jenkins-todo-list-principal/to_do/.env:/usr/src/app/to_do/.env --name=todo-list-teste django_todolist_image_build
    # Testando a imagem
    docker exec -i todo-list-teste python manage.py test --keep
    exit_code=$?
    # Derrubando o container velho
    docker rm -f todo-list-teste
    if [ $exit_code -ne 0 ]; then
	    exit 1
    fi
</kbd>
<br />
<br />