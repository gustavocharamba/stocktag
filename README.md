# StockTag

Sistema full stack de gerenciamento de estoque para uma loja de roupas, desenvolvido como projeto de portfólio com foco em backend Java.

## Objetivo

Demonstrar conhecimentos relevantes para uma vaga de estágio ou desenvolvimento Java júnior por meio de um projeto concluível, bem documentado e fácil de executar localmente.

O sistema controlará o saldo de cada combinação de produto, cor e tamanho, manterá o histórico das movimentações e tratará vendas concorrentes sem permitir estoque negativo.

## Stack planejada

### Backend

- Java 21
- Spring Boot, Spring Web, Spring Data JPA e Spring Security
- PostgreSQL e Flyway
- Maven
- JUnit, Mockito e Testcontainers
- OpenAPI / Swagger

### Frontend

- React
- TypeScript
- Vite

### Ambiente

- Docker e Docker Compose
- Git e GitHub

## Principais funcionalidades planejadas

- autenticação e perfis ADMIN/FUNCIONARIO;
- produtos com variantes de cor e tamanho e SKU único;
- saldo e estoque mínimo por SKU;
- histórico auditável de entradas, saídas, ajustes e vendas;
- vendas transacionais, cancelamento e proteção contra estoque negativo;
- QR Code por variante e leitura pelo celular;
- dashboard simples de estoque e vendas;
- interface responsiva para computador e celular.

## Arquitetura

O backend será um monólito modular organizado por funcionalidades. O repositório será um monorepo com `backend/`, `frontend/` e `docs/`.

O planejamento completo, incluindo escopo, regras de negócio, modelagem, APIs, testes e roadmap, está em [docs/PLAN.md](docs/PLAN.md).

## Status do projeto

**Em planejamento.** Nenhuma funcionalidade foi implementada ainda.

## Como executar

As instruções de pré-requisitos, configuração, banco de dados, backend e frontend serão adicionadas conforme a fundação executável for criada.

Futuramente, esta seção incluirá:

1. como configurar as variáveis de ambiente;
2. como iniciar o PostgreSQL com Docker Compose;
3. como executar o backend com o Maven Wrapper;
4. como executar o frontend com npm;
5. como acessar a aplicação e o Swagger;
6. como executar os testes.

## Documentação

- [Plano completo e roadmap](docs/PLAN.md)

Este README será atualizado ao longo do desenvolvimento e, ao final, funcionará como a apresentação principal do projeto no GitHub.
