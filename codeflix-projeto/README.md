# CodeFlix

## Objetivo

Uma especie de Netflix
Assinatura do servico pelo cliente
Catalogo de videos para navegacao
Payback de videos
Busca full text no catalogo

Processamento e encoding dos videos
Administracao do catalogo de videos
Administraacao do servico de assinatura

## Arquitetura

Arquitetura baseada em microsservicos 
Tecnologia adequada para cada contexto
Nao exite uma unica verdade na escolha das tecnologias
Microservicos podem ser substituidos por outros com tecnologias diferentes
Cada micrsoservico tera seru proprio processo de CI/CD

API Gateway
Acesso externo aos microsservicos atraves do Ingress do Kubernates/Istio como API Gateway
Unico ponto de acesso direto as aplicacoes
Controle de trafego
Rate limit, Politicas de Retry, etc

Service Discovery
Nao havera necessidade de trabalhar com um sistema de Service Discovery como "Consul"
O projeto utilizara o Kubernetes para orquestrar os containers, logo Service Discovery ja faz parte do processo

Escala Horizontal
O processo de escala podera ser configurado a nivel de microservico
Todos os microservicos trabalharam de forma "Stateless"
Quando utilizado upload de qualquer tipo de asset, o mesmo sera armazenado em um Cloud Storage
O processo de escala se dara no aumento na quantidade de PODs do Kubernetes
O processo de autoscaling tambem sera utilizado atraves de um recurso chamado HPA (Horizontal Pod Autoscales)
Todos os logs gerados serao persistido em sistemas externos como Prometheus e Elasticsearch

Consistencia Eventual
Grande parte da comunicacao entre os microsservicos sera assincrona
Cada microservico possuira sua propria base de dados
Eventualemente os dados poderao ficar incosistentes, desde que nao hava prejuizo direto ao negocio

Duplicacao de dados
Eventualmente um microsservico podera persistir dados ja existenets em outro microsservico em seru banco de dados
Essa duplicacao ocorre para deixar o microsservico mais autonomo preciso
O microsservico duplicara apenas os dados necessario para seu contexto

Mensageria
Grande parte da cominicacao eh assincrona, um sistema de mensageria eh necessario
O RabbitMQ foi escolhiodo para esse caso
Por que nao o Apache Kafka ou Amazon SQS, entre outros?
Apache kafka poderia ser utilizado nessa caso, por outro lado, muitos recuros que ele poderia prover nao seria utilizados
Evitaremos ao maximo o Lock in nos cloud provides, logo, Amazon SQS e similares foram descartados
Nao ha uma verdade unica sobre a escolha realizada

Resiliencia e self healing
Resiliencia - garantir que continue funcionando mesmo que haja um problema
Para garantir a resiliencia caso um ou mais microservicoes fiquem fora do ar, as filas serao essenciais
Caso uma mensagem venha em um padrao nao esoperado para determinado microservico, o miocroservico podera rejeita-la e automaticamente a mesma podera ser encaminhada para uma dead-letter queue
Pelo falor do Kubernetes e Istio possuirem recursos de **Circuit breaker** e **Liveness** e **Readness probes**
se um container na ttiver um chash, atumaticamente ele sera reiniciado ou mesmo recriado
Caso um container nao aguente determinado trafego, temos a opcao de trabalhar com circuit breaker que impede que ele receba mais requisicoes entquanto esta se "curando"

Autenticacao
Servico centralizado de identidfade opensouce: Keycloak
OpenID Connect
Customizacao do tema
* Utilizacao do create-react-app
Compartilhamentode chave publica com os servicos para verificacao de autenticidade dos tokens
Diversos tipos de ACL
Flow de autenticacao para frontend e backend


Microsservicos do projeto
Admin Catalogo de Videos (Backend com Laravel) & Admin Catalogo de Videos (Frontend com React)
* Frontend envia para o backend
* Backend faz o upload no Cloud Storage (GCP) 
* Backend produz uma mensagem (Video Criado) e envia ao RabbitMQ
* Backend le a mensagem do RabbitMQ (Video Processado)

