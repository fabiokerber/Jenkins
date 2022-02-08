# Jenkins

Pré requisito:

|Tool    |Link|
|-------------|-----------|
|`Vagrant`| https://releases.hashicorp.com/vagrant/2.2.19/vagrant_2.2.19_x86_64.msi
|`VirtualBox`| https://download.virtualbox.org/virtualbox/6.1.30/VirtualBox-6.1.30-148432-Win.exe


# Pipeline
<kbd>
    <img src="https://github.com/fabiokerber/Jenkins/blob/main/img/040220221000.jpg">
</kbd>
<br />
<br />

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
    alura-jenkins<br>
    "Colar chave pública"<br>
<kbd>
    <img src="https://github.com/fabiokerber/Jenkins/blob/main/img/020220220920.jpg"><br>
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
    Kind: SSH Username with private key (necessário para interagir com repositório Git)<br>
    Scope: Global<br>
    ID: github-ssh<br>
    Description: github-ssh<br>
    Username: git (sempre "git" pois a conexão será realizada por chave SSH)<br>
    Private Key:<br>
    - Enter directly: "Colar chave privada"<br>
    Passphrase: (não necessário pois chave criada sem passphrase)<br>
<kbd>
    <img src="https://github.com/fabiokerber/Jenkins/blob/main/img/020220220945.jpg">
</kbd>
<br />
<br />

# Alterar/Verificar fuso horário<br>
Acessar configurações do usuário.<br>
<kbd>
    <img src="https://github.com/fabiokerber/Jenkins/blob/main/img/020220221125.jpg"><br>
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
    jenkins-todo-list-principal<br>
    (a maioria das interações com o banco será realizada a partir deste job)<br>
<kbd>
    <img src="https://github.com/fabiokerber/Jenkins/blob/main/img/020220220953.jpg">
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
    Consultar frequentemente o repositório se há alguma alteração a cada minuto.<br>
    (aumentar tempo em prd = "crontabguru")<br>
    * * * * *<br>
    <br>
    Marcar Deletar Workspace antes do projeto iniciar.<br>
    Necessário para evitar que haja alguma "sujeira".<br>
    Novo diretório dentro do Jenkins.<br>
<kbd>
    <img src="https://github.com/fabiokerber/Jenkins/blob/main/img/020220221115.jpg"><br>
</kbd>
<br />
<br />
    Job realizado com sucesso<br>
<kbd>
    <img src="https://github.com/fabiokerber/Jenkins/blob/main/img/020220221121.jpg"><br>
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
    Selecionar docker.<br>
    Será listado todos os plugins instalados que podem ser gerenciados pelo Jenkins.<br>
<kbd>
    <img src="https://github.com/fabiokerber/Jenkins/blob/main/img/030220220929.jpg">
</kbd>
<br />
<br />
    Inserido o endereço de localhost e teste realizado com sucesso.<br>
<kbd>
    <img src="https://github.com/fabiokerber/Jenkins/blob/main/img/030220220932.jpg">
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
    Etapa 1. Irá executar um container para checar o Dockerfile.<br>
<kbd>
    <img src="https://github.com/fabiokerber/Jenkins/blob/main/img/030220220948.jpg"><br>
</kbd>
<br />
<br />
    Etapa 2. Fará o build da Imagem conforme o Dockerfile contido na raiz.<br>
    ./ (Dockerfile está contido na raiz do repositório)<br>
<kbd>
    <img src="https://github.com/fabiokerber/Jenkins/blob/main/img/030220220953.jpg"><br>
</kbd>
<br />
<br />
    Etapa 3.<br>
    Nesta etapa será clonado o repositório do GitHub para o Jenkins local.<br>
    Ler o Dockerfile, construir a imagem e vai registrar localmente.<br>
<kbd>
    <img src="https://github.com/fabiokerber/Jenkins/blob/main/img/030220221003.jpg"><br>
</kbd>
<br />
<br />
    Imagem "buildada".<br>
