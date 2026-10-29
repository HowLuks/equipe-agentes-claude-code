---
tags: [recurso, desenvolvimento]
status: ativo
---

# Aprendizados de Desenvolvimento

Aprendizados que servem para mais de um projeto, agrupados pela área da equipe de agentes. O registro completo de cada um continua na nota do projeto de origem, que fica linkada em cada item. Aqui entra só o que foi marcado como aplicável a outros projetos.

Cada agente lê a seção da sua área antes de começar a trabalhar, para não repetir um erro que já apareceu. Para ver tudo o que um agente registrou, busque pela tag dele no Obsidian (`#agente/backend`, `#agente/frontend`…).

Formato de cada item: `AAAA-MM-DD: aprendizado — aplicável a X ([[nota de origem]]) #agente/<área>`.

Os itens abaixo são exemplos reais do meu cofre, com os links para notas de clientes removidos.

## Arquitetura
-

## Frontend
- 2026-10-18: No preload do Electron, o nome exposto com `contextBridge.exposeInMainWorld('x', …)` vira global no renderer e conflita com um `const x` de mesmo nome no script da página, que quebra inteira com "Identifier already declared" — aplicável a qualquer app Electron.
- 2026-10-22: O template do Next 16.4 vem com `cacheComponents` e `partialPrefetching`, que quebram o `output: export` ("PPR cannot be enabled in export mode") — aplicável a qualquer Next com static export. #agente/frontend

## Backend
- 2026-10-18: Uma restrição `EXCLUDE` do Postgres que não é `DEFERRABLE` é checada a cada `UPDATE`, então deslocar várias reservas da mesma equipe exige atualizar primeiro quem anda na direção do movimento — aplicável a qualquer agenda/reserva com restrição de sobreposição.
- 2026-10-19: Testar o backend com Python 3.14 local escondeu um `NameError` que derrubou a API em produção (Python 3.12): no 3.14 as anotações de tipo são avaliadas depois. Testar sempre com a mesma versão da produção — aplicável a qualquer backend Python. #agente/orquestrador
- 2026-10-26: Com `EnableRetryOnFailure` no EF Core (SQL Server), uma transação aberta pelo usuário lança `InvalidOperationException`; ela tem que rodar dentro de `Database.CreateExecutionStrategy().ExecuteAsync(...)` — aplicável a qualquer projeto .NET com EF Core e retry. #agente/orquestrador

## DevOps
- 2026-10-19: O primeiro deploy automático encontrou a tabela de migrações vazia e tentou reaplicar as que tinham sido rodadas à mão. Foi preciso registrar as antigas como aplicadas (baseline) antes — aplicável a qualquer projeto que passe a ter runner de migração depois de migrações manuais.

## Segurança
-

## QA
-
