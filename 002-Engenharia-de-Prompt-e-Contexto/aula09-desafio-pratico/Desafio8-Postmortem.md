# Um dia na Hill Valley Tech

## Cenário

A **Hill Valley Tech** é uma empresa fictícia que serve de palco para este desafio. Tem cinco sistemas em produção, cada um com seu papel bem definido:

| Sistema | Papel |
| --- | --- |
| **Chronos** | API gateway e plataforma core, ponto de entrada de todo tráfego da empresa. |
| **Ledger** | Data warehouse em PostgreSQL que guarda histórico de transações e eventos. |
| **Reactor** | Processamento assíncrono por filas de mensagens. |
| **Beacon** | Observabilidade do ambiente inteiro — métricas, logs e alertas; é por ele que o plantão enxerga o que está acontecendo. |
| **Lift** | Produto em beta que o time vem amadurecendo à parte, fora do core principal. |

## Personagens

O time que toca essa operação também é enxuto:

| Personagem | Papel |
| --- | --- |
| **Doc Brown** | CTO, responde pela direção técnica. |
| **Jennifer Parker** | PM que prioriza o que o produto entrega. |
| **Lorraine Baines** | Lidera a SRE e responde pelo plantão; é dela a cobrança por runbooks e procedimentos documentados. |
| **George McFly** | Engenheiro sênior veterano que escreveu boa parte do sistema legado; muita coisa ainda roda exatamente como ele deixou anos atrás. |
| **Goldie Wilson** | CEO, observa tudo pelo prisma de custo e crescimento. |
| **Strickland** | Head de segurança e compliance; quem bate carimbo nos padrões internos que todo código novo precisa seguir. |

## Como funciona o desafio

Nos próximos cenários você vai pegar algumas dessas demandas que chegam à mesa do time. Em cada questão, a entrega é:

- um **prompt de IA** aplicando o framework indicado no enunciado;
- executado em um **modelo**;
- com o **output** registrado;
- e a **justificativa** mostrando como os componentes do framework apareceram no prompt.

> ⚠️ A **Questão 08** foge desse padrão: a escolha do framework fica por sua conta entre os cinco do capítulo, com comparação explícita contra duas alternativas.


---

## Como a entrega deve ser feita

A entrega é um **repositório público no GitHub** contendo os prompts, outputs e justificativas das 8 questões. O link do repositório deve ser enviado ao final.

> 💡 A organização interna do repositório é **livre e intencional**. No Capítulo 4 deste módulo será abordado *Criação e Versionamento de Prompts*, e a estrutura escolhida aqui será comparada com as práticas apresentadas lá. **Guardar a decisão** — ela é parte do aprendizado.

### Campos obrigatórios por questão

| Campo | O que deve conter |
| --- | --- |
| **Prompt** | O texto exato usado. |
| **Modelo** | Qual modelo foi executado (ex.: GPT-4o, Claude Sonnet 4, Gemini 2.5 Pro, Llama 3 via Ollama) e, em 1 linha, por que esse modelo foi escolhido para a tarefa. |
| **Output** | A resposta real do modelo, na íntegra ou em trecho relevante. |
| **Justificativa** | Em 2 a 4 linhas, mostrar como os componentes do framework indicado no enunciado aparecem no prompt. Na **Q08** a justificativa precisa **comparar o framework escolhido com 2 alternativas**. |

### Orientações práticas

- Usar **ao menos 2 providers distintos** ao longo do desafio (OpenAI, Anthropic, Google, Meta ou local via Ollama).
- **Registrar outputs ruins também**. Se um resultado não ficou bom, comentar na justificativa o que faria diferente.
- Os dados dos cenários são fictícios — sem necessidade de sanitização.

> ℹ️ Sete das oito questões já trazem o framework definido no enunciado: ali o valor está em **aplicar bem os componentes no prompt** e explicar como cada um aparece. Na **Q08** a escolha é sua entre os cinco do capítulo, e a justificativa compara com 2 alternativas. **Registrar o raciocínio, inclusive o que não funcionou, faz parte do valor da entrega.**


---

## Questão 08 — Postmortem técnico de incidente em produção

Um incidente está em andamento durante pico de tráfego. Doc Brown entrou na call e precisa de um **postmortem técnico em 20 minutos** para decidir entre:

- **rollback** do deploy `v2.48.0` (que subiu ontem);
- ou **scaling emergencial** (aumento de limits do RDS e do pool de conexões).

Os artefatos disponíveis para análise são os seguintes.

### Evento do deploy anterior (ontem, 18:42 UTC)

```text
Deploy chronos-api: v2.47.0 -> v2.48.0
Argo CD sync: 2026-04-23 18:42:11 UTC
Changelog:
- Adicionado endpoint POST /v2/transactions/batch
- Refatorado cliente do Ledger (pool de conexoes movido para nova biblioteca interna)
- Bump de psycopg 3.1.18 -> 3.2.0
- Reduzido timeout do Ledger de 5s para 2s
```