<kbd>
    <img src="https://github.com/fabiokerber/Jenkins/blob/main/img/030220221012.jpg"><br>
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
    [config]<br>
    # Secret configuration<br>
    SECRET_KEY = 'r*5ltfzw-61ksdm41fuul8+hxs$86yo9%k1%k=(!@=-wv4qtyv'<br>
    <br>
    # conf<br>
    DEBUG=True<br>
    <br>
    # Database<br>
    DB_NAME = "todo_dev"<br>
    DB_USER = "devops_dev"<br>
    DB_PASSWORD = "mestre"<br>
    DB_HOST = "localhost"<br>
    DB_PORT = "3306"<br>
<kbd>
    <img src="https://github.com/fabiokerber/Jenkins/blob/main/img/030220221457.jpg"><br>
</kbd>
<br />
<br />
<kbd>
    <img src="https://github.com/fabiokerber/Jenkins/blob/main/img/030220221454.jpg"><br>
</kbd>
<br />
<br />
    [config]<br>
    # Secret configuration<br>
    SECRET_KEY = 'r*5ltfzw-61ksdm41fuul8+hxs$86yo9%k1%k=(!@=-wv4qtyv'<br>
    <br>
    # conf<br>
    DEBUG=True<br>
    <br>
    # Database<br>
    DB_NAME = "todo"<br>
    DB_USER = "devops"<br>
    DB_PASSWORD = "mestre"<br>
    DB_HOST = "localhost"<br>
    DB_PORT = "3306"<br>
<kbd>
    <img src="https://github.com/fabiokerber/Jenkins/blob/main/img/030220221500.jpg"><br>
</kbd>
<br />
<br />

# Configurar o Job para DEV
<kbd>
    <img src="https://github.com/fabiokerber/Jenkins/blob/main/img/030220221608.jpg"><br>
</kbd>
<br />
<br />
    Adicionar mais um step que rodará um container na porta 82 que será de DEV.<br>
    Caso o retorno do test que será executado no container, for diferente de zero, o teste irá falhar.<br>
    <br>
    #!/bin/sh<br>
    # Subindo o container de teste<br>
    docker run -d -p 82:8000 -v /var/run/mysqld/mysqld.sock:/var/run/mysqld/mysqld.sock -v /var/lib/jenkins/workspace/jenkins-todo-list-principal/to_do/.env:/usr/src/app/to_do/.env --name=todo-list-teste django_todolist_image_build<br>
    <br>
    # Testando a imagem<br>
    docker exec -i todo-list-teste python manage.py test --keep<br>
    exit_code=$?<br>
    <br>
    # Derrubando o container velho<br>
    docker rm -f todo-list-teste<br>
    if [ $exit_code -ne 0 ]; then<br>
	    exit 1<br>
    fi<br>
<kbd>
    <img src="https://github.com/fabiokerber/Jenkins/blob/main/img/030220221612.jpg"><br>
</kbd>
<br />
<br />
Executar o job e analisar o resultado.

# Instalar plugin Parameterized Trigger<br>
Plugin para "gestão de parâmetros".<br>
<kbd>
    <img src="https://github.com/fabiokerber/Jenkins/blob/main/img/040220220831.jpg"><br>
</kbd>
<br />
<br />

# Configurar plugin Parameterized Trigger<br>
Este plugin fornece parâmetros para os Jobs que serão executados.<br>
Valor padrão refere-se ao nome que será registrado no DockerHub.<br>
<br>
image<br>
aluracursos/django_todolist_image_build<br>
<kbd>
    <img src="https://github.com/fabiokerber/Jenkins/blob/main/img/040220220846.jpg"><br>
</kbd>
<br />
<br />
Parâmetro definido para que o próximo job execute localmente.<br>
DOCKER_HOST<br>
tcp://127.0.0.1:2376<br>
<kbd>
    <img src="https://github.com/fabiokerber/Jenkins/blob/main/img/040220220848.jpg"><br>
</kbd>
<br />
<br />
Selecione Push image
E adicione as credenciais do DockerHub.
<kbd>
    <img src="https://github.com/fabiokerber/Jenkins/blob/main/img/040220220852.jpg"><br>
</kbd>
<br />
<br />
Neste caso por default (lá no serviço do Docker que foi exposto e está rodando localmente), está setado para enviar a imagem para o DockerHub.<br>
Se configurar para enviar para um registro local, então enviará por padrão para este registro local.<br>
Vantagem de subir para o DockerHub ou Docker Registry, é que será compartilhada essa imagem.<br>
<kbd>
    <img src="https://github.com/fabiokerber/Jenkins/blob/main/img/040220220854.jpg"><br>
