# 08 — Resumo: Técnicas de Prompt Engineering (revisão rápida)

> **Folha de revisão do capítulo 002.** Leia de cima a baixo para relembrar todas as 7 técnicas: o que é, quando usar, como o prompt se monta e a armadilha de cada uma. Cada seção fecha com a "frase-âncora" que resume a ideia.

---

## 🗺️ Mapa mental — as 7 técnicas em uma linha

| # | Técnica | Ideia em uma frase | Eixo |
|---|---------|--------------------|------|
| 01 | **Zero-Shot** | Instrua sem exemplos; o modelo usa o treinamento. | exemplos |
| 02 | **Few-Shot** | Ensine pelo exemplo o que o modelo não conhece. | exemplos |
| 03 | **Chain of Thought (CoT)** | Force o raciocínio passo a passo antes da resposta. | raciocínio (linear) |
| 04 | **Tree of Thought (ToT)** | Explore vários caminhos em paralelo e debata até o consenso. | raciocínio (ramificado) |
| 05 | **Skeleton of Thought (SoT)** | Gere o esqueleto, valide, depois expanda. | estrutura |
| 06 | **Self-Consistency** | Gere análises independentes; convergência = confiança. | confiança |
| 07 | **Step-Back** | Abstraia os princípios do domínio antes de aplicar. | profundidade |

### Como escolher (árvore de decisão)

```text
Preciso de exemplos pra ensinar um padrão?
  ├─ padrão público  ─────────────────► ZERO-SHOT
  └─ padrão privado/seu ──────────────► FEW-SHOT

A tarefa exige raciocínio/cálculo/correlação?
  ├─ um caminho lógico ───────────────► CHAIN OF THOUGHT
  └─ decisão com trade-offs ("depende")► TREE OF THOUGHT

O resultado é um documento longo/estruturado? ─► SKELETON OF THOUGHT

Preciso MEDIR confiança numa conclusão crítica? ─► SELF-CONSISTENCY

O sintoma engana / quero diagnóstico fundamentado? ─► STEP-BACK
```

> As técnicas **se combinam**: Step-Back ou SoT como primeira camada, CoT dentro de cada análise, Self-Consistency ou ToT para decidir.

---

## 01 — Zero-Shot

- **O que é:** dar a instrução **sem nenhum exemplo**. "Zero" = zero exemplos no prompt, **não** zero conhecimento — o modelo usa o que aprendeu no treinamento.
- **Quando usar:** tarefa comum/pública, rapidez, protótipos, formato de saída padrão de mercado.
- **Quando evitar:** formato proprietário, estilo particular, consistência crítica, convenções internas.
- **Como fica o prompt:** instrução clara + papel (role) + formato de saída explícito.
  - Ex.: *"Classifique o alerta como critical/high/medium/low. Responda em JSON com severity, justification, recommended_action."*
- **4 boas práticas:** (1) seja detalhado; (2) use Role Prompting ("Você é um SRE sênior..."); (3) defina o formato de saída; (4) combine com outros frameworks.
- **Armadilha:** prompt vago → o modelo chuta. A clareza do input carrega o resultado.

> 🔑 **Âncora:** Zero-Shot **instrui** — o trabalho está em pensar antes de enviar.

---

## 02 — Few-Shot

- **O que é:** ensinar pelo **exemplo** (pares entrada→saída no prompt). Demonstra o padrão em vez de descrevê-lo.
- **Escala:** Zero-Shot (0 ex.) → One-Shot (1 ex., pega o formato barato) → Few-Shot (2+ ex., padrão específico).
- **Mecanismo — In-Context Learning (ICL):** o modelo identifica o padrão **na hora** e o reaplica.
  - ⚠️ **ICL ≠ Fine-Tuning:** os pesos NÃO mudam; vale só na conversa atual; nova sessão = começa do zero.
