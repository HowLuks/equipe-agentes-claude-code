# Uma equipe de agentes que aprende com os próprios erros

*Como organizei o Claude Code em 8 subagentes com funções, permissões e uma memória compartilhada.*

Uso o Claude Code em todos os meus projetos: APIs em FastAPI e ASP.NET, apps em Next.js, automações de WhatsApp e um app desktop em Electron. Depois de algumas semanas, três problemas se repetiam.

1. **O mesmo erro em projetos diferentes.** Um detalhe do Postgres que me custou uma tarde num projeto aparecia de novo em outro, e o modelo não tinha como saber, porque cada sessão começa do zero.
2. **Quem escreve o código também revisa.** Numa sessão única, o agente que implementou é o mesmo que confere. Ele tende a confirmar o que acabou de fazer.
3. **A documentação ficava pela metade.** Eu queria registrar decisões e aprendizados no meu cofre do Obsidian, mas cada sessão escrevia num formato diferente, às vezes em dois lugares, às vezes em nenhum.

A solução foi tratar o Claude Code como uma pequena equipe, e não como um assistente único.

## A estrutura

A sessão principal virou **orquestrador**. Ela recebe o pedido, quebra em partes e chama subagentes especializados, cada um definido num arquivo Markdown em `.claude/agents/`:

- `arquiteto`: desenha a solução antes do código e não implementa;
- `backend`, `frontend` e `devops`: implementam;
- `qa`: reproduz o bug, escreve o teste e relata a saída real;
- `revisor` e `seguranca`: leem o diff e apontam problemas;
- `documentador`: registra tudo no cofre e mantém o README em dia.

Cada arquivo tem poucas linhas: uma descrição (que o orquestrador usa para decidir quem chamar), a lista de ferramentas e um parágrafo sobre como aquele papel trabalha. O que faz a diferença são três regras que valem para todos.

## Regra 1: quem audita não edita

O `revisor`, o `seguranca` e o `arquiteto` só têm ferramentas de leitura (`Read`, `Grep`, `Glob`, `Bash`). Eles não conseguem "dar uma ajeitada" no código enquanto revisam. O resultado deles é uma lista de achados com `arquivo:linha`, do mais grave ao menos grave, e quem decide o que corrigir é o orquestrador, que manda o trabalho de volta para quem implementa.

Parece uma restrição pequena, mas muda o comportamento. Um revisor que pode editar acaba virando um segundo implementador. Um revisor que só lê precisa explicar o problema, e a explicação fica registrada.

## Regra 2: só um agente escreve na memória

Todos os agentes terminam o trabalho devolvendo, além do resultado, as linhas novas de decisões e aprendizados num formato fixo:

```
- 2026-10-19: aprendizado — aplicável a qualquer backend Python #agente/backend
```

Só o `documentador` grava no cofre. Isso resolve as escritas concorrentes (dois agentes editando a mesma nota), mantém o formato e deixa um único ponto que conhece as regras do cofre: onde fica a nota de cada projeto, como é a nota diária, o que nunca pode ser apagado.

A tag no fim da linha (`#agente/backend`, `#agente/seguranca`…) dá rastreabilidade de graça: no Obsidian, buscar `#agente/seguranca` mostra tudo o que a auditoria já encontrou, em todos os projetos.

## Regra 3: ler os erros antigos antes de começar

Aprendizados marcados como "aplicável a outros projetos" também vão para uma nota compartilhada, separada pelas áreas da equipe (Arquitetura, Frontend, Backend, DevOps, Segurança, QA). A primeira instrução de cada agente é ler a seção dele nessa nota.

Um exemplo real. Uma API em FastAPI caiu em produção com um `NameError` que os testes locais não pegaram. A causa: eu testava com Python 3.14, em que as anotações de tipo são avaliadas depois, e a produção rodava 3.12. O aprendizado foi para a seção Backend:

> Testar sempre com a mesma versão do Python da produção — aplicável a qualquer backend Python.

Na próxima tarefa de backend, em qualquer repositório, o agente começa sabendo disso. É uma memória simples, em Markdown, mas que muda o que o agente faz, e não só o que ele registra.

## Regras em camadas

As regras gerais ficam num `CLAUDE.md` na raiz da pasta de projetos: dar `git pull` antes de mexer, ler as pendências do projeto no cofre e avisar o que está em aberto, fazer commits pequenos com Conventional Commits, nunca commitar segredos. Cada projeto pode ter o próprio `CLAUDE.md`, que manda sobre o que for específico. Num projeto de cliente estrangeiro, por exemplo, commits e documentação são em inglês, enquanto as notas no cofre continuam em português.

Esses arquivos não entram nos commits dos projetos. A cópia deles fica no próprio cofre, que é um repositório git, e um script faz o backup e a restauração. Assim a mesma equipe funciona nas duas máquinas que uso.

## O que mudou na prática

Nos primeiros 11 dias de uso, o cofre recebeu 65 registros com tag de agente, em 6 projetos, e a base compartilhada já reúne 14 aprendizados. Mais importante que o número: abrir um projeto depois de dias parado ficou barato, porque a primeira coisa que a sessão faz é ler as pendências e me dizer o que ficou em aberto.

## Limites

- **Custo.** Cada subagente começa sem o contexto da conversa e precisa reler arquivos. Para tarefas pequenas, chamar a equipe toda é desperdício; o orquestrador resolve sozinho.
- **A memória cresce.** Com dezenas de aprendizados por área, ler a seção inteira vai ficar caro. O próximo passo é buscar só os aprendizados relevantes para a tarefa (com embeddings, por exemplo) em vez de carregar tudo.
- **Falta medir.** Ainda não sei quantas vezes um aprendizado evitou um erro de verdade. Registrar quando um agente cita um aprendizado antigo seria uma forma de medir isso.

## Conclusão

Nenhuma parte disso é complicada: são oito arquivos Markdown e algumas regras. O ganho veio de três ideias de engenharia comuns aplicadas a agentes: separar quem faz de quem confere, ter um único responsável pela escrita no estado compartilhado e transformar erros passados em contexto para o próximo trabalho.

Os arquivos estão neste repositório: [github.com/HowLuks/equipe-agentes-claude-code](https://github.com/HowLuks/equipe-agentes-claude-code).
