# Escalabilidade, Performance e Capcity

## Performance

Performance representa o quão rápido meu sistema consegue processar uma ou mais transações. O quão rápido ou não meu sistema é, representa um sentimento do usuário final.

A performance ela precisa ser traduzida em números para que possamos metrificar o quão lento ou o quão rápido está meu sistema. Deve conseguir metrificar requisitos funcionais e não funcionais.

### Métricas de Preformance

As métricas variam a depender das condições:

- Picos e spikes de carga
- Falhas de componentes
- Mudanças de padrão de uso
- Mudanças de design
- Mudanças de Business Cases
- Mudanças de implementações

Frameworks starters (não sabe por onde começar, comece por aqui):
- Service levels (SLAs, SLOs)
- Four Golden Signals
- RED
- USE

Todos esses citados, ele representam o básico e mínimo para começarmos a metrificar o nosso sistema. Encontrar o monitoramento ideal pode levar tempo e não é tão trivial.

### Four Golden Signals

É um conceito dentro de monitoramento e observabilidade, popularizado pela Google no livro SRE. Representa 04 sinais de ouro que nos ajuda a monitorar nosso sistema, que são:

- Saturação: o quanto de um recurso está em uso
- Tráfego: o quanto de operações estão acontecendo
- Tempo de resposta: tempo total das operações
- Taxa de erros: quanto dessas operações estão falhando

A ideia aqui é dar o mínimo de monitoramento.

#### Saturação

Remete a utilização de um recurso e ajuda a responder o quanto de um recurso está sendo utilizado. Ele metrifica a utilização de CPU, memória, disco, conexões com bancos de dados, rede. Quando um recurso está sendo considerado saturado, indica que ele está chegando ao nível máximo de utilização, por exemplo, minha aplicação está com um consumo de 50% de  CPU. Isso pode indicar que a aplicação não está saturada. Agora se a aplicação estiver usando 98% de  CPU, pode indicar uma aplicação saturada.

Para chegarmos na métrica, o cálculo matemático é simples: (taxa de utilização / máximo permitido de utilização) * 100.

#### Tráfego - Throughput

Indica o número de operações que um sistema consegue realizar em um determinado período de tempo. Essa é uma métrica temporal.

É quantas requisições estou atendendo em um determinado período de tempo. Ela é associada a uma métrica de negócio, por exemplo: vendas por segundo, cadastro de propostas por segundo.

Não necessariamente precisa ser por segundo, a gente pode trabalhar com segundo, hora, minuto, dia, mês.

A fórmula matemática por detrás é: unidade de trabalho processada / tempo

#### Tempo de Resposta

É o tempo total que um cliente espera para receber uma resposta do servidor. Inclui latência e processamento do servidor. É a soma dos dois.

- Latência: conexão entre o cliente e servidor
- Tempo de processamento: quanto tempo o servidor processa a requisições
- Tempo de resposta: é a soma dos dois

#### Taxa de erro

Porcentagem de quantas requisições resultaram em algum erro. É a ponta final do monitoramento.

Quanto menor a taxa de erro, mais confiável o sistema é.

Matematicamente representamos da seguinte forma: (número de erros / número de tentativas) * 100

### Percentis

Os percentis nos ajuda a remover o problema da média. Nós usamos essa métrica como P90, P95, P99. Cada um desses, representa o percentual que minhas respostas ocorreram abaixo de algo. Por exemplo, se minha aplicação está respondendo em um P90 De 300ms, indica que 90% das requisições responderam abaixo de 300ms e 10% responderam acima disso.

## Capacidade

A capacidade é a quantidade máximo de trabalho que o sistema consegue receber e processar de forma eficaz, dentro de um determinado período de tempo. É encontrar o limite atual do nosso sistema, considerando ele como um todo (CPU, memória, rede, etc).

Planejar a capacidade de um sistema é importante para poder lidar com momentos de picos, encontrar o dimensionamento correto da aplicação e lidar com rescimento orgânico.

## Gargálo de Capacidade

