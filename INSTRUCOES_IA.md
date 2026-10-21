# Instruções para IA — Diretório de Contatos do Gabinete Nacional (contatos-GN)

Este documento foi criado para orientar qualquer **Assistente de Inteligência Artificial (IA)** ou **desenvolvedor** responsável pela manutenção, atualização e evolução do sistema **Contatos GN** (Gabinete Nacional do DeMolay Brasil — Gestão 2026/2027 *"Ordem em Progresso"*).

Leia este arquivo integralmente antes de realizar qualquer alteração no código.

---

## 1. Visão Geral da Arquitetura

O projeto é uma aplicação web **Single-File** contida inteiramente no arquivo [`index.html`](index.html). Não há backend, banco de dados externo ou processo de build (Node, Webpack, Vite, etc.).

### Stack Tecnológica:
* **Tailwind CSS (via CDN):** Estilização utilitária com tema customizado estendido (`brand.dark: #050f26`, `brand.gold: #c9a13b`, `brand.gold-bright: #e6c66b`, `brand.red: #dc2626`, `brand.navy-card: rgba(18, 42, 99, 0.4)`).
* **Phosphor Icons (via CDN):** Biblioteca de ícones (`ph`, `ph-bold`, `ph-fill`).
* **Tipografia:** *Cormorant Garamond* (títulos/serif), *Inter* (corpo/sans), *IBM Plex Mono* (cargos, CIDs, metadados).
* **Three.js (via CDN):** Fundo animado de partículas estelares interativo ao mouse.
* **GSAP 3 + ScrollTrigger & Anime.js (via CDN):** Animações de entrada, scroll fluido e efeitos hover nos botões do WhatsApp.

---

## 2. Onde Ficam os Dados dos Contatos?

Todos os contatos e seções do sistema estão centralizados em formato JSON no elemento:
```html
<script id="gn-data" type="application/json">
[
  [
    {
      "sheet": "...",
      "label": "...",
      "group": "...",
      "entries": [ ... ]
    },
    ...
  ]
]
</script>
```

> **Atenção:** O script `gn-data` armazena um array contendo um array de seções (`[[ { section1 }, { section2 } ]]`). O JavaScript da aplicação desaninha automaticamente (`parsed[0]`), mantendo compatibilidade total.

---

## 3. Schema de Dados

### 3.1. Estrutura de uma Seção (`Section`)
```json
{
  "sheet": "Nome Interno da Planilha / Identificador",
  "label": "Título Visível da Seção",
  "group": "Categoria para o Painel de Filtros",
  "entries": [ /* Array de pessoas */ ]
}
```

#### Grupos Atuais (`group`):
1. `"MCNs"` (Liderança Nacional Executiva)
2. `"Secretarias"` (Secretarias Nacionais)
3. `"Macrorregionais"` (Oficiais de Ligação Macrorregional)
4. `"Coordenação CNIEs"` (Comissões Nacionais de Incentivo à Excelência)
5. `"Coordenação CRNs"` (Comissões Regionais Nacionais)
6. `"Coordenações de Campanhas"` (Campanhas e Prêmios Nacionais)
7. `"MCEs e MCEAs"` (Lideranças Estaduais — Mestres Conselheiros Estaduais e Adjuntos)

---

### 3.2. Estrutura de um Contato (`Person`)
```json
{
  "nome": "NOME COMPLETO EM MAIÚSCULAS",
  "cargo": "CARGO PADRONIZADO",
  "uf": "UF",
  "telefone": "DD 9XXXX-XXXX",
  "wa": "55DD9XXXXXXXX",
  "cid": "XXX.XXX",
  "coordena": "Estado ou Regiões sob sua coordenação",
  "atividades": [
    "Projeto ou Responsabilidade 1",
    "Projeto ou Responsabilidade 2"
  ]
}
```

#### Campos Detalhados:
| Campo | Tipo | Obrigatório? | Descrição e Formato |
| :--- | :--- | :---: | :--- |
| `nome` | `string` | **Sim** | Nome completo em caixa alta (ex: `"PEDRO HENRIQUE BEZERRA LIMA"`). |
| `cargo` | `string` | **Sim** | Cargo institucional (ex: `"MCN"`, `"SECRETÁRIO"`, `"COORDENADOR"`, `"COORD.-GERAL"`, `"ASSESSOR"`, `"MCE"`, `"MCEA"`, `"MCD"`, `"MCDA"`). |
| `uf` | `string` | **Sim** | Sigla do estado (ex: `"SP"`, `"DF"`, `"MG"`) ou indicação regional (ex: `"NORTE/RR"`). |
| `telefone` | `string` | **Sim** | Número formatado para exibição visual: `"DD 9XXXX-XXXX"`. Se não houver, use `""`. |
| `wa` | `string` | **Sim** | Apenas dígitos com DDI Brasil (55): `"55DD9XXXXXXXX"`. Se não houver, use `""`. |
| `cid` | `string` | Opcional | Identificador DeMolay com pontos (ex: `"111.210"`, `"80.748"`). Omita se desconhecido. |
| `coordena` | `string` | Opcional | Jurisdição atribuída (ex: `"Acre (AC)"`, `"SP"`, `"MG e SC"`, `"Sudeste/Nordeste"`). |
| `atividades` | `array` | Opcional | Lista de responsabilidades ou projetos sob coordenação do oficial. |

