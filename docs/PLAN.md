# Plano do projeto StockTag

> Documento principal de planejamento e fonte de verdade do projeto.
>
> Status atual: planejamento — nenhuma funcionalidade implementada.
>
> Última atualização: 2026-08-06.

## Como usar este documento

- Consultar este plano antes de iniciar cada etapa do roadmap.
- Atualizar este arquivo quando uma decisão relevante de escopo, regra de negócio, arquitetura, banco, API ou implementação mudar.
- Registrar a motivação e o impacto das decisões importantes na seção **Registro de decisões**.
- Não avançar várias etapas simultaneamente sem uma necessidade concreta.
- Tratar o MVP como compromisso de entrega; ideias posteriores não devem bloquear sua conclusão.

## 1. Visão geral

### Objetivo

Construir um sistema full stack de gerenciamento de estoque para uma loja de roupas, com foco em aprendizado e apresentação em processos seletivos para estágio ou desenvolvimento backend Java júnior.

O projeto deve demonstrar, de maneira simples e verificável:

- Java 21 e orientação a objetos;
- API REST com Spring Boot;
- autenticação e autorização;
- modelagem relacional com PostgreSQL;
- transações e concorrência;
- testes unitários e de integração;
- integração entre React e backend;
- documentação e uso profissional de Git.

O sistema não terá deploy em produção inicialmente. O resultado esperado é um repositório GitHub organizado, documentado e fácil de executar localmente.

### Usuários

- **ADMIN:** administra usuários e catálogo, consulta todo o sistema, faz ajustes sensíveis e cancela vendas.
- **FUNCIONARIO:** consulta o catálogo e o estoque, registra entradas e vendas, consulta movimentações e usa o scanner de QR Code.

Não haverá cadastro público. Usuários serão criados por um ADMIN.

### Problema resolvido

Uma loja precisa saber quanto existe de cada combinação de produto, cor e tamanho; registrar quem alterou o estoque; impedir vendas sem saldo; e localizar rapidamente uma variante por QR Code.

### Fluxos principais

1. ADMIN configura categorias, marcas, produtos e variantes.
2. Cada variante recebe um SKU único, saldo próprio e identificador de QR Code.
3. Um usuário registra uma entrada e o sistema atualiza o saldo e grava o histórico.
4. Um FUNCIONARIO cria uma venda; o backend valida e baixa os itens em uma única transação.
5. Se duas vendas disputarem a última unidade, apenas uma é concluída.
6. Um ADMIN cancela uma venda concluída e o estoque retorna automaticamente.
7. Um usuário lê o QR Code e consulta os dados atuais da variante.

## 2. Escopo

### Dentro do projeto

- Monorepo com backend, frontend, documentação e configuração local.
- Monólito modular: uma aplicação backend, um banco e módulos organizados por funcionalidade.
- Uma única loja e um único estoque físico.
- Valores monetários em BRL, sem conversão de moeda.
- Catálogo de roupas com categoria, marca, produto e variantes por cor/tamanho.
- Saldo e estoque mínimo por variante/SKU.
- Histórico auditável de movimentações.
- Vendas e cancelamento total de venda.
- Interface responsiva para desktop e celular.
- PostgreSQL em Docker durante o desenvolvimento.

### Fora do escopo inicial

- Deploy, infraestrutura cloud e observabilidade de produção.
- Microserviços, mensageria, cache distribuído e busca externa.
- Múltiplas lojas, depósitos ou filiais.
- Integração fiscal, emissão de nota, pagamentos e meios de pagamento.
- Clientes, fidelidade, e-commerce, carrinho público e frete.
- Upload e tratamento de imagens; no MVP será usada uma URL de imagem.
- Reserva de estoque e pedidos pendentes.
- Operação offline do scanner.
- Aplicativo móvel nativo.

## 3. MVP

O MVP estará completo quando uma pessoa conseguir clonar o repositório, iniciar as dependências seguindo o README e executar estes fluxos pela interface:

- fazer login com um usuário local;
- administrar usuários com papéis ADMIN e FUNCIONARIO;
- cadastrar e consultar categorias, marcas, produtos e variantes;
- manter SKU único por variante e saldo independente por SKU;
- registrar entrada, saída manual autorizada e ajuste de inventário;
- consultar saldo, estoque baixo e histórico de movimentações;
- registrar uma venda com um ou mais itens;
- impedir saldo negativo, inclusive em vendas concorrentes;
- cancelar uma venda por completo, devolvendo seus itens ao estoque;
- gerar e ler um QR Code que identifica uma variante;
- visualizar pelo scanner nome, imagem, preço, cor, tamanho e saldo atual;
- acessar um dashboard simples com quantidades essenciais;
- entender e testar a API pelo Swagger/OpenAPI;
- executar testes unitários e de integração relevantes.

### Simplificações deliberadas do MVP

- Produto tem uma URL de imagem, sem upload de arquivo.
- Cor e tamanho são textos normalizados na variante, sem tabelas próprias.
- A venda é concluída no momento em que é criada; não há rascunho nem pagamento.
- O cancelamento é sempre total; devoluções parciais ficam para depois.
- Entradas não precisam estar ligadas a um fornecedor no MVP.
- O QR Code é exibido individualmente; montagem de folhas de etiquetas fica para depois.
- O dashboard terá apenas contagens, alerta de estoque baixo e resumo simples de vendas.

## 4. Funcionalidades posteriores

### Importantes

- Cadastro de fornecedores e associação do fornecedor às entradas.
- Devolução total ou parcial vinculada a itens de uma venda.
- Impressão de etiquetas em lote, preferencialmente por página pronta para impressão ou PDF.
- Filtros e relatórios por período, produto e tipo de movimentação.
- Exportação CSV de estoque e movimentações.
- Recuperação ou redefinição de senha por ADMIN.

### Diferenciais

- Contagem de inventário guiada e fechamento de divergências.
- Gráfico de produtos mais vendidos e evolução do estoque.
- Auditoria de mudanças relevantes no cadastro, além das movimentações.
- Teste de ponta a ponta do principal fluxo de venda e cancelamento.
- Página pública somente de consulta do item por QR, caso exista uma necessidade demonstrável.

### Opcionais

- Múltiplas imagens por produto.
- Importação de catálogo por CSV.
- Leitura de código de barras além de QR Code.
- Temas de interface e preferências do usuário.

Uma funcionalidade posterior só entra no roadmap ativo depois que o MVP estiver concluído ou se resolver um bloqueio comprovado.

## 5. Regras de negócio

### Usuários e acesso

1. Não existe cadastro público de usuário.
2. Email é obrigatório, armazenado em minúsculas e único.
3. Senha nunca é armazenada em texto puro.
4. Apenas ADMIN cria, altera papel ou desativa usuários.
5. Usuário desativado não pode autenticar nem continuar usando um token após a validação do seu estado.

### Catálogo

6. Um produto pertence a uma categoria e a uma marca.
7. Um produto pode ter várias variantes.
8. No MVP, cada variante tem cor e tamanho obrigatórios.
9. A combinação produto + cor normalizada + tamanho normalizado não pode se repetir.
10. Cada variante possui um SKU único em todo o sistema. O SKU é normalizado para maiúsculas e não é alterado após a criação.
11. Cada variante possui um token público de QR imutável e não sequencial.
12. Preço é maior ou igual a zero, tem duas casas decimais e fica na variante para permitir preços diferentes.
13. Produto ou variante inativa permanece no histórico, mas não pode entrar em uma nova venda.
14. Registros já referenciados por venda ou movimentação não são excluídos fisicamente; são desativados.

