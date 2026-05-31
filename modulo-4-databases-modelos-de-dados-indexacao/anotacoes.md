# Databases, Modelos de Dados e Indexação

## Definindo um Database

É uma forma como organizamos dados em uma estrutura. É uma camada que abstrai a escrita e leitura de dados. É um software intermediário entre o cliente (software) e o disco, que abstraí a necessidade do desenvolvedor de ter que lidar  com operações de escrita e leitura no próprio disco, uma operação mais próxima do sistema operacional.

---

## Tipos de Bancos de Dados

Sempre ao escolher um banco de dados, precisamos ter em mente que tudo dependerá da feature a ser implementada. A escolha do banco de dados dependerá trade-offs, que você precisará escolher, como performance, consistência, etc.

### Bancos de Dados Relacionais (SQL)

Banco de dados relacionais ele veio por um modelo acadêmico de um cara chamado Modd, em 1970. Ele é um banco de dados de schema rígido, que possui tabelas, linhas e colunas e que permitem relacionamente entre si. É um banco de dados que está dentro do modelo ACID.

Bancos relacionais possuem schema rígido, pois nos permitem definir os atributos de forma rígida, como por exemplo, o atributo id será numérico, portanto, ele não aceitará dados do tipo booleano ou string.

Exemplos:
- MySQL
- MariDB
- Postgree
- SQL Server

### Bancos de Dados Não-Relacionais (NoSQL)

É um banco não relacional, sem schema rígido, permitindo inserir documentos de diversas formas. Os formatos que geralmente são aceitos: JSON, BSON, entre outros.

Um banco não relacional, como o próprio nome já diz, recomenda-se não relacionar objetos. É possível, mas você poderá em termos de performance. Se precisar de relacionamento entre as entidades, prefira um banco **relacional**.

São bancos de dados que priorizam performance, escalabilidade, performance em escrita e leitura, em troca de baixa consistência. São bancos de dados que geralmente estão em modleo BASE.

Exemplos:
- MongoDB
- DynamoDB
- Elasticsearch
- Cassandra
- CosmosDB
- Redis
- Memcached

### Bancos de Dados NewSQL

É uma proposta nova de bancos de dados, que visa trazer a consistência e confiabilidade de bancos relacionais, sem perder a performance para sistemas distribuídos.

É um modelo ainda com baixa maturidade, mas é basicamente um ACID com alta performance.

Exemplos:
- CockroachDB
- Google Cloud Spanner
- MemSQL
- AltiBase
- VoltDB

### Bancos de Dados em Memória (In-Memory)

São basicamente caches. São bancos de dados que escrevem o dado em memória RAM, ao invés de escrever o dado em disco. Isso nos ajuda a ter mais performance para vários casos de uso.

Dados escritos em memória são mais caros do que escrever e armazenar o dado em disco, então se eu tiver 1 TB de dados em memória é muito mais caro do que escrever 1 TB em disco.

Outro ponto importante aqui é que, ao reiniciar meu nó ou se houver algum problema onde o cache está, este dado será perdido, pois ele não está em um armazenamento durável.

Exemplos:
- Redis
- Memcached
- Valkey

### Bancos de Dados Time-Series (TSDB)

Voltados a armazenamento de séries temporais, muito voltados a estatística, sua indexação é feita em tempo, não possui feature de update, todo dado inserido é basicamente um append, ou seja, é um banco append only. Possui feature de expurgo automático, alta volumetria de escrita.

Exemplos:
- Timescale
- InfluxDB
- Prometheus
- VictoriaMetrics
- Graphite

---

## Níveis de Consistência

Fator mais importante para decidir qual é o banco de dados. A consistência ela é o que garante a confiabilidade da escrita e leitura do dado ao usuário.

### Consistência Forte

Bancos com consistência forte, ele vai priorizar isso em detrimento a tudo. Ele garante que a escrita do dado acontecerá em todos os nós, réplicas, etc, intependente de quanto isso demore ou falhará caso não aconteça, dando maior confiabilidade.

Qualquer resposta de commit, garante que os dados estejam em todas as réplicas.

