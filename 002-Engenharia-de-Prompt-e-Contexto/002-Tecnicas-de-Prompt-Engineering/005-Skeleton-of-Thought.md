# 05 — Skeleton of Thought (SoT): esqueleto antes do conteúdo

> **SoT = gerar o esqueleto antes de expandir o conteúdo.** Primeiro o modelo lista a estrutura (tópicos/seções); você valida; só então ele preenche cada parte. Previsível e controlável.

---

## 1. O que é Skeleton of Thought?

Em vez de gerar um texto longo de uma vez, o SoT separa em **duas fases**:

```text
Fase 1 ─► ESQUELETO   (só os tópicos/seções, sem detalhar)
            │  ◄── você valida a estrutura aqui
            ▼
Fase 2 ─► EXPANSÃO    (preenche cada tópico com conteúdo detalhado)
```

A grande vantagem é a **previsibilidade**: você valida a estrutura **antes** de receber o conteúdo final, evitando retrabalho num texto longo já pronto.

## 2. Duas modalidades

1. **Modelo gera o esqueleto:** você pede a estrutura e depois manda expandir.
2. **Você fornece o esqueleto:** útil quando a empresa já tem um **template formal** (ex.: post-mortem padronizado) — o modelo só preenche.

## 3. Quando usar

Documentos longos e estruturados: guias, relatórios, post-mortems, documentação técnica, propostas, artigos. Qualquer caso em que **a estrutura importa** e errar o esqueleto custaria caro depois.

---

## 4. Demo não-técnica — guia de mudança de carreira

O modelo primeiro lista o esqueleto do guia e só depois expande cada tópico.

```text
PROMPT
------
Vou criar um guia completo para quem quer mudar de carreira e entrar na
área de TI como DevOps/Cloud Engineer.

Primeiro, liste o esqueleto do guia — os principais tópicos que serão
cobertos, sem detalhar ainda.

Depois, expanda cada tópico com conteúdo detalhado, exemplos práticos e
recursos.

Retorne em markdown.
```

---

## 5. Demo técnica — análise estruturada de log de incidente

### Prompt 2 — modelo gera o esqueleto

O modelo cria o esqueleto da análise (Resumo Executivo, Timeline, Causa Raiz, Impacto, Evidências, Ações) **antes** de preencher com os dados dos logs.

```text
PROMPT
------
Analise os logs de incidente abaixo e produza uma análise estruturada do
ocorrido.

Primeiro, crie o esqueleto da análise — as seções que serão cobertas
(sem detalhar ainda).

Depois, expanda cada seção com base nos logs.

Logs:
[ver bloco LOGS abaixo]
```

### Prompt 3 — esqueleto fornecido pelo usuário (2ª modalidade)

Você fornece a estrutura e o modelo só preenche. Útil quando há um template formal a seguir.

```text
PROMPT
------
Com base na análise de incidente gerada acima, crie um documento formal
de post-mortem seguindo este esqueleto:

1. Título e Metadados (data, severidade, responsáveis)
2. Resumo Executivo (máximo 3 parágrafos)
3. Timeline Detalhada
4. Análise de Causa Raiz (5 Whys)
5. Impacto ao Negócio
6. Ações Imediatas Tomadas
7. Ações Preventivas
8. Lições Aprendidas

Expanda cada seção sem implementar ainda.

Depois, expanda os tópicos e sub tópicos gerando o documento para
compartilhar com a equipe de engenharia.
```

---

## 6. LOGS do incidente (database connection timeout)

Bloco fictício usado no Prompt 2. Reaproveite o output dele como contexto do Prompt 3.

```log
[2024-03-15 02:14:33] ERROR app-server-01: Database connection timeout after 30s (attempt 1/3)
[2024-03-15 02:14:35] ERROR app-server-02: Database connection timeout after 30s (attempt 1/3)
[2024-03-15 02:14:38] ERROR app-server-01: Database connection timeout after 30s (attempt 2/3)
[2024-03-15 02:14:40] WARN load-balancer: Upstream app-server-01 health check failed
[2024-03-15 02:14:41] ERROR app-server-02: Database connection timeout after 30s (attempt 2/3)
[2024-03-15 02:14:43] CRITICAL app-server-01: All retry attempts exhausted. Service degraded
[2024-03-15 02:14:45] CRITICAL app-server-02: All retry attempts exhausted. Service degraded
[2024-03-15 02:14:46] WARN load-balancer: Upstream app-server-02 health check failed
[2024-03-15 02:14:50] ERROR db-primary: Max connections reached (current: 500/500)
[2024-03-15 02:14:51] CRITICAL load-balancer: All upstreams unhealthy. Returning 503 to clients
[2024-03-15 02:15:10] INFO on-call-alert: PagerDuty alert triggered — P1 incident opened
[2024-03-15 02:31:22] INFO db-primary: Connections dropped to 45/500 after connection pool restart
[2024-03-15 02:31:25] INFO app-server-01: Database connection established
[2024-03-15 02:31:26] INFO app-server-02: Database connection established
[2024-03-15 02:31:28] INFO load-balancer: All upstreams healthy
```

---

## 7. Boas práticas

- **Valide o esqueleto antes de expandir** — é o ponto de controle que economiza retrabalho.
- **Use a 2ª modalidade** (esqueleto fornecido) quando houver template corporativo obrigatório.
- **Peça expansão por seção** quando o documento for muito grande, para manter foco e qualidade.
- Combine com **CoT** dentro de seções analíticas (ex.: causa raiz com 5 Whys).
- Ótimo para **padronizar entregáveis** entre times.
