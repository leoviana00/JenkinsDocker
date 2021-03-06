# Por que devo criar minha imagem?
Vamos explicar as motivações desta abordagem:  
- Esta será a garantia de que sabemos exatamente como configurar nosso Jenkins;
- As ferramentas, plugins e configurações serão precisas;
- Segurança e qualidade no processo de atualização das ferramentas;
- Subida de novas instâncias de Jenkins em questão de segundos;
- Reprodutibilidade e Disaster Recovery;
  
# Instalação do Docker
Vamos começar com a instalação do Docker, neste primeiro momento ele será o responsável por manusear nossa imagem Jenkins.  
  
Instale: https://docs.docker.com/install/  
  
Você pode optar por:  
- Instalar em sua máquina;  
- Dentro de numa máquina virtual;  
- Utilizar uma cloud qualquer;  
  
Neste primeiro momento vou gerar as imagens em minha máquina, mas você pode consultar a sessão 'Extras' deste curso para explorar as demais opções.  
  
# Imagem Base, Primeiro Build e Versionamento
Vamos iniciar criando nossa primeira versão do arquivo 'Dockerfile'  
*Copie o arquivo 'v.0.1.0/Dockerfile' disponilizado nos recursos da aula*  

No terminal execute:  
`docker build . --tag <usuario-docker-hub>/missao-devops-jenkins:0.1.0`  
(REMOVER) docker build . --tag rodrigogrohl/md-jenkins:0.1.0  

Por último e não menos importante, vamos armazenar no DockerHub a primeira versão da nossa imagem!
Faça o login em sua sua conta:  
`docker login <usuario-docker-hub>`  
E envie sua imagem:  
`docker push <usuario-docker-hub>/missao-devops-jenkins:0.1.0`  

# Execução da Nossa Imagem (v 0.1.0) 
Sucesso! Imagem pronta, agora vamos subir nosso primeiro container Jenkins:  
`docker run --name docker-jenkins -p 8080:8080 <usuario-docker-hub>/missao-devops-jenkins:0.1.0`  
(REMOVER) docker run --name docker-jenkins -p 8080:8080 -v jenkins_home:/var/jenkins_home rodrigogrohl/md-jenkins:0.1.0  
  
Agora, vamos dar uma navegada na nossa nova instância de Jenkins.  
  
E repassar alguns comandos úteis:  
. Ver todas imagens disponíveis: `docker images`  
. Listar Containers em Execução: `docker ps`  
. Listar Todos os Containers: `docker ps -a`  
. Logs: `docker logs <id|name>`  
. Para remover containers: `docker rm <id|name>`
. Para remover imagens: `docker rmi <id|name>`
    
Na próxima aula, daremos o primeiro passo rumo a automação!  
  
# Instalação Automática de Plugins (v 0.2.0)
Antes de mais nada, devemos ressaltar que os plugins são **parte vital** do ecossistema do Jenkins.  
  
Se você está iniciando, ou ainda trabalha em um contexto simples, muito provavelmente ainda não necessitou ter esse nível de detalhe, porém, quando o intuito é **atuar em larga escala**, todo plugin deve ser registrado junto a sua versão. 

Iniciaremos com o meu setup de plugins, depois vocês podem (e devem) trabalhar em sua versão, por isso copie o arquivo **plugins.txt** do diretório **v.0.2.0** disponibilizado nos recursos da aula.

Com a lista de plugins gerada, que tal se nossa imagem já viesse com **todos esses plugins** configurados?
Sem a necessidade de ter que selecioná-los durante o **wizard de configuração**? Assim ninguém correria o risco de esquecer ou deixar passar um ou mais plugins!  

Se você acha que isso seria ótimo, então é exatamente o que vamos fazer!  
*Copie também o arquivo 'Dockerfile' disponilizado no diretório 'v.0.2.0', nos recursos da aula*  

Vamos gerar essa nova versão? Execute:  
`docker build . --tag <usuario-docker-hub>/missao-devops-jenkins:0.2.0`  
(REMOVE) docker build . --tag rodrigogrohl/md-jenkins:0.2.0  

Você vai reparar muita coisa diferente nos logs por conta da instalação dos plugins.  

Então vamos subir um novo container, com base na nova versão da imagem:  
`docker run --name docker-jenkins -p 8080:8080 <usuario-docker-hub>/missao-devops-jenkins:0.2.0`  
(REMOVE) docker run --name docker-jenkins -p 8080:8080 rodrigogrohl/md-jenkins:0.2.0  

Push: `docker push <usuario-docker-hub>/missao-devops-jenkins:0.2.0`  

E finalmente com os plugins instalados, vamos verificar se está tudo OK!

