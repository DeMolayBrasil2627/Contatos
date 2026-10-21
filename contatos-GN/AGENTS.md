# Diretrizes para Agentes de IA — Contatos GN

Este repositório contém a aplicação web do Diretório de Contatos Oficiais do Gabinete Nacional do DeMolay Brasil (2026/2027).

> 📌 **DOCUMENTO PRINCIPAL DE REFERÊNCIA:**
> Consulte sempre o arquivo [`INSTRUCOES_IA.md`](INSTRUCOES_IA.md) para a documentação técnica completa, schema de dados, regras inegociáveis e exemplos de prompts.

---

## ⚡ Regras Rápidas e Críticas para o Agente:

1. **Arquivo Único (`index.html`):** Todo o projeto reside em [`index.html`](index.html). Os dados de todos os contatos ficam centralizados no elemento `<script id="gn-data" type="application/json">`.
2. **Proibição de "Apelido":** NUNCA crie campos, filtros ou buscas por `apelido`. Isso foi terminantemente banido pelo cliente.
3. **Padrão de Badges MCE / MCEA:**
   - `MCE` e `MCD`: Fundo em gradiente dourado (`bg-gradient-to-r from-brand-gold to-amber-300 text-brand-dark font-bold uppercase`), sem ícones.
   - `MCEA` e `MCDA`: Texto e bordas em dourado (`text-brand-gold bg-brand-gold/10 border border-brand-gold/40 font-semibold uppercase`), sem ícones.
   - Nome das seções: `"Mestres Conselheiros Estaduais — (Macrorregião)"`.
4. **Formatação de Contatos:**
   - `nome`: Sempre em MAIÚSCULAS.
   - `telefone`: `"DD 9XXXX-XXXX"`.
   - `wa`: `"55DD9XXXXXXXX"` (somente dígitos com DDI 55).
   - Sem telefone? Deixe `telefone: ""` e `wa: ""` (exibirá "Aguardando dados").
   - `coordena`: Jurisdição atribuída (ex: `"Acre (AC)"`, `"SP"`, `"MG e SC"`).
5. **Verificação Obrigatória de Sintaxe:** Após qualquer edição em `index.html`, execute no terminal:
   ```bash
   node -e 'const fs = require("fs"); const html = fs.readFileSync("index.html", "utf8"); const m = html.match(/<script\s+id="gn-data"\s+type="application\/json">([\s\S]*?)<\/script>/); JSON.parse(m[1]); console.log("OK");'
   ```

