# Diretório de Contatos — Gabinete Nacional (DeMolay Brasil 2026/2027)

Aplicação web interativa para consulta de contatos oficiais do Gabinete Nacional do DeMolay Brasil — Gestão *"Ordem em Progresso"*.

---

## 🚀 Como Executar o Projeto

Como o sistema é uma aplicação **Single-File** sem dependências de compilação ou backend, basta abrir o arquivo [`index.html`](index.html) em qualquer navegador moderno ou rodar um servidor estático local:

```bash
# Com Python 3:
python3 -m http.server 8000

# Com Node.js (npx serve):
npx serve .
```

Acesse em `http://localhost:8000`.

---

## 🤖 Manutenção e Atualização com IA

Este projeto foi desenhado para ser atualizado diretamente via assistentes de inteligência artificial (Cursor, Windsurf, Claude Code, Copilot, Antigravity, ChatGPT).

Para entender a arquitetura do sistema, o schema do banco de dados e conferir **modelos de prompts prontos** para adicionar, remover e editar contatos, consulte o guia oficial:

👉 **[INSTRUCOES_IA.md](INSTRUCOES_IA.md)** 👈

---

## 📁 Estrutura de Arquivos

* [`index.html`](index.html): Aplicação completa (HTML, Tailwind CSS, animações GSAP, Three.js e banco de dados JSON embutido).
* [`INSTRUCOES_IA.md`](INSTRUCOES_IA.md): Manual técnico completo para agentes de IA e desenvolvedores.
* [`AGENTS.md`](AGENTS.md): Diretrizes rápidas auto-carregadas por agentes de IA.
* [`.cursorrules`](.cursorrules): Regras automáticas para o editor Cursor.
* [`RELATORIO.md`](RELATORIO.md): Histórico técnico de melhorias e novas capacidades desenvolvidas.