### Métricas do Beacon nos últimos 30 minutos

| timestamp | p99_latency_ms | req_rate_s | err_rate_pct |
| --- | ---: | ---: | ---: |
| 2026-04-24 13:30 UTC | 420  | 1200 | 0.2  |
| 2026-04-24 13:45 UTC | 510  | 1450 | 0.3  |
| 2026-04-24 14:00 UTC | 780  | 1780 | 0.8  |
| 2026-04-24 14:10 UTC | 2400 | 2100 | 4.5  |
| 2026-04-24 14:15 UTC | 5200 | 2400 | 8.2  |
| 2026-04-24 14:20 UTC | 8100 | 2650 | 11.7 |

### Trecho do log do pod `chronos-api-79c4d8b9-xk2jp`

```log
2026-04-24 14:19:48 [ERROR] [ledger-client] connection pool exhausted (max=20, active=20, waiting=147)
2026-04-24 14:19:49 [WARN]  [ledger-client] query timeout after 2000ms: SELECT ... FROM transactions WHERE ...
2026-04-24 14:19:49 [ERROR] [handler] POST /v2/transactions/batch failed: context deadline exceeded
2026-04-24 14:19:50 [ERROR] [ledger-client] connection reset by peer
2026-04-24 14:19:51 [WARN]  [circuit-breaker] ledger-client OPEN (threshold 50%, current 87%)
2026-04-24 14:19:52 [ERROR] [reactor] failed to publish message: chronos-api upstream error
```

### Estado do Reactor (fila `chronos-transactions`)

- **50.127 mensagens** acumuladas, crescendo a **~800/min**.
- **Consumer lag** atual: **18 minutos** e aumentando.

### Estado do cluster

- **Chronos**: 12/12 pods running (HPA no máximo).
- **CPU médio** dos pods: 62%.
- **Memória média** dos pods: 71%.
- **Conexões ativas** ao Ledger: 240/250 (limite do RDS).

> **Tarefa.** Escolher entre os cinco frameworks do capítulo (**R-T-F**, **T-A-G**, **B-A-B**, **C-A-R-E** ou **R-I-S-E**) aquele que se aplica melhor a esse cenário e escrever o prompt de IA que produza o postmortem técnico que o Doc pediu.
>
> Nesta questão a **justificativa é o coração da entrega**. Além de explicar o framework escolhido e como seus componentes aparecem no prompt, **comparar explicitamente com pelo menos 2 outros frameworks candidatos**, apontando o que se ganharia e o que se perderia em cada um.
>
> **Entregue.** Prompt, modelo, output e justificativa estendida com comparação entre frameworks.

---


# RESPOSTA

---

## 💻 Campo 1: Prompt
[ROLE]
Você é um engenheiro SRE com grande experiencia em ambientes cloud native e resposta a incidentes.
Você tem a missão de criar um postmortem perfeito para a recuperação do ambiente de produção.

[INPUT]
Um incidente está em andamento durante pico de tráfego. 
Os artefatos disponíveis para análise são os seguintes.

### Evento do deploy anterior (ontem, 18:42 UTC)

```text
Deploy chronos-api: v2.47.0 -> v2.48.0
Argo CD sync: 2026-04-23 18:42:11 UTC
Changelog:
- Adicionado endpoint POST /v2/transactions/batch
- Refatorado cliente do Ledger (pool de conexoes movido para nova biblioteca interna)
- Bump de psycopg 3.1.18 -> 3.2.0
- Reduzido timeout do Ledger de 5s para 2s
```

### Métricas do Beacon nos últimos 30 minutos

| timestamp | p99_latency_ms | req_rate_s | err_rate_pct |
| --- | ---: | ---: | ---: |
| 2026-04-24 13:30 UTC | 420  | 1200 | 0.2  |
| 2026-04-24 13:45 UTC | 510  | 1450 | 0.3  |
| 2026-04-24 14:00 UTC | 780  | 1780 | 0.8  |
| 2026-04-24 14:10 UTC | 2400 | 2100 | 4.5  |
| 2026-04-24 14:15 UTC | 5200 | 2400 | 8.2  |
| 2026-04-24 14:20 UTC | 8100 | 2650 | 11.7 |

### Trecho do log do pod `chronos-api-79c4d8b9-xk2jp`

