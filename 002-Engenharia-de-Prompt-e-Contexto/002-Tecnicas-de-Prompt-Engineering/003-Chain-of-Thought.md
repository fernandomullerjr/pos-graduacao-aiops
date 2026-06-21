# 03 — Chain of Thought (CoT): raciocinar passo a passo

> **CoT força o modelo a mostrar o caminho do raciocínio antes da conclusão.** Em vez de pular para a resposta, ele decompõe o problema em etapas — o que aumenta acerto e dá **auditabilidade**.

---

## 1. O que é Chain of Thought?

Chain of Thought é instruir o modelo a **pensar em etapas explícitas** antes de responder. A diferença não é o genérico "pense passo a passo" — é **dizer quais etapas** seguir.

```text
SEM CoT:   pergunta ─────────────────────► resposta (pula o raciocínio)

COM CoT:   pergunta ─► etapa 1 ─► etapa 2 ─► ... ─► etapa N ─► resposta
                       (raciocínio visível e verificável)
```

## 2. Por que funciona

- **Decompõe a complexidade:** problemas grandes viram subproblemas tratáveis.
- **Reduz erros de salto lógico:** o modelo não "adivinha" a conclusão.
- **Cria auditabilidade:** você vê _por que_ ele chegou ali e pode validar cada passo.
- **Melhora cálculos e correlações:** especialmente útil em quantidades, timestamps e causa raiz.

## 3. Quando usar

Use CoT quando a tarefa exige **raciocínio, cálculo ou correlação**: análise de causa raiz, matemática, decisões lógicas, debugging, planejamento. Evite em tarefas triviais — adiciona verbosidade sem ganho.

> **CoT estruturado** (dizer as etapas) é mais robusto que CoT genérico ("pense passo a passo"). Você controla o caminho.

---

## 4. Demo não-técnica — lista de churrasco

### Prompt 1 — sem CoT (baseline)

Pede a lista direto. Resultado: lista plausível mas **genérica**, sem cálculo de quantidades nem justificativa.

```text
PROMPT
------
Monte uma lista de compras para um churrasco para 15 pessoas, incluindo
2 crianças. Preciso comprar tudo, incluindo descartáveis e bebidas.
```

### Prompt 2 — com CoT estruturado

Mesmo contexto, mas com 5 etapas de raciocínio explícito. O modelo calcula quantidades por perfil, separa categorias e entrega lista **justificada**.

```text
PROMPT
------
Monte uma lista de compras para um churrasco para 15 pessoas, incluindo
2 crianças. Preciso comprar tudo, incluindo descartáveis e bebidas.

Antes de montar a lista, raciocine passo a passo:
1. Calcule a quantidade de carne por pessoa considerando adultos e crianças
2. Separe as carnes por bovinos, suínos e aves
3. Calcule bebidas separando adultos e crianças, alcoólicos e não alcoólicos
4. Liste os itens complementares (descartáveis, carvão, temperos)
5. Monte a lista final com quantidades calculadas
```

> Observe: não é "pense passo a passo" genérico — é dizer **quais** etapas seguir.

---

## 5. Demo técnica — análise de causa raiz de incidente

### Prompt 3 — sem CoT (baseline)

```text
PROMPT
------
Analise os logs abaixo de um incidente em produção e identifique a causa raiz.

[logs do incidente — ver bloco LOGS abaixo]
```

Sem CoT, o modelo acerta o problema ("connection pool esgotado") mas **pula direto para a conclusão**, sem mostrar o caminho.

### Prompt 4 — com CoT estruturado

```text
PROMPT
------
Analise os logs abaixo de um incidente em produção e identifique a causa raiz.

Antes de concluir, raciocine passo a passo:
1. Identifique o evento inicial que desencadeou o incidente
2. Correlacione os timestamps para reconstruir a sequência de falha
3. Calcule os recursos envolvidos (conexões, réplicas, limites)
4. Explique por que o sistema falhou com base nos números
5. Apresente a causa raiz e o que a provocou

[logs do incidente — ver bloco LOGS abaixo]
```

