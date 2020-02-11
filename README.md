<a href="https://gitlab.com/rocha.public/cursos/jenkins-em-larga-escala/-/wikis/home"><img src="https://gitlab.com/rocha.public/cursos/jenkins-em-larga-escala/-/wikis/images/missaodevops-jenkins-docker-kube.png" title="jenkins" ="jenkins"></a>

# Topicos

*******
Introdução e Conceitos 
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

### Arquitetura Proposta




<div id='tools'/> 

### Jenkins & Docker

*TESTE*

<div id='config'/>

### Jenkins AutoConfig via Groovy

TESTE

<div id='together'/>

### Jenkins & Kubernetes

TESTE

<div id='automation'/>

### Extra: VMs com Vagrant e Ansible

TESTE

<div id='kubernetes'/>

### Extra: Play With K8s

TESTE