### Estoque

15. Cada variante possui exatamente um saldo de estoque, criado inicialmente com quantidade zero.
16. Quantidade e estoque mínimo são números inteiros maiores ou iguais a zero.
17. Toda alteração de saldo deve ocorrer por um caso de uso de estoque e gerar uma movimentação imutável na mesma transação.
18. Não é permitido alterar o saldo diretamente por controller ou por outro módulo.
19. Uma movimentação registra variante, diferença assinada, saldo anterior, saldo posterior, tipo, motivo/origem, data e usuário.
20. O saldo posterior deve ser igual ao saldo anterior mais a diferença da movimentação.
21. Nenhuma operação pode resultar em saldo negativo.
22. Estoque baixo significa quantidade disponível menor ou igual ao estoque mínimo, considerando apenas variantes ativas.
23. Entrada exige quantidade positiva. Saída manual exige quantidade positiva e saldo suficiente. Ajuste informa a quantidade física contada e exige justificativa.
24. Ajuste e saída manual são restritos a ADMIN; entrada pode ser registrada por ADMIN ou FUNCIONARIO.

### Vendas

25. Uma venda deve possuir pelo menos um item e cada quantidade deve ser positiva.
26. Itens repetidos da mesma variante são consolidados antes da validação.
27. Preço unitário, nome, SKU, cor e tamanho relevantes à exibição histórica são copiados como snapshot no item da venda.
28. Total da venda é calculado pelo backend; valores enviados pelo cliente não são aceitos como fonte de verdade.
29. Criar uma venda, salvar seus itens, baixar todos os saldos e gerar todas as movimentações é uma única transação atômica.
30. Se qualquer item não tiver saldo, a venda inteira falha, sem baixa parcial.
31. Uma venda nasce com status `COMPLETED` no MVP.
32. Somente uma venda `COMPLETED` pode ser cancelada, apenas uma vez e por ADMIN.
33. O cancelamento total devolve todos os itens e gera movimentações positivas na mesma transação; o histórico original não é apagado.
34. Datas e usuário responsável são definidos no servidor.

## 6. Modelagem do domínio

| Entidade | Responsabilidade | Atributos principais | Relacionamentos |
| --- | --- | --- | --- |
| `Usuario` | Identidade e permissão de acesso | id, nome, email, senhaHash, papel, ativo, criadoEm, atualizadoEm | cria vendas e movimentações |
| `Categoria` | Classificação do produto | id, nome, ativo, timestamps | possui muitos produtos |
| `Marca` | Marca comercial | id, nome, ativo, timestamps | possui muitos produtos |
| `Produto` | Dados comuns às variantes | id, nome, descrição, imagemUrl, ativo, categoriaId, marcaId, timestamps | pertence a categoria/marca; possui variantes |
| `VariacaoProduto` | Unidade comercial identificável | id, produtoId, sku, cor, tamanho, preço, qrToken, ativo, timestamps | pertence a produto; possui um saldo; aparece em movimentos e itens de venda |
| `Estoque` | Saldo atual de uma variante | variacaoId, quantidade, estoqueMinimo, atualizadoEm | relação 1:1 com variante |
| `MovimentacaoEstoque` | Registro imutável de mudança | id, variacaoId, tipo, quantidadeDelta, saldoAnterior, saldoPosterior, motivo, vendaId opcional, usuarioId, criadoEm | pertence a variante/usuário; pode referenciar venda |
| `Venda` | Operação comercial atômica | id, status, total, criadaEm, canceladaEm, criadaPorId, canceladaPorId | possui itens e movimentações |
| `ItemVenda` | Linha e snapshot de uma venda | id, vendaId, variacaoId, sku, descrição snapshot, quantidade, preçoUnitario, subtotal | pertence a venda e referencia variante |
| `Fornecedor` | Cadastro de origem de mercadorias | id, nome, documento, contato, ativo | funcionalidade posterior; poderá ser referenciado por uma entrada |

### Decisões de modelagem

- `Estoque` não é apenas uma coleção de movimentações. Ele representa o saldo materializado para leitura rápida, enquanto `MovimentacaoEstoque` oferece auditoria e reconciliação. Isso não é event sourcing.
- Cor e tamanho não terão entidades no MVP. Tabelas próprias só se justificam se surgirem atributos ou regras específicas para elas.
- SKU identifica a variante comercial; `qrToken` identifica de forma estável e não previsível a rota usada pelo QR.
- Fornecedor não é necessário para provar o fluxo principal e, portanto, não integra o MVP.
- Devolução parcial exige entidades próprias (`DevolucaoVenda` e `ItemDevolucao`) para controlar quanto de cada item já retornou. Ela será modelada quando entrar no roadmap.

## 7. Modelagem do banco PostgreSQL

IDs internos usarão `BIGINT GENERATED ... AS IDENTITY`. Datas usarão `TIMESTAMPTZ`, gravadas em UTC. Valores monetários usarão `NUMERIC(12,2)` e no Java serão representados por `BigDecimal`.

### Tabelas do MVP

#### `users`

- PK: `id`.
- Campos: `name`, `email`, `password_hash`, `role`, `active`, `created_at`, `updated_at`.
- Únicos: `email`.
- Constraints: email salvo em minúsculas; `role IN ('ADMIN','EMPLOYEE')`; campos essenciais não nulos.
- Índice: `active` apenas se as consultas demonstrarem necessidade; o índice único de email atende o login.

#### `categories`

- PK: `id`.
- Campos: `name`, `normalized_name`, `active`, timestamps.
- Único: `normalized_name`.
- Constraints: nome não vazio.

#### `brands`

- PK: `id`.
- Campos: `name`, `normalized_name`, `active`, timestamps.
- Único: `normalized_name`.
- Constraints: nome não vazio.

#### `products`

- PK: `id`.
- FKs: `category_id -> categories.id`, `brand_id -> brands.id`.
- Campos: `name`, `description`, `image_url`, `active`, timestamps.
- Índices: `category_id`, `brand_id`, `active`; busca textual simples pode usar nome sem Elasticsearch.
- Constraints: nome não vazio.

#### `product_variants`

- PK: `id`.
- FK: `product_id -> products.id`.
- Campos: `sku`, `color`, `normalized_color`, `size`, `normalized_size`, `price`, `qr_token UUID`, `active`, timestamps.
- Únicos: `sku`, `qr_token`, e (`product_id`, `normalized_color`, `normalized_size`).
- Índices: `product_id`, `active`.
- Constraints: SKU em maiúsculas; `price >= 0`; cor e tamanho não vazios.

#### `inventory_balances`

- PK e FK: `variant_id -> product_variants.id`, garantindo 1:1.
- Campos: `quantity`, `minimum_quantity`, `updated_at`.
- Constraints: `quantity >= 0`, `minimum_quantity >= 0`.
- Índice opcional posterior: não criar índice prematuramente para a expressão de estoque baixo; medir primeiro.

#### `sales`