- **Quando usar:** o padrão é **seu/privado** — template interno, estilo de escrita, taxonomia/severidade não-padrão.
  - Regra: padrão de mercado → Zero-Shot basta. Padrão da empresa → Few-Shot obrigatório.
- **Boas práticas:** exemplos consistentes entre si; cubra casos de borda; rótulos balanceados (não enviese); 2–4 exemplos (mais satura e custa tokens); **demonstre, não descreva**.
- **Armadilha:** exemplos divergentes confundem o modelo sobre qual padrão seguir.

> 🔑 **Âncora:** Few-Shot transforma exemplos em "regras vivas" — que somem ao fim da sessão.

---

## 03 — Chain of Thought (CoT)

- **O que é:** forçar o modelo a **mostrar as etapas** do raciocínio antes da conclusão.
- **Pulo do gato:** não é o genérico "pense passo a passo" — é **dizer quais etapas** seguir (CoT estruturado).
- **Por que funciona:** decompõe a complexidade; reduz erros de salto lógico; cria **auditabilidade**; melhora cálculos/correlações (números, timestamps, causa raiz).
- **Quando usar:** raciocínio, cálculo, correlação — causa raiz, matemática, debugging, planejamento. **Evite em tarefas triviais** (vira ruído).
- **Como fica o prompt:** instrução + lista numerada de etapas explícitas.
  - Ex.: *"Antes de concluir, raciocine: 1) evento inicial; 2) correlacione timestamps; 3) calcule recursos; 4) explique por que falhou; 5) causa raiz."*
- **CoT encadeado:** o output de uma análise alimenta o próximo artefato (análise → post-mortem).
- **Boas práticas:** especifique e **numere** etapas; peça cálculo explícito; encadeie quando há dependência.

> 🔑 **Âncora:** CoT troca o "salto para a resposta" por um caminho visível e verificável.

---

## 04 — Tree of Thought (ToT)

- **O que é:** explorar **vários caminhos em paralelo**, comparar trade-offs e convergir para uma recomendação consensual. É o CoT ramificado — estrutura o "depende do contexto".
- **Padrão "3 experts":** simular especialistas com **perspectivas diferentes** (ex.: Dev × Cloud × SRE) que **debatem** antes de recomendar. Cada um vê um risco que os outros não veem.
- **Quando usar:** decisões com trade-offs e múltiplas dimensões, onde a resposta é "depende": arquitetura, estratégia de migração, dimensionamento, carreira.
- **Estrutura do resultado:** cada especialista apresenta sua leitura → debatem os conflitos → fecham em **CONSENSO** justificado.
- **Boas práticas:** dê perspectivas **distintas** (não clones); defina **critérios objetivos** (custo, viabilidade, risco); peça explicitamente o **debate + consenso**; force a justificativa do **descarte** (por que A e B perderam); ancore com números do contexto (budget, SLA, prazo).
- **Diferença de "liste prós e contras":** o que distingue ToT é o **debate** e o **consenso**.

> 🔑 **Âncora:** ToT é uma reunião de arquitetura simulada — torna auditável *por que* cada alternativa foi descartada.

---

## 05 — Skeleton of Thought (SoT)

- **O que é:** separar em **duas fases** — (1) gerar o **esqueleto** (só tópicos/seções); (2) **expandir** cada parte. Você valida a estrutura **antes** do conteúdo final.
- **Vantagem-chave:** **previsibilidade** — evita retrabalho num texto longo já pronto.
- **Duas modalidades:**
  1. **Modelo gera o esqueleto** → você pede para expandir.
  2. **Você fornece o esqueleto** → útil com template formal obrigatório (ex.: post-mortem padronizado), o modelo só preenche.
- **Quando usar:** documentos longos e estruturados — guias, relatórios, post-mortems, documentação, propostas, artigos.
- **Boas práticas:** **valide o esqueleto antes de expandir** (ponto de controle); use a 2ª modalidade com template corporativo; expanda por seção quando for grande; combine com **CoT** em seções analíticas (ex.: 5 Whys); ótimo para **padronizar entregáveis** entre times.