# Configuração Jenkins
Agora, vamos executar os seguintes passos:  
- Acessamos o Jenkins com a credencial inicial;
- Configuração de um novo usuário;
- Configuração da JDK;  
  
E tudo parece funcionar perfeitamente bem, exceto que, caso este container seja destruído, vamos ter que realizar essas configurações iniciais novamente?  
Infelizmente sim! :(   
Neste ponto temos duas opções para solucionar este 'problema':  
   
1. Gerar uma nova tag desta imagem docker  
**Prós**: É prático!   
**Contras**: A imagem vai ficar enorme com workspaces, plugins e tudo mais. Você vai perder o controle do que existe lá dentro. Se torna inviável replicar um ambiente neste estado;    
  
2. Automatizar um backup preciso, com apenas o que importa:  
**Prós**: Todos!  
**Contras**: Exige conhecimento (o que **NÃO** será um 'contra' para nós a partir da próxima aula)   
    
# Automação de Backups e Recovery (v 0.3.0)
Ótimo, nossa versão do Jenkins possui todos os plugins e a JDK que precisamos, tudo instalado de maneira automática, porém no passo anterior nós configuramos os acessos, plugins e ferramentas, e tudo se perdeu!  
  
Hashtag chateado!  
  
Mas isso termina agora, vamos começar a configuração do nosso backup atualizando o Dockerfile:  
*Copie o arquivo '0.3.0.Dockerfile' disponilizado nos recursos da aula*  

Geramos uma nova versão:  
```
docker build . \
    --tag <usuario-docker-hub>/missao-devops-jenkins:0.3.0
```  
  
(REMOVE) docker build . --tag rodrigogrohl/md-jenkins:0.3.0  
  
E executamos:  
```
docker run --name docker-jenkins-3 \
    -p 8080:8080 \
    -v jenkins_home_3:/var/jenkins_home \
    -v jenkins_backup_3:/srv/backup \
    <usuario-docker-hub>/missao-devops-jenkins:0.3.0
```
1. Login na Ferramenta  
2. Troca de Senha  
3. Configuração da JDK  
4. Criação de 2 Jobs  
5. Configurar Backup  
6. Executar Backup  
7. Obter Backup

Push: `docker push <usuario-docker-hub>/missao-devops-jenkins:0.3.0`  

# Gerando Imagem com Import de Configurações (v 0.4.0)
Chegamos ao ponto crucial, iremos juntar o **setup 'limpo'** da ferramenta com nossas **configurações essenciais**, e é aqui em que as coisas começam a fazer muito mais sentido!  
*Copie todo o conteúdo, ou navegue, para o diretório 'v.0.4.0', disponilizado nos recursos da aula*  
Build:  
```
docker build . \
    --tag <usuario-docker-hub>/missao-devops-jenkins:0.4.0
```    
Execução:  
```
docker run --name docker-jenkins-4 \
    -d \
    -p 8080:8080 \
    -v jenkins_home_4:/var/jenkins_home \
    -v jenkins_backup_4:/srv/backup \
    <usuario-docker-hub>/missao-devops-jenkins:0.4.0
```
Push: `docker push <usuario-docker-hub>/missao-devops-jenkins:0.4.0`  
Finalmente temos o melhor dos dois mundos, um **Jenkins sob medida**, com todas configurações que eu necessito!  
    
# Atualizações e Evoluções (v 1.0.0)
Quando se tratam de updates, 99% deles são executados na própria interface, e em larga escala (quem nunca atualizou 5.. 10 plugins de uma só vez?)  
Há algumas aulas atrás, vimos vários plugins desatualizados, chegou a hora de **atualizar tudo** e deixar nossa imagem em sua **versão de 'release'**!
Nossa logística ficou tão **fácil** que, é necessário apenas abrir o arquivo **plugins.txt**, atualizar as versões dos plugins e gerar uma nova imagem!  
Por se tratar de nossa primeira versão estável, finalmente vamos gerar a tag **1.0.0**
```
docker build . \
    --tag <usuario-docker-hub>/missao-devops-jenkins:1.0.0
```    
  
```
docker run --name docker-jenkins-100 \
    -d \
    -p 8080:8080 \
    -v jenkins_home_100:/var/jenkins_home \
    -v jenkins_backup_100:/srv/backup \
    <usuario-docker-hub>/missao-devops-jenkins:1.0.0
```
Push: `docker push <usuario-docker-hub>/missao-devops-jenkins:1.0.0`  
    
Vamos repassar os conceitos e vantagens que tivemos até aqui!  
   
# Overview e Conclusões    
![](images/02-jenkins-docker-conclusion.png)
  
![missaodevops.com.br](images/logo-missaodevops-02.png)     
