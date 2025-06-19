+++
date = '2025-05-09T13:14:52-03:00'
draft = false
title = 'Melhor local para mapeamento DTO'
description = 'Melhor local para mapeamento DTO ↔ Domain na arquitetura em camadas: Service ou Controller?'
tags = ['DTO', 'JAVA', 'SPRING', 'PADRÔES']
categories = ['SYSTEM-DESIGN']
+++

# 
**Autor: Rogério Fontes**  
**Tema: Arquitetura em Camadas | Boas Práticas | Design Clean**

---

## 🔥 Introdução

Ao desenvolver APIs REST seguindo a **arquitetura em camadas (Layered Architecture)**, surge uma dúvida comum:

**Devo fazer o mapeamento entre DTOs e as entidades de domínio no Controller ou no Service?**

Essa é uma decisão importante que impacta:
- A clareza do código.
- O acoplamento entre camadas.
- A escalabilidade futura.

Vamos analisar as opções e as melhores práticas.

---

## 🏛 Revisando rapidamente: arquitetura em camadas

A clássica arquitetura em camadas divide a aplicação em:

| Camada | Responsabilidade |
|--------|------------------|
| Controller (Web/API) | Recebe requisições e retorna respostas. |
| Service | Contém regras de negócio e orquestra operações. |
| Repository | Acesso a dados persistentes. |
| Domain | Entidades e lógica de negócio. |

Cada camada deve ter responsabilidades bem definidas para manter a separação de preocupações.

---

## ❓ Onde realizar o mapeamento DTO ↔ Domain?

### Opção 1️⃣: No Controller (**Recomendado ✅**)

```java
Employee employee = employeeMapper.toDomain(dto);
Employee saved = employeeService.create(employee);
```

**Vantagens**:
- O Service fica isolado do mundo externo (não conhece DTOs).
- Facilidade de manutenção.
- Alinhado ao princípio de inversão de dependência (DIP).

**Quando usar**:
- Quando o Service precisa trabalhar exclusivamente com entidades do domínio.
- Quando a lógica de mapeamento é simples ou pode ser reutilizada pelo Controller.

---

### Opção 2️⃣: No Service (**Aceitável em casos específicos ⚠**)

```java
Employee saved = employeeService.create(dto);
```

**Vantagens**:
- Reduz código repetitivo no Controller (especialmente em CRUD simples).
- Útil em cenários onde o Service é considerado um "application service" voltado para APIs.

**Desvantagens**:
- O Service passa a conhecer o DTO.
- Quebra parcial da separação entre as camadas.
- Se o DTO mudar, o Service pode precisar ser alterado.

**Quando é aceitável**:
- Em aplicações simples ou monolitos com pouca complexidade.
- Quando os DTOs são estáveis e controlados pela mesma equipe que mantém o Service.

---

## ✅ Boas práticas de mercado

**Padrão em projetos profissionais**:
- Controller → Recebe DTO, converte para domínio.
- Service → Recebe e trabalha com entidades de domínio.
- Repository → Trabalha com entidades do domínio ou projeções.

**Frameworks como Spring, Micronaut e Jakarta EE seguem e recomendam essa separação.**

---

## ✅ Exemplo correto

```java
@PostMapping
public ResponseEntity<EmployeeResponseDTO> create(@RequestBody EmployeeRequestDTO dto) {
    Employee employee = employeeMapper.toDomain(dto);
    Employee created = employeeService.create(employee);
    EmployeeResponseDTO response = employeeMapper.toResponse(created);
    return ResponseEntity.ok(response);
}
```

---

## 🚀 Resumo

| Onde colocar o mapeamento DTO ↔ Domain? | Melhor opção |
|-----------------------------------------|--------------|
| Controller | ✅ Sim (recomendado) |
| Service | ⚠ Aceitável em casos simples, mas com ressalvas |

**Regra geral**:
> Controllers lidam com contratos externos (DTOs).  
> Services lidam com regras de negócio (Domínio).

---

## 📝 Conclusão

Para manter uma arquitetura limpa e de fácil manutenção:
- Prefira realizar o mapeamento no Controller.
- Evite acoplar os Services a contratos externos como DTOs.
- Em casos específicos e controlados, o mapeamento no Service pode ser considerado, mas com consciência dos trade-offs.

**A boa separação de responsabilidades é o primeiro passo para sistemas escaláveis e sustentáveis.**

---

## 📌 Sobre o autor

**Rogério Fontes**  
Arquiteto de Software | Especialista em Java, Go e Microsserviços | Criador de conteúdos sobre Arquitetura, Clean Code e DevOps.
