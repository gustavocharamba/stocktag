# AGENTS.md — Instruções permanentes do StockTag

Estas instruções se aplicam a todo o repositório. Elas devem funcionar em qualquer computador e em qualquer nova sessão do Codex.

## 1. Protocolo obrigatório ao iniciar uma tarefa

Antes de propor ou alterar qualquer coisa:

1. Identifique a raiz que contém este `AGENTS.md` e `docs/PLAN.md`. Use sempre caminhos relativos a essa raiz e nunca presuma um caminho absoluto ou trabalhe em outra cópia chamada `stocktag`.
2. Leia este arquivo por completo.
3. Leia em `docs/PLAN.md` pelo menos: status atual, escopo, MVP, regras relacionadas à tarefa, decisões registradas, roadmap e critério da etapa atual.
4. Inspecione a árvore de arquivos, o código relevante, os testes, o `README.md` e o estado do Git. Não presuma que o código está no mesmo estado de uma conversa anterior.
5. Compare o estado real do repositório com o status registrado no plano. Se divergirem, não escolha silenciosamente um deles: preserve o que existe, investigue a diferença e atualize a documentação quando a tarefa autorizar essa mudança.
6. Delimite a menor entrega coerente com o pedido do usuário e com a etapa atual do roadmap.

O simples início de uma sessão não autoriza começar a implementação. Execute apenas o que o usuário pedir. Se `docs/PLAN.md` estiver ausente ou ilegível, não invente um planejamento substituto nem avance no roadmap; informe o problema.

## 2. Fonte de verdade e continuidade

- A solicitação atual do usuário define o trabalho autorizado.
- Este `AGENTS.md` define as regras permanentes de trabalho.
- `docs/PLAN.md` é a fonte de verdade para produto, escopo, regras de negócio, arquitetura, APIs, modelagem, testes e roadmap.
- O código, as migrations e os testes mostram o estado efetivamente implementado.
- O `README.md` apresenta o projeto e deve descrever apenas comandos e funcionalidades que realmente existam.
- Conversas anteriores podem ajudar, mas nunca substituem os arquivos versionados do repositório.

Quando uma decisão relevante mudar, atualize na mesma entrega todas as partes afetadas de `docs/PLAN.md`, inclusive o registro de decisões e o roadmap. Não altere uma regra de negócio, contrato de API ou decisão arquitetural silenciosamente.

## 3. Identidade e prioridades do projeto

- Projeto de portfólio para vaga de estágio ou desenvolvimento backend Java júnior.
- Prioridades: aprendizado, conclusão do MVP, clareza técnica, testes e execução local reproduzível.
- Backend: Java 21, Spring Boot e Maven.
- Frontend: React, TypeScript e Vite.
- Banco: PostgreSQL, com schema versionado por Flyway.
- Arquitetura: monólito modular organizado por funcionalidade, uma aplicação backend e um banco.
- Ambiente inicial: PostgreSQL com Docker Compose; não planejar deploy sem novo pedido explícito.
- Idioma preferencial da documentação e das respostas: português do Brasil. Identificadores técnicos e nomes físicos do banco seguem as convenções definidas no plano.

Uma solução simples, legível e explicável em entrevista é preferível a uma solução mais sofisticada que não resolva uma necessidade concreta.

## 4. Fluxo de desenvolvimento

Ao receber autorização para implementar:

1. Localize a primeira etapa não concluída do roadmap ou a etapa explicitamente pedida.
2. Confirme seus pré-requisitos e critérios objetivos em `docs/PLAN.md`.
3. Inspecione a implementação existente antes de criar arquivos ou dependências.
4. Faça uma alteração pequena e vertical, mantendo o sistema compilável.
5. Implemente ou atualize os testes proporcionais ao risco.
6. Execute os testes relevantes e, antes de concluir uma etapa, a suíte completa existente e os builds aplicáveis.
7. Atualize o status do roadmap somente quando todos os critérios da etapa forem comprovados.
8. Atualize o README quando comandos, configuração ou funcionalidades visíveis mudarem.
9. Ao terminar, relate arquivos alterados, testes executados, decisões tomadas e pendências reais.

Não avance várias etapas de uma vez sem necessidade. Funcionalidades posteriores não devem entrar no MVP antes de sua etapa ser aprovada. Não marque uma etapa como concluída com testes falhando, sem executar os testes possíveis ou sem atender seus critérios objetivos.

## 5. Regras técnicas permanentes