Encoder e Video com Golang
* Encoder faz download do video no Cloud Storage
* Encoder faz upload do video processado no Cloud Storage
* Encoder envia mensagem (Video Processado) ao RabbitMQ
  
API do catalogo (Backend com Node.js) & Aplicacao do Catalogo (Frontend com React.js)
* API le do RabbitMQ(Video Criado)
* API persiste no Elastic Search
* Frontend vai consumir a API

Assinatura do Codeflix pelo cliente (Python com Django)
* Assinatura envia informacao ao Keycloak (sincrona)

Autenticacao entre microsservicos com Keycloack
Comunicacao assincrona entre os Microsservicos com RabbitMQ

Faz a Assinatura -> KeyCloack

Admin Catalogo ou Catalogo faz login pelo Keycloak

Cadastro: Admin Catalogo(F) -> Admin Catalogo(B) -> RabbitMQ -> Encoder de video
Finalizar: conversao Encoder de video -> RabbitMQ -> Admin Catalogo(B)
Avisar a conversar OK: Admin Catalogo(B) -> RabbitMQ -> Catalogo(B)
Visualizar video: Catalogo(F) ->  Catalogo(B)

## Testes
Testes vao estar dispersos nos microsservicos
* Unidade
* Integracao
* End-to-end
* Selenium / frontend
* Upload

## Autenticacao
Assinatura registrar um novo usuario no KeyCloack
Frontend utiliza o **OpenID Connect** com o KeyCloack e retornar um **JWT**
Frontend envia o **JWT** para o backend e o backend verifica no KeyCloack se eh valido e devolve a response.
* Problema (Dupla Latencia), a cada requisicao, o backend precisa validar o JWT no Keycloack 
* Solucao
Frontend utiliza o **OpenID Connect** com o KeyCloack e retornar um **JWT**
Frontend envia o **JWT** para o backend (Verificar com a chave publica do KeyCloack) e devolve a response

### Validacao de tokens
**key.public** somente valida se o token foi emitido ou nao.
**key.private** ai ferrou, pq da para criar qualquer token e sera validado corretamente
KEyCloack (key.private)
Servico A (key.public)
Servico B (key.public)
Servico C (key.public)

**jwt.io**

## CI (Continuos integration) / CD (Continuos Deployment/Delivey)
Para cada pull erquest gerada em uma aplicacao, iniciaremos o processo de CI
Github Actions
Processo de CI
* Subir a aplicacao usando Docker
* Executar os Testes
* Utilizar o Sonarqube
No caso de acontecer o "merge" da Pull Request, o prceoss de CO acontece
* Fara a geracao da imagem Docker
* Realizar o upload da imagem em um container   registry
* Executara o deplu no Kubernetes

## Kubernetes
Cluster Kubernetes gerenciado
O deploy da aplicacao
Startup, Readlness e Liveness PRobe para seaf healing
Horizontal Pod Autoscaler (HPA) para escalar horizontalmente a aplicacao
TLS/SSL com cert-manager
Kubelens para ter uma visao geral do Cluster

## Service Mesh
Monitorar e interceptar todas as requisicoes do servico
Isto
Gerenciamento de trafego das informacoes
* Virtual Service
* Ingress Gateway
* Envoy Sidecar Proxy
* Circuit Breaking
* Requests Timeout
TLS/SSL com cert-manager

## Cloud Providers (interessante estudar algum curso de cloud provider)
Providers: AWS, Microsoft Azure, GCP

## Observabilidade
Metricas (qualquer coisa que consiga medir)
* Prometheus/Grafana (coleta/visualizacao)
* Elastic Stack
Logs
* Elastic Stack
** Elastic Search
** Beasts (agente que pega as metricas e envia ao Elastic Search)
** Kibana (ter acesso as ferramentas)

Tracing/APM (rastreabilidade/Aplication Performance Monitoring)
* Elastic Stack
** APM
** Kibana (tem o Service Map)
** Kiali (pega as informacoes do Istio e ver em tempo real um servico falando com outro e outras informacoes)