---

## 4. Regras Críticas e Restrições Inegociáveis (Diretrizes para a IA)

Ao atender pedidos dos programadores, a IA **DEVE** seguir rigorosamente as regras abaixo:

### 🚫 REGRA 1: PROIBIÇÃO ABSOLUTA DE "APELIDO"
* **NUNCA** adicione o campo `"apelido"` no JSON.
* **NUNCA** crie categorias, seções ou filtros baseados em apelido.
* **NUNCA** altere a função de busca para pesquisar apelidos. O usuário removeu e baniu essa funcionalidade expressamente.

### 🎖️ REGRA 2: ESTILIZAÇÃO E BADGES DE MCE E MCEA
* **MCE / MCD (Titular):**
  * Cargo: `"MCE"` (ou `"MCD"` no caso do Distrito Federal).
  * Estilo do badge: Fundo sólido com gradiente dourado (`bg-gradient-to-r from-brand-gold to-amber-300 text-brand-dark font-bold uppercase`), sem ícones de coroa ou escudo.
* **MCEA / MCDA (Adjunto):**
  * Cargo: `"MCEA"` (ou `"MCDA"` no caso do Distrito Federal).
  * Estilo do badge: Borda dourada, texto dourado e fundo translúcido (`text-brand-gold bg-brand-gold/10 border border-brand-gold/40 font-semibold uppercase`), sem ícones.
* **Nome das Seções de MCEs:**
  * O `label` deve seguir o padrão: `"Mestres Conselheiros Estaduais — (Macrorregião)"` (ex: `Mestres Conselheiros Estaduais — Norte`).

### 📱 REGRA 3: FORMATAÇÃO DE TELEFONE E WHATSAPP
* Telefone visual: `"DD 9XXXX-XXXX"` (ex: `"82 99615-0147"`).
* Link do WhatsApp: `"55" + DDD + número` (ex: `"5582996150147"`).
* Se o membro ainda não informou telefone:
  * Deixe `"telefone": ""` e `"wa": ""`.
  * O sistema renderiza automaticamente o badge elegante **"Aguardando dados"** com ícone `ph-phone-slash`.

### 🔍 REGRA 4: FUNCIONAMENTO DA BUSCA DINÂMICA
A função `applySearch()` no JavaScript já está programada para:
* Normalizar acentuação (NFD: `joao` acha `João`, `regioes` acha `Regiões`).
* Tratar variações de plural e singular (`campanhas`/`campanha`, `coordenadores`/`coordenador`).
* Suportar siglas institucionais (`cniec`, `cniep`, `cnier`, `cnieg`, `crnc`, `crnp`, `crnr`, `crng`, `snoc`, `snoe`).
* Buscar CIDs com ou sem pontuação (`129065` localiza `CID 129.065`).
* Buscar concorrentemente por: Nome, Cargo, UF, Jurisdição (`coordena`), CID e Projetos (`atividades`).
* **Não quebre essa função.** Ela roda a cada digitação e no clique do `Enter`.

### 🛡️ REGRA 5: VALIDAÇÃO APÓS CADA EDIÇÃO
Sempre que editar o arquivo `index.html`, execute um teste no terminal para garantir que o JSON não contém erros de sintaxe (como vírgulas sobrando ou faltando):
```bash
node -e '
const fs = require("fs");
const html = fs.readFileSync("index.html", "utf8");
const match = html.match(/<script\s+id="gn-data"\s+type="application\/json">([\s\S]*?)<\/script>/);
JSON.parse(match[1]);
console.log("JSON 100% válido!");
'
```

---

## 5. Guia de Prompts Prontos para os Programadores

Quando outros programadores precisarem atualizar contatos, eles podem simplesmente copiar um dos modelos abaixo e enviar para a IA:

### ➕ Prompt 1: Adicionar Novo Contato em uma Seção Existente
```markdown
Adicione o seguinte contato na seção "[NOME DA SEÇÃO OU SECRETARIA]":
- Nome: [NOME COMPLETO]
- Cargo: [CARGO]
- UF: [UF]
- Telefone: [TELEFONE COM DDD]
- CID: [NUMERO DO CID SE HOUVER]
- Coordena: [ESTADO OU REGIÃO SE HOUVER]
```

### ➖ Prompt 2: Remover Membro Exonerado / Desligado
```markdown
Remova o seguinte membro da lista de contatos do sistema:
- Nome: [NOME DO MEMBRO]
```

