![alt text](https://gitlab.com/rocha.public/cursos/jenkins-em-larga-escala/-/wikis/images/missaodevops-jenkins-docker-kube.png)


# Topicos

*******
 1. [Introdução](#intro)
 2. [Jenkins & Docker](#tools)
 3. [Jenkins AutoConfig via Groovy](#config)
 4. [Jenkins & Kubernetes](#together)
 
 5. [Extra: VMs com Vagrant e Ansible](#automation)
 6. [Extra: Play With K8s](#kubernetes)

*******

<div id='intro'/>  

# Introdução 

### DevOps
*São práticas utilizadas para melhorar a integração entre equipes de tecnologia.DevOps são simplesmente conceitos. DevOps não é, nem trata de ferramentas diretamente.*

### Integração Contínua
*É um dos conceitos contidos dentro da disciplina de DevOps que viabiliza o feedback rápido aos times envolvidos (fail-fast). Conhecida como Continous Integration (CI), assim como o DevOps, também não é uma ferramenta, e sim conceitos.*

### Jenkins
*Atualmente a ferramenta mais utilizada para viabilizar os processos de integração contínua. Podemos assumir o Jenkins como o orquestrador do processo de CI, responsável por obter os códigos-fonte, avaliar, compilar, testar e prover um feedback aos interessados.*

### Docker
*Plataforma de Containers, com ela iremos construir uma imagem personalizada de Jenkins. Esta imagem, por sua vez, nos irá garantir reprodutibilidade, escala e rápida resposta a desastres.*

### Kubernetes
*Orquestrador de Containers, gerencia todo o processo de instalação e execução de containers, ele será responsável por gerir a execução da nossa imagem Jenkins e também prover recursos para a construção dos nossos códigos.*

### Cenário: Crítico, com recursos limitados
*Vamos começar com a seguinte situação: temos uma instância de Jenkins (Master), que possui 500 jobs. Todos seus processos de construção dependem desta instância.*

1- *Um Jenkins 'comum' normalmente costuma ter cerca de 100 plugins instalados. Atualizá-los de uma maneira geral é uma tarefa tranquila e executada de uma só vez. Feito isso, o Jenkins sofre um restart e passa a operar com os novos plugins. Ao longo das próximas horas alguns jobs vão parando de funcionar, e seu eu precisar retornar todos esses plugins a suas versões anteriores? No fim... está aberta a temporada de caça as bruxas!!! **Problema 1: Atualização E Rollback Seguro de Componentes***

2- *O server pegou fogo? Trágico, não? O último backup é do mês passado? O que você faria numa situação de desastre? **Problema 2: Automação de Backups e Recuperação de Desastres***

## Arquitetura Proposta

![alt text](https://gitlab.com/rocha.public/cursos/jenkins-em-larga-escala/-/wikis/images/01-intro-arquitetura-proposta.png)


<div id='tools'/> 

# Jenkins & Docker

## Por que devo criar minha imagem?

### Vamos explicar as motivações desta abordagem:

* *Esta será a garantia de que sabemos exatamente como configurar nosso Jenkins;*
* *As ferramentas, plugins e configurações serão precisas;*
* *Segurança e qualidade no processo de atualização das ferramentas;*
* *Subida de novas instâncias de Jenkins em questão de segundos;*
* *Reprodutibilidade e Disaster Recovery;*

### Instalação do Docker

Vamos começar com a instalação do Docker, neste primeiro momento ele será o responsável por manusear nossa imagem Jenkins.
Instale: https://docs.docker.com/install/
Você pode optar por:

* *Instalar em sua máquina;*
* *Dentro de numa máquina virtual;*
* *Utilizar uma cloud qualquer;*

Neste primeiro momento vou gerar as imagens em minha máquina, mas você pode consultar a sessão 'Extras' deste curso para explorar as demais opções.

### Imagem Base, Primeiro Build e Versionamento

*Vamos iniciar criando nossa primeira versão do arquivo 'Dockerfile'. Copie o arquivo 'v.0.1.0/Dockerfile' disponilizado nos repositório.*

**No terminal execute:**

```docker build . --tag <usuario-docker-hub>/missao-devops-jenkins:0.1.0```

**Por último e não menos importante, vamos armazenar no DockerHub a primeira versão da nossa imagem!
Faça o login em sua sua conta**

```docker login <usuario-docker-hub>```

**E envie sua imagem:**

**Push:**```docker push <usuario-docker-hub>/missao-devops-jenkins:0.1.0```

**Execução da Nossa Imagem (v 0.1.0)**

*Sucesso! Imagem pronta, agora vamos subir nosso primeiro container Jenkins:*

```docker run --name docker-jenkins -p 8080:8080 <usuario-docker-hub>/missao-devops-jenkins:0.1.0```
 
**Agora, vamos dar uma navegada na nossa nova instância de Jenkins.**

*E repassar alguns comandos úteis:*
* *Ver todas imagens disponíveis: **docker image***
* *Listar Containers em Execução: **docker ps***
* *Listar Todos os Containers: **docker ps -a***
* *Logs: **docker logs <id|name>***
* *Para remover containers: **docker rm <id|name>***
* *Para remover imagens: **docker rmi <id|name>***

Na próxima aula, daremos o primeiro passo rumo a automação!

### Instalação Automática de Plugins (v 0.2.0)

*Antes de mais nada, devemos ressaltar que os plugins são **parte vital** do ecossistema do Jenkins.*

*Se você está iniciando, ou ainda trabalha em um contexto simples, muito provavelmente ainda não necessitou ter esse nível de detalhe, porém, quando o intuito é **atuar em larga escala**, todo plugin deve ser registrado junto a sua versão.*

*Iniciaremos com o meu setup de plugins, depois vocês podem (e devem) trabalhar em sua versão, por isso copie o arquivo **plugins.txt** do diretório **v.0.2.0** disponibilizado nos recursos da aula.*

* A lista de plugins gerada, que tal se nossa imagem já viesse com **todos esses plugins** configurados? Sem a necessidade de ter que selecioná-los durante o **wizard de configuração**? Assim ninguém correria o risco de esquecer ou deixar passar um ou mais plugins!*


*Se você acha que isso seria ótimo, então é exatamente o que vamos fazer!*
*Copie também o arquivo 'Dockerfile' disponilizado no diretório 'v.0.2.0', nos recursos da aula*

*Vamos gerar essa nova versão? Execute:*

```docker build . --tag <usuario-docker-hub>/missao-devops-jenkins:0.2.0```
 
*Você vai reparar muita coisa diferente nos logs por conta da instalação dos plugins*

*Então vamos subir um novo container, com base na nova versão da imagem:*

```docker run --name docker-jenkins -p 8080:8080 <usuario-docker-hub>/missao-devops-jenkins:0.2.0```
 
**Push:**```docker push <usuario-docker-hub>/missao-devops-jenkins:0.2.0```
 
*E finalmente com os plugins instalados, vamos verificar se está tudo OK!*

### Configuração Jenkins

*Agora, vamos executar os seguintes passos:*

* *Acessamos o Jenkins com a credencial inicial;*
* *Configuração de um novo usuário;* 
* *Configuração da JDK;*

*E tudo parece funcionar perfeitamente bem, exceto que, caso este container seja destruído, vamos ter que realizar essas configurações iniciais novamente?*

*Infelizmente sim! :(*

*Neste ponto temos duas opções para solucionar este 'problema':*


*1. Gerar uma nova tag desta imagem docker*

**Prós:** É prático!

**Contras:** A imagem vai ficar enorme com workspaces, plugins e tudo mais. Você vai perder o controle do que existe lá dentro. Se torna 

*inviável replicar um ambiente neste estado;*


*2. Automatizar um backup preciso, com apenas o que importa:*

***Prós:** Todos!*

***Contras:** Exige conhecimento (o que NÃO será um 'contra' para nós a partir da próxima aula)*

### Automação de Backups e Recovery (v 0.3.0)

*Ótimo, nossa versão do Jenkins possui todos os plugins e a JDK que precisamos, tudo instalado de maneira automática, porém no passo anterior nós configuramos os acessos, plugins e ferramentas, e tudo se perdeu!*

*Mas isso termina agora, vamos começar a configuração do nosso backup atualizando o Dockerfile:*

*Copie o arquivo '0.3.0.Dockerfile' disponilizado nos recursos da aula*

*Geramos uma nova versão:*

```sh
docker build . \
    -tag <usuario-docker-hub>/missao-devops-jenkins:0.3.0
```

*E executamos:*

```sh
docker run --name docker-jenkins-3 \
    -p 8080:8080 \
    -v jenkins_home_3:/var/jenkins_home \
    -v jenkins_backup_3:/srv/backup \
    <usuario-docker-hub>/missao-devops-jenkins:0.3.0
```

* *1. Login na Ferramenta*
* *2. Troca de Senha*
* *3. Configuração da JDK*
* *4. Criação de 2 Jobs*
* *5. Configurar Backup*
* *6. Executar Backup*
* *7. Obter Backup*

 **Push:**`docker push <usuario-docker-hub>/missao-devops-jenkins:0.3.0`

### Gerando Imagem com Import de Configurações (v 0.4.0)

*Chegamos ao ponto crucial, iremos juntar o setup 'limpo' da ferramenta com nossas configurações essenciais, e é aqui em que as coisas começam a fazer muito mais sentido!*

*Copie todo o conteúdo, ou navegue, para o diretório 'v.0.4.0', disponilizado nos recursos da aula
Build:*

```sh
docker build . \
    --tag <usuario-docker-hub>/missao-devops-jenkins:0.4.0
```

**Execução:**

```sh
docker run --name docker-jenkins-4 \
    -d \
    -p 8080:8080 \
    -v jenkins_home_4:/var/jenkins_home \
    -v jenkins_backup_4:/srv/backup \
    <usuario-docker-hub>/missao-devops-jenkins:0.4.0
```

**Push:** ```docker push <usuario-docker-hub>/missao-devops-jenkins:0.4.0```

Finalmente temos o melhor dos dois mundos, um **Jenkins sob medida**, com todas configurações que eu necessito!

### Atualizações e Evoluções (v 1.0.0)

Quando se tratam de updates, 99% deles são executados na própria interface, e em larga escala (quem nunca atualizou 5.. 10 plugins de uma só vez?

Há algumas aulas atrás, vimos vários plugins desatualizados, chegou a hora de **atualizar tudo** e deixar nossa imagem em sua **versão de 'release'**!

Nossa logística ficou tão **fácil** que, é necessário apenas abrir o arquivo **plugins.txt**, atualizar as versões dos plugins e gerar uma nova imagem!

Por se tratar de nossa primeira versão estável, finalmente vamos gerar a tag **1.0.0**

```sh
docker build . \
    --tag <usuario-docker-hub>/missao-devops-jenkins:1.0.0
```

```sh
docker run --name docker-jenkins-100 \
    -d \
    -p 8080:8080 \
    -v jenkins_home_100:/var/jenkins_home \
    -v jenkins_backup_100:/srv/backup \
    <usuario-docker-hub>/missao-devops-jenkins:1.0.0
```

**Push:**```docker push <usuario-docker-hub>/missao-devops-jenkins:1.0.0```

Vamos repassar os conceitos e vantagens que tivemos até aqui!

### Overview e Conclusões

![alt text](https://gitlab.com/rocha.public/cursos/jenkins-em-larga-escala/-/wikis/images/02-jenkins-docker-conclusion.png)

<div id='config'/>

### Jenkins AutoConfig via Groovy

#### Jenkins AutoConfig via Groovy (v.2.0.0)

*Neste capítulo traremos uma alternativa que também soluciona o problema de backup e configuração manual de instâncias. Lembrando que esta abordagem se sobrepõe a estratégia anterior, aquela em que criamos na v.1.0.0. O objetivo aqui é que o Jenkins se 'auto configure' em tempo de execução, e, em grandes linhas, agora nosso Docker Build irá funcionar da seguinte forma:*

* *1. Instalamos os plugins (como sempre fizemos);*
* *2. Injetaremos novos arquivos de propriedades (JDKs, Variáveis de Ambiente, Globals, etc.);*
* *3. Injetaremos os novos códigos Groovy responsáveis pela configuração da instância;*
* *4. O Jenkins em tempo de construção do container, executa os scripts do item 3, tendo como base os arquivos do item 2.*

*E vamos ao que interessa:*

```#SHOWMETHECODE```

### Jenkins DSL, Groovy e IntelliJ

*Toda Domain Specific Languague (DSL) requer um curva de aprendizado dentro de seu contexto funcional. Porém a parte técnica não precisa doer tanto, por issso vamos agora configurar uma IDE, no caso o IntelliJ, para nos dar recursos de auto-complete e facilitar nossa vida em tempo de desenvolvimento!*

*O que iremos fazer juntos agora na aula:*

* *1. O primeiro passo é criar um projeto Gradle com Java e Groovy.*
* *2 Na sequência configuramos a IDE de forma que a mesma saiba interpretar as extenções dos Plugins.*
* *3 Configuramos o Build Gradle com o Jenkins Core Library e também o pacote dos Plugins*

```java
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

*Agora iremos dar um up em nosso Gradle, informando a ele todas dependências do nosso Jenkins, para isso ficar muito mais fácil, iremos utilizar nosso velho amigo 'plugins.txt' novamente!*

*Aproveitando que todos plugins seguem o padrão de empacotamento do Java, vamos automatizar um trabalho manual de configurar as dependências:*

```sh
awk -v prefix="compile 'org.jenkins-ci.plugins:" -v postfix="'" '{print prefix $0 postfix}' plugins.txt > dependencies.txt
```

*Após atualizarmos o arquivo build.script vamos ao update!*

*Por último teremos que importar as bibliotecas no diretório abaixo, e também atualizar nosso **build.gradle**:*

```java
compile fileTree(dir: 'lib', include: ['*.jar'])
```

*Nossa referência sempre foram as documentações:*

*https://jenkins.io/pipeline/getting-started-pipelines/*

*https://jenkins.io/doc/pipeline/steps/*

<div id='together'/>

### Jenkins & Kubernetes

Agora, vocês devem escolher como pretender executar o Kubernetes (muito provavelmente vocês já tenham isso), eu optei pelo Minikube!
Fiquem a vontade para escolher, e, caso tenham dúvida, dêem uma olhada na Seção Extra dexei para vocês overview sobre o Play With K8s para ajudar com essa 'dúvida'.

#### Cluster HandsOn

##### Service

Hora de criar nosso Service, responsável por expor nosso endpoint, sem isso nosso POD, gerado pelo Deployment, nunca seria acessado:

**1.**```vi service.yml```
**2.**```Copie o conteúdo do arquivo jenkins-service.yml disponibilizado na aula```
**3.**```kubectl apply -f service.yml```
**4.**```Verifique o serviço kubectl get services```
**5.**```Verifique o serviço kubectl get deployments```

##### Deployment

Hora de criar nosso deployment, responsável por orientar o kubernetes de como se devem ser criados os PODs:

**1.**```vi deployment.yml```
**2.**```Copie o conteúdo do arquivo jenkins-deployment.yml disponibilizado na aula```
**3.**```Atualize a ENV KUBERNETES_SERVER_URL com o ip do seu Cluster: kubectl cluster-info | grep master```

kubectl apply -f deployment.yml
Verifique o serviço kubectl get deployments


Show! O serviço foi exposto e já podemos acessá-lo!

<div id='automation'/>

### Extra: VMs com Vagrant e Ansible

TESTE

<div id='kubernetes'/>

### Extra: Play With K8s

TESTE