Exemplos:
- MySQL
- MariaDB
- PostgreeSQL
- SQL Server
- Oracle
- Cassandra (Quorum ALL)
- ScyllaDB (Quorum ALL)
- DynamoDB (Consistency True) -> garante consistência, mas pagamos mais caro para a AWS

### Consistência Eventual

É o oposto da consistência forte. Indica que em um dado momento (segundos ou milisegundos) todas as réplicas do banco de dados estarão com o dado mais atualizado, independente do tamanho de escrita. Isso significa que, em momentos de consulta, pode ser que a gente não obtenha o dado mais fiel possível.

Estes tipos de databases são replicações asíncronas.

São focados em alta disponibilidade e alta performance.

Exemplos:
- ScyllaDB (Quorum)
- Cassandra (Quorum Baixo)
- DynamoDB
- CouchDB
- MongoDB
- Elasticsearch

---

## Modelos de Dados

Banco de dados é a engine intermediária entre a engine e o dado. Os dados precisam estar estruturados e armazenados de alguma forma, para que possa ser acessado e alterado de alguma forma.

### Row Oriented

O row oriented, é um modelo de tupla. Tupla indica linha e coluna (ou linha e atributo). São dados que ficam armazenados dentro de uma única linha no banco de dados, o que indica também que essa linha está escrita em um bloco dentro do disco, o que melhora a performance no momento de consulta.

O modelo de linha também indica que leitura, escrita, update, deleção acontecem inteiramente numa linha.

### Document-Oriented

Tratamento entidades gravadas no banco de forma autônoma e de forma autocondita. Tem um schema livre e flexível, não temos verificações de rigidez, armazenados em JSON. Facilita a evolução do modelo de dados, sem mgirações complexas.

Em bancos dados orientados a documentos, não temos relacionamento entre tabelas, geralmente todos os dados já estão inseridos dentro da estrutura do documento, sepração por objetos e listas dentro do documento pai.

### Column Oriented

O banco de dados orientado a colunas é bem parecido com o orientado a linhas. Você tem estrutura de tabelas, que podem se relacionar entre si. Você pode executar consultas SQL e queries complexas, no entanto, a principal diferença está em como e onde o dado fica escrito no disco. Diferente de banco de dados orientado a linhas, o banco de dados orientado a colunas, armazena os dados em disco agrupando as colunas e não as linhas. Por exemplo:

> Imagine um banco de dados orientado a linhas. As informações seriam escritas em disco sequencialmente da seguinte forma:

#### Exemplo: Escrita Orientada a Linhas

**Tabela de Usuários (em memória):**

| ID | Nome | Email | Idade |
|---|---|---|---|
| 1 | Alice Silva | alice@example.com | 28 |
| 2 | Bob Santos | bob@example.com | 35 |
| 3 | Carol Lima | carol@example.com | 42 |

**Representação em Disco (Modelo Row-Oriented):**

| Posição em Disco | Conteúdo |
|---|---|
| Bloco 1 | `[1][Alice Silva][alice@example.com][28]` |
| Bloco 2 | `[2][Bob Santos][bob@example.com][35]` |
| Bloco 3 | `[3][Carol Lima][carol@example.com][42]` |

**Explicação:**
- Cada **linha completa** é armazenada sequencialmente em um bloco de disco
- Uma única operação de leitura/escrita pode recuperar **todos os atributos** de um registro (ID, Nome, Email, Idade)
- Ideal para consultas que precisam de múltiplas colunas de um registro
- Mais eficiente para INSERT, UPDATE e DELETE em registros completos

> Imagine um banco de dados orientado a colunas. As informações seriam escritas em disco sequencialmente da seguinte forma:

#### Exemplo: Escrita Orientada a Colunas

**Mesma Tabela de Usuários (em memória):**

| ID | Nome | Email | Idade |
|---|---|---|---|
| 1 | Alice Silva | alice@example.com | 28 |
| 2 | Bob Santos | bob@example.com | 35 |
| 3 | Carol Lima | carol@example.com | 42 |

**Representação em Disco (Modelo Column-Oriented):**

