# Scale Cube

O Scale Cube é um modelo mental teórico que ajuda a arquitetar microsserviços em D0, com técnicas e teorias que apoiarão na decisão de um microsserviço escalável, sem que tenhamos que levar para produção, para saber o quanto precisaremos escalar. Este modelo foi proposto no livro The Art of Scalability.

É a necessidade de transformar a escalabilidade em algo projetável e não reativo.

Ele é composto por três eixos:
X (largura)
Y (altura)
Z (profundidade)

## Eixo X - Escalabilidade horizontal

O eixo X corresponde a escalabilidade horizontal. Ele diz que minha aplicação deve ser capaz de escalar horizontalmente, sem impactos em ambiente produtivo.

## Eixo Y - Decomposição

O eixo Y corresponde a decomposição funcional de microsserviços em domínios, sem que haja acoplamento alto entre eles, com contextos e ações específicas.

## Eixo Z - Sharding

O eixo Z corresponde a decomposição dos dados da minha aplicação, para não termos todos os ovos na mesma cesta.

---

## Eixo X - Escalabilidade Horizontal

O eixo X ele representa a capacidade da minha aplicação escalar horizontalmente em produção, sem que minha aplicação sofra indisponibilidade. Ela precisa ser capaz de adicionar e remover máquinas sem downtime. Ela precisa ser capaz de balancear a carga entre essas máquinas através de um load balancer.

### Boas pŕaticas

- Externalizar as configurações de escalabilidade, sem que haja a necessidade de um novo deploy caso seja necessário reconfigurar o autoscalling. Ter forma externas de configurar, sem precisar mexer na aplicação
- Eviar Sticky Session e Session Affinity

- Externalizar estado (descoplamento): onde mantenho o estado da minha aplicação, precisam estar externalizados, ou seja, bancos de dados, databases, filas, precisam estar externas ao servidor da minha aplicação

- Permitir autoscalling, para que a aplicação consiga crescer e diminuir sem a necessidade de ações manuais, usando por exemplo: Keda, Karpenter, HPA

---

## Eixo Y - Desacoplamento

O eixo Y representa a quebra de serviços grandes e componentes (microsserviços) com suas responsabilidades segregadas. É a prática de quebrar um monolito em microsserviços, separando domínios e responsabilidades, visando baixo acoplamento.

Resumo em uma frase: Transforma uma aplicação grande em um conjunto de serviços menores, cada um otimizado para o seu cenário.

---

## Eixo Z - Sharding

Eixo Z representa que todos os dados da aplicação pode ser particionado entre vários servidores, bancos de dados, etc.

Cada partição/sharding representa um conjunto de dados, que quando unidos, representam o todo. Os shardings precisam de um algoritmo de distribuição e chaves de partição.

Chaves de partição representa o que vai particionar os dados, para que eles sejam distribuídos corretamente.

