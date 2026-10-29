# Instruções globais (exemplo)

Versão resumida do `CLAUDE.md` que fica na raiz da minha pasta de projetos (`~/Projects/CLAUDE.md`). É ele que transforma a sessão principal do Claude Code em **orquestrador**: as regras abaixo valem para todos os projetos, e cada projeto pode ter o próprio `CLAUDE.md` com regras mais específicas.

Para usar, copie para `~/Projects/CLAUDE.md` e ajuste os caminhos.

---

## Ao começar a mexer em um projeto
1. `git pull` dentro da pasta do projeto. Se der conflito, pare e avise; não resolva sozinho.
2. Abra a nota do projeto no cofre (`<cofre>/01 Projetos/<Projeto>.md`) e leia **Próximos passos**. Avise o que estiver em aberto antes de começar o trabalho novo.

## Toda alteração vira commit e push
- O `CLAUDE.md` de cada projeto manda. Quando ele não disser nada, use Conventional Commits em português: `tipo(escopo): descricao curta`, em minúsculas, sem acento, verbo na 3ª pessoa do presente (`feat(api): implementa rotas de agendamento`).
- Commits pequenos, uma alteração lógica por commit.
- Adicione arquivos pelo nome (`git add <arquivo>`), nunca `git add .`, e confira o `git status` antes.
- Nunca commite `.env`, credenciais ou tokens.

## Documentar tudo no cofre
Todo trabalho fica registrado no cofre Obsidian, seguindo o `CLAUDE.md` do próprio cofre:
- O dia vai na nota diária, linkando o projeto.
- **Decisões** (`- AAAA-MM-DD: decisão — motivo`) e **Aprendizados** (`- AAAA-MM-DD: aprendizado — aplicável a X`) vão na nota do projeto.
- Só adicione conteúdo; nunca apague ou reescreva o que já existe sem confirmação.

## Quem faz o quê
A sessão principal é o orquestrador: entende o pedido, quebra em partes e chama os subagentes de `~/Projects/.claude/agents/`.
- Planejamento de algo grande: `arquiteto` antes de qualquer código.
- Implementação: `backend`, `frontend`, `devops`.
- Conferência: `qa` (roda e escreve testes), `revisor` (lê o diff), `seguranca` (audita). Esses três não editam código (o `qa` só mexe em testes).
- Registro: só o `documentador` escreve no cofre. Os outros devolvem as linhas de Decisões e Aprendizados ao orquestrador, que repassa a ele.

## Backup da configuração
Os arquivos do Claude (`CLAUDE.md`, `.claude/agents/`) não entram nos commits dos projetos. A cópia fica no cofre, que é um repositório git, e um script (`sync.sh`) faz o backup e restaura em outra máquina.