- PK: `id`.
- FKs: `created_by_id -> users.id`, `cancelled_by_id -> users.id` opcional.
- Campos: `status`, `total_amount`, `created_at`, `cancelled_at`.
- Constraints: `status IN ('COMPLETED','CANCELLED')`, `total_amount >= 0`; dados de cancelamento coerentes com o status.
- Índices: (`created_at`), (`status`, `created_at`), `created_by_id`.

#### `sale_items`

- PK: `id`.
- FKs: `sale_id -> sales.id`, `variant_id -> product_variants.id`.
- Campos: `sku_snapshot`, `product_name_snapshot`, `color_snapshot`, `size_snapshot`, `quantity`, `unit_price`, `subtotal`.
- Único: (`sale_id`, `variant_id`), pois itens iguais serão consolidados.
- Constraints: `quantity > 0`, `unit_price >= 0`, `subtotal = quantity * unit_price` dentro da precisão adotada.
- Índices: `sale_id`, `variant_id`.

#### `stock_movements`

- PK: `id`.
- FKs: `variant_id -> product_variants.id`, `user_id -> users.id`, `sale_id -> sales.id` opcional.
- Campos: `movement_type`, `quantity_delta`, `balance_before`, `balance_after`, `reason`, `created_at`.
- Constraints: `quantity_delta <> 0`, saldos não negativos e `balance_after = balance_before + quantity_delta`.
- Índices: (`variant_id`, `created_at DESC`), (`movement_type`, `created_at`), `sale_id`, `user_id`.
- Não haverá `UPDATE` ou `DELETE` por fluxo normal da aplicação.

### Tabelas posteriores

- `suppliers` e referência opcional de uma entrada ao fornecedor.
- `sale_returns` e `sale_return_items` para devoluções parciais auditáveis.
- Uma tabela de contagem de inventário somente quando esse fluxo for implementado.

### Convenções

- Nomes físicos em inglês e `snake_case`; nomes de domínio e linguagem da interface podem permanecer em português.
- Constraints críticas existem no banco e também são validadas pela aplicação.
- `ON DELETE CASCADE` só será usado em dados sem vida independente e ainda não auditados. Histórico de estoque e vendas não será apagado em cascata.
- Paginação será aplicada a vendas, produtos e movimentações.

## 8. Controle de estoque

### Saldo atual e histórico

`inventory_balances.quantity` é o saldo atual. `stock_movements` é o livro de auditoria imutável. Toda mudança executa, dentro da mesma transação:

1. carrega e bloqueia o saldo da variante;
2. valida a operação e calcula o novo saldo;
3. atualiza o saldo atual;
4. insere uma movimentação com saldo anterior e posterior;
5. confirma ambos juntos ou reverte ambos juntos.

O saldo não será recalculado a cada consulta somando todo o histórico. Poderá existir, para testes ou administração futura, uma consulta de reconciliação que compare o saldo materializado à soma das diferenças.

### Tipos de movimentação

| Tipo | Delta | Origem | Regra |
| --- | ---: | --- | --- |
| `ENTRY` | positivo | entrada manual | quantidade positiva e motivo obrigatório |
| `MANUAL_EXIT` | negativo | saída não relacionada a venda | ADMIN, motivo e saldo suficiente |
| `SALE` | negativo | criação de venda | vinculada à venda e atômica com todos os itens |
| `RETURN` | positivo | devolução | posterior ao MVP e vinculada ao item devolvido |
| `ADJUSTMENT` | positivo ou negativo | contagem física | backend calcula `contado - atual`; ADMIN e justificativa |
| `SALE_CANCELLATION` | positivo | cancelamento total | mesma quantidade da venda original e uma única vez |

Se um ajuste não produzir diferença, nenhuma movimentação financeira será criada; o sistema poderá apenas informar que o saldo já coincide. Movimentações nunca serão editadas para “corrigir” o passado: uma correção gera uma nova movimentação.

## 9. Concorrência e transações

### Cenário crítico

Com saldo 1, duas requisições tentam vender a mesma variante. A validação e a atualização não podem ser feitas em transações separadas.

### Optimistic locking

- Usa uma coluna de versão (`@Version`).
- Duas transações podem ler o mesmo saldo; a primeira confirma e a segunda falha ao atualizar a versão antiga.
- É apropriado quando conflitos são raros e tentar novamente é barato.
- Exige traduzir a falha de concorrência e, em alguns casos, repetir toda a operação de venda com cuidado.

### Pessimistic locking

- Usa bloqueio de linha, equivalente a `SELECT ... FOR UPDATE`, durante a transação.
- A segunda venda espera a primeira terminar e então enxerga o saldo atualizado.
- Simplifica a garantia de saldo para um caso de uso curto e deliberadamente concorrente.
- Pode reduzir paralelismo e gerar deadlock se várias linhas forem bloqueadas em ordens diferentes.

### Decisão para o MVP

Usar **pessimistic write lock** no repositório de saldo para vendas e demais baixas. Todas as variantes de uma venda serão consolidadas e bloqueadas em ordem crescente de ID dentro de uma única transação curta. Depois do bloqueio, o saldo será revalidado.

Não haverá chamada HTTP externa, geração de arquivo ou trabalho lento dentro da transação. Exceções de estoque insuficiente retornarão `409 Conflict`. Um teste de integração concorrente provará que, para saldo 1, apenas uma de duas vendas é concluída.

Optimistic locking poderá ser reavaliado se medições futuras mostrarem contenção relevante; as duas estratégias não serão misturadas sem documentação.

## 10. Arquitetura

### Estilo

O backend será um **monólito modular organizado por funcionalidade**, com um único projeto Maven e um único banco PostgreSQL. Não serão usados microserviços, Spring Modulith, arquitetura hexagonal completa ou múltiplos módulos Maven no início.

Pacotes globais apenas por camada (`controller`, `service`, `repository`) espalhariam uma funcionalidade por todo o projeto. A organização por funcionalidade mantém próximos os elementos que mudam juntos, sem adicionar abstrações desnecessárias.

### Estrutura de pacotes proposta

```text
com.stocktag
├── auth
│   ├── AuthController
│   ├── AuthService
│   └── dto
├── user
│   ├── User
│   ├── UserController
│   ├── UserService
│   ├── UserRepository
│   └── dto
├── catalog
│   ├── category
│   ├── brand
│   ├── product
│   └── variant
├── inventory
│   ├── InventoryBalance
│   ├── StockMovement
│   ├── InventoryController
│   ├── InventoryService
│   ├── InventoryRepository
│   └── dto
├── sale
│   ├── Sale
│   ├── SaleItem
│   ├── SaleController
│   ├── SaleService
│   ├── SaleRepository
│   └── dto
├── scan
├── dashboard
└── shared
    ├── config
    ├── exception
    └── security
```

Cada funcionalidade pode conter controller, service, repository, entidades, DTOs e mappers apenas quando precisar deles. Mapeamentos simples serão métodos explícitos; MapStruct e mapeadores genéricos não serão introduzidos no MVP.

### Responsabilidades

