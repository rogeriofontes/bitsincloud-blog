+++
date = '2025-05-09T13:14:52-03:00'
draft = false
title = 'Melhor local para mapeamento DTO'
description = 'Onde colocar o mapeamento de DTO na Arquitetura Hexagonal? (E por que!)'
tags = ['DTO', 'JAVA', 'SPRING', 'PADRÔES']
categories = ['SYSTEM-DESIGN']
+++

**Autor: Rogério Fontes**  
**Tema: Arquitetura Hexagonal | Boas Práticas | Design Clean**

---

## 🔥 Introdução

Quando estamos desenvolvendo aplicações seguindo a **Arquitetura Hexagonal (Ports & Adapters)**, uma dúvida muito comum surge:

**Onde devo colocar o mapeamento entre DTOs e as entidades do domínio? No Controller? No Service? Em outro lugar?**

Neste artigo, vamos responder essa pergunta com exemplos práticos e boas práticas adotadas no mercado.

---

## 🔎 Revisando rapidamente: o que é DTO?

**DTO (Data Transfer Object)** é um objeto usado para:
- Transportar dados entre cliente e servidor.
- Expor ou receber dados de APIs sem acoplar diretamente às entidades do domínio.

**Exemplo**:
```java
public class EmployeeRequestDTO {
    private String name;
    private String email;
}
```

---

## 🏛 Arquitetura Hexagonal em resumo

Na **Arquitetura Hexagonal**:

| Camada | Responsabilidade |
|--------|------------------|
| Controller / Adapter | Receber requisições externas e entregar dados internos. |
| Application (Use Case) | Contém as regras de negócio (serviços). |
| Domain | Entidades puras com lógica de negócio. |
| Ports | Interfaces que definem contratos entre camadas. |

**Regra de ouro**:  
👉 **O domínio nunca conhece as camadas externas (como DTOs)**.

---

## ❓ Onde colocar o mapeamento DTO ↔ Domain?

### Opção 1️⃣: No Controller (**Recomendado ✅**)

```java
Employee employee = employeeMapper.toDomain(dto);
```

**Vantagens**:
- Mantém o Service isolado do mundo externo.
- Controller já é o ponto de entrada e deve preparar os dados.

**Desvantagens**:
- Controller fica com um pouco mais de responsabilidade (mas ainda aceitável).

---

### Opção 2️⃣: No Service (**Não recomendado ❌**)

```java
createEmployeeService.create(dto);
```

**Problema**:
- Acopla a lógica do domínio com a API pública.
- Quebra o isolamento da hexagonal.
- Se mudar o DTO, impacta o domínio.

---

## 🧠 Boas práticas de mercado

**Padrão em projetos bem estruturados**:
- Controller → Recebe DTO, converte para domínio.
- Service → Recebe e trabalha com entidades de domínio.
- Domain → Nunca conhece DTOs.

**Frameworks como Spring, Micronaut e Quarkus** seguem e recomendam esse padrão.

---

## ✅ Exemplo correto

```java
@PostMapping
public ResponseEntity<EmployeeResponseDTO> create(@RequestBody EmployeeRequestDTO dto) {
    Employee employee = employeeMapper.toDomain(dto);
    Employee created = createEmployeeService.create(employee);
    EmployeeResponseDTO response = employeeMapper.toResponse(created);
    return ResponseEntity.ok(response);
}
```

**Note**:
- O Service só vê `Employee` (domínio).
- O Controller faz a conversão.

---

## 🚀 Resumo

| Onde colocar o mapeamento DTO ↔ Domain? | Melhor opção |
|-----------------------------------------|--------------|
| Controller | ✅ Sim |
| Service (UseCase) | ❌ Não |

**Regra de ouro**:  
> Controllers lidam com contratos externos (DTOs).  
> Services lidam com regras de negócio (Domínio).

---

## 📝 Conclusão

Colocar o mapeamento no Controller ajuda a manter a arquitetura limpa, desacoplada e fácil de manter.  
Essa prática favorece a escalabilidade e a manutenção a longo prazo.

**Se você busca construir sistemas com arquitetura bem definida, seguir essa prática é um ótimo caminho.**

---

## 📌 Sobre o autor

**Rogério Fontes**  
Arquiteto de Software | Especialista em Java, Go e Microsserviços | Criador de conteúdos sobre Arquitetura, Cloud e Clean Code.
