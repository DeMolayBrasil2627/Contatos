# Relatório de Modificações e Melhorias do Projeto

Este relatório documenta as evoluções estruturais, funcionais e de interface implementadas no sistema do diretório de contatos do **Gabinete Nacional do DeMolay Brasil 2026/2027** (`contatos-GN`), com foco exclusivo nas soluções técnicas, novas capacidades e melhorias de usabilidade desenvolvidas.

---

## 1. Arquitetura de Dados e Estruturação

* **Estrutura JSON Embutida e Reativa:** Centralização do banco de dados institucional no elemento `<script id="gn-data" type="application/json">`, permitindo carregamento instantâneo (*zero network latency*) e independência de servidores externos.
* **Modelo Extensível de Entidades:** Enriquecimento do modelo de dados dos membros para suportar novos campos opcionais de metadados sem quebrar a compatibilidade de registros existentes:
  * `cid`: Identificador DeMolay único.
  * `coordena`: Jurisdição geográfica ou estados sob responsabilidade direta.
  * `atividades`: Lista dinâmica de projetos, comissões e rotinas atribuídas.
* **Higienização e Padronização de Contatos:** Tratamento automatizado de números telefônicos para links diretos da API do WhatsApp (`wa.me/55...`), garantindo interoperabilidade entre formatos nacionais e internacionais.

---

## 2. Sistema de Busca Dinâmica e Inteligente

O algoritmo de pesquisa (`applySearch`) foi ampliado para ir além da filtragem simples de nomes e cargos:

* **Busca Tokenizada Multi-Termos:** Suporte a pesquisas por múltiplos termos independentes (ex: `Saulo Storel`, `Pedro Campos`, `Arthur MG`), localizando pessoas mesmo quando possuem nomes ou sobrenomes intermediários.
* **Normalização Fonética e de Acentos (NFD):** Remoção de diacríticos para permitir buscas indiferentes à acentuação (`antonio`, `joao`, `regioes`, `capitulos`, `maes`).
* **Tratamento Inteligente de Plurais e Singulares:** Sistema heurístico de variações morfológicas em português (`campanhas`/`campanha`, `regioes`/`regiao`, `capitulos`/`capitulo`, `coordenadores`/`coordenador`).
* **Navegação com Tecla Enter:** Confirmação de busca via tecla `Enter` com recolhimento automático do menu suspenso de filtros e rolagem suave direta para os resultados.
* **Exibição Imediata em Resultados de Busca:** Durante a filtragem ativa, os cartões são animados e exibidos instantaneamente na tela, desacoplando do ScrollTrigger para garantir visibilidade imediata sem exigir rolagem manual.
* **Mapeamento Automático de Siglas Institucionais:** Suporte a buscas diretas por siglas institucionais, traduzindo termos abreviados para as seções correspondentes:
  * `cniec`, `cniep`, `cnier`, `cnieg` (Coordenações das CNIEs).
  * `crnc`, `crnp`, `crnr`, `crng` (Coordenações das CRNs).
  * `snoc`, `snoe` (Secretarias Nacionais de Cavalaria e Escudeiros).
* **Busca Numérica Normalizada (CIDs):** Extração de dígitos (`termDigits`) permitindo que buscas numéricas encontrem registros independentemente de pontuação (ex.: buscando `132194` localiza `CID 132.194`).
* **Filtro Multi-Atributo Integrado:** A pesquisa varre concorrentemente:
  1. Nome do membro.
  2. Cargo institucional.
  3. UF de origem.
  4. CID numérico.
  5. Estados sob coordenação (`coordena`).
  6. Atividades e projetos atribuídos (`atividades`).
* **Filtros Setoriais Dinâmicos:** Painel de categorias gerado em tempo de execução via agregação dos grupos declarados (`MCNs`, `Secretarias`, `Macrorregionais`, `Coordenação CNIEs`, `Coordenação CRNs`, `Coordenações de Campanhas`, `MCEs e MCEAs`), permitindo combinar filtros de setor com busca textual.