> 🔑 **Âncora:** primeiro a estrutura, depois o conteúdo — errar o esqueleto cedo é barato; tarde, caro.

---

## 06 — Self-Consistency

- **O que é:** gerar **várias análises independentes** do mesmo problema e comparar.
  - **Convergência** → confiança alta. **Divergência** → ponto de incerteza, investigar mais.
- **Por que funciona:** um raciocínio único pode estar errado e parecer convincente; caminhos independentes fazem erros aleatórios divergirem, enquanto a resposta correta se repete. A **convergência espontânea** é o sinal mais robusto.
- **⚠️ Regra de execução:** isole as análises — **conversas separadas** ou instrua "não considere as outras análises" (evita enviesamento).
- **Quando usar:** diagnósticos críticos, decisões de alto impacto, causa raiz, **war rooms** — quando precisa **medir confiança**, não só obter resposta.
- **Boas práticas:** isole as análises; use **perfis/perspectivas diferentes**; trate **divergência como sinal** (não ruído); reserve para decisões que justificam o custo extra de tokens; combine com **CoT** dentro de cada análise.
- **vs. ToT:** ToT os especialistas **debatem** e convergem; Self-Consistency eles ficam **isolados** e você compara o resultado. ToT decide; Self-Consistency mede confiança.

> 🔑 **Âncora:** buscar a 2ª e 3ª opinião — concordância independente é o sinal de que está certo.

---

## 07 — Step-Back

- **O que é:** **dar um passo atrás** — abstrair os **princípios fundamentais** do domínio antes de resolver o caso concreto.
  - Etapa 1: ABSTRAIR ("quais os princípios fundamentais de X?"). Etapa 2: APLICAR ao problema.
- **Por que funciona:** ativa o conhecimento certo antes; evita soluções de superfície (tratar o sintoma); permite **eliminar hipóteses por camada**.
- **Quando usar:** diagnósticos profundos, problemas onde **o sintoma engana**, decisões que se beneficiam de fundamentos (TCO, redes, memória, performance).
- **Exemplos:** K8s OOMKilled → abstrai requests/limits/OOM Killer → distingue under-provisioning vs. memory leak. VPC sem conexão (mas ping funciona) → abstrai camadas (SG, NACL, Route Table, DNS) → aponta para NACL/stateful vs. stateless.
- **Boas práticas:** **peça os princípios explicitamente**; **liste as dimensões** a cobrir; **force a ponte** ("depois, use esses princípios para...") senão abstrai e não aplica; combine com **CoT** na aplicação; ótimo como **primeira camada** antes de Self-Consistency ou ToT.

> 🔑 **Âncora:** entenda *como o domínio funciona* antes de diagnosticar — fundamento, não chute.

---

## 🔗 Como as técnicas se combinam (cola final)

```text
Step-Back  ─► ativa os fundamentos do domínio        (primeira camada)
   │
   ├─ SoT   ─► organiza o entregável em esqueleto     (estrutura)
   │
   ├─ CoT   ─► raciocínio passo a passo dentro de cada parte/análise
   │
   ├─ ToT          ─► quando é DECISÃO com trade-offs (debate → consenso)
   └─ Self-Consist.─► quando é CONFIANÇA num diagnóstico (isola → compara)

Zero-Shot é a base de tudo; Few-Shot entra quando o padrão é seu.
```

**Tabela de gatilho (decisão de 1 segundo):**

| Se a pergunta for... | Use... |
|----------------------|--------|
| "faça X" (tarefa comum) | Zero-Shot |
| "faça no MEU formato/estilo" | Few-Shot |
| "por quê? / qual a causa? / calcule" | CoT |
| "qual a melhor opção entre A, B, C?" | ToT |
| "escreva esse documento longo" | SoT |
| "tenho certeza disso? (crítico)" | Self-Consistency |
| "o sintoma pode estar me enganando" | Step-Back |
