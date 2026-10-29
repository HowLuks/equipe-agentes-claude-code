# Equipe de agentes para o Claude Code

Uma equipe de 8 subagentes do [Claude Code](https://claude.com/claude-code) que eu uso no dia a dia em todos os meus projetos (APIs em FastAPI e ASP.NET, apps Next.js, automações de WhatsApp, Electron). Cada agente tem uma função, permissões de acordo com ela e uma memória compartilhada que guarda os erros já cometidos, para que não se repitam.

A sessão principal do Claude Code funciona como **orquestrador**: entende o pedido, quebra em partes, chama os agentes certos e junta o resultado.

```
                         você
                           │
                           ▼
                 ┌───────────────────┐
                 │   orquestrador    │  sessão principal + ~/Projects/CLAUDE.md
                 └─────────┬─────────┘
       ┌──────────┬────────┼─────────┬──────────┬──────────┐
       ▼          ▼        ▼         ▼          ▼          ▼
  arquiteto   backend  frontend   devops   qa · revisor · seguranca
  (só lê)       (editam código)              (conferem; revisor e
       │          │        │         │        segurança só leem)
       └──────────┴────────┴────┬────┴──────────┘
                                │ devolvem resultado + linhas de
                                │ Decisões e Aprendizados com a tag do agente
                                ▼
                         ┌──────────────┐
                         │ documentador │  único que escreve no cofre
                         └──────┬───────┘
                                ▼
            cofre Obsidian (git): nota do projeto, nota diária,
            "Aprendizados de Desenvolvimento" ──┐
                                                │ cada agente lê a seção
                                                │ da sua área antes de começar
                     ◀──────────────────────────┘
```

## Os agentes

| Agente | Ferramentas | Função |
|---|---|---|
| [`arquiteto`](agents/arquiteto.md) | só leitura | Propõe a menor solução: arquivos afetados, modelo de dados, contratos de API, riscos. Não implementa |
| [`backend`](agents/backend.md) | todas | API, banco, migrações reversíveis, integrações; roda os testes antes de entregar |
| [`frontend`](agents/frontend.md) | todas | Telas e componentes, reusando o que o projeto já tem; roda build e lint |
| [`devops`](agents/devops.md) | todas | CI/CD, Docker, deploy; explica como desfazer cada mudança de infraestrutura |
| [`qa`](agents/qa.md) | todas | Reproduz o bug antes de declarar corrigido e relata a saída real dos testes |
| [`revisor`](agents/revisor.md) | só leitura | Lê o diff e aponta bugs e excesso de código, com `arquivo:linha` |
| [`seguranca`](agents/seguranca.md) | só leitura | Procura falhas exploráveis (injeção, controle de acesso, segredos) e explica a correção |
| [`documentador`](agents/documentador.md) | leitura e escrita | Único que grava no cofre; também atualiza README e `docs/` do projeto |

## Decisões de projeto

**Permissões por função.** Quem audita não edita. O `revisor`, o `seguranca` e o `arquiteto` só têm `Read`, `Grep`, `Glob` e `Bash`. Assim a revisão fica independente da implementação, e um agente que só deveria opinar não "conserta" nada por conta própria.

**Um único agente escreve na memória.** Os outros devolvem as decisões e os aprendizados num formato fixo (`- AAAA-MM-DD: aprendizado — aplicável a X #agente/backend`), e só o `documentador` grava. Isso evita escritas concorrentes e notas duplicadas, e mantém o formato do cofre consistente.

**Rastreabilidade por tag.** Toda linha registrada termina com a tag do agente que a originou. No Obsidian, buscar `#agente/seguranca` mostra tudo o que a auditoria já encontrou, em todos os projetos.

**Memória que muda o comportamento.** Aprendizados que servem para outros projetos também vão para uma nota compartilhada ([exemplo](exemplos/aprendizados-de-desenvolvimento.md)), separada por área. Antes de trabalhar, cada agente lê a seção dele. Um erro descoberto num projeto .NET ou numa migração de banco vira contexto para a próxima tarefa parecida, em qualquer repositório.

**Regras em camadas.** As regras gerais ficam no `CLAUDE.md` da pasta de projetos ([exemplo](exemplos/instrucoes-globais.md)): `git pull` ao começar, ler as pendências do projeto no cofre, commits pequenos, nunca commitar segredos. O `CLAUDE.md` de cada projeto sobrescreve o que for específico, como idioma dos commits e testes obrigatórios.

**Configuração versionada e portátil.** Os arquivos do Claude não entram nos commits dos projetos. A cópia deles fica no cofre (que é um repositório git), e um script faz o backup e a restauração, para a mesma equipe funcionar nas duas máquinas que eu uso.

## Em números

Em uso desde 21/10/2026. Nos primeiros 8 dias, o cofre recebeu 55 registros com tag de agente (decisões, aprendizados e trabalho do dia), em 6 projetos, e a base compartilhada já reúne 13 aprendizados.

## Como usar

1. Copie a pasta `agents/` para `~/.claude/agents/` (vale para todos os projetos) ou para `<pasta>/.claude/agents/` (vale para os projetos dentro dela).
2. Troque `<cofre>` pelo caminho do seu cofre Obsidian (ou de qualquer pasta de notas em Markdown versionada com git).
3. Crie `03 Recursos/Aprendizados de Desenvolvimento.md` no cofre a partir do [exemplo](exemplos/aprendizados-de-desenvolvimento.md).
4. Adapte as [instruções globais](exemplos/instrucoes-globais.md) para o seu `CLAUDE.md`.

No Claude Code, os agentes aparecem em `/agents`. O orquestrador os chama sozinho pela descrição de cada um, ou você pede direto ("use o revisor neste diff").

## Artigo

A explicação completa, com os problemas que motivaram cada decisão, está em [docs/artigo.md](docs/artigo.md).
