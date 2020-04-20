# Jenkins AutoConfig via Groovy (v.2.0.0) 
Neste capítulo traremos uma **alternativa** que também soluciona o problema de backup e configuração manual de instâncias.  
Lembrando que esta abordagem se sobrepõe a estratégia anterior, aquela em que criamos na **v.1.0.0**.  
O objetivo aqui é que o Jenkins se 'auto configure' em tempo de execução, e, em grandes linhas, agora nosso **Docker Build** irá funcionar da seguinte forma:  
1.  Instalamos os plugins (como sempre fizemos);   
2.  Injetaremos novos arquivos de propriedades (JDKs, Variáveis de Ambiente, Globals, etc.);  
3.  Injetaremos os novos códigos Groovy responsáveis pela configuração da instância;  
4.  O Jenkins, em tempo de construção do container, executa os scripts do item 3, tendo como base os arquivos do item 2.  
  
E vamos ao que interessa:  
`#SHOWMETHECODE`  

# Jenkins DSL, Groovy e IntelliJ
Toda *Domain Specific Languague (DSL)* requer um curva de aprendizado dentro de seu contexto funcional.  
Porém a parte técnica não precisa doer tanto, por issso vamos agora configurar uma IDE, no caso o IntelliJ, para nos dar recursos de auto-complete e facilitar nossa vida em tempo de desenvolvimento!  
  
O que iremos fazer juntos agora na aula:
1.  O primeiro passo é criar um projeto Gradle com Java e Groovy.
2.  Na sequência configuramos a IDE de forma que a mesma saiba interpretar as extenções dos Plugins.
3.  Configuramos o Build Gradle com o **Jenkins Core Library** e também o pacote dos **Plugins**  

```
group 'br.com.missaodevops'
version '1.0-SNAPSHOT'

apply plugin: 'groovy'
apply plugin: 'java'
apply plugin: 'idea'

idea {
    module {
        downloadJavadoc = true
        downloadSources = true
    }
}

sourceCompatibility = 1.8
targetCompatibility = 1.8

repositories {
    maven { url 'http://repo.jenkins-ci.org/releases/'}
    maven { url 'http://updates.jenkins-ci.org/download/plugins/'}
    maven { url 'http://jenkins-updates.cloudbees.com/download/plugins/'}
    mavenCentral()
}

dependencies {
    compile 'org.codehaus.groovy:groovy-all:2.3.11'
    testCompile group: 'junit', name: 'junit', version: '4.11'
    compile 'org.jenkins-ci.main:jenkins-core:2.45'
}
```  

Agora iremos dar um up em nosso Gradle, informando a ele **todas dependências** do nosso Jenkins, para isso ficar muito mais fácil, iremos utilizar nosso **velho amigo** 'plugins.txt' novamente!  

Aproveitando que todos plugins seguem o padrão de empacotamento do Java, vamos automatizar um trabalho manual de configurar as dependências:  
`awk -v prefix="compile 'org.jenkins-ci.plugins:" -v postfix="'" '{print prefix $0 postfix}' plugins.txt > dependencies.txt`  
  
Após atualizarmos o arquivo build.script vamos ao update!  

Por último teremos que importar as bibliotecas no diretório abaixo, e também atualizar nosso **build.gradle**:   
`compile fileTree(dir: 'lib', include: ['*.jar'])`  

Nossa referência sempre foram as documentações: 
https://jenkins.io/pipeline/getting-started-pipelines/   
https://jenkins.io/doc/pipeline/steps/  