| Posição em Disco | Coluna | Conteúdo |
|---|---|---|
| Bloco 1 | ID | `[1][2][3]` |
| Bloco 2 | Nome | `[Alice Silva][Bob Santos][Carol Lima]` |
| Bloco 3 | Email | `[alice@example.com][bob@example.com][carol@example.com]` |
| Bloco 4 | Idade | `[28][35][42]` |

**Explicação:**
- Cada **coluna** é armazenada sequencialmente em seus próprios blocos de disco
- Dados da mesma coluna ficam juntos no disco, facilitando compressão
- Uma operação de leitura recupera apenas os atributos necessários
- Ideal para consultas analíticas que leem poucas colunas de muitos registros (ex: SELECT Idade FROM usuarios)
- Mais eficiente em termos de I/O quando você precisa de dados específicos

Exemplos:
- Amazon Redshift
- Google BigQuery
- Snowflake
- MariaDB ColumnStore

---

### Wide-Column (Coluna Larga)

É um banco de dados que possui o armazenamento por linhas, no entanto, cada linha pode ter o seu conjunto de colunas, como se fossem famílias de colunas. Então pense numa tabela de usuários. Cada usuário tem id, nome, email e atributos. Os atributos representam uma família de colunas, dentro da família atributos, eu posso ter role, cor favorita, comida favorita. Eu posso ter o atributo personal_info, que é a família para as colunas email, telefone, endereço.

Quando uma consulta explícita via query é feita, dependendo do atributo que você utilize como filtro, o banco buscará somente as linhas/registros que contém aqueles atributos.

Casos de uso:
- Data Lakes
- Séries Temporais

Os dados são armazenados por família de colunas no disco, o que faz com que a consulta seja mais performática em termos de consultarmos dados de uma mesma família.

São muito pensando para databases distribuídos, com consistência eventual, mas pode executar transações atômicas e joins quando configurados. Esses tipos de bancos de dados podem ter até milhares de nós distribuídos, com dependência grande de sharding e replicação de dados.

#### Exemplo: Tabela de Usuários em Wide-Column

| Row Key | Column Family | Column | Value |
|---|---|---|---|
| user_001 | personal_info | name | Alice Silva |
| user_001 | personal_info | email | alice@example.com |
| user_001 | personal_info | age | 28 |
| user_001 | contact_info | phone | +55 11 98765-4321 |
| user_001 | contact_info | city | São Paulo |
| user_002 | personal_info | name | Bob Santos |
| user_002 | personal_info | email | bob@example.com |
| user_002 | personal_info | age | 35 |
| user_002 | contact_info | phone | +55 21 99876-5432 |
| user_002 | contact_info | city | Rio de Janeiro |
| user_003 | personal_info | name | Carol Lima |
| user_003 | personal_info | email | carol@example.com |
| user_003 | personal_info | age | 42 |
| user_003 | contact_info | phone | +55 85 97654-3210 |
| user_003 | contact_info | city | Fortaleza |

Exemplos de bancos:
- CassandraDB
- ScyllaDB
- DynamoDB
- CosmosDB

### Key Value (Chave-Valor)

Tipo mais simples de bancos de dados NoSQL que podemos encontrar. Tem schema fraco e recebe um identificador, que é a chave e um valor, que pode ser basicamente qualquer coisa. Eu poderia ter algo assim:

#### Exemplo: Armazenamento Key-Value de Usuários

| Key | Value |
|---|---|
| user_001 | `{"name": "Alice Silva", "email": "alice@example.com", "age": 28}` |
| user_002 | `{"name": "Bob Santos", "email": "bob@example.com", "age": 35}` |
| user_003 | `{"name": "Carol Lima", "email": "carol@example.com", "age": 42}` |
| session_abc123 | `{"user_id": "user_001", "login_time": "2026-05-31T10:30:00Z"}` |
| cache_produto_456 | `{"id": 456, "name": "Notebook", "price": 3500}` |

### Grafos

São bancos de dados que armazenam dados que contém relação entre propriedades. É um banco de dados composto por vértices e arestas, sendo que um vértice pode estar ligado a outro vértice através de uma aresta.

