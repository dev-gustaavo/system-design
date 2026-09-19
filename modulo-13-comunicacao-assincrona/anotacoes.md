# Comunicações Assíncronas

Comunicações ou arquiteturas assíncronas elas existem para apoiar arquiteturas já complexas e não para tornar uma arquitetura simples mais complexa. Ela existe para reduzir dependências temporais entre serviços, resolver acoplamento forte, latência e dispnibilidade.

Uma arquitetura assíncrona ela é desacoplada by default, traz mecanismos de resiliência embutidos como DLQ e retry, permitindo trabalharmos com eventos e mensagens (existe uma diferença conceitual entre os dois tipos).

## Mensagens e Eventos

### Mensagens

Uma mensagem trafegada entre dois sistemas, funciona através de um produtor, um intermediário (a fila) e um consumidor. A arquitetura de mensageria presume sempre 1 para 1, ou seja, para um produtor eu terei um único consumidor daquela mensagem.

Uma mensagem ela caracteriza um comando, como algo imperativo e que também representa continuidade de um processo.

As filas elas podem ser ordenadas ou não, a depender de como a configuração for feita.

### Eventos

Quando estamos falando de eventos, ele funciona de forma a notificar algo, como se algo tivesse terminado e o consumidor gostaria de consumir este evento para fazer algo.

Neste caso, o produtor não conhece os consumidores e podem haver N consumidores de um tópico de eventos.

Uma arquitetura orientada a eventos tem uma natureza mais desacoplada, dado que o produtor não quer e nem precisa saber quem são os consumidores e nem o que eles farão com este evento. Os consumidores são interessados neste evento por algum motivo.

### Command e Event Patterns

De acordo com a ciência da computação, eventos são mensagens em um nível. Nós temos três tipos de eventos:

1. Documento: um documento representa uma entidade anêmica. Algo enviado como mensagem/evento contendo dados que não farão algo. O cadastro de um cliente por exemplo.
2. Mensagem: como vimos, a mensagem é algo imperativo e ativo. Tem a característica de dar continuidade em um processo, por exemplo: faça o envio de e-mails, ou, cadastre este cliente.
3. Evento: como vimos, o evento é algo reativo e tem característica de notificar que algo ocorreu. Por exemplo: uma venda foi feita, ou, o cadastro do cliente foi efetivado.

## Conceitos de Mensageria

### FIFO (First In, First Out)

Filas FIFO tem em sua essência o sentido literal de uma fila. O primeiro a entrar é o primeiro a sair. Ela tem essa característica, pois cada mensagem enfileirada, segue uma ordem, que é: a primeira mensagem que entrou, será a primeira a sair. É adequada para situações ou sistemas que demandam ordenação de algo.

Existem flexibilidades em cima deste tipo de fila, eu posso ter duas características, sendo que uma eu literalmente travo toda a fila até a primeira mensagem que entrou sair e outra que posso trabalhar de forma mais flexível, liberando a fila mesmo sem ter processado com sucesso uma mensagem, tratando ela posteriormente.

### LIFO (Last In, First Out)

O LIFO é o contrário do FIFO. Ele indica que o último elemento que entrou na fila, será o primeiro elemento a ser  consumido. Ele trabalha numa estrutura de pilhas (stack).

Imagine uma pilha de pratos para lavar. O último prato colocado, será o primeiro a ser lavado.

Não é comumente utilizado, no entanto, é uma estrutura de dados existente para mensageria, bastante utilizada quando você precisa recompor algo. Pensando no sistema operacional, o CTRL + Z trabalha com pilhas, ou seja, quando você pressiona este atalho, ele remove o último elemento adicionado na pilha :)

### Fanout

O fanout ele é utilizado quando uma mesma mensagem precisa ser enviada para um grupo grande de consumidores. É bastante parecido com o conceito de eventos. É uma estratégia utilizada para replicação de dados e não notificação de eventos.

### DLQ (Dead Letter Queue)

Uma DLQ é um mecanismo de fallback para mensagens. Ela funciona como um mecanismo que armazena mensagens que não puderam ser consumidas ou por que tentamos processar essa mensagem várias vezes e não deu certo, por qualquer que seja o motivo.

Isso existe para não impactar outras mensagens que estejam na fila e precisam ser processadas. Por exemplo, imagine que temos uma fila de mensagens para cadastrar um cliente. Se determinada mensagem chega na fila faltando um dado obrigatório, o consumidor não conseguirá processá-la corretamente. O ideal é que esta mensagem seja levada para a DLQ, para que posteriormente a gente analise e entenda o motivo de não ter sido processada.