- **Controller:** HTTP, validação sintática, status e DTOs; sem regra de negócio.
- **Service:** casos de uso, autorização contextual e fronteiras transacionais.
- **Repository:** persistência e consultas, incluindo a consulta explícita com lock.
- **Entity/domain:** estado persistido e invariantes locais simples.
- **DTO:** contrato da API; entidades JPA nunca são retornadas diretamente.
- **Shared:** somente preocupações realmente compartilhadas; não será uma pasta de utilitários genéricos.

Módulos não devem acessar diretamente o repositório interno de outro módulo. Por exemplo, vendas usam um serviço de aplicação de estoque dentro da mesma transação. Não será criado um barramento de eventos interno para esse fluxo síncrono.

## 11. API REST planejada

Prefixo: `/api/v1`. Listagens grandes aceitam `page`, `size` e filtros simples. Alterações retornam DTOs ou `204` conforme o caso.

### Autenticação

| Método | URL | Objetivo | Acesso |
| --- | --- | --- | --- |
| POST | `/auth/login` | validar credenciais e emitir token | público |
| GET | `/auth/me` | obter usuário autenticado | autenticado |

### Usuários

| Método | URL | Objetivo | Acesso |
| --- | --- | --- | --- |
| GET | `/users` | listar usuários | ADMIN |
| POST | `/users` | criar usuário | ADMIN |
| GET | `/users/{id}` | consultar usuário | ADMIN |
| PATCH | `/users/{id}` | alterar nome, papel ou estado | ADMIN |

### Categorias e marcas

| Método | URL | Objetivo | Acesso |
| --- | --- | --- | --- |
| GET | `/categories` | listar categorias | autenticado |
| POST | `/categories` | criar categoria | ADMIN |
| PUT | `/categories/{id}` | editar categoria | ADMIN |
| PATCH | `/categories/{id}/status` | ativar/desativar | ADMIN |
| GET | `/brands` | listar marcas | autenticado |
| POST | `/brands` | criar marca | ADMIN |
| PUT | `/brands/{id}` | editar marca | ADMIN |
| PATCH | `/brands/{id}/status` | ativar/desativar | ADMIN |

### Produtos e variantes

| Método | URL | Objetivo | Acesso |
| --- | --- | --- | --- |
| GET | `/products` | listar e filtrar produtos | autenticado |
| POST | `/products` | criar produto | ADMIN |
| GET | `/products/{id}` | detalhar produto e variantes | autenticado |
| PUT | `/products/{id}` | editar produto | ADMIN |
| PATCH | `/products/{id}/status` | ativar/desativar produto | ADMIN |
| POST | `/products/{productId}/variants` | criar variante e saldo zero | ADMIN |
| GET | `/variants/{id}` | consultar variante | autenticado |
| PUT | `/variants/{id}` | editar dados permitidos, nunca o SKU | ADMIN |
| PATCH | `/variants/{id}/status` | ativar/desativar variante | ADMIN |
| GET | `/variants/{id}/qr-code` | obter imagem PNG do QR | autenticado |

### Estoque

| Método | URL | Objetivo | Acesso |
| --- | --- | --- | --- |
| GET | `/inventory` | listar saldos e filtrar estoque baixo | autenticado |
| GET | `/inventory/{variantId}` | consultar saldo de uma variante | autenticado |
| GET | `/inventory/movements` | consultar histórico paginado | autenticado |
| POST | `/inventory/entries` | registrar entrada | ADMIN/FUNCIONARIO |
| POST | `/inventory/manual-exits` | registrar saída avulsa | ADMIN |
| POST | `/inventory/adjustments` | ajustar para quantidade contada | ADMIN |

### Vendas

| Método | URL | Objetivo | Acesso |
| --- | --- | --- | --- |
| GET | `/sales` | listar vendas por período/status | autenticado |
| POST | `/sales` | concluir venda e baixar estoque | ADMIN/FUNCIONARIO |
| GET | `/sales/{id}` | detalhar venda e itens | autenticado |
| POST | `/sales/{id}/cancellation` | cancelar totalmente e repor estoque | ADMIN |

### Scanner e dashboard

| Método | URL | Objetivo | Acesso |
| --- | --- | --- | --- |
| GET | `/scan/{qrToken}` | consultar os dados atuais pelo token | autenticado |
| GET | `/dashboard/summary` | obter indicadores essenciais | autenticado |

### Endpoints posteriores

- `/suppliers` para CRUD e ativação de fornecedores.
- `POST /sales/{id}/returns` e `GET /sales/{id}/returns` para devoluções.
- `/reports` somente após definição de consultas realmente úteis.

## 12. DTOs

DTOs serão `record` quando representarem dados imutáveis e simples.

### Exemplos por módulo

- Auth: `LoginRequest`, `LoginResponse`, `CurrentUserResponse`.
- User: `CreateUserRequest`, `UpdateUserRequest`, `UserResponse`.
- Catalog: `CreateCategoryRequest`, `CategoryResponse`, equivalentes de marca; `CreateProductRequest`, `UpdateProductRequest`, `ProductSummaryResponse`, `ProductDetailResponse`.
- Variant: `CreateVariantRequest`, `UpdateVariantRequest`, `VariantResponse`.
- Inventory: `StockEntryRequest`, `ManualExitRequest`, `InventoryAdjustmentRequest`, `InventoryBalanceResponse`, `StockMovementResponse`.
- Sale: `CreateSaleRequest`, `SaleItemRequest`, `SaleSummaryResponse`, `SaleDetailResponse`, `SaleItemResponse`.
- Scan: `ScannedVariantResponse`.
- Dashboard: `DashboardSummaryResponse`.
- Infraestrutura: `PageResponse<T>` apenas se a resposta nativa do Spring expuser detalhes desnecessários.

Requests usarão Jakarta Bean Validation. DTOs de resposta não exporão senha, hash, detalhes internos do JWT, proxies JPA ou relações recursivas.

## 13. Tratamento de erros

Um `@RestControllerAdvice` produzirá um formato consistente baseado em `ProblemDetail`, contendo ao menos:

- `type` ou código estável do problema;
- `title`;
- `status`;
- `detail` seguro para o cliente;
- `instance`/caminho;
- `timestamp`;
- `fieldErrors` quando houver validação de campos.

| Situação | HTTP | Código sugerido |
| --- | ---: | --- |
| JSON ou parâmetros inválidos | 400 | `INVALID_REQUEST` |
| Falha de Bean Validation | 400 | `VALIDATION_ERROR` |
| Credenciais ausentes/inválidas | 401 | `UNAUTHORIZED` |
| Papel insuficiente | 403 | `FORBIDDEN` |
| Recurso inexistente | 404 | `RESOURCE_NOT_FOUND` |
| SKU, email ou nome normalizado duplicado | 409 | `DUPLICATE_RESOURCE` |
| Estoque insuficiente | 409 | `INSUFFICIENT_STOCK` |
| Venda já cancelada/estado inválido | 409 | `INVALID_STATE` |
| Conflito de integridade não previsto | 409 | `DATA_CONFLICT` |
| Erro inesperado | 500 | `INTERNAL_ERROR` |

Mensagens internas, SQL, stack traces e segredos não serão enviados ao cliente. Erros de constraint serão mapeados de forma controlada, sem depender apenas da mensagem textual do banco.

## 14. Autenticação e autorização

### Decisão do MVP