### ✏️ Prompt 3: Atualizar Telefone, Cargo ou CID de um Membro
```markdown
Atualize as informações do membro [NOME DO MEMBRO]:
- Novo telefone: [NOVO TELEFONE]
- Novo cargo: [NOVO CARGO SE APLICÁVEL]
- CID: [NOVO CID SE APLICÁVEL]
```

### 🗺️ Prompt 4: Atualizar Jurisdição / Estados sob Coordenação
```markdown
Atualize as responsabilidades de coordenação na seção "[NOME DA SEÇÃO]":
- [Nome do Oficial 1]: [Estados que ele coordena, ex: PR, SC, RS]
- [Nome do Oficial 2]: [Estados que ele coordena, ex: SP, RJ, MG]
```

### 👑 Prompt 5: Adicionar Lideranças Estaduais (MCE e MCEA)
```markdown
Adicione os novos MCE e MCEA do estado [ESTADO (UF)] na seção "Mestres Conselheiros Estaduais — [MACRORREGIÃO]":
- MCE: [NOME DO MCE] / ID: [CID] / Tel: [TELEFONE]
- MCEA: [NOME DO MCEA] / ID: [CID] / Tel: [TELEFONE]
```

### 📁 Prompt 6: Criar uma Nova Secretaria, Campanha ou Comissão
```markdown
Crie uma nova seção no diretório com as seguintes informações:
- Título da seção (label): [NOME DA SEÇÃO]
- Grupo de filtro (group): ["Secretarias", "Coordenações de Campanhas", etc.]
- Membros:
  1. [Nome] / [Cargo] / [UF] / [Tel] / [CID se houver]
  2. [Nome] / [Cargo] / [UF] / [Tel] / [CID se houver]
```

---

## 6. Mapeamento Atual das Seções e Grupos

Para referência rápida ao editar o JSON em `index.html`:

| Grupo (`group`) | Rótulo da Seção (`label`) |
| :--- | :--- |
| **MCNs** | Mestres Conselheiros Nacionais |
| **Secretarias** | Secretaria-Geral |
| **Secretarias** | Secretaria de Projetos |
| **Secretarias** | Secretaria de Comunicação |
| **Secretarias** | Secretaria de Organizações Afiliadas |
| **Secretarias** | Secretaria de Ações Sociais |
| **Secretarias** | Secretaria Nacional de Ritualística |
| **Secretarias** | Secretaria Nacional da Ordem dos Escudeiros |
| **Secretarias** | Secretaria Nacional da Ordem da Cavalaria |
| **Macrorregionais** | Macrorregionais |
| **Coordenação CNIEs** | CNIE — Capítulos |
| **Coordenação CNIEs** | CNIE — Castelos |
| **Coordenação CNIEs** | CNIE — Priorados |
| **Coordenação CNIEs** | CNIE — Regiões |
| **Coordenação CNIEs** | CNIE — Gabinetes |
| **Coordenação CRNs** | CRN — Capítulos |
| **Coordenação CRNs** | CRN — Castelos |
| **Coordenação CRNs** | CRN — Priorados |
| **Coordenação CRNs** | CRN — Regiões |
| **Coordenação CRNs** | CRN — Gabinetes |
| **Coordenações de Campanhas** | Coordenação da Caneta de Ouro |
| **Coordenações de Campanhas** | Prêmio Protocolista de Ouro |
| **Coordenações de Campanhas** | Desafio Nacional de Filantropia |
| **Coordenações de Campanhas** | Campanha — Clube de Mães de Honra |
| **MCEs e MCEAs** | Mestres Conselheiros Estaduais — Norte |
| **MCEs e MCEAs** | Mestres Conselheiros Estaduais — Nordeste |
| **MCEs e MCEAs** | Mestres Conselheiros Estaduais — Centro-Oeste |
| **MCEs e MCEAs** | Mestres Conselheiros Estaduais — Sudeste |
| **MCEs e MCEAs** | Mestres Conselheiros Estaduais — Sul |

---

## 7. Checklist para a IA Antes de Entregar a Resposta ao Usuário

Antes de concluir qualquer tarefa de manutenção solicitada pelo desenvolvedor, a IA deve verificar:
- [ ] O JSON dentro de `<script id="gn-data">` é válido? (Execute `node -e ...`)
- [ ] O nome do membro está em CAIXA ALTA?
- [ ] O número do WhatsApp contém apenas dígitos e o código do país (`55`)?
- [ ] O campo `coordena` inclui o estado e a sigla quando aplicável (ex: `"Acre (AC)"`)?
- [ ] Não foi introduzido nenhum campo ou filtro com o nome `apelido`?
- [ ] Os badges de MCE e MCEA mantiveram o estilo correto sem ícones de coroa ou escudo?

