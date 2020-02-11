<a href="https://gitlab.com/rocha.public/cursos/jenkins-em-larga-escala/-/wikis/home"><img src="https://gitlab.com/rocha.public/cursos/jenkins-em-larga-escala/-/wikis/images/missaodevops-jenkins-docker-kube.png" title="FVCproductions" alt="FVCproductions"></a>

# Topicos


## Seção 1: Introdução

### Introdução e Conceitos

-DevOps

São práticas utilizadas para melhorar a integração entre equipes de tecnologia.
DevOps são simplesmente conceitos. DevOps não é, nem trata de ferramentas diretamente.


Integração Contínua
É um dos conceitos contidos dentro da disciplina de DevOps que viabiliza o feedback rápido aos times envolvidos (fail-fast). Conhecida como Continous Integration (CI), assim como o DevOps, também não é uma ferramenta, e sim conceitos.


Jenkins
Atualmente a ferramenta mais utilizada para viabilizar os processos de integração contínua. Podemos assumir o Jenkins como o orquestrador do processo de CI, responsável por obter os códigos-fonte, avaliar, compilar, testar e prover um feedback aos interessados.


Docker
Plataforma de Containers, com ela iremos construir uma imagem personalizada de Jenkins. Esta imagem, por sua vez, nos irá garantir reprodutibilidade, escala e rápida resposta a desastres.


Kubernetes
Orquestrador de Containers, gerencia todo o processo de instalação e execução de containers, ele será responsável por gerir a execução da nossa imagem Jenkins e também prover recursos para a construção dos nossos códigos.

```
Seção 2: Jenkins & Docker
Seção 3: Jenkins AutoConfig via Groovy
Seção 4: Jenkins & Kubernetes

Extra: VMs com Vagrant e Ansible
Extra: Play With K8s