Existem meios de reprocessar as mensagens de um DLQ, fazendo um redrive.

Uma DLQ tem um limite, as mensagens que ficam lá, são configuradas para ficar por um determinado momento e podem ser excluídas de lá.

### Processamento em Batches

Processamentos em batches indicam o acumulo de algo que será processado em lote em um determinado momento. Ele é o motivador de comunicações assíncronas e tem a característica de serem agendados. Vou processar o relatório do último mês sempre no dia 05 de cada mês, por exemplo.

## Streaming de Dados

Streaming de dados indica uma arquitetura que faz envio de dados para N consumidores, que processarão estes dados para tomada de uma decisão NRT (near real time).

Alguns exemplos disso são:
1. Motores de fraudes: recebe eventos de compra de vários clientes e processam isso e conseguem indicar compras fraudulentas;
2. Recomendações da Netflix: recebe eventos de séries e filmes que você assiste e consegue muito próximo do tempo real te indicar uma nova série ou filme, baseado no que você assistiu.
3. Redes sociais: recebem eventos de stories que você viu, posts que curtiu e te recomendam coisas parecidas.

## Event Driven

Event Driven são arquitetura orientadas a eventos, que produzem eventos de um determinado domínio e distribui para outros N domínios, que precisam processar este evento.

É um padrão arquitetural que visa desacoplamento entre os domínios e arquitetura complexas que precisam processar um alto volume de dados muito próximo do tempo real.

Imagine um sistema de vendas. Quando o vendedor registra esta compra, que é do domínio de Vendas, ele distribui o evento para os domínios Fiscal (que vai emitir a nota), Pagamento (que vai faturar o pedido), Frete (que vai enviar o pedido).

## Protocolos de Mensageria

### Kafka, Streaming e Eventos

Kafka é uma plataforma de streaming, projetada intencionalmente para lidar com alto volume de dados, permitindo performance na produção e consumo de dados.

O Kafka é baseado em produção e consumo.

#### Producer

Responsável por publicar eventos em tópicos Kafka. Podem ou não especificar qual partição o evento será publicado. Caso não seja especificado, o próprio Kafka se encarregará de fazer essa distribuição.

Chave de partição é importante quando queremos garantir que um consumidor obtenha sempre os dados de um produtor, dando experiência de continuidade e ordem. Isso pode acarretar um problema que chamamos de Hot Partition, onde o produtor envia um volume muito grande de dados para um único consumidor.

Usar chave de partição pode ser importante quando eu preciso garantir ordenação no consumo dos eventos e não permitir que eventos sejam consumidos fora de ordem.

Replication Factory, o producer pode querer aguardar um ACK do broker de eventos. Ou seja, eu faço a produção e aguardo o broker sinalizar que recebeu o evento. Quanto maior o número de ACK, maior a confiabilidade da entrega do dado. Podemos não querer receber a confirmação de recebimento em determinados cenários, por exemplo: se eu quiser contar a quantidade de clicks em um botão em uma página, tudo bem se eu perder uma quantidade de eventos. Agora se eu estiver falando de eventos de transações bancárias, é necessário que eu receba todos os eventos, exigindo um número de ACK maior.

Outra forma de escrita em tópicos Kafka é o Batch Size. O Batch Size ele permite o acumulo de mensagens para posterior envio ao tópico, podemos parametrizar por exemplo o acumulo de 1000 mensagens para que então façamos a publicação no tópico.

Junto com o Batch Size podemos trabalhar também com o Linger Time. O Linger Time é o tempo parametrizado para envio das mensagens ao tópico sem que o Batch Size tenha chego no limite, bastante importante para não atrasarmos o envio de mensagens. Por exemplo, imagine um Batch Size de 1000, o Linger Time pode ser configurado para 1 minuto. Isso indica que, se em 1 minuto não tivermos chego em 1000 mensagens, elas serão enviadas na quantidade acumulada até o momento.

O Batch Size mantém mensagens em memória, portanto, há risco de perda de mensagens caso haja alguma intercorrência antes da publicação no tópico.

#### Consumer

Responsável por consumir eventos uma ou mais partições de tópicos Kafka. Consumidores não compartilham partições, são sempre 1 para 1 (1 consumer para 1 partição). Imagine um tópico Kafka com 04 partições. Se eu tenho dois consumidores, serão 02 partições para cada consumidor.

Podemos permitir leitura do mesmo dado por consumidores com propósitos diferentes