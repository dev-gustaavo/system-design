# Teorema CAP, ACID, BASE e PACELC

## Introdução - Teorema CAP

**C**onsistency
**A**vailability
**P**artition Tolerance

É um modelo conceitual acadêmico, que nos ajuda a escolher uma base de dados. Basicamente são trade-offs que precisamos observar ao escolher um banco

Precisaremos escolher entre:
1. CA - Consistente e Disponível
2. CP - Consistente e Tolerante a Partição
3. AP - Disponível e Tolerante a Prtição

Obs.: Partição neste caso, é partição de rede e não partição do banco de dados

- Um banco de dados consistente, é o que na leitura, entrega o mesmo dado em qualquer partição
- Um banco de dados disponível, é o que garante disponibilidade em caso de falha em alguma partição de rede
- Um banco com tolerância a partição, é o que tem o mesmo dado em todas as partições

---

## Modelo ACID Transacional

**A**tomicidade
**C**onsistência
**I**solamento
**D**urabilidade

Estamos falando diretamente de bancos transacionais. Bancos de dados SQL

### Atomicidade

Atomicidade representa uma transação indivisível em operações em um banco de dados SQL. É basicamente o tudo ou nada

Exemplo:
> Imagine que nós faremos uma operação de transferência financeira entre duas pessoas. Neste caso, teremos que, verificar se a pessoa A tem saldo disponível, depois registrar o débito do valor da pessoa A e registrar o crédito (transferência) para a pessoa B. Todas essas operações estão contidas em uma transação, que **não** pode ser divisível, ou seja, eu não posso fazer o débito da pessoa A, sem fazer o crédito para a pessoa B e não posso fazer o crédito para a pessoa B sem fazer o débito para a pessoa A. Isso seria inconsistente. Levando isso para uma operação real, nós teríamos mais ou menos a seguinte estrutura em um banco de dados:

```sql
begin;

select * from pessoa p where p.id = 1; -- verificação do saldo

update pessoa p set saldo = saldo - 100 where p.id = 1; -- débito

update pessoa p set saldo = saldo + 100 where p.id = 2; -- crédito

-- caso tudo funcione corretamente
commit;

-- caso algum erro aconteça (seja ele qual for)
rollback;
```

Ou seja, ou eu commito tudo, ou eu faço rollback de toda a operação. Em caso de fazer rollback, **todas** as operações realizada dentro do `begin` serão revertidas

### Consistência

Garantia que todas as regras definidas em um schema de um banco de dados SQL serão respeitadas. Por exemplo, a definição de uma tabela:

```sql
CREATE TABLE IF NOT EXISTIS carros (id INTEGER PRIMARY KEY, marca TEXT NOT NULL);
```
No exemplo acima, se eu tentar inserir um registro na tabela `carros` com um id que é um texto, o próprio banco de dados vai recusar essa inserção, dado que o dado não é **consistente**. O mesmo acontecerá se caso eu não preencher a marca, pois ele é um atributo não nulo

Consistência no modelo ACID é o que consiste que os dados inseridos estão em conformidade com o schema definido

Outro ponto super importante com relação a consistência é que, após uma transação ser efetuada, é garantido pelo banco de dados que **todas** as réplicas/nós que disponibilizam o dado, estão com o dado mais fiel e atualizado

### Isolamento

O isolamento ele representa isolamento entre transações que estejam acontecendo num banco de dados. Ele garante que uma transação não consiga interferir em outra transação. Por exemplo, se eu tenho a transação A e a transação B iniciadas ao mesmo tempo ou muito próximas uma da outra, cada transação terá sua informação de forma isolada:

```sql
-- TRANSACAO 1
BEGIN;

INSERT INTO clientes VALUES (1, "GUSTAVO");

SELECT * FROM clientes c WHERE id = 1;

-- O resultado da query acima será 
-- 1 | GUSTAVO

UPDATE clientes SET nome = "GUSTAVO TAVARES" WHERE id = 1;

COMMIT;
```

```sql
-- TRANSACAO 2
BEGIN;

-- Considere que o SELECT abaixo acontecerá antes do COMMIT da TRANSACAO 1
SELECT * FROM clientes c WHERE id = 1;

-- O resultado da query acima será
-- 1 | GUSTAVO

-- Considere que o UPDATE abaixo acontecerá antes do COMMIT da TRANSACAO 1
UPDATE clientes SET nome = "JOAQUIM" WHERE id = 1;

-- O UPDATE acima terá que esperar o update da TRANSACAO 1, pois a tabela ja está sendo modificada em uma transação iniciada anteriormente. Elas entram em uma fila de espera
```

### Durabilidade

Uma vez que uma transação é confirmada, ela garante que o dado estará escrito em disco, independente de ações arbitrárias que aconeçam no meu banco de dados. Um dado só poderá ser modificado caso outra transação inicie e modifique este dado