- Manter o monólito modular; não criar microserviços.
- Organizar o backend por funcionalidade. Dentro de cada funcionalidade, criar controller, service, repository, entidades e DTOs somente quando necessários.
- Controllers tratam HTTP e validação de entrada; regras de negócio e transações ficam nos services.
- Não retornar entidades JPA diretamente pela API.
- Não acessar o repository interno de outro módulo quando houver um caso de uso do módulo responsável.
- Não criar abstrações genéricas, camadas, interfaces ou padrões sem benefício concreto.
- Não adicionar dependências sem verificar se a stack atual já resolve o problema e sem documentar uma inclusão relevante.
- Configurações variáveis ficam em propriedades ou variáveis de ambiente. Nunca versionar senhas, tokens ou segredos reais.
- Usar Flyway para mudanças de schema e `ddl-auto=validate` quando a persistência estiver configurada.
- Não editar migration já consolidada/compartilhada; criar uma nova migration corretiva.
- Manter constraints críticas também no PostgreSQL, não apenas na aplicação.
- Tratar dinheiro com `BigDecimal`/`NUMERIC`, datas persistidas em UTC e listagens potencialmente grandes com paginação.
- Preservar alterações preexistentes do usuário e arquivos fora do escopo. Não executar operações destrutivas de Git nem criar commit, branch ou push sem solicitação.

Não reorganize toda a estrutura existente apenas para fazê-la coincidir visualmente com a árvore conceitual do plano. Uma mudança estrutural ampla exige motivo, avaliação de impacto e registro da decisão.

## 6. Invariantes críticas do domínio

Estas regras não podem ser enfraquecidas durante a implementação:

- Cada variante de produto possui SKU globalmente único e saldo próprio.
- O SKU e o token do QR Code são estáveis após a criação.
- Estoque nunca pode ficar negativo.
- Toda alteração de saldo passa pelo módulo de estoque e gera uma movimentação imutável na mesma transação.
- Saldo atual e histórico devem permanecer consistentes; não editar ou apagar movimentos para corrigir o passado.
- Vendas calculam preços e totais no backend, mantêm snapshots dos itens e são atômicas.
- Uma falha em qualquer item reverte a venda inteira.
- A baixa concorrente usa o mecanismo definido no plano: lock pessimista, saldos bloqueados em ordem determinística e revalidação dentro de transação curta.
- Cancelamento de venda é total no MVP, ocorre uma única vez, devolve os itens e cria novas movimentações sem apagar as originais.
- Autorização deve ser garantida no backend, mesmo quando o frontend oculta ações.

Se uma tarefa exigir mudar uma dessas regras, pare a implementação dessa parte, apresente o motivo e o impacto e só prossiga com decisão explícita e documentação atualizada.

## 7. Testes e verificação

- Usar JUnit e Mockito para regras unitárias quando mocks trouxerem valor.
- Usar Testcontainers com PostgreSQL real para migrations, JPA, constraints, segurança, transações e concorrência.
- Não substituir PostgreSQL por H2 nos testes de integração do domínio.
- Cobrir primeiro riscos: estoque insuficiente, rollback, permissões, SKU único, cancelamento e disputa pela última unidade.
- No frontend, testar comportamentos importantes, não detalhes internos de componentes.
- Descobrir e usar os comandos reais do repositório; não afirmar que um teste ou build passou sem executá-lo.
- Se um teste não puder ser executado, informar exatamente qual, por quê e qual risco permanece.

## 8. Limites contra overengineering

Não introduzir sem necessidade concreta, aprovação e atualização do plano:

- microserviços, Kafka, RabbitMQ, Kubernetes ou serviços cloud;
- Redis, Elasticsearch ou outro banco;
- CQRS, event sourcing ou arquitetura distribuída;
- Spring Modulith, múltiplos módulos Maven ou arquitetura hexagonal completa;
- Lombok, MapStruct, repositórios genéricos ou barramento de eventos apenas para reduzir código simples;
- múltiplas lojas, pagamentos, fiscal, e-commerce ou funcionalidades posteriores antes do MVP;
- bibliotecas grandes de estado/UI no frontend sem problema demonstrado.

## 9. Critério de entrega de qualquer tarefa

Uma tarefa só está concluída quando:

- o pedido autorizado foi atendido sem ampliar silenciosamente o escopo;
- regras e decisões do plano continuam válidas ou foram atualizadas com motivo e impacto;
- código e documentação afetados estão coerentes;
- testes relevantes foram criados/atualizados e executados quando possível;
- nenhum segredo ou artefato local indevido foi adicionado;
- o repositório permanece compilável no limite verificável da tarefa;
- pendências e limitações reais foram comunicadas claramente.