```log
2026-04-24 14:19:48 [ERROR] [ledger-client] connection pool exhausted (max=20, active=20, waiting=147)
2026-04-24 14:19:49 [WARN]  [ledger-client] query timeout after 2000ms: SELECT ... FROM transactions WHERE ...
2026-04-24 14:19:49 [ERROR] [handler] POST /v2/transactions/batch failed: context deadline exceeded
2026-04-24 14:19:50 [ERROR] [ledger-client] connection reset by peer
2026-04-24 14:19:51 [WARN]  [circuit-breaker] ledger-client OPEN (threshold 50%, current 87%)
2026-04-24 14:19:52 [ERROR] [reactor] failed to publish message: chronos-api upstream error
```

### Estado do Reactor (fila `chronos-transactions`)

- **50.127 mensagens** acumuladas, crescendo a **~800/min**.
- **Consumer lag** atual: **18 minutos** e aumentando.

### Estado do cluster

- **Chronos**: 12/12 pods running (HPA no máximo).
- **CPU médio** dos pods: 62%.
- **Memória média** dos pods: 71%.
- **Conexões ativas** ao Ledger: 240/250 (limite do RDS).

[STEPS]
Você precisa criar um **postmortem técnico rapidamente** para decidir entre:
- **rollback** do deploy `v2.48.0` (que subiu ontem);
- ou **scaling emergencial** (aumento de limits do RDS e do pool de conexões).

[EXPECTATION]
O documento postmortem precisa conter todos os detalhes para efetuar as 2 opções, considerando o tempo para recuperação em cada cenário e demais particularidades.
Deve ser um documento que facilite a tomada de decisão, trazendo de forma fácil o resumo sobre cada opção e informações minimas para a tomada de decisão rápida.
No restante do documento é esperado o detalhamento, comandos de apoio, comandos para rollback e todas informações para acompanhar a recuperação do ambiente após execução dos passos de recuperação.


---

## 🤖 Campo 2: Modelo

* **Modelo:** `ChatGPT GPT-5.5`
* **Justificativa de Escolha:** Pra análise de incidente com várias evidências (deploy, métricas, log, fila, cluster) costuma cruzar tudo numa hipótese coerente e ainda entrega plano de ação com comandos prontos.

---

## 📄 Campo 3: Output
A análise dos dados aponta para uma hipótese principal bastante forte: **o deploy v2.48.0 introduziu regressão no consumo de conexões do Ledger**, e o aumento de tráfego apenas acelerou a manifestação do problema.

# Executive Summary

| Item               | Status                                  |
| ------------------ | --------------------------------------- |
| Severidade         | SEV-1                                   |
| Impacto            | Degradação crítica da API de transações |
| Início observado   | 14:00 UTC                               |
| Situação atual     | Ambiente saturado                       |
| HPA                | 12/12 pods (máximo)                     |
| CPU                | 62%                                     |
| Memória            | 71%                                     |
| Ledger Connections | 240/250                                 |
| Queue Lag          | 18 min                                  |
| Error Rate         | 11.7%                                   |
| P99                | 8.1s                                    |

## Hipótese Principal

A versão **v2.48.0** introduziu simultaneamente três mudanças de alto risco:

```text
- Novo endpoint batch
- Refatoração do pool de conexões do Ledger
- Upgrade psycopg
- Timeout reduzido de 5s → 2s
```

Evidências:

```text
connection pool exhausted
waiting=147

Ledger connections:
240/250

CPU dos pods:
62%

Memória:
71%
```

Se o problema fosse falta de capacidade computacional:

* CPU estaria próxima de 100%
* Memória próxima do limite
* Pods reiniciando

Nada disso está ocorrendo.

O gargalo está claramente em:

```text
Ledger / PostgreSQL connections
```

---

# Avaliação das Opções

# Opção 1 — Rollback imediato (Recomendação)

## Argumentos favoráveis

Existe correlação temporal forte entre:

```text
Deploy v2.48.0
↓
Mudança no pool
↓
Exaustão de conexões
↓
Circuit breaker aberto
↓
Fila acumulando
```

Os sintomas apontam diretamente para a mudança implantada.

Além disso:

```text
v2.47.0 operou normalmente
```

durante o mesmo perfil de carga anteriormente.

---

## Tempo estimado

| Etapa                    | Tempo     |
| ------------------------ | --------- |
| Rollback ArgoCD          | 2-5 min   |
| Pods atualizarem         | 3-5 min   |
| Conexões estabilizarem   | 2-5 min   |
| Drenagem inicial da fila | 10-20 min |
| Recuperação percebida    | 5-10 min  |

Tempo total estimado:

```text
10 a 20 minutos
```

---

## Risco

Baixo.

Já existe histórico operacional conhecido da v2.47.0.

---

## Comando de rollback

### ArgoCD

```bash
argocd app history chronos-api
```

Identificar revision anterior.

```bash
argocd app rollback chronos-api <ID>
```

---

### GitOps

Reverter commit:

```bash
git revert <commit-v2480>
git push origin main
```

Aguardar sincronização:

```bash
argocd app sync chronos-api
```