- Spring Security com API stateless.
- Login por email e senha.
- Senhas com BCrypt.
- JWT de acesso de curta duração, assinado com segredo vindo de variável de ambiente.
- Token enviado em `Authorization: Bearer <token>`.
- Sem OAuth externo e sem refresh token no MVP.
- O frontend mantém o token em memória; persistência em `sessionStorage` pode ser usada por conveniência local, documentando o risco de XSS e limpando no logout/expiração. Não usar `localStorage` como decisão automática.
- CORS restrito à origem local configurada do frontend.

O token informa o identificador e o papel, mas o backend continua sendo a fonte de verdade e verifica se o usuário existe e está ativo. Regras de acesso serão configuradas no filtro HTTP e reforçadas nos casos sensíveis com autorização por método quando isso tornar a intenção mais clara.

O primeiro ADMIN será criado por um bootstrap local idempotente, usando email e senha fornecidos por variáveis de ambiente. Nenhuma credencial real será commitada.

## 15. QR Code

### Conteúdo

O QR conterá uma URL do frontend semelhante a:

```text
http://localhost:5173/scan/{qrToken}
```

`qrToken` será um UUID aleatório e estável. O QR não armazenará nome, preço, saldo ou SKU, pois esses valores podem mudar. Também não concederá autorização: a consulta do backend continuará protegida.

### Fluxo

```text
produto cadastrado
→ variante e saldo zero criados
→ SKU e qrToken definidos
→ QR gerado sob demanda
→ usuário abre o scanner no celular
→ câmera lê a URL/token
→ React chama GET /api/v1/scan/{qrToken}
→ Spring Security valida o usuário
→ backend consulta variante e saldo atuais
→ frontend mostra dados e ações permitidas
```

O PNG pode ser gerado sob demanda no backend com uma biblioteca pequena e consolidada. Não é necessário armazenar o arquivo binário no PostgreSQL. A leitura no frontend usará uma biblioteca compatível com `getUserMedia`; entrada manual do SKU/token será oferecida como alternativa para testes sem câmera. HTTPS é normalmente exigido pela câmera fora de `localhost`, o que será documentado sem planejar deploy agora.

## 16. Frontend

### Páginas do MVP

- `/login`: autenticação e mensagens de erro.
- `/dashboard`: vendas recentes, total de SKUs, unidades e estoque baixo.
- `/products`: lista, filtros e status.
- `/products/new` e `/products/{id}/edit`: produto e variantes, conforme permissão.
- `/inventory`: saldos, estoque mínimo e ações autorizadas.
- `/movements`: histórico paginado e filtros.
- `/sales`: lista de vendas.
- `/sales/new`: montagem e confirmação de venda.
- `/sales/{id}`: detalhe e cancelamento autorizado.
- `/scan` e `/scan/{qrToken}`: câmera, entrada alternativa e resultado.
- `/users`: administração de usuários.
- Fornecedores serão adicionados apenas na funcionalidade posterior correspondente.

### Organização proposta

```text
src/
├── app/          # rotas, providers e configuração
├── features/     # auth, products, inventory, sales, scan, dashboard, users
├── components/   # componentes realmente reutilizados
├── services/     # cliente HTTP e infraestrutura de API
├── types/        # contratos compartilhados pequenos
└── styles/
```

Não será adotada uma biblioteca global de estado sem necessidade. Estado do formulário fica local; autenticação usa Context; dados remotos começam com hooks e um cliente HTTP simples. Uma biblioteca de cache só será adicionada se repetição, invalidação e carregamento demonstrarem necessidade concreta.

### Responsividade e acessibilidade

- Layout começa por telas pequenas e se adapta ao desktop.
- Tabelas críticas terão alternativa em cards ou rolagem horizontal controlada.
- Botões e alvos de toque terão tamanho adequado.
- Formulários terão labels, foco visível, mensagens associadas e navegação por teclado.
- O scanner terá fallback de digitação/pesquisa.

## 17. Integração frontend e backend

```text
React → HTTP/JSON → Controller REST → Service transacional → Repository/JPA → PostgreSQL
```

- O frontend usa uma URL base vinda de `VITE_API_URL`.
- Um cliente HTTP central adiciona o JWT, converte respostas e trata 401 de modo consistente.
- Requests e responses seguem os DTOs documentados no OpenAPI.
- O backend configura CORS para a origem local do Vite.
- Datas viajam em ISO 8601; valores monetários são enviados como números decimais e formatados em BRL na interface.
- Regra de negócio e autorização nunca dependem apenas de validações do React.

## 18. Vite

O Vite terá somente estas responsabilidades:

- servidor de desenvolvimento com atualização rápida;
- build de produção para validar que o frontend é empacotável;
- carregamento de variáveis públicas prefixadas por `VITE_`;
- configuração opcional de proxy local para `/api`, caso simplifique CORS no desenvolvimento;
- execução do ambiente de testes frontend quando essa etapa for iniciada.

Não serão adicionados plugins avançados, configuração manual de bundling ou otimizações sem medição.

## 19. Estrutura planejada do repositório

```text
stocktag/
├── backend/
│   ├── src/
│   │   ├── main/
│   │   │   ├── java/com/stocktag/
│   │   │   └── resources/
│   │   │       ├── application.yml
│   │   │       └── db/migration/
│   │   └── test/
│   │       └── java/com/stocktag/
│   ├── pom.xml
│   └── mvnw, mvnw.cmd
├── frontend/
│   ├── src/
│   ├── public/
│   ├── package.json
│   └── vite.config.ts
├── docs/
│   ├── PLAN.md
│   ├── diagrams/              # criado somente quando houver diagramas
│   └── images/                # screenshots na etapa de apresentação
├── .env.example
├── .gitignore
├── docker-compose.yml
├── AGENTS.md
└── README.md
```

## 20. Docker e execução local

### Decisão inicial

Somente o PostgreSQL será executado em container durante o desenvolvimento. Backend com Maven Wrapper e frontend com npm serão executados no host, o que simplifica hot reload, depuração e aprendizado.

O `docker-compose.yml` deverá definir:

- imagem do PostgreSQL com versão fixada;
- banco, usuário e senha de desenvolvimento vindos de variáveis com exemplos seguros;
- volume nomeado para persistência local;
- porta configurável;
- healthcheck.

Dockerfiles de backend e frontend não fazem parte das primeiras etapas. Na etapa final do MVP, poderá ser avaliado um Compose completo apenas se melhorar de fato a experiência “clonar e executar”. Isso continuará sendo execução local, não deploy.

## 21. Flyway

Flyway será a única forma normal de evoluir o schema. O Hibernate usará validação (`ddl-auto=validate`), nunca criação automática do banco compartilhado.

Sequência conceitual inicial:

```text
V1__create_users.sql
V2__create_catalog.sql
V3__create_inventory.sql
V4__create_sales.sql
```

Uma nova migration será criada quando uma alteração de código exigir mudança persistente: tabela, coluna, constraint, índice ou transformação de dados. Depois que uma migration estiver commitada e compartilhada, ela não será editada; corrigir significa criar a próxima migration. Assim, todos percorrem o mesmo histórico e o checksum do Flyway permanece confiável.

Migrations devem ser pequenas, revisáveis e executadas nos testes de integração.

## 22. Estratégia de testes

### Pirâmide prática