</kbd>
<br />
<br />
<kbd>
    <img src="https://github.com/fabiokerber/Jenkins/blob/main/img/040220220858.jpg"><br>
</kbd>
<br />
<br />
Aqui você pode alterar quais parâmetros utilizará para ao executar o job.<br>
<kbd>
    <img src="https://github.com/fabiokerber/Jenkins/blob/main/img/040220220859.jpg"><br>
</kbd>
<br />
<br />

# Configurar Slack no Jenkins<br>
<kbd>
    <img src="https://github.com/fabiokerber/Jenkins/blob/main/img/070220221320.jpg"><br>
</kbd>
<br />
<br />
<kbd>
    <img src="https://github.com/fabiokerber/Jenkins/blob/main/img/070220221324.jpg"><br>
</kbd>
<br />
<br />
<kbd>
    <img src="https://github.com/fabiokerber/Jenkins/blob/main/img/070220221331.jpg"><br>
</kbd>
<br />
<br />
<kbd>
    <img src="https://github.com/fabiokerber/Jenkins/blob/main/img/070220221326.jpg"><br>
</kbd>
<br />
<br />
<kbd>
    <img src="https://github.com/fabiokerber/Jenkins/blob/main/img/070220221329.jpg"><br>
</kbd>
<br />
<br />
<kbd>
    <img src="https://github.com/fabiokerber/Jenkins/blob/main/img/070220221349.jpg"><br>
</kbd>
<br />
<br />
Secret = ID da credencial do token de integração.<br>
<kbd>
    <img src="https://github.com/fabiokerber/Jenkins/blob/main/img/070220221350.jpg"><br>
</kbd>
<br />
<br />
<kbd>
    <img src="https://github.com/fabiokerber/Jenkins/blob/main/img/070220221414.jpg"><br>
</kbd>
<br />
<br />

# Novo Job: todo-list-desenvolvimento:<br>
    # Tipo: Pipeline
    # Este build é parametrizado com 2 Builds de Strings:
        Nome: image
        Valor padrão: - Vazio, pois o valor sera recebido do job anterior.

        Nome: DOCKER_HOST
        Valor padrão: tcp://127.0.0.1:2376

    pipeline {
        environment {
            dockerImage = "${image}"
        }
        agent any

        stages {
            stage('Carregando o ENV de desenvolvimento') {
                steps {
                    configFileProvider([configFile(fileId: '<id do seu arquivo de desenvolvimento>', variable: 'env')]) {
                        sh 'cat $env > .env'
                    }
                }
            }
            stage('Derrubando o container antigo') {
                steps {
                    script {
                        try {
                            sh 'docker rm -f django-todolist-dev'
                        } catch (Exception e) {
                            sh "echo $e"
                        }
                    }
                }
            }        
            stage('Subindo o container novo') {
                steps {
                    script {
                        try {
                            sh 'docker run -d -p 81:8000 -v /var/run/mysqld/mysqld.sock:/var/run/mysqld/mysqld.sock -v /var/lib/jenkins/workspace/jenkins-todo-list-desenvolvimento/.env:/usr/src/app/to_do/.env --name=django-todolist-dev ' + dockerImage + ':latest'
                        } catch (Exception e) {
                            slackSend (color: 'error', message: "[ FALHA ] Não foi possivel subir o container - ${BUILD_URL} em ${currentBuild.duration}s", tokenCredentialId: 'slack-token')
                            sh "echo $e"
                            currentBuild.result = 'ABORTED'
                            error('Erro')
                        }
                    }
                }
            }
            stage('Notificando o usuario') {
                steps {
                    slackSend (color: 'good', message: '[ Sucesso ] O novo build esta disponivel em: http://192.168.33.10:81/ ', tokenCredentialId: 'slack-token')
                }
            }
        }
    }

# Encadeando Job's dentro de um projeto (freestyle)<br>
<kbd>
    <img src="https://github.com/fabiokerber/Jenkins/blob/main/img/070220221146.jpg"><br>
