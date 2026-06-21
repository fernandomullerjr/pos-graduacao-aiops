# 04 — Tree of Thought (ToT): explorar caminhos e debater

> **ToT estrutura o "depende do contexto".** Em vez de um raciocínio linear, o modelo explora **vários caminhos em paralelo**, compara trade-offs e chega a uma recomendação consensual — como uma reunião de arquitetura.

---

## 1. O que é Tree of Thought?

Enquanto o CoT segue **uma** linha de raciocínio, o ToT abre **vários ramos** (alternativas), avalia cada um sob múltiplos critérios e converge para a melhor decisão.

```text
CoT:   ideia ─► passo ─► passo ─► resposta            (uma linha)

ToT:                  ┌─► Estratégia A ─► análise ─┐
        problema ─────┼─► Estratégia B ─► análise ─┼─► debate ─► CONSENSO
                      └─► Estratégia C ─► análise ─┘            (várias linhas)
```

## 2. O padrão "3 experts"

A aula adota um padrão prático e poderoso: pedir ao modelo que **simule especialistas com perspectivas diferentes**, que **debatem os trade-offs** entre si antes de recomendar. Cada especialista enxerga um risco que os outros não veem.

```text
┌── Especialista Dev ────► perspectiva 1 ─┐
├── Especialista Cloud ──► perspectiva 2 ─┼─► debatem ─► recomendação
└── Especialista SRE ────► perspectiva 3 ─┘            consensual
```

## 3. Quando usar

Use ToT em **decisões com trade-offs** e múltiplas dimensões, onde a resposta certa é "depende": escolha de arquitetura, estratégia de migração, dimensionamento, decisões de carreira. O ToT torna **auditável** _por que_ cada alternativa foi descartada.

---

## 4. Demo não-técnica — escolher entre 3 ofertas de emprego

Aplica o padrão "3 consultores de carreira" a uma decisão pessoal com várias dimensões (salário, modelo de trabalho, área, risco, networking).

```text
PROMPT
------
Imagine três consultores de carreira experientes avaliando minha
situação. Cada um apresenta sua análise considerando as três ofertas,
debatem os trade-offs entre si, e chegam a uma recomendação consensual.

Contexto: 28 anos, moro sozinho, prefiro trabalho remoto, quero crescer
na área de IA, preciso de estabilidade financeira no curto prazo, 2 anos
de experiência em Cloud.

Ofertas:
- Empresa A: startup, R$8k, 100% remoto, área de IA, stock options, risco alto
- Empresa B: grande empresa, R$12k, híbrido 3x semana, área de DevOps, estabilidade alta
- Empresa C: consultoria, R$10k, presencial + viagens, múltiplos projetos, networking intenso
```

---

## 5. Demo técnica 1 — estratégia de migração para AWS

Avalia três estratégias clássicas (Lift-and-Shift, Replatform, Refactor) sob restrições reais de equipe, prazo e tolerância a downtime.

```text
PROMPT
------
Avalie três estratégias de migração para nosso sistema de e-commerce (3
servidores web, PostgreSQL, processamento síncrono, pico na Black
Friday) para AWS:

Estratégia 1: Lift-and-Shift — EC2 + RDS, migração direta sem mudanças de arquitetura
Estratégia 2: Replatform — Containers no ECS + RDS + SQS para processamento assíncrono de pedidos
Estratégia 3: Refactor — Serverless com Lambda + API Gateway + DynamoDB

Para cada estratégia, analise:
1. Como resolve o problema de pico na Black Friday
2. Principais vantagens para este contexto
3. Principais riscos e limitações
4. Complexidade de migração (baixa / média / alta)

Após avaliar as três, recomende a melhor considerando: equipe de 4
engenheiros, prazo de 3 meses, sem downtime tolerável.
```

---

## 6. Demo técnica 2 — escalabilidade com 3 especialistas

O prompt mais robusto da aula: padrão "3 experts" completo (Dev × Cloud × SRE) × 3 estratégias × 5 critérios. Simula uma reunião de arquitetura.

```text
PROMPT
------
Imagine três especialistas seniores avaliando a seguinte decisão de
escalabilidade. Cada um apresenta sua análise, debatem os trade-offs
entre si, e chegam a uma recomendação consensual.

- Especialista em Desenvolvimento: foco em complexidade de código,
  refatoração necessária, impacto no time de devs e débito técnico
- Especialista em Cloud: foco em serviços AWS, custos, arquitetura de
  infra e limites de plataforma
- Especialista SRE: foco em operação, observabilidade, resiliência, SLOs
  e resposta a incidentes

Contexto: Nossa API REST está em um EC2 m5.xlarge (4 vCPUs, 16GB RAM).
Atual: 1.000 req/s, latência p99 de 120ms. Meta: 10.000 req/s com
p99 < 200ms. Budget: $3.000/mês.

Estratégias a avaliar:
Estratégia 1: Vertical — upgrade para c5.4xlarge (16 vCPUs, 32GB) ou r5.4xlarge
Estratégia 2: Horizontal — Auto Scaling Group com instâncias menores + ALB + externalização de sessão
Estratégia 3: Serverless — migrar para Lambda + API Gateway com concorrência provisionada

Para cada estratégia, os três especialistas devem analisar:
1. Viabilidade técnica — consegue atingir 10.000 req/s com p99 < 200ms?
2. Custo — cabe no budget de $3.000/mês?
3. Complexidade de implementação — esforço pra migrar do estado atual
4. Riscos operacionais — o que pode dar errado em produção
5. Escalabilidade futura — tem espaço pra crescer além dos 10k req/s?

Cada especialista analisa as três estratégias sob sua perspectiva usando
esses critérios, debatem os pontos de conflito, e chegam a uma
recomendação final com justificativa.
```

> **Estrutura do resultado:** cada especialista apresenta sua leitura → os três debatem os pontos de conflito → fecham em **CONSENSO** com justificativa. É o ToT simulando a tomada de decisão coletiva.

---

## 7. Boas práticas

- **Dê perspectivas distintas e complementares** aos especialistas (não três clones).
- **Defina critérios objetivos** (custo, viabilidade, risco...) para o debate não virar opinião solta.
- **Peça explicitamente o debate e o consenso** — é o que diferencia ToT de "liste prós e contras".
- **Force a justificativa do descarte:** por que A e B perderam para C.
- Combine com **números do contexto** (budget, SLA, prazo) para ancorar a decisão na realidade.