1. Muitos testes unitários para regras e cálculos.
2. Testes de integração focados em JPA, segurança, transações e PostgreSQL.
3. Poucos testes web do principal fluxo; E2E completo é posterior ao MVP.

### Testes unitários — JUnit e Mockito

- normalização de email, SKU, cor e tamanho;
- cálculo de subtotal e total;
- consolidação de itens repetidos;
- validação de estados da venda;
- cálculo do delta de ajuste;
- permissões contextuais quando não cobertas só pela configuração;
- mapeamento de entidades para DTOs.

Mockito será usado quando uma unidade tiver dependências relevantes. Não se deve mockar classes de valor ou testar apenas chamadas triviais.

### Testes de integração — Spring Boot e Testcontainers

Usar PostgreSQL real temporário para evitar diferenças de dialeto com H2. Reutilizar uma configuração base de container entre classes quando isso reduzir tempo sem compartilhar estado de teste.

Cenários obrigatórios:

- migrations sobem em banco vazio;
- email e SKU são únicos também no banco;
- criar variante cria saldo zero;
- entrada aumenta saldo e gera exatamente uma movimentação;
- saída sem saldo retorna conflito e não grava movimento;
- ajuste registra antes, depois, motivo e usuário;
- venda reduz todos os saldos, grava itens e movimentos na mesma transação;
- falha em um item reverte a venda inteira;
- cancelamento devolve estoque e não pode ocorrer duas vezes;
- usuário não autenticado recebe 401;
- FUNCIONARIO recebe 403 em cadastro, ajuste e cancelamento restritos;
- duas vendas simultâneas para a última unidade resultam em uma venda concluída e uma rejeitada;
- consultas paginadas e filtros principais retornam resultados corretos.

### Frontend

Quando o frontend começar, introduzir Vitest e React Testing Library apenas para comportamentos importantes:

- login e expiração de sessão;
- proteção de rota por autenticação/papel;
- montagem de venda e exibição de conflito de estoque;
- resultado do scanner e fallback manual;
- estados de carregamento, vazio e erro das páginas principais.

### Critério geral

Cada alteração executa os testes do módulo afetado. Antes de concluir uma etapa, executar toda a suíte existente e o build dos dois projetos quando ambos existirem.

## 23. Swagger / OpenAPI

O backend usará `springdoc-openapi` para expor a documentação e o Swagger UI no perfil local.

A documentação deve incluir:

- autenticação Bearer e como obter o token;
- objetivo dos endpoints;
- exemplos mínimos de requests e responses;
- códigos 400, 401, 403, 404 e 409 relevantes;
- paginação e filtros;
- schemas sem expor entidades JPA.

O Swagger será protegido/configurado de forma apropriada fora do perfil local se um dia houver outro ambiente. A documentação deve refletir o contrato real e será verificada ao fim de cada módulo de API.

## 24. Estratégia de Git

- `main` deve permanecer executável e com testes passando.
- Criar branches curtas como `feat/inventory-entry`, `feat/sales` e `fix/concurrent-sale`.
- Integrar pequenas entregas por pull request quando possível, mesmo trabalhando sozinho, para deixar decisões revisáveis.
- Usar um subconjunto simples de Conventional Commits, sem Git Flow complexo.

Exemplos:

```text
docs: define MVP and development roadmap
feat(catalog): add product variant creation
feat(inventory): record stock entry movement
test(sales): cover concurrent sale of last unit
fix(security): deny inventory adjustment to employee
```

Commits devem representar uma mudança coerente, sem misturar refatoração ampla com nova funcionalidade.

## 25. Evolução do README

Durante o desenvolvimento, o README será atualizado gradualmente. Na conclusão deverá funcionar como apresentação principal e conter:

- problema e proposta do StockTag;
- GIF ou screenshots dos fluxos principais;
- funcionalidades e papéis de acesso;
- stack e arquitetura resumida;
- diagrama de banco;
- pré-requisitos e execução reproduzível;
- credenciais apenas de demonstração local ou instrução de bootstrap;
- links do Swagger e frontend;
- como executar testes;
- decisões técnicas de maior valor, especialmente estoque e concorrência;
- limitações e funcionalidades futuras.

O planejamento completo permanece neste arquivo; não será duplicado no README.

## 26. Documentação visual

Somente quatro diagramas são planejados, criados quando o modelo correspondente estiver estável:

1. **Entidade-relacionamento:** tabelas, cardinalidades e constraints principais.
2. **Arquitetura geral:** React → REST → módulos Spring → PostgreSQL.
3. **Sequência da venda:** autenticação, transação, locks, validação, saldo, movimentos e resposta.
4. **Fluxo do QR Code:** geração do token até a consulta dos dados atuais.

Diagramas devem ser mantidos próximos do código que representam. Diagramas de classes de todos os DTOs ou infraestrutura inexistente não agregam valor.

## 27. Roadmap de desenvolvimento

As etapas são sequenciais. Ao iniciar uma etapa, revisar este plano; ao encerrá-la, atualizar status, decisões e README. APIs poderão ser exercitadas pelo Swagger antes de existir uma tela correspondente.

| Etapa | Status |
| --- | --- |
| 1 — Fundação executável | não iniciada |
| 2 — Usuários, autenticação e autorização | não iniciada |
| 3 — Catálogo básico | não iniciada |
| 4 — Variantes, SKUs e saldo inicial | não iniciada |
| 5 — Movimentações de estoque | não iniciada |
| 6 — Vendas, cancelamento e concorrência | não iniciada |
| 7 — QR Code e consulta móvel | não iniciada |
| 8 — Frontend: fundação, autenticação e catálogo | não iniciada |
| 9 — Frontend: estoque, vendas, scanner e dashboard | não iniciada |
| 10 — Qualidade e apresentação do MVP | não iniciada |

### Etapa 1 — Fundação executável

**Objetivo:** criar a base mínima reproduzível do monorepo.

**Funcionalidades:** projetos Spring Boot e React/Vite; PostgreSQL via Compose; configuração por ambiente; health check; Flyway conectado; tratamento de erro base.

**Java praticado:** estrutura de projeto, pacotes, records/configuração e exceções.

**Spring praticado:** starters, configuração, profiles, injeção de dependência, Actuator ou endpoint simples de saúde.

**SQL praticado:** conexão, schema, migration inicial e tipos PostgreSQL.

**Componentes principais:** `backend/pom.xml`, classe principal, `application.yml`, migration inicial, `frontend/package.json`, rotas base, `docker-compose.yml`, `.env.example`.

**Testes:** contexto Spring inicia; migration roda em Testcontainers; backend compila; frontend faz build.

**Concluída quando:** em clone limpo, comandos documentados iniciam PostgreSQL, backend e frontend; a saúde responde; Swagger abre; builds e testes base passam.

### Etapa 2 — Usuários, autenticação e autorização

**Objetivo:** estabelecer identidade e proteção antes dos dados de negócio.

**Funcionalidades:** entidade/tabela de usuário; bootstrap local do primeiro ADMIN; criação/listagem/ativação; login; JWT; BCrypt; papéis ADMIN e FUNCIONARIO.

**Java praticado:** enums, interfaces, exceptions, imutabilidade e tratamento de `Optional`.

**Spring praticado:** Spring Security, filtros, `PasswordEncoder`, validação, controllers, services e repositories.