Por exemplo, uma vez que eu tenha o dado escrito numa tabela no disco, se o banco de dados cair, este dado não será perdido, diferente de bancos de dados em memória como o REDIS

---

## Modelo BASE e Estado Eventual

O BASE e ACID são excludente. Ou é BASE ou é ACID

BASE é mais voltado para performance e disponibilidade em troca da consistência. É mais focado em bancos de dados de sistemas distribuídos

**BA**sic Availability
**S**oft State
**E**ventual Consistency

### Basicamente Disponível

Isso significa que o banco sempre estará disponível (ou quase sempre). Indica que se um nó falhar, ele ainda vai dar uma resposta ao consumidor, no entanto, ele não garante consistência, pode ser que algum dado esteja desatualizado por um certo momento

Ele é um conceito que trabalha com múltiplos servidores e nós

Projeto para ambientes de larga escala e alta demanda

### Soft State

Indica que os dados podem mudar seu estado sem intervenção de algo externo. O próprio banco de dados pode mudar automaticamente o estado de um dado.

Exemplos disso são definições de TTL em um banco de dados em memória (Redis, Memchace, etc).

Outro exemplo pode ser o schema less de bancos de dados No SQL.

### Eventualmente Consistente

Uma consistência eventual, indica que por um período curto de tempo, o dado entregue numa leitura pode não ser o dado mais atualizado possível

Ao escrever em um banco de dados **eventualmente consistente**, o banco pode te responder que a operação já foi feita, no entanto, ele pode ter enfileirado essa escrita e ainda estar replicando este dado

Então imagine que você criou um registro no banco por uma API, num DynamoDB e que você faça uma query e leitura deste dado logo após. Pode ser que você não encontre o dado ou encontre ele numa eventual defasagem. Em troca disso, estes tipos de banco de dados te entregam uma performance maior do que bancos **consistentes**

---

## Componentes do CAP

**C**onsistency
**A**vailability
**P**artition Tolerance

### Consistency

A consistência indica que um dado escrito em um nó, ele também estará escrito em todos os outros nós de réplica. Isso faz com que bancos de dados que englobem o **C** do teorema **CAP**, ele majoritariamente é **ACID**, que por sua vez majoritariamente é um banco de dados SQL.

Imagine que estamos escrevendo um dado em um banco de dados que possui 03 réplicas. Ao escrever o dado, o banco de dados só me confirmará a resposta, quando **todas** as réplicas tiverem o dado escrito, enquanto isso, o cliente continua aguardando a resposta. O que indica que é um banco de dados mais lento, no entanto, é consistente.

### Availability

A disponibilidade do teorema CAP, indica que ele sempre (ou quase sempre) estará disponível. Ele assegura que as requisições terão uma resposta, em detrimento da consistência.

É indicado para sistemas que tem uma taxa escrita muito alta e de alta performance. E por não garantir consistência, pode ser que ao ler um dado, você pode não ter a visão mais atual. Portanto, bancos de dados que se enquadram no **A** do teorema **CAP** é majoritariamente **BASE**.

### Partition Tolerance

Aqui quando estamos falando de bancos de dados tolerantes a partição, não quer dizer que é possível particionar uma tabela em dados diferentes (partição por data, por tipo de cliente, etc). Tolerância a partição no teorema CAP quer dizer que ele é tolerante a uma partição de rede.

Quando um banco de dados possui várias réplicas, o cliente pode fazer operações de escritas em réplicas diferentes, por exemplo, ele pode fazer a escrita do dado na réplica A e depois este mesmo dado será replicado para a réplica B e C. Ele pode escrever o dado na réplica C, que depois será replicado para a réplica A e B.

Quando ocorre de haver uma **partição de rede** entre duas réplicas, o banco de dados consegue continuar operando normalmente e isso que indica a tolerância a partição. Por sua vez, bancos de dados que se encaixam no **P** do teorema **CAP**, são eventualmente **BASE**, pois se enquadram em **eventualmente consistente**, pois você pode tentar ler o dado de uma réplica que ainda não foi atualizada e ter um dado desatualizado.

Outro ponto importante aqui é que, bancos de dados tolerantes a partição, tem a capacidade de sincronizar os dados após a falha de partição de rede de forma automática.

---

## Combinaçẽos do teorema CAP

O teorema CAP ele é um teorema de escolha 2. O teorema indica que nunca poderemos ter as três capacidades juntas, sempre precisaremos escolher somente 2 em um banco de dados.

### CP (Consistência e Tolerância a Partição)

Como a própria combinação já diz, são bancos de dados que priorizam **Consistência** e **Tolerancia a Patrições** em troca da disponibillidade. Uma vez que ele prioriza a consistência, caso haja alguma partição de rede, ele possui mecanismos que façam com que o banco desative todo e qualquer acesso a partição com falha ou até mesmo corte a resposta, evitando um dado inconsistente.