Alguns casos de uso:
1. Quando você recebe indicações de amizades em redes sociais, elas podem estar sendo providas de bancos de dados de grafos. Isso acontece por que Maria tem amizade com João e você tem amizade com Maria, logo, eventualmente João pode ser seu amigo
2. Em aplicativos de localização. Quando você usa Waze ou Google Maps, ele traçará a rota baseado em grafos, que eventualmente trará a menor rota, baseado em relações (trânsito, distância, etc)

#### Exemplo: Rede Social com Grafos

```
Vértices (Usuários):
- Alice
- Bob
- Carol
- David

Arestas (Amizades):
- Alice --- Bob
- Alice --- Carol
- Bob --- Carol
- Carol --- David
```

| Usuário | Amigos | Distância |
|---|---|---|
| Alice | Bob, Carol | - |
| Bob | Alice, Carol | - |
| Carol | Alice, Bob, David | - |
| David | Carol | - |

**Possíveis Recomendações:**
- Alice pode ser amiga de David (através de Carol)
- Bob pode ser amigo de David (através de Carol)

Exemplos de bancos:
- Neo4j
- Amazon Neptune
- ArangoDB
- JanusGraph

Artigo para relembrar o que é grafo: https://medium.com/@gtbarbosa/estruturas-de-dados-al%C3%A9m-do-b%C3%A1sico-tabelas-hash-grafos-e-pesquisa-em-largura-044a6928acb6

---

## Armazenamento e Indexação

Formas de armazenar e indexar os dados de um banco de dados. Isso terá impacto direto em desempenho e flexibilidade, pensando que eles são o coração do desempenho de queries.

### Page Size

Armazenamento em page size indica que os dados podem ser armazenados por similaridade em páginas em blocos fixos. Isso é configurado quando criamos a engine do banco de dados

Os blocos são as páginas e orientados a linhas (bancos SQL ou row oriented). Suportam alguns NoSQL, mas está mais próximo de bancos SQL.

As páginas contém metadados que indexam e ajudam na busca dos dados dentro das páginas.

Um exemplo é se eu definir um page size de 8 KB, indexados por data, sempre que eu armazenar estes dados, eu vou abrir e armazenar esses dados nesta página até completar 8 KB e ai então vou par aa próxima página.

Páginas grandes, tendem a diminuir I/O para fazer leitura de grandes volumes de dados. Por exemplo, imagina que temos uma páginade 10 MB e que armazena pedidos por datas. Em uma consulta, se eu quiser trazer todos os pedidos do mês de janeiro, eles estarão próximos ou em uma única página. O que evita que minha engine tenha que ficar indo e voltando e abrindo diversas páginas, ou seja, diminui I/O. Por outro lado, a quantidade de dados retornados, será maior, o que aumenta o custo de transferência de dados. Por outro lado, para consultas simples, será ruim, por que eu tenho que abrir uma página muito grande, para pegar um único registro. Prefira páginas grandes quando for querer buscar extensas listas de dados. Prefira páginas pequenas, quando quiser poucos ou um único dado.

Páginas menores, minimizam a leitura de dados irrelevantes e consegue fazer consultas pequenas otimizadas. Temos mais I/O, mas o custo com transferência de dados é menor.

Exemplos de bancos de dados:
- RDBMS's Padrão
- Postgree
- MySQL/InnoDB
- Oracle Databases
- Apache Cassandra

### Indexação Colunar

Funciona indexando dados por colunas, o que permite que os bancos colunares existam. Existe uma compressão de dados por similaridade na coluna, o que permite que consultas analíticas por um determinado valor sejam otimizadas. Por exemplo, se eu tenho a coluna `país` e 80% dos registros dessa coluna é Brasil. A indexação colunar vai comprimir este dado e fazer uma referência ao total dele, ao invés de tê-lo todo. Isso otimizaria uma consulta de clientes por país, por exemplo.

Comparando com o page size, que uma página é fechada depois de um terminado tamanho de linhas armazenadas numa página, a indexação colunar os dados de uma mesma coluna ficam numa mesma págian e são comprimidos por similaridade.

Exemplos de bancos de dados:
- Amazon Redshift
- BigQuery
- Snowflake
- DuckDB

