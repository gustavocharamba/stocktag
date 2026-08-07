# Instruções permanentes do repositório

- Este é um projeto de portfólio para vaga de estágio ou desenvolvimento Java júnior.
- Backend principal: Java 21, Spring Boot e Maven.
- Frontend: React, TypeScript e Vite.
- Banco: PostgreSQL, com schema versionado por Flyway.
- Manter um monólito modular; não utilizar microserviços.
- Não fazer overengineering nem adicionar tecnologias sem necessidade concreta e documentada.
- Priorizar código simples, legível, testável e bem estruturado.
- Consultar `docs/PLAN.md` antes de implementar uma nova funcionalidade ou iniciar outra etapa.
- Seguir o roadmap de `docs/PLAN.md` e não avançar várias etapas de uma vez sem necessidade.
- Manter `docs/PLAN.md` atualizado quando decisões relevantes de escopo, arquitetura, banco, API ou implementação mudarem.
- Não alterar regras de negócio silenciosamente; documentar a decisão, o motivo e o impacto.
- Não retornar entidades JPA diretamente pela API nem colocar regra de negócio em controllers.
- Preservar o histórico de estoque e a atomicidade das operações definidas no planejamento.
- Executar os testes relevantes após alterações e a suíte completa antes de concluir uma etapa.
- Atualizar o README conforme a execução local e os fluxos reais evoluírem.

O planejamento completo e os critérios de conclusão estão em `docs/PLAN.md`. Outros materiais técnicos devem permanecer no diretório `docs/` sem duplicar o plano neste arquivo.