Resultado: o modelo **reconstrói a narrativa temporal**, calcula os recursos e explica _por que_ o sistema falhou, com auditabilidade completa.

### Prompt 5 — CoT encadeado (post-mortem)

Pega o output da análise anterior e gera um post-mortem. Demonstra **CoT em cadeia** — o raciocínio de uma etapa alimenta o próximo artefato sem retrabalho manual.

```text
PROMPT
------
Com base na análise de causa raiz acima, gere um documento de
post-mortem estruturado. Raciocine passo a passo para:
1. Definir a severidade e o impacto com base nos dados dos logs
2. Montar a timeline com os marcos críticos
3. Derivar ações corretivas a partir da causa raiz identificada
4. Sugerir ações preventivas para evitar recorrência

Use o formato:
- Título do incidente
- Data, duração, severidade
- Resumo executivo
- Timeline
- Causa raiz
- Ações corretivas (com responsável sugerido e prazo)
- Ações preventivas
```

---

## 6. LOGS do incidente (payment-service)

Bloco de logs fictícios usado nos Prompts 3 e 4. Cole junto com o prompt ao reproduzir a demo.

```log
[2025-11-15 14:30:01] deploy-controller: Starting rollout for deployment payment-service v2.3.1
[2025-11-15 14:30:15] deploy-controller: Rollout completed — 3/3 replicas updated
[2025-11-15 14:31:02] payment-service-pod-7x9k2: INFO - Connecting to PostgreSQL at rds-payment.internal:5432
[2025-11-15 14:31:03] payment-service-pod-7x9k2: INFO - Connection pool initialized: max_connections=20
[2025-11-15 14:31:10] payment-service-pod-a3m8f: INFO - Connection pool initialized: max_connections=20
[2025-11-15 14:31:12] payment-service-pod-q1n5p: INFO - Connection pool initialized: max_connections=20
[2025-11-15 14:32:00] alb-ingress: 200 OK - /api/payments (23ms)
[2025-11-15 14:35:22] payment-service-pod-7x9k2: WARN - Connection pool usage: 18/20 (90%)
[2025-11-15 14:36:45] payment-service-pod-a3m8f: WARN - Connection pool usage: 19/20 (95%)
[2025-11-15 14:37:01] payment-service-pod-q1n5p: ERROR - Cannot acquire connection from pool: timeout after 5000ms
[2025-11-15 14:37:02] alb-ingress: 500 Internal Server Error - /api/payments
[2025-11-15 14:37:15] payment-service-pod-7x9k2: ERROR - Cannot acquire connection from pool: timeout after 5000ms
[2025-11-15 14:37:16] payment-service-pod-a3m8f: ERROR - Cannot acquire connection from pool: timeout after 5000ms
[2025-11-15 14:37:30] cloudwatch-alarm: ALARM - payment-service error rate > 50% (current: 87%)
[2025-11-15 14:38:00] rds-monitoring: INFO - Active connections: 60/100 — CPU: 92%
[2025-11-15 14:40:00] oncall-pagerduty: P1 incident opened — payment-service unavailable
```

**Causa raiz esperada (para conferência):** o deploy v2.3.1 subiu 3 réplicas, cada uma com pool de 20 conexões (60 no total). Sob carga, os pools saturaram (90% → 95% → timeout) enquanto o RDS chegou a 60/100 conexões e 92% de CPU — gargalo no dimensionamento do pool de conexões, não no banco em si.

---

## 7. Boas práticas

- **Especifique as etapas** em vez de só pedir "pense passo a passo".
- **Numere as etapas** para forçar ordem e completude.
- **Peça o cálculo explícito** quando houver números (quantidades, limites, timestamps).
- **Encadeie** quando um artefato depende do anterior (análise → post-mortem).
- Para tarefas simples, **não use CoT** — adiciona ruído.
