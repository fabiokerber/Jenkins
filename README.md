# Jenkins

Pré requisito:

|Tool    |Link|
|-------------|-----------|
|`Vagrant`| https://releases.hashicorp.com/vagrant/2.2.19/vagrant_2.2.19_x86_64.msi
|`VirtualBox`| https://download.virtualbox.org/virtualbox/6.1.30/VirtualBox-6.1.30-148432-Win.exe


**Preparação ambiente**<br>
```
# Subindo o ambiente virtualizado
    > cd .\Jenkins\install
    > vagrant plugin install vagrant-disksize (permite alteração de tamanho do disco à ser criado)
    > vagrant up
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

**Conexão VM Jenkins > Github e Criação Repo Lab**<br>

# Criar par de chaves VM Jenkins
    > cd .\Jenkins\install
    > vagrant ssh
        $ cp -R /vagrant/jenkins-todo-list ~
        $ ssh-keygen -t rsa -b 4096 -C "fabio.kerber@gmail.com" (conta Github) (Enter 3x)
        $ cat ~/.ssh/id_rsa.pub (anotar!)
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
    <img src="https://github.com/fabiokerber/Jenkins/blob/main/img/020220220920.jpg">
    alura-jenkins
    "Colar chave pública"
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
<br />

**Criação de usuário**<br>
<kbd>
    <img src="https://github.com/fabiokerber/Jenkins/blob/main/img/020220220841.jpg">
</kbd>
<br />
<br />
<kbd>
    <img src="https://github.com/fabiokerber/Jenkins/blob/main/img/020220220841.jpg">
    Kind: SSH Username with private key - Necessário para interagir com repositório Git
    Scope: Global
    ID: github-ssh
    Description: github-ssh
    Username: git (sempre "git" pois a conexão será realizada por chave SSH)
    Private Key:
        Enter directly
</kbd>
<br />
<br />