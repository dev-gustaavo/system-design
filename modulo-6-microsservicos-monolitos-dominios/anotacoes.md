# Microsserviços, Monolitos e Domínios

## Monolitos

Uma arquitetura monolítica representa que todas as capacidades de um software estão em um único conjunto ou todos os domínios estão em um único conjunto. Por exemplo: capacidades de cadastro de cliente, pagamento, controle de estoque.

Uma arquitetura monílitica não representa algo ruim. Ela tem seus benefícios e malefícios conforme todas as outras arquiteturas.

As vantagens de se pensar em uma arquitetura monolítica são:
- Geralmente são sistemas que estão iniciando e mais simples
- Trabalha com menos dependências externas
- Não tem overhead de vários protocolos de comunicação
- Geralmente se trabalha com um único banco de dados
- Validações mais simples, sem dependências externas
- Facilidade de testes e integrações
- Deploy unificado, sem necessidade de orquestrar vários deploys de peças diferentes

As desvantagens de uma arquitetura monolítica são:
- Conforme o software cresce, validações se tornam complexas
- Muitas pessoas trabalhando num mesmo codebase e desafios de code merge, code reviews, atenções e guard rails para evitar códigos ruins em produção
- Um único banco de dados pode trazer uma performance ruim para a aplicação
- Desafios em escalabilidade. Você precisa escalar todo sistema, enquanto só uma pequena parte dele precisa ser escalada
- Torna-se um único ponto de falha (se o cadastro de cliente degrada o sistema, ele se degrada como um todo)

---

## Microsserviços

É um estilo arquitetural onde o software é dividido em pequenas partes, geralmente distribuídas em domínios, de forma indivisível. Por exemplo, se tenho a capacidade de cadastro de cliente, essa capacidade é representada por um microsserviço dentro da minha arqutetura. Todo o ciclo de vida do microsserviço é independente, ou seja, do desenvolvimento ao deploy, ele é independente.

A comunicação entre vários microsserviços de se da por meio de protocolos de rede (HTTP, gRPC ou mensagerias).

As vantagens de uma arquitetura de microsserviços:
- Descentralização, podendo cada microsserviço ser desenvolvimento sozinho, em linguagens e framworks diferentes
- Resposanbilidade reduzida
- Escalabilidade independente. Caso eu queria escalar somente o microsserviço de cliente, eu consigo
- Os times conseguem trabalhar em microsserviços independentes, diminuindo acoplamento dos times em um mesmo code base


As desvantagens de uma arquitetura de microsserviços:
- Adiciona uma complexidade operacional muito maior
- Orquerstrar vários microsserviços é muito mais complexo do que um único monolito
- Controlar deploys, versionamento de APIs, coordenação do software como um todo
- Comunicação por rede terão problemas (latência, necessidade de resiliências, circuit brakers, retires, DLQs)
- Testes mais complexdos (distribuídos, integrados)
- Mocks podem nos dar falsos positivos
- Monitoração distribuída é mais complexa
- Correlação de logs

---

## Domain Driven Design

O Domain Driven Design ou DDD, visa aproximar ao máximo a escrita de software ao negócio que aquele software se propõe a resolver problemas. É uma metodologia que vai trabalhar linguagens de negócio e aproximar ao máximo na escrita do software.

Ajuda a separar de forma mais clara os domínios (cliente, pagamento, loja). Identifica limites entre domínios e funcionalidades de domínios diferentes.

DDD não é exclusivo de microsserviços. Ele pode ser aplicado em code bases de monolítos.

Uma entidade anêmia é uma entidade de domínio que não faz nada. É algo que devemos evitar.