**SQL praticado:** unique constraint, normalização, índices e atualização de estado.

**Componentes principais:** módulos `auth`, `user` e `shared/security`; DTOs; migration de usuários; configuração OpenAPI Bearer.

**Testes:** login correto/incorreto; hash de senha; email duplicado; 401/403; usuário desativado; somente ADMIN administra usuários.

**Concluída quando:** um ADMIN local consegue autenticar, criar FUNCIONARIO e os dois recebem exatamente as permissões planejadas em testes e Swagger.

### Etapa 3 — Catálogo básico

**Objetivo:** cadastrar os dados comuns dos produtos.

**Funcionalidades:** categorias, marcas e produtos; consulta paginada; filtros simples; ativação/desativação; URL de imagem.

**Java praticado:** composição, validações, DTOs, mapeamento e paginação.

**Spring praticado:** Spring MVC, Bean Validation, Spring Data JPA e tratamento de erros.

**SQL praticado:** FKs, uniques normalizados, joins e índices simples.

**Componentes principais:** `catalog/category`, `catalog/brand`, `catalog/product`; migration de catálogo; endpoints e DTOs.

**Testes:** nomes duplicados normalizados; FKs inválidas; autorização; filtros; desativação sem apagar histórico.

**Concluída quando:** ADMIN gerencia catálogo pelo Swagger e FUNCIONARIO apenas consulta, com validações e paginação cobertas.

### Etapa 4 — Variantes, SKUs e saldo inicial

**Objetivo:** representar corretamente cada combinação vendável.

**Funcionalidades:** criação e consulta de variantes; SKU/token únicos; cor/tamanho; preço; criação automática do saldo zero; estoque mínimo.

**Java praticado:** invariantes, `BigDecimal`, normalização e transação simples.

**Spring praticado:** relacionamento JPA, serviço transacional e consultas por SKU/token.

**SQL praticado:** relação 1:1, constraints compostas, UUID e precisão decimal.

**Componentes principais:** `catalog/variant`, `inventory/InventoryBalance`, migrations de variantes e saldo, DTOs.

**Testes:** SKU global único; combinação única; preço inválido; variante cria um único saldo zero; SKU não é editado.

**Concluída quando:** cada variante ativa é consultável por ID/SKU e sempre possui saldo consistente, provado em PostgreSQL real.

### Etapa 5 — Movimentações de estoque

**Objetivo:** alterar estoque somente com histórico auditável.

**Funcionalidades:** entrada, saída manual, ajuste, listagem de saldos/estoque baixo e histórico paginado; auditoria do usuário.

**Java praticado:** polimorfismo apenas se reduzir duplicação real, cálculos de delta, exceptions e coleções.

**Spring praticado:** `@Transactional`, segurança por método, queries e paginação.

**SQL praticado:** checks, histórico append-only, ordenação e índices compostos.

**Componentes principais:** `inventory` service/controller/repositories/entities/DTOs; migration de movimentos.

**Testes:** entrada; saída; ajuste positivo/negativo/sem diferença; saldo insuficiente; rollback; usuário responsável; permissões; estoque baixo.

**Concluída quando:** não existe caminho da aplicação que altere saldo sem movimento, e todos os cenários críticos passam em testes de integração.

### Etapa 6 — Vendas, cancelamento e concorrência

**Objetivo:** implementar o caso de uso técnico mais valioso do projeto.

**Funcionalidades:** criar/listar/detalhar venda; snapshots; baixa atômica; cancelamento total; bloqueio pessimista ordenado.

**Java praticado:** agregados, consolidação com collections, cálculo monetário, estados e concorrência.

**Spring praticado:** transações abrangendo serviços, JPA lock, propagação de exceções e testes concorrentes.

**SQL praticado:** lock de linha, atomicidade, isolamento, FKs, constraints e consultas por período.

**Componentes principais:** módulo `sale`; método de lock no inventory repository; migrations de vendas/itens; DTOs e endpoints.

**Testes:** venda simples/múltipla; total calculado; rollback de todos os itens; cancelamento; segundo cancelamento; produto inativo; duas vendas disputando a última unidade.

**Concluída quando:** o teste concorrente termina repetidamente com um sucesso, um conflito e saldo zero, sem venda ou movimento parcial.

### Etapa 7 — QR Code e consulta móvel

**Objetivo:** localizar uma variante rapidamente sem duplicar dados mutáveis no código.

**Funcionalidades:** geração PNG sob demanda; consulta por token; rota de scanner; entrada manual alternativa.

**Java praticado:** integração controlada com biblioteca e manipulação de bytes/streams na borda HTTP.

**Spring praticado:** content type de imagem, endpoint por token e autorização.

**SQL praticado:** busca por UUID único e join de variante/produto/saldo.

**Componentes principais:** módulo `scan`, gerador QR, endpoints e testes; página React inicial do scanner se a fundação frontend já estiver pronta.

**Testes:** QR contém somente URL/token; token inexistente; dados atuais após mudança de preço/saldo; acesso não autenticado; resposta PNG válida.

**Concluída quando:** um QR gerado pode ser lido e leva a dados atuais da variante, com fallback funcional sem câmera.

### Etapa 8 — Frontend: fundação, autenticação e catálogo

**Objetivo:** tornar os módulos já prontos utilizáveis por uma interface responsiva.

**Funcionalidades:** layout, rotas, login/logout, proteção por papel, cliente HTTP, feedback de erro, produtos e formulários de catálogo/variantes.

**Java praticado:** revisão dos contratos e adequação de DTOs sem mover regras para o frontend.

**Spring praticado:** CORS, serialização e refinamento do OpenAPI.

**SQL praticado:** ajuste de queries somente se a interface revelar problema mensurável.

**Componentes principais:** `app`, `features/auth`, `features/products`, componentes de formulário/lista, serviço HTTP.

**Testes:** login; rota protegida; diferença de ações por papel; validação de formulário; estados de loading/erro/vazio; build.

**Concluída quando:** ADMIN gerencia o catálogo e FUNCIONARIO consulta por desktop e viewport móvel, sem depender do Swagger.

### Etapa 9 — Frontend: estoque, vendas, scanner e dashboard

**Objetivo:** completar todos os fluxos do MVP na interface.

**Funcionalidades:** saldos, alertas, movimentos, entrada/ajuste conforme papel, nova venda, detalhes/cancelamento, scanner e dashboard simples.

**Java praticado:** refinamento orientado por uso, sem adicionar camadas artificiais.

**Spring praticado:** filtros/paginação, contratos de erros e queries de resumo.

**SQL praticado:** agregações simples e análise das consultas do dashboard.

**Componentes principais:** features `inventory`, `sales`, `scan`, `dashboard`; endpoint de resumo; estilos responsivos.

**Testes:** venda pela UI em nível de componente; conflito 409; permissões; cancelamento; scanner/fallback; dashboard; build completo.

**Concluída quando:** todos os critérios do MVP são executáveis pela interface em desktop e celular, e erros importantes recebem feedback claro.

### Etapa 10 — Qualidade e apresentação do MVP

**Objetivo:** converter a aplicação funcional em um repositório de portfólio reproduzível.

**Funcionalidades:** revisão de testes e segurança; dados de demonstração locais; documentação final; diagramas essenciais; screenshots; avaliação de Compose completo.