---

### Verificar rollout

```bash
kubectl rollout status deployment/chronos-api -n production
```

---

# Opção 2 — Escalabilidade Emergencial

## O que seria feito

Aumentar:

### RDS

```text
250 conexões
↓
500 conexões
```

### Pool da aplicação

```text
20
↓
40 ou 50
```

---

## Benefícios

Pode aliviar imediatamente:

```text
pool exhausted
waiting=147
```

---

## Riscos

### Cenário 1

Existe vazamento de conexões.

Resultado:

```text
500 conexões
↓
500 conexões esgotadas
```

Incidente reaparece.

---

### Cenário 2

Problema no psycopg 3.2.0

Resultado:

```text
Mais conexões
=
Mais sessões presas
```

---

### Cenário 3

Problema no endpoint batch

Resultado:

```text
Mais carga
↓
Mais queries simultâneas
↓
Mais saturação
```

---

## Tempo estimado

| Etapa                 | Tempo     |
| --------------------- | --------- |
| Alterar RDS           | 10-30 min |
| Aplicar pool          | 5-10 min  |
| Reiniciar pods        | 5 min     |
| Validar comportamento | 10-20 min |

Tempo total:

```text
30 a 60 minutos
```

---

## Risco

Médio/Alto.

Não elimina causa raiz.

Apenas aumenta capacidade.

---

# Recomendação Técnica

## Decisão sugerida

```text
1) Rollback imediato
2) Monitorar recuperação
3) Somente escalar se rollback não estabilizar
```

Prioridade:

```text
Rollback > Scale
```

Motivo:

A evidência aponta para regressão introduzida pelo deploy.

Escalar um componente possivelmente defeituoso pode apenas ampliar o problema.

---

# Plano Operacional de Recuperação

## Fase 1 — Rollback

Executar rollback para v2.47.0.

Acompanhar:

```bash
kubectl get pods -n production -w
```

---

## Fase 2 — Confirmar recuperação

### Erros

```promql
sum(rate(http_requests_total{status=~"5.."}[5m]))
```

Meta:

```text
< 1%
```

---

### Latência

```promql
histogram_quantile(
  0.99,
  rate(http_request_duration_seconds_bucket[5m])
)
```

Meta:

```text
< 1000 ms
```

---

### Conexões Ledger

```promql
ledger_active_connections
```

Meta:

```text
< 180
```

---

### Pool Waiting

```promql
ledger_pool_waiting
```

Meta:

```text
0
```

---

### Circuit Breaker

Logs:

```bash
kubectl logs deployment/chronos-api \
-n production \
--tail=1000 | grep circuit-breaker
```

Meta:

```text
CLOSED
```

---

# Recuperação da Fila

Após estabilização da API:

Monitorar:

```text
Queue: chronos-transactions
```

Situação atual:

```text
50.127 mensagens
Lag: 18 min
```

---

### Taxa de drenagem

```text
Produção atual:
+800/min
```

Necessário observar:

```text
consumo > produção
```

Exemplo:

```text
produção = 800/min
consumo = 3000/min
```

Drenagem líquida:

```text
2200/min
```

Tempo para zerar:

```text
50.127 / 2200
≈ 23 minutos
```

---

# Critérios de Encerramento do Incidente

Todos os itens abaixo devem ser verdadeiros por pelo menos 30 minutos:

```text
✓ Error rate < 1%
✓ P99 < 1s
✓ Circuit breaker fechado
✓ Pool waiting = 0
✓ Ledger < 80% das conexões disponíveis
✓ Queue lag em queda contínua
✓ Backlog < 5.000 mensagens
✓ Sem novos timeouts de Ledger
```

# Veredito

**Executaria rollback imediatamente para v2.47.0.**

A combinação de:

```text
- connection pool exhausted
- waiting=147
- 240/250 conexões do RDS
- CPU normal
- memória normal
- alteração recente no pool
- alteração recente do psycopg
```

fornece evidência suficiente para tratar a v2.48.0 como principal suspeita. O rollback tem maior probabilidade de restaurar o serviço em menos tempo (10–20 min) e com menor risco do que uma expansão emergencial da capacidade do banco.


---

## ⚖️ Campo 4: Justificativa
Frameworks que eu não usaria:
B-A-B e C-A-R-E eu não acho que seriam bons neste caso. Pois o B-A-B foca muito no antes/depois e fornecer a bridge, aqui o objetivo não é ir para um cenário em especifico e sim obter o postmortem para atuar. Já o C-A-R-E, devido a necessidade de um exemplo e por não termos um Postmortem de exemplo, não seria adequado.
Framework que eu optei:
R-I-S-E, porque ele oferece o nível de detalhamento necessário para o trabalho envolvido, consigo levar dados de logs e trazer expectativas também.