Ele é o meio do caminho entre bancos BASE e ACID.

Exemplos de bancos de dados CP:
1. MongoDB (depende da configuração)
2. Cassandra
3. Coachbase
4. Etcd
5. Consul

### AP (Disponibilidade e Tolerância a Patrições)

São bancos que priorizam na sua ponta mais alta a **disponibilidade**. Ele garante que sempre estará disponível para escrita e leitura em troca da consistência. É mais indicado para sistemas que precisam de um alto throughput, estamos mais próximo de bancos de dados **BASE**. Além disso, é tolerante a patrição.

Exemplos de bancos de dados AP:
1. DynamoDB
2. CouchDB
3. Cassandra (depende da configuração)
4. SimpleDB

### CA (Consistência e Disponibilidade)

São bancos que priorizam a **consistência** em sua ponta mais alta. São bancos que preferem estar indisponíveis, do que entregar um dado inconsistente. Eles também priorizam alta disponibilidade, as transações precisam ser atômicas, então são bancos de dados mais próximos do **ACID**.

Caso haja algum particionamento de rede, a **operação vai falhar por completo**, por ele não ter tolerância a partição.

Exemplos de bancos de dados CA:
1. MySQL/PostGree
2. SQL Server

---

## Teorema PACELC

É uma extensão do teorema CAP, não são teoremas excludentes. Ele é um teorema que me ajudará a escolher entre situações de partição de rede e quando meu sistema não estiver em partição de rede

Por isso, PACELC é um acrônimo para:

ON
**P**artition
**A**vailability
**C**onsistency
**E**lse
**L**atency
**C**onsistency

O que isso quer dizer na prática. Se meu sistema estiver em particão de rede, ou seja, (ON **P**artition) -> então priorizo **A**vailability OU **C**onsistency se não (**E**LSE) priorizo **L**atency OU **C**onsistency

Vamos a alguns exemplos. Imagine um sistema em condições normais, que não está sofrendo com partição de rede:
> Eu vou priorizar um sistema com baixa latência OU vou entregar uma alta consistência, ou seja, o dado mais fiel possível. São trade-offs, se quiser baixa latência, pode ser que o banco não te entregue alta consistência OU se quiser consistência, pode ser que não te entregue baixa latência

Agora imagine um sistema que esteja com um nó em falha, ou seja, em partição de rede:
> Eu vou priorizar a disponibilidade OU a consistência do dado. Se quiser priorizar a disponibilidade, ou seja, o banco vaite responder, pode ser que e não te entregue o dado mais fiel. Por outro lado, se quiser o dado mais fiel, ou seja, priorizar consistência, pode ser que o banco não te responda, dado que ele pode te dar uma resposta errada, caso esteja com partição de rede

Tudo isso significa que, mesmo em situações normais, eu sempre vou ter que priorizar algo

## Combinações PACELC

1. PA/EL (On Partition, Availability; Else, Latency)
2. PC/EL (On Partition, Consistency; Else, Latency)
3. PA/EC (On Partition, Availability; Else, Consistency)
4. PC/EC (On Partition, Consistency; Else, Consistency)

### PA/EL (On Partition, Availability; Else, Latency)

Essa combinação indica que, em condições normais, o banco priorizará baixíssima latência em detrimento a consistência do dado. Agora, quando estiver em partição de rede, ele priorizará a disponibilidade, ele vai responder, não garantindo a melhor latência possível, mas vai responder, mesmo que algum dado esteja errado ou desatualizado. Essa combinação é majoritariamente **BASE**

### PC/EL (On Partition, Consistency; Else, Latency)

Essa combinação indica que, em condições normais, o banco priorizará baixa latência, mesmo que haja uma pequena consistência eventual. Já em condições de partição de rede, ele vai priorizar 100% a consistência, podendo até inviabilizar consultas, caso haja um particionamento de rede, derrubando todo o cluster do banco de dados. Por isso ele é majoritariamente **ACID**

### PA/EC (On Partition, Availability; Else, Consistency)

Essa combinação indica que, em condições normais, o banco priorizará a consistência do dado, ou seja, vai entregar o dado mais atualizado possível. Já em condições de particionamento de rede, ele priorizará a disponibilidade, em detrimento a consistência do dado. Pode ser que, caso haja uma indisponibilidade de algum nó ou réplica, ele te responda um dado desatualizado

### PC/EC (On Partition, Consistency; Else, Consistency)

Essa combinação indica que ele **SEMPRE** priorizará a consistência. Em casos normais, ele vai te entregar o dado mais atualizado possível em caso de particionamento de rede, ele vai quebrar, ele não vai te dar uma resposta. É melhor falhar, do que entregar o dado inconsistente