**Java praticado:** refatoração segura, legibilidade, cobertura de casos críticos e análise de trade-offs.

**Spring praticado:** profiles, configuração segura, logs úteis e documentação do contrato.

**SQL praticado:** revisão de plano de consultas e índices somente para queries relevantes.

**Componentes principais:** README final, `docs/diagrams`, imagens, scripts/comandos documentados, suíte de testes e configuração local.

**Testes:** suíte completa backend/frontend; build limpo; execução em clone/ambiente limpo; roteiro manual do fluxo principal.

**Concluída quando:** outra pessoa consegue clonar, configurar, executar, autenticar e realizar o fluxo catálogo → entrada → venda → QR → cancelamento apenas com o README; todos os testes passam e o repositório explica suas principais decisões.

## 28. Ordem de aprendizado

Não é necessário dominar todo o Spring antes de começar. Aprender o mínimo imediatamente necessário para a próxima etapa:

| Antes da etapa | Conhecimentos necessários |
| --- | --- |
| 1 | sintaxe Java, classes, métodos, Maven, HTTP básico, Git básico, terminal, variáveis de ambiente e Docker Compose básico |
| 2 | interfaces, enums, exceptions, annotations, DI, hash de senha, autenticação versus autorização e noções de JWT |
| 3 | collections, records/DTOs, generics básicos, REST, status HTTP, validação, SQL `SELECT/INSERT/UPDATE`, PK e FK |
| 4 | relacionamentos JPA, `BigDecimal`, constraints únicas/compostas, UUID e transações simples |
| 5 | ACID, `@Transactional`, rollback, locks em conceito, paginação, índices e auditoria |
| 6 | isolamento, corrida crítica, lock pessimista/otimista, threads suficientes para entender o teste, agregados e cálculos monetários |
| 7 | URLs, content types, permissões de câmera e integração com biblioteca externa pequena |
| 8 | React funcional, TypeScript, props/state, hooks, rotas, formulários, fetch/HTTP, CORS e armazenamento do token |
| 9 | composição de componentes, responsividade, agregações SQL simples e estados assíncronos de UI |
| 10 | refatoração, leitura de logs, segurança básica, documentação técnica e revisão de GitHub |

Método sugerido por etapa: estudar o conceito mínimo, criar um exemplo pequeno se necessário, implementar um caso vertical, testá-lo e só então ampliar.

## 29. Diferenciais para portfólio

As melhores partes para demonstrar em entrevista serão:

- explicar por que variante, e não produto, possui SKU e saldo;
- mostrar saldo materializado junto de histórico imutável, sem chamar isso de event sourcing;
- percorrer a transação de venda e provar rollback integral;
- executar o teste concorrente da última unidade e comparar locks;
- demonstrar autorização real no backend, não apenas botões escondidos;
- explicar snapshots do item de venda e por que o histórico não muda com o catálogo;
- mostrar migrations, constraints e Testcontainers com PostgreSQL real;
- abrir o Swagger e depois realizar o mesmo fluxo no React responsivo;
- justificar conscientemente o que foi deixado fora do escopo.

## 30. Riscos de overengineering

Não adicionar sem problema concreto e documentado:

- microserviços, service discovery ou API gateway;
- Kafka, RabbitMQ ou qualquer mensageria;
- Kubernetes, serviços cloud ou pipeline de deploy;
- Redis, Elasticsearch ou banco adicional;
- CQRS, event sourcing ou arquitetura distribuída;
- arquitetura hexagonal completa, DDD tático em todas as classes ou repositórios genéricos;
- Spring Modulith, múltiplos módulos Maven ou eventos internos para chamadas síncronas simples;
- Lombok, MapStruct ou abstrações genéricas só para reduzir poucas linhas;
- múltiplos depósitos, reservas, pagamentos, fiscal ou multi-tenant;
- estado global complexo no frontend e biblioteca de UI grande antes de existir uma necessidade;
- cobertura de 100% como meta; priorizar cenários de risco.

Sinais para cortar escopo: uma funcionalidade exige infraestrutura nova, não reforça o núcleo Java/Spring/SQL, não é demonstrável no fluxo principal ou impede finalizar uma etapa.

## 31. Análise final

O projeto é forte para estágio/júnior porque combina CRUD compreensível com problemas reais de backend: modelagem de variantes, consistência de estoque, transações, concorrência, segurança e testes de integração.

Os elementos mais valiosos são estoque auditável, venda atômica, cancelamento reversível, lock concorrente e execução reproduzível. O frontend e QR Code dão uma demonstração visual, mas não devem consumir mais esforço que o núcleo backend.

Os maiores riscos são tentar incluir fornecedores, devoluções parciais, impressão em lote, relatórios e refinamento visual antes de fechar o fluxo principal. Se for necessário cortar ainda mais, remover primeiro dashboard avançado, impressão, fornecedores e devoluções; preservar catálogo com variantes, estoque/histórico, venda/cancelamento, segurança e testes.

Uma versão suficientemente boa para o currículo é o MVP até a Etapa 10, mesmo sem nenhuma funcionalidade posterior. Ela deve ter interface limpa, README reproduzível, alguns screenshots, Swagger e testes críticos passando. Terminar e explicar bem essa versão vale mais do que aumentar a stack.

## 32. Critério global de conclusão do MVP

O MVP só será marcado como concluído quando:

- todos os itens da seção **MVP** estiverem presentes;
- todas as etapas 1 a 10 atenderem seus critérios objetivos;
- regras críticas tiverem testes automatizados;
- a suíte completa e os builds passarem a partir de um banco vazio;
- o README permitir execução local por outra pessoa;
- Swagger e documentação visual refletirem o sistema real;
- não houver decisão relevante divergente deste plano sem registro.

## 33. Registro de decisões

| Data | Decisão | Motivo | Impacto |
| --- | --- | --- | --- |
| 2026-08-06 | Adotar monólito modular por funcionalidade | organização clara sem custo distribuído | um backend Maven, um banco e pacotes por feature |
| 2026-08-06 | Limitar o MVP a uma loja/estoque | manter o projeto concluível | nenhuma entidade de filial ou transferência |
| 2026-08-06 | Manter saldo materializado e movimentos imutáveis | leitura simples com auditoria | toda alteração ocorre no serviço de estoque em uma transação |
| 2026-08-06 | Usar lock pessimista ordenado na venda | caso de conflito explícito e transação curta | teste concorrente obrigatório; sem retry otimista no MVP |
| 2026-08-06 | Usar JWT simples sem OAuth/refresh | API React local simples e profissional | token Bearer curto; segredo por ambiente |
| 2026-08-06 | Postergar fornecedores, devolução parcial e impressão em lote | reduzir escopo sem perder o núcleo | permanecem como funcionalidades importantes posteriores |
| 2026-08-06 | Rodar inicialmente só PostgreSQL no Docker | facilitar debug e hot reload | Maven e Vite executados no host durante o desenvolvimento |

### Modelo para novas decisões

Ao mudar uma decisão importante, adicionar uma linha com data, decisão, motivo e impacto; atualizar também todas as seções afetadas. Se a mudança alterar contratos ou comportamento já implementado, incluir migration/testes/documentação correspondentes.
