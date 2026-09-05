# Comunicações Síncronas

Uma comunicação sícrona é uma estrutura onde um sistema chama outro e aguarda sua resposta para prosseguir. Por natureza ela tem uma característica bloqueante, ou seja, até que o sistema que está sendo chamado responda, as próximas instruções de um sistema ficam bloqueadas.

Comunicações síncronas são fáceis de serem implementadas, fácil de debugar, temos previsibilidade, no entanto, apresenta características como alto acoplamento, degradação de latência devido a espera pela resposta, pode apresentar baixa escalabilidade e também pode apresentar erros em cascata, caso a gente precise fazer integrações com vários sistemas de forma síncrona.

---

## HTTP e REST (Representational State Transfer)

Linguagem comum de comunicação entre microsserviços, é um estilo arquitetural proposta por Roy Fielding em 2000.

O REST se baseia no HTTP para permitir a comunicação entre microsserviços, ou seja, dispõe de recursos, URIs, verbos HTTP (GET, POST, PUT, DELETE, PATCH). É baseado em client/server, usa headers, query strings, body, etc.

O REST é um estilo arquitetura baseado no HTTP, que deixa o uso do HTTP de forma mais declarativa. Isso quer dizer que, o uso do REST é bastante explícito, por exemplo:

- GET /pessoas -> indica que quero uma listade todas as pessoas
- POST /pessoas -> indica que quero cadastrar uma pessoa
- GET /pessoas?id=1 -> indic que quero os dados da pessoa de id 1
- PUT /pessoas -> indica que quero editar uma pessoa
- DELETE /pessoas?id=1 -> indica que quero deletar a pessoa de id 1

Cada uma das requisições exemplificadas acima, também podem declarar os headers:
- Content-Type = application/json -> indica que será uma requisição que será enviado um JSON
- Authorization = Bearer token -> indica que a rota precisa de autenticação e que ela pode ou não ser acessada pelo client requisitante

Além disso, também trabalhamos no REST com o HTTP Status Code:

- 200 OK -> indica uma resposta com sucesso, podendo conter um response body
- 204 NO CONTENT -> indica uma resposta com sucesso, sem um response body
- 201 CREATED -> indica uma resposta com sucesso, sem um response body por padrão
- 404 PAGE NOT FOUND -> pode indicar um recurso não encontrado (/pessoa não existe, por exemplo) ou um item não encontrado no servidor - o uso cabe discussões

Existem vários outros HTTP status codes, mas a ideia acima é mostrar o uso do REST como um padrão arquitetural.

### Idempotência

A idempotência dentro do REST, representa que a execução de algo possa acontecer mais de uma vez sem causar um efeito colateral. Existem métodos HTTP que são naturalmente idempotentes, como por exemplo:

- GET -> é um método naturalmente idempotente. Se eu executar GET /pessoas 1 milhão de vezes, a resposta deve ser sempre a mesma
- PUT -> é um método naturalmente idempotente. Se eu executar PUT /pessoas passando o mesmo body 1 milhão de vezes, a resposta deve ser sempre a mesma
- DELETE -> é um método naturalmente idempotente. Se eu executar DELETE /pessoas?id=1 1 milhão de vezes, a resposta deve ser sempre a mesma
- POST -> não é um método naturalmente idempotente. Se eu executar POST /pessoas passando o mesmo body, o estilo arquitetural do REST tende a criar novos recursos sempre que esta rota for chamada. Para tornar um método POST idempotente, precisamos criar mecanismos adicionais, permitindo que o servidor consiga identificar que trata-se de uma requisição duplicada e então, tratar ela de forma idempotente.

---

## Webhooks e Pooling

### Pooling

O pooling é uma forma em que um client fica fazendo requisições em um servidor, até obter determinada resposta que possa processar. Imagine um e-commerce que faz uma requisição para obter o status de um pagamento. O client faz a requisição ao servidor de 10 em 10 segundos, até obter o status de pagamento concluído e assim processar o pedido de fato. Esse mecanismo é chamado de pooling.

