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

# Build manual da aplicação (DEV)
Perceber a diferença entre build manual e automatizado.<br>
```
> cd .\Jenkins\install
> vagrant ssh
    $ cd ~/jenkins-todo-list/to_do/
    $ vi .env
        [config]
        # Secret configuration
        SECRET_KEY = 'r*5ltfzw-61ksdm41fuul8+hxs$86yo9%k1%k=(!@=-wv4qtyv' (utilizado por exemplo para os hashs de senha)

        # conf
        DEBUG=True

        # Database
        DB_NAME = "todo_dev"
        DB_USER = "devops_dev"
        DB_PASSWORD = "mestre"
        DB_HOST = "localhost"
        DB_PORT = "3306"
```

```
    $ sudo pip3 install virtualenv nose coverage nosexcover pylint
```

# Isolar a aplicação e ativar o virtualenv
```
    $ cd ~/jenkins-todo-list/    
    $ virtualenv --always-copy venv-django-todolist
    $ source venv-django-todolist/bin/activate
    $ cat requirements.txt (visualiza todas as dependencias a serem instaladas com o proximo comando)
    $ pip install -r requirements.txt
```

# Criando tabelas do banco de dados e ajustando permissões
```
    $ python manage.py makemigrations   
    $ python manage.py migrate
```

# Criando superusuario para o Django
```
    $ python manage.py createsuperuser
        Username : alura
        Email address: aluno@alura.com.br
        Password: mestre123
```

# Rodar aplicação
```
    $ python manage.py runserver 0:8000
http://192.168.33.10:8000 ( alura | mestre123 )
    $ CTRL+C (parar aplicação)
```
<kbd>
    <img src="https://github.com/fabiokerber/Jenkins/blob/main/img/020220221702.jpg">
</kbd>
<br />
<br />

# Build manual da aplicação (PRD)
```
> cd .\Jenkins\install
> vagrant ssh
    $ cd ~/jenkins-todo-list/to_do/
    $ vi .env
        [config]
        # Secret configuration
        SECRET_KEY = 'r*5ltfzw-61ksdm41fuul8+hxs$86yo9%k1%k=(!@=-wv4qtyv'

        # conf
        DEBUG=True

        # Database
        DB_NAME = "todo"
        DB_USER = "devops"
        DB_PASSWORD = "mestre"
        DB_HOST = "localhost"
        DB_PORT = "3306"
```

# Criando tabelas do banco de dados e ajustando permissões, agora para PRD
```
    $ python manage.py migrate
```

# Criando superusuario para o Django (PRD)
```
    $ python manage.py createsuperuser
        Username : alura
        Email address: aluno@alura.com.br
        Password: mestre123
```

# Rodar aplicação
```
    $ python manage.py runserver 0:8000
http://192.168.33.10:8000 ( alura | mestre123 )
    $ CTRL+C (parar aplicação)
```

# Habilitar Daemon do Docker para que seja controlado remotamente (não vem habilitado por padrão)
Serviço será inicializado sendo "exposto" na porta 2376 desta VM, então o Jenkins vai poder controlar esse docker remotamente (de qualquer máquina da rede).<br>
O plugin do Docker dentro do Jenkins vai poder executar os comandos para inserir toda a aplicação dentro de um container.<br>
```
    $ sudo mkdir -p /etc/systemd/system/docker.service.d/
    $ sudo cp /vagrant/configs/override.conf /etc/systemd/system/docker.service.d/
    $ sudo systemctl daemon-reload
    $ sudo systemctl restart docker.service
```

# Instalação de plugin
Com a instalação deste plugin o Jenkins pode gerenciar o Docker em qualquer servidor da rede, mas neste caso irá gerenciar localmente.<br>
<kbd>
    <img src="https://github.com/fabiokerber/Jenkins/blob/main/img/030220220915.jpg">
</kbd>
<br />
<br />
<kbd>
    <img src="https://github.com/fabiokerber/Jenkins/blob/main/img/030220220916.jpg">
</kbd>
<br />
<br />
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

# IConfigurar o plugin
<kbd>
    <img src="https://github.com/fabiokerber/Jenkins/blob/main/img/030220220915.jpg">
</kbd>
<br />
<br />
<kbd>
    <img src="https://github.com/fabiokerber/Jenkins/blob/main/img/030220220927.jpg">
</kbd>
<br />
<br />
<kbd>
    <img src="https://github.com/fabiokerber/Jenkins/blob/main/img/030220220928.jpg">
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