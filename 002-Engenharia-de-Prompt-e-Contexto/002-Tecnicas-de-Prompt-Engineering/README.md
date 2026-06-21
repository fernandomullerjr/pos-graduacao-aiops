# Técnicas de Prompt Engineering

> **Módulo 02 — Engenharia de Prompt e Contexto**
> Pós-Graduação AIOps / DevOps PRO

Material de estudo das 7 técnicas fundamentais de _prompt engineering_, com foco em aplicação prática em cenários de **Cloud, DevOps e SRE**. Cada técnica traz a teoria, quando usar, exemplos não-técnicos e demos técnicas reais (migrações AWS, análise de incidentes, Kubernetes, etc.).

---

## Índice das aulas

| # | Técnica | Ideia central | Quando usar |
|---|---------|---------------|-------------|
| 01 | [Zero-Shot](./001-Zero-Shot.md) | Instruir sem exemplos | Tarefa comum, modelo já viu no treino |
| 02 | [Few-Shot](./002-Few-Shot.md) | Ensinar pelo exemplo (in-context learning) | Formato/padrão específico que o modelo não conhece |
| 03 | [Chain of Thought (CoT)](./003-Chain-of-Thought.md) | Raciocinar passo a passo | Problemas com cálculo, lógica ou causa raiz |
| 04 | [Tree of Thought (ToT)](./004-Tree-of-Thought.md) | Explorar múltiplos caminhos e debater | Decisões com trade-offs ("depende do contexto") |
| 05 | [Skeleton of Thought (SoT)](./005-Skeleton-of-Thought.md) | Esqueleto antes do conteúdo | Documentos longos e estruturados |
| 06 | [Self-Consistency](./006-Self-Consistency.md) | Múltiplas análises independentes | Validar confiança via convergência |
| 07 | [Step-Back](./007-Step-Back.md) | Abstrair princípios antes de aplicar | Diagnósticos profundos e fundamentados |

---

## Mapa mental: qual técnica escolher?

```text
A tarefa é comum e o modelo já conhece o padrão?
├── SIM ─────────────────────────► Zero-Shot
└── NÃO (formato/padrão próprio) ─► Few-Shot

O problema exige raciocínio?
├── Linear (cálculo, causa raiz) ─► Chain of Thought (CoT)
├── Decisão com trade-offs ───────► Tree of Thought (ToT)
└── Documento longo/estruturado ──► Skeleton of Thought (SoT)

Preciso de confiança / fundamento?
├── Validar via convergência ─────► Self-Consistency
└── Entender os fundamentos ──────► Step-Back
```

> As técnicas **não são exclusivas** — combiná-las é comum (ex.: Step-Back + CoT, ou Few-Shot + CoT).

---

## Convenções deste material

- Blocos marcados como **`PROMPT`** podem ser copiados e colados direto no modelo.
- Blocos marcados como **`LOGS`** são dados fictícios para reproduzir as demos.
- Cada aula segue a estrutura: **O que é → Por que funciona → Quando usar → Demos → Boas práticas**.