#### Long-Pooling

O long-pooling indica que nesse mecanismo entre client / server, a requisição ficará aberta por um período longo de tempo. Então eu abro a requisição com um servidor e aguardo por exemplo por 30 segundos. O client recebe ou não a resposta dentro deste timeout e caso não receba, abre uma nova requisição. Caso receba a resposta, ele processa e encerra.

Ele é um mecanismo recomendado que eu sei que meu servidor demora para processar determinada requisição.

#### Short-Pooling

O short-pooling por sua vez, indica que a requisição ficará aberta por um curto período de tempo. É quando eu sei que o recurso que estou querendo muda com uma certa frequência e a cada 5 segundos, por exemplo, eu preciso capturar o novo status dele ou uma nova informação. Imagina que preciso atualizar a tela do meu aplicativo de investimentos para saber qual o preço da ação da NVIDIA. Uma estratégia de short-pooling pode ser utilizada aqui, então eu faço um pooling a cada 2 segundos, a fim de atualizar o preço da ação da NVIDIA no meu app mobile, por exemplo.

### Webhooks

O webhook por sua vez é um mecanismo onde o servidor informará o client que determinada ação aconteceu. Então imagine que o client envia uma solicitação de análise de crédito ao servidor. O servidor vai processar essa análise de crédito por 1 minuto. O client pode informar ao servidor uma URL de callback e cortar essa requisição em 100 ms. Assim que o servidor processar toda análise de crédito, que costuma demorar em torno de 1 minuto, o servidor chama a URL de callback, informando ao client que o processamento foi concluído, informando o status do crédito. Esse mecanismo descrito é um webhook.

---

## RPC (Remote Procedure Call)

Um RPC é um protocolo de comunicação entre dois sistemas, em uma forma mais simples. Podemos dizer que uma arquitetura client / server é um RPC, no entanto, ele pode representar isso de forma mais simples.

Uma conexão TCP/IP entre um client e server, pode ser considerado um RPC.

### gRPC e Protobufs

O gRPC foi um framework desenvolvido pela Google, baseado em RPC, voltado para arquiteturas distribuídas. É baseado no protocolo HTTP 2, permite contratos fortes, autenticação, performance e escalabilidade.

O gRPC ele permite conexões do tipo keep alive, onde o client abre uma conexão com o servidor uma única vez e permite que novas requisições sejam feitas dentro desta mesma conexão, diminuindo assim a latência entre client / server.

O gRPC permite também comunicaçaõ bidirecional, baseadas em streaming.

### Protobufs

O protobuf ele é o contrato criado entre o client / server. É onde conseguimos definir o contrato de entrada e saída entre as duas pontas.

Ele é definido em um arquivo .proto, que é compilado e serializado para permitir a comunicação.

---

## Websockets

Websocket é um tipo de conexão ativa entre client e server. O client abre uma solicitação de websocket com um server e essa conexão fica ativa, permitindo comunicação bidirecional entre eles, permitindo que o client envie requisições ao server e o server também envie requisições ao client e vice-versa.

É ideal para chats, dashboards atualizados em real time, etc.

---

## GraphQL

O GraphQL nasceu para permitir que clients consigam especificar quais são os dados que eles querem buscar de uma determinada API no seu backend. Ele resolve problemas onde se um determinado endpoint retorna um response muito extenso, mas o client não precisa de todos estes dados, o GraphQL permitir que o cliente faça uma query (informando quais dados ele quer) e o backend consegue interpretar isso e retornar somente aquilo que foi especificado pelo client.

Essa forma de requisição é definida pelo que chamamos de Schema SDL (schema definition language) e é separado por 02 conceitos:
- Query: permite uma query para buscar dados
- Mutations: permite modificar determinados dados

Por de trás de um endpoint GraphQL existem os Resolvers, que dado a especificação da solicitação, fará a busca nos determinados data sources.

