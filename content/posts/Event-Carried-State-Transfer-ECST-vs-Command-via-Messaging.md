+++
date = '2025-03-09T20:14:52-03:00'
draft = false
title = '📦 Event-Carried State Transfer (ECST) vs Command via Messaging vs BFF'
description = 'Event-Carried State Transfer (ECST) vs Command via Messaging vs BFF'
tags = ['EC2', 'blog', 'hugo']
categories = ['AWS']
+++

Neste artigo, explico de forma prática e objetiva os padrões **ECST**, **Command via Messaging** e como eles se comparam ao tradicional **BFF (Backend for Frontend)** em uma arquitetura de microsserviços.

---

## 📘 1. Event-Carried State Transfer (ECST)

### ✅ Definição técnica
> Event-Carried State Transfer é um padrão onde um serviço, ao modificar um dado (como criar ou atualizar um cliente), **emite um evento contendo o estado completo da entidade**. Outros serviços que precisem desses dados escutam esse evento e mantêm uma cópia local, garantindo **consistência eventual**.

### 💡 Explicação facilitada
Imagine o `cliente-service` como o serviço principal de cadastro de clientes. Quando ele cria ou atualiza um cliente, ele **avisa os outros serviços** enviando um evento com **todos os dados** do cliente (nome, e-mail, etc).

Outros serviços (como um `marketing-service`) escutam esse evento e **atualizam seu banco local**, sem precisar perguntar diretamente ao `cliente-service`.

---

## 📘 2. Command via Messaging

### ✅ Definição técnica
> Command via Messaging é um padrão onde um serviço envia uma **mensagem com uma intenção clara (um comando)** para outro serviço realizar uma ação específica, como criar um novo cliente. Essa mensagem é processada de forma assíncrona por quem recebe.

### 💡 Explicação facilitada
Imagine que o `API Gateway` quer cadastrar um novo cliente. Em vez de chamar diretamente o `cliente-service` via HTTP, ele **envia uma mensagem** para uma fila dizendo:

> “`cliente-service`, por favor, **crie esse cliente com nome e email X`”.

O `cliente-service` escuta essa fila, processa a mensagem e salva o cliente no banco.

---

## 🔄 Comparação com BFF (Backend for Frontend)

| Conceito              | ECST                                   | Command via Messaging                         | BFF (Backend for Frontend)                      |
|-----------------------|----------------------------------------|-----------------------------------------------|-------------------------------------------------|
| Tipo de comunicação   | **Evento**                             | **Comando**                                   | **Requisição direta HTTP**                     |
| Quem inicia a ação?   | Serviço que detém os dados             | Outro serviço ou gateway                      | Frontend via browser ou app                    |
| Objetivo              | Notificar mudança de estado            | Solicitar que outro serviço execute uma ação  | Adaptar chamadas para múltiplos serviços       |
| Quem salva os dados?  | Serviço original (dono do estado)      | Serviço que recebeu o comando                 | BFF não salva, apenas coordena                 |
| Forma de acoplamento  | Fraco (publica/escuta)                 | Fraco (comando assíncrono)                    | Médio (precisa conhecer os serviços chamados)  |
| Comunicação           | Assíncrona (eventos via fila)          | Assíncrona (comando via fila)                 | Síncrona (requisições HTTP)                    |
| Exemplo de uso        | `cliente-service` envia evento com cliente criado | `API Gateway` envia comando para criar cliente | Frontend chama `/bff/clientes` que redireciona |

---

## ✍️ Quando usar cada um?

| Situação                                                      | Ideal                            |
|---------------------------------------------------------------|----------------------------------|
| Você quer manter cópias locais de dados em outros serviços    | ✅ ECST                           |
| Você quer executar uma ação remota de forma desacoplada       | ✅ Command via Messaging          |
| Você precisa adaptar dados de múltiplos serviços para o front | ✅ BFF                            |
| Você quer que os serviços sejam independentes e resilientes   | ECST ou Command via Messaging    |

---

## 🚀 Conclusão

- O **ECST** é excelente para replicar dados entre serviços e reduzir acoplamento.
- O **Command via Messaging** ajuda a executar ações entre serviços sem dependência direta.
- O **BFF** continua sendo útil para adaptar dados entre o front-end e os microserviços, mas tem maior acoplamento.

Cada padrão tem seu contexto ideal, e usá-los corretamente ajuda a construir sistemas mais **resilientes, escaláveis e desacoplados**.

---

💬 Gostou? Tem dúvidas ou quer ver exemplos práticos com Spring Boot e RabbitMQ? Deixe seu comentário ou me chame!