Refere ao ponto do sistema onde o meu desempenho ou a capacidade chegam ao limite. Os gargalos podem acontecer em diversas partes de nossa arquitetura, seja CPU, memória, infraestrutura de rede, banco de dados, latência, código mal otimizado, tratando algoritmos de forma ineficiente.

Quando conseguimos encontrar onde está o gargálo, conseguimos otimizar nosso sistema e melhorar a performance dele.

## Backpressure de Capcidade

É a resistência a um fluxo desejado. É quando intencionalmente ou não, eu faço o blqueio ou resistência de um fluxo, para conter determinadas solicitações ou fluxos.

Imagine por exemplo uma arquitetura composta por 03 microsserviços, que opera da seguinte forma:

- Microsserviço 1: capacity de 100 tps
- Microsserviço 2: capacity de 60 tps
- Microsserviço 3: capacity de 90 tps

Agora imagine que, dentro deste cenário, a gente tenha um cenário onde estão recebendo 50 tps. Neste cenário, os 03 microsserviços trabalharão de forma otimizada.

Agora imagine que, dentro deste cenário, a gente tenha solicitações com 90 tps. O microsserviço 1 trabalhará bem, o microsserviço 2 não, ele processará 60 tps e represará 30 tps. Já o microsserviço 3, ele processará bem. Aqui neste cenário o backpressure (ou pressão contrária) estará no microsserviço 2.

O segundo exemplo acima tende a aumentar exponencialmente, pois novas requisições continuarão chegando e a degradação da arquitetura pode começar por ai.

A nossa arquitetura de microsserviço é tão resistente quanto o microsserviço mais fraco.

## Escalabilidade

É a capacidade de um sistema ou aplicação de crescer e lidar com a carga de trabalho, sem degradar nosso desempenho ou ter uma ineficiência sistemica. E também representa a capacidade de reduzir a força de trabalho para manter o fluxo normal de trabalho.

### Escalabilidade Vertical

Representa aumentar e diminuir os recursos computacionais do servidor (CPU, memória, disco, por exemplo). É como deixar um único servidor mais parrudo.

Normalmente escalabilidade vertical se aplica em servidores de banco de dados, onde é mais difícil escalar horizontalmente.

Existem limitações físicas para este tipo de escalabilidade, limites de custo, geográfico.

A escalabilidade vertical também pode estar representada em melhorias de algoritmos, a fim de melhorar a performance.

### Escalabilidade Horizontal

Representa em adicionar ou remover novas unidades computacionais sob demanda. Não é aumentar hardware ou infra de um mesmo servidor, mas sim duplicar ela.

Se estivermos trabalhando em um cluster com vários nós, podemos ter novas pods, tasks, containers, sendo balanceados as requisições atrás de um load balancers.

## Métricas de Escala

Importante considerar métricas das aplicações para conseguirmos estimar o quanto precisamos adicionar de novos recursos para ela.

Usando a fórmula do HPA (Horizontal Pod Autoscaling), podemos chegar a um número um pouco mais fiel de quanto precisamos aumentar a nossas réplicas para que ela atenda a demanda desejada.

A fórmula é representada da seguinte forma:

Réplicas Desejadas = Réplicas Atuais * (Valor Atual da Variável / Valor Desejado da Variável)

A variável aqui pode ser representada por CPU, TPS, etc. Para ter estes valores, precisamos utilizar os cálculos de troughput, utilização de CPU, por exemplo.

Se estivermos falando de capacidade de CPU, seria o seguinte:

Utilização de Recurso = (Recurso Solicitado / Recurso Disponível) * 100 -> representa cálculo da Saturação

Então vamos imaginar o seguinte cenário:

Réplicas atuais: 3
CPU solicitada: 90
CPU disponível: 100
CPU desejada: 50

Saturação: (90 / 100) * 100 = 90

Réplicas desejadas: 3 * (90 / 50) = 6

Ou seja, para chegar em 50 de utilização de CPU eu deveria ter 6 réplicas da minha aplicação.