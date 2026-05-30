# Protocolos HTTP/1, HTTP/2 e HTTP/3

![alt text](image.png)

![alt text](image-1.png)

![alt text](image-2.png)

![alt text](image-3.png)

![alt text](image-4.png)

![alt text](image-5.png)

![alt text](image-6.png)

![alt text](image-7.png)

![alt text](image-8.png)

![alt text](image-9.png)

| Protocolo | Handshake por requisição | Requisições paralelas |
|-----------|--------------------------|----------------------|
| HTTP/1.0 | Sim | Não |
| HTTP/1.1 | Não (keep alive) | Não (serial) - a requisição vai e volta, para que a próxima consiga ir, mesmo que a conexão continue aberta |
| HTTP/2 | Não | Sim (multiplexing) - permite múltiplas requisições paralelas |

![alt text](image-10.png)

![alt text](image-11.png)

