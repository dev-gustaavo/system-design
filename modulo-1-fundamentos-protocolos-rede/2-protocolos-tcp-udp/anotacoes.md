# Protocolo TCP e UDP

![alt text](image.png)

UDP nao necessariamente é entregue

É simples, mas não é confiável

Geralmente usado em streaming

![alt text](image-1.png)

![alt text](image-2.png)

É menos performático que o UDP, mas é muito mais confiável. Tem um mecanismo que tem confirmação da entrega dos pacotes

![alt text](image-3.png)

Tudo que é enviado e recebido exige confirmação no TCP.

O ciclo do desenho acima acontece toda vez que uma conexão é aberta entre duas aplicações. No entanto, o handshake não necessariamente vai abrir toda vez. Geralmente as aplicações abrem um pool de conexão e reutiliza ela sempre que for sair para outra aplicação.

![alt text](image-4.png)

A escolha entre estes dois protocolos, vai depender da confiabilidade que precisamos ter na aplicação