---

## 3. Interface do Usuário e Componentes Visuais

* **Badges Visuais de Identificação:**
  * **Badge MCE / MCD (Liderança Estadual Titular):** Selo sólido com fundo gradiente dourado (`bg-gradient-to-r from-brand-gold to-amber-300`), tipografia destacada em contraste escuro e brilho sutil.
  * **Badge MCEA / MCDA (Liderança Estadual Adjunta):** Selo refinado com borda e texto em dourado (`border-brand-gold/40 text-brand-gold bg-brand-gold/10`), garantindo harmonia visual e distinção imediata do titular.
  * **Badge de CID:** Destaque monoespaçado com moldura dourada e fundo sutil (`CID XX.XXX`) no topo do cartão de cada membro.
  * **Badge de Jurisdição ("Coordena:"):** Identificador destacado com os estados ou regiões sob coordenação direta de coordenadores regionais e lideranças estaduais.
  * **Badges de Atividades ("Responsável por:"):** Contêiner flexível que renderiza etiquetas compactas individuais para cada projeto ou tarefa sob responsabilidade do membro.
* **Tratamento de Contatos Pendentes:** Quando um membro ainda não possui telefone cadastrado, o card substitui o link do WhatsApp por um badge elegante de status **"Aguardando dados"** com ícone `ph-phone-slash`, evitando links quebrados ou cartões vazios.
* **Layout Adaptativo (*Grid System*):** Reestruturação do container principal em grid CSS fluido de 1 a 4 colunas (`grid-cols-1 sm:grid-cols-2 lg:grid-cols-3 xl:grid-cols-4`), otimizado para dispositivos móveis, tablets e monitores widescreen.
* **Estilização Glassmorphism:** Acabamento translúcido com Tailwind CSS (`glass-card`), bordas suaves, efeitos de profundidade e transições suaves de hover (`-translate-y-2`).

---

## 4. Animações e Ciclo de Renderização

* **Integração GSAP & ScrollTrigger:** As animações de rolagem e transição de seções são reinicializadas de forma limpa (`ScrollTrigger.getAll().forEach(t => t.kill())`) a cada nova filtragem ou pesquisa, prevenindo vazamentos de memória e bugs de layout.
* **Feedback de Resultados Vazios:** Mensagem contextual personalizada quando nenhum membro corresponde aos critérios combinados de busca e setor.

---

## 5. Resumo das Capacidades Adicionadas

| Funcionalidade | Descrição |
| :--- | :--- |
| **Busca Tokenizada** | Permite encontrar nomes compostos independentemente de sobrenomes intermediários. |
| **Normalização de Acentos** | Busca indiferente a acentos gráficos (`joao`, `antonio`, `regioes`). |
| **Plural/Singular Heurístico** | Identifica seções e termos tanto no plural quanto no singular (`campanhas`/`campanha`). |
| **Atalho Enter Inteligente** | Fecha painéis sobrepostos e desce suavemente até a visualização dos resultados. |
| **Busca por Siglas** | Localiza seções completas via acrônimos institucionais (`cniec`, `crnp`, etc.). |
| **Busca por Jurisdição** | Filtra coordenadores por estado ou região atribuída (`SP`, `SC, CE, TO, AM`, etc.). |
| **Busca por Projeto** | Encontra responsáveis ao pesquisar nomes de atividades e projetos. |
| **Normalização de CID** | Busca flexível por identificadores com ou sem formatação de pontos. |
| **Badges de Atividades** | Exibição visual granular de projetos e tarefas por oficial. |
| **Tratamento "Aguardando dados"** | Fallback visual para membros com cadastros em processo de preenchimento. |
| **Exibição Imediata de Busca** | Renderização sem bloqueio de opacidade por rolagem para resultados instantâneos. |