</kbd>
<br />
<br />
<kbd>
    <img src="https://github.com/fabiokerber/Jenkins/blob/main/img/080220221150.jpg"><br>
</kbd>
<br />
<br />
Setado o job e inserido qual parâmetro será informado assim que iniciar a execução do próximo job neste projeto.<br>
Save!<br>
<kbd>
    <img src="https://github.com/fabiokerber/Jenkins/blob/main/img/080220221156.jpg"><br>
</kbd>
<br />
<br />
O parâmetro "Image" está sendo referenciado desta string informada mais acima neste projeto.
<kbd>
    <img src="https://github.com/fabiokerber/Jenkins/blob/main/img/080220221155.jpg"><br>
</kbd>
<br />
<br />

# Sonarqube<br>

```
# Comando para rodar o Sonarqube em container:
    > cd .\Jenkins\install
    > vagrant ssh
    > docker run -d --name sonarqube -p 9000:9000 sonarqube:lts
```
Acessar: http://192.168.33.10:9000<br>
admin | admin <br>
Novo projeto.<br>
<kbd>
    <img src="https://github.com/fabiokerber/Jenkins/blob/main/img/080220221534.jpg"><br>
</kbd>
<br />
<br />
<kbd>
    <img src="https://github.com/fabiokerber/Jenkins/blob/main/img/080220221535.jpg"><br>
</kbd>
<br />
<br />
<kbd>
    <img src="https://github.com/fabiokerber/Jenkins/blob/main/img/080220221536.jpg"><br>
</kbd>
<br />
<br />
<kbd>
    <img src="https://github.com/fabiokerber/Jenkins/blob/main/img/080220221538.jpg"><br>
</kbd>
<br />
<br />
Anotar o token e comandos de scan do sonar.
<kbd>
    <img src="https://github.com/fabiokerber/Jenkins/blob/main/img/080220221544.jpg"><br>
</kbd>
<br />
<br />
Criando novo projeto no Jenkins
<kbd>
    <img src="https://github.com/fabiokerber/Jenkins/blob/main/img/080220221551.jpg"><br>
</kbd>
<br />
<br />
Repository URL: https://github.com/fabiokerber/jenkins-todo-list.git<br>
<kbd>
    <img src="https://github.com/fabiokerber/Jenkins/blob/main/img/080220221555.jpg"><br>
</kbd>
<br />
<br />
<kbd>
    <img src="https://github.com/fabiokerber/Jenkins/blob/main/img/080220221557.jpg"><br>
</kbd>
<br />
<br />
<kbd>
    <img src="https://github.com/fabiokerber/Jenkins/blob/main/img/080220221558.jpg"><br>
</kbd>
<br />
<br />
```
#!/bin/bash
# Baixando o Sonarqube
wget https://s3.amazonaws.com/caelum-online-public/1110-jenkins/05/sonar-scanner-cli-3.3.0.1492-linux.zip
#
# Descompactando o scanner
unzip sonar-scanner-cli-3.3.0.1492-linux.zip
#
# Rodando o Scanner
./sonar-scanner-3.3.0.1492-linux/bin/sonar-scanner   -X \
-Dsonar.projectKey=jenkins-todolist \
-Dsonar.sources=. \
-Dsonar.host.url=http://192.168.33.10:9000 \
-Dsonar.login=<seu token> (alterar para o token que foi criado no sonarqube!)
```
<kbd>
    <img src="https://github.com/fabiokerber/Jenkins/blob/main/img/080220221606.jpg"><br>
</kbd>
<br />
<br />
<kbd>
    <img src="https://github.com/fabiokerber/Jenkins/blob/main/img/080220221608.jpg"><br>
</kbd>
<br />
<br />
Acessar: http://192.168.33.10:9000<br>
Ao rodar o projeto no Jenkins, o sonarqube checa se há inconsistências, bugs ou vulnerabilidades no código.
<kbd>
    <img src="https://github.com/fabiokerber/Jenkins/blob/main/img/080220221610.jpg"><br>
</kbd>
<br />
<br />
<kbd>
    <img src="https://github.com/fabiokerber/Jenkins/blob/main/img/080220221613.jpg"><br>
</kbd>
<br />
<br />