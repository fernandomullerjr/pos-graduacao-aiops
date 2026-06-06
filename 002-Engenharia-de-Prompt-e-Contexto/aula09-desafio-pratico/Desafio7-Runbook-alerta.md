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

## Questão 07 — Runbook para alerta recorrente

Toda semana, em média 4 vezes, o **Beacon** dispara o mesmo alerta no canal de plantão:

> `[CRITICAL] High memory usage on Chronos API pods (>85% for 10min)`

Quem assume o plantão gasta de **30 a 40 minutos** até resolver, e o tempo varia muito porque não existe procedimento documentado. Lorraine quer um runbook que **qualquer plantonista consiga seguir de ponta a ponta**, sem depender de quem conhece o sistema.

**Ambiente que o runbook precisa considerar:**

- Chronos roda no **EKS**, namespace `production`, **6 réplicas** com **HPA** configurado (min 4, max 12, CPU target 70%).
- Deploy via **Argo CD** a partir do repositório `hvt/chronos-api`.
- Dependências diretas: **Ledger** (PostgreSQL) e **Reactor** (filas SQS).
- Observabilidade: métricas expostas em `/metrics`, logs centralizados no Beacon, dashboards em Grafana.
- Ferramentas disponíveis para o plantão: `kubectl`, `aws cli`, `argocd cli`.
- Canal de plantão: `#oncall-chronos` no Slack.
- Time sênior de escalação: `@chronos-core` (SLA de resposta: **15 minutos** em horário comercial, **30** fora).

**O runbook precisa cobrir:**

- passos iniciais de diagnóstico (com os comandos específicos a rodar);
- verificação esperada ao final de cada passo;
- critérios objetivos para escalar para o time sênior;
- critério para encerrar o incidente.

> **Tarefa.** Aplicando o framework **R-I-S-E**, escrever o prompt de IA que produza esse runbook procedural completo.
>
> **Entregue.** Prompt, modelo, output e justificativa mostrando como **Role**, **Input**, **Steps** e **Expectation** aparecem no prompt.

---

# RESPOSTA

---

## 💻 Campo 1: Prompt
[Role]
Você é um engenheiro SRE Especialista, com larga experiencia em ambientes com Kubernetes e rotinas de resposta a incidentes em ambientes complexos.

[Input]
Toda semana, em média 4 vezes, o **Beacon** dispara o mesmo alerta no canal de plantão:
> `[CRITICAL] High memory usage on Chronos API pods (>85% for 10min)`
Quem assume o plantão gasta de **30 a 40 minutos** até resolver, e o tempo varia muito porque não existe procedimento documentado. 

[Steps]
**Ambiente que o runbook precisa considerar:**
- Chronos roda no **EKS**, namespace `production`, **6 réplicas** com **HPA** configurado (min 4, max 12, CPU target 70%).
- Deploy via **Argo CD** a partir do repositório `hvt/chronos-api`.
- Dependências diretas: **Ledger** (PostgreSQL) e **Reactor** (filas SQS).
- Observabilidade: métricas expostas em `/metrics`, logs centralizados no Beacon, dashboards em Grafana.
- Ferramentas disponíveis para o plantão: `kubectl`, `aws cli`, `argocd cli`.
- Canal de plantão: `#oncall-chronos` no Slack.
- Time sênior de escalação: `@chronos-core` (SLA de resposta: **15 minutos** em horário comercial, **30** fora).

[Expectation]
Lorraine quer um runbook que **qualquer plantonista consiga seguir de ponta a ponta**, sem depender de quem conhece o sistema.
**O runbook precisa cobrir:**

- passos iniciais de diagnóstico (com os comandos específicos a rodar);
- verificação esperada ao final de cada passo;
- critérios objetivos para escalar para o time sênior;
- critério para encerrar o incidente.

---

## 🤖 Campo 2: Modelo

* **Modelo:** `Claude Sonnet 4.6`
* **Justificativa de Escolha:** Costuma organizar bem procedimento sequencial com critério objetivo por passo, que é exatamente o que um runbook de plantão precisa pra reduzir variação entre quem assume.

---

## 📄 Campo 3: Output
O runbook está completo e interativo — aqui vai um resumo do que está coberto e por que as escolhas foram feitas assim:

**Estrutura de diagnóstico em sequência lógica**

A ordem dos passos segue o fluxo de triagem real: primeiro confirmar o escopo (quantos pods, HPA), depois verificar se é sintoma de dependência (Ledger/Reactor), e só então agir. Isso evita o erro comum de reiniciar pods sem entender a causa, o que neste caso acontece 4x por semana.

**Sinalizador-chave embutido nos passos**

O passo do HPA tem um aviso explícito: o HPA escala por CPU, não por memória. Isso é crítico porque um plantonista sem experiência poderia achar que "o HPA vai resolver" — e não vai.

**Critérios objetivos em vez de subjetivos**

Os critérios de escalação e encerramento usam valores concretos (85%, 10 minutos consecutivos, 12 réplicas) para eliminar o julgamento individual que fazia o tempo de resolução variar tanto.

**Duas ações corretivas distintas**

`rollout restart` para casos de vazamento de memória pontual, e `rollback via Argo CD` para casos correlacionados a deploy recente. São cenários diferentes com ações diferentes.

---

**Próximos passos recomendados para a Lorraine:**

Preencher dois campos que precisam de informação interna antes de distribuir o runbook: o nome real das filas SQS do Reactor (`QUEUE_NAME`), e o endpoint de saúde do Ledger se for externo ao cluster. O restante está funcional como está.

---

## ⚖️ Campo 4: Justificativa
Na **Role** eu trouxe um pouco sobre o profissional que vai criar tal material, trazendo que é um engenheiro SRE com um determinado Background.
No **Input** eu trouxe um pouco de detalhes sobre o alerta e o cenário atual.
Em **Steps** eu trouxe algumas camadas do ambiente e especificações.
Em **Expectation** eu trouxe detalhes sobre o que é esperado e alguns requisitos.



# NOVA RESPOSTA
Considerando os pontos sinalizados pelo avaliador.

---

## 💻 Campo 1: Prompt
[Role]
Você é um engenheiro SRE Especialista, com larga experiencia em ambientes com Kubernetes e rotinas de resposta a incidentes em ambientes complexos.

[Input]
Toda semana, em média 4 vezes, o **Beacon** dispara o mesmo alerta no canal de plantão:
> `[CRITICAL] High memory usage on Chronos API pods (>85% for 10min)`
Quem assume o plantão gasta de **30 a 40 minutos** até resolver, e o tempo varia muito porque não existe procedimento documentado. 

[Steps]
**Ambiente que o runbook precisa considerar:**
- Chronos roda no **EKS**, namespace `production`, **6 réplicas** com **HPA** configurado (min 4, max 12, CPU target 70%).
- Deploy via **Argo CD** a partir do repositório `hvt/chronos-api`.
- Dependências diretas: **Ledger** (PostgreSQL) e **Reactor** (filas SQS).
- Observabilidade: métricas expostas em `/metrics`, logs centralizados no Beacon, dashboards em Grafana.
- Ferramentas disponíveis para o plantão: `kubectl`, `aws cli`, `argocd cli`.
- Canal de plantão: `#oncall-chronos` no Slack.
- Time sênior de escalação: `@chronos-core` (SLA de resposta: **15 minutos** em horário comercial, **30** fora).

[Expectation]
Lorraine quer um runbook que **qualquer plantonista consiga seguir de ponta a ponta**, sem depender de quem conhece o sistema.
**O runbook precisa cobrir:**

- passos iniciais de diagnóstico (com os comandos específicos a rodar);
- verificação esperada ao final de cada passo;
- critérios objetivos para escalar para o time sênior;
- critério para encerrar o incidente.

**O runbook precisa estar num formato:**
- que sejá possível copiar para Confluence ou outros editores, sem perda de formatação.
- priorizar elementos de formatação Markdown, sem utilizar elementos de formatação exclusivos para uma linguagem ou editor especificos.
- que seja de fácil leitura por humanos

---

## 🤖 Campo 2: Modelo

* **Modelo:** `Claude Sonnet 4.6`
* **Justificativa de Escolha:** Costuma organizar bem procedimento sequencial com critério objetivo por passo, que é exatamente o que um runbook de plantão precisa pra reduzir variação entre quem assume.

---

## 📄 Campo 3: Output

# Runbook — Alta Utilização de Memória: Chronos API

**Alerta:** `[CRITICAL] High memory usage on Chronos API pods (>85% for 10min)`  
**Canal de plantão:** `#oncall-chronos`  
**Time de escalação:** `@chronos-core`  
**Tempo esperado de resolução:** 15–25 minutos

---

## Índice

1. [Primeira resposta](#1-primeira-resposta--primeiros-5-minutos)
2. [Diagnóstico inicial — estado dos pods](#2-diagnóstico-inicial--estado-dos-pods)
3. [Investigar dependências](#3-investigar-dependências)
4. [Ações corretivas](#4-ações-corretivas)
5. [Critérios de escalação](#5-critérios-de-escalação-para-chronos-core)
6. [Encerramento do incidente](#6-encerramento-do-incidente)

---

## Contexto rápido do ambiente

| Item | Detalhe |
|---|---|
| Namespace | `production` |
| Réplicas | 6 (HPA: mín 4, máx 12, target CPU 70%) |
| Deploy | Argo CD — repositório `hvt/chronos-api` |
| Dependências | Ledger (PostgreSQL) · Reactor (filas SQS) |
| Métricas | `/metrics` em cada pod · Dashboards no Grafana |
| Logs | Centralizados no Beacon |
| Ferramentas | `kubectl` · `aws cli` · `argocd cli` |
| SLA escalação | 15 min (horário comercial) · 30 min (fora) |

---

## 1. Primeira resposta — primeiros 5 minutos

### 1.1 Confirme o alerta e assuma o incidente

Poste no `#oncall-chronos`:

```
🔴 Assumindo incidente: alta memória Chronos API
   Iniciando diagnóstico às [HH:MM]
```

### 1.2 Abra o dashboard no Grafana

Acesse **Chronos API / Memory & Resources** e observe:

- **Pico gradual** → suspeita de vazamento de memória (_memory leak_)
- **Pico súbito** → suspeita de spike de carga ou deploy recente

> **Dica:** anote o horário exato em que a memória começou a subir. Esse dado vai ser essencial para correlacionar com deploys ou eventos de infraestrutura.

---

## 2. Diagnóstico inicial — estado dos pods

Execute os passos a seguir **na ordem apresentada**.

### Passo 2.1 — Listar pods e uso de memória

```bash
kubectl top pods -n production \
  --sort-by=memory | grep chronos-api
```

**Verificação esperada:** 6 pods listados em ordem decrescente de memória.

- Se **1–2 pods** estiverem acima de 85% → problema isolado, provavelmente reinício pontual resolve.
- Se **3 ou mais pods** estiverem acima de 85% → situação sistêmica; continue o diagnóstico antes de agir.

---

### Passo 2.2 — Verificar status e restarts dos pods

```bash
kubectl get pods -n production \
  -l app=chronos-api -o wide
```

**Verificação esperada:** todos com status `Running` e coluna `RESTARTS` com valor baixo e estável.

- Status `OOMKilled` ou `CrashLoopBackOff` → há estouro de memória ativo. Vá direto para o [Passo 4.2](#passo-42--rollback-via-argo-cd-se-deploy-recente-for-o-culpado).

---

### Passo 2.3 — Verificar o HPA

```bash
kubectl describe hpa chronos-api -n production
```

**Verificação esperada:** `REPLICAS` entre 4 e 12. Leia o campo `Conditions` para entender o estado atual do autoscaler.

> ⚠️ **Atenção:** O HPA escala por **CPU** (target 70%), não por memória. É possível que a CPU esteja normal enquanto a memória está alta — isso indica **vazamento de memória**, não pico de carga.
>
> Se o HPA já estiver em **12 réplicas** e a memória continuar acima de 85%, o autoscaler atingiu o teto. Escale imediatamente para `@chronos-core`.

---

### Passo 2.4 — Inspecionar os logs do pod mais crítico

Substitua `POD_NAME` pelo pod identificado no Passo 2.1 com maior consumo.

```bash
kubectl logs POD_NAME -n production \
  --tail=200 | grep -iE "error|warn|oom|memory|heap|gc|timeout"
```

**Verificação esperada:** sem erros críticos em rajada.

- Erros de conexão com `ledger` ou `reactor` → siga para a [Seção 3](#3-investigar-dependências).
- Menções a `OOM`, `heap` ou `GC overhead` → confirma vazamento de memória; siga para [Passo 4.1](#passo-41--rolling-restart-do-deployment).

---

### Passo 2.5 — Verificar eventos recentes do namespace

```bash
kubectl get events -n production \
  --sort-by=.lastTimestamp \
  | grep -i chronos-api | tail -20
```

**Verificação esperada:** sem eventos `OOMKilling` ou `BackOff` recentes. Eventos de `Scheduled`, `Pulled` e `Started` são normais após deploys.

---

## 3. Investigar dependências

Execute esta seção se os logs (Passo 2.4) indicarem erros de conexão ou se a causa ainda estiver indefinida.

### 3.1 — Ledger (PostgreSQL)

**Verificar pool de conexões via métricas do pod:**

```bash
kubectl exec -n production POD_NAME -- \
  curl -s localhost:8080/metrics \
  | grep -E "db_pool|connection|pg_"
```

**Verificação esperada:** pool de conexões abaixo de 80% de capacidade. Valores acima indicam pool exausto — o Chronos pode estar acumulando requests na memória aguardando conexões disponíveis.

**Verificar saúde do serviço Ledger:**

```bash
kubectl exec -n production POD_NAME -- \
  curl -s -o /dev/null -w "%{http_code}" \
  http://ledger-service/health
```

**Verificação esperada:** HTTP `200`. Qualquer outro código indica Ledger degradado, o que pode causar acúmulo de retentativas em memória no Chronos.

---

### 3.2 — Reactor (SQS)

**Verificar profundidade da fila:**

> Substitua `QUEUE_NAME` pelo nome real da fila do Reactor.

```bash
aws sqs get-queue-attributes \
  --queue-url $(aws sqs get-queue-url \
    --queue-name QUEUE_NAME \
    --output text --query 'QueueUrl') \
  --attribute-names \
    ApproximateNumberOfMessages \
    ApproximateNumberOfMessagesNotVisible
```

**Verificação esperada:** `ApproximateNumberOfMessages` dentro do baseline normal de operação. Filas com backlog acima de 10 mil mensagens podem indicar consumidores travados segurando mensagens em memória.

**Verificar a Dead Letter Queue (DLQ):**

```bash
aws sqs get-queue-attributes \
  --queue-url $(aws sqs get-queue-url \
    --queue-name QUEUE_NAME-dlq \
    --output text --query 'QueueUrl') \
  --attribute-names ApproximateNumberOfMessages
```

**Verificação esperada:** DLQ vazia ou com volume estável. Crescimento repentino na DLQ indica falhas sistêmicas no processamento — notifique `@chronos-core`.

---

## 4. Ações corretivas

Escolha a ação com base no diagnóstico feito nas seções anteriores.

### Passo 4.1 — Rolling restart do deployment

**Quando usar:** pods com memória alta mas sem `OOMKilled`, sem deploy recente suspeito, e dependências saudáveis. Trata o sintoma de forma controlada.

```bash
# Reinício gradual — Kubernetes garante disponibilidade durante o processo
kubectl rollout restart deployment/chronos-api \
  -n production

# Acompanhe o andamento do rollout
kubectl rollout status deployment/chronos-api \
  -n production --timeout=5m
```

**Verificação esperada:** mensagem `successfully rolled out`. A memória deve cair abaixo de 75% nos primeiros 2–3 minutos após o rollout completar.

> ⚠️ **Atenção:** O restart trata o sintoma, não a causa. Monitore os pods por 10 minutos após o rollout. Se a memória voltar a subir para acima de 85%, há um vazamento de memória real — escale para `@chronos-core`.

---

### Passo 4.2 — Rollback via Argo CD (se deploy recente for o culpado)

**Quando usar:** memória começou a subir logo após um deploy, ou pods estão em `OOMKilled` / `CrashLoopBackOff`.

**Verificar histórico de deploys:**

```bash
argocd app history chronos-api
```

Se houver um deploy nas últimas 2 horas correlacionado com o início do alerta, faça o rollback:

```bash
# Substitua REVISION_ID pelo ID listado no comando acima
argocd app rollback chronos-api REVISION_ID

# Confirme que o rollback foi aplicado
argocd app get chronos-api
```

**Verificação esperada:** status `Synced` e `Healthy` no Argo CD. Os pods retornam ao estado anterior em aproximadamente 3 minutos.

> ⚠️ **Após o rollback:** poste no `#oncall-chronos` informando a revisão revertida e mencione o responsável pelo deploy. Inclua o ID da revisão no registro do incidente.

---

### Passo 4.3 — Monitorar após a ação corretiva

```bash
# Monitorar uso de memória a cada 30 segundos
watch -n 30 "kubectl top pods \
  -n production | grep chronos-api"
```

**Verificação esperada:** todos os pods abaixo de 75% após 5 minutos. Se após 10 minutos qualquer pod ainda estiver acima de 85%, escale para `@chronos-core`.

---

## 5. Critérios de escalação para `@chronos-core`

Escale **imediatamente** se qualquer uma das condições abaixo for verdadeira:

- HPA em **12 réplicas** (teto máximo) e memória ainda acima de 85%
- Pods em `OOMKilled` de forma contínua, sem estabilizar após o restart
- Memória volta para acima de 85% dentro de 10 minutos após o rollout restart
- Erros de conexão com Ledger ou Reactor confirmados nos logs
- DLQ do Reactor crescendo ativamente
- Rollback impossível (Argo CD em estado de sync error ou conflito)
- Incidente ativo por mais de **20 minutos** sem melhora visível

**Modelo de mensagem para o Slack:**

```
@chronos-core escalando incidente de alta memória no Chronos API (production).

Situação atual:
- Pods afetados: [X de 6] · HPA em [N] réplicas
- Memória: [valor%] nos pods afetados
- Ações já tomadas: [rollout restart / rollback rev. X / nenhuma]
- Resultado: [não resolveu / piorou / sem melhora]
- Logs relevantes: [link Beacon ou trecho]
- Início do alerta: [HH:MM]
```

---

## 6. Encerramento do incidente

O incidente só pode ser encerrado quando **todos** os critérios abaixo forem atendidos simultaneamente:

- Todos os pods com memória abaixo de **75% por pelo menos 10 minutos consecutivos**
- Nenhum pod em estado `OOMKilled` ou `CrashLoopBackOff`
- HPA com réplicas estáveis (sem escalar ou desescalar ativamente)
- Alerta no Beacon resolvido ou silenciado com justificativa registrada
- Nenhum erro novo nos logs nas últimas 5 minutos

### Verificação final de saúde

```bash
kubectl top pods -n production | grep chronos-api
kubectl get pods -n production -l app=chronos-api
kubectl describe hpa chronos-api -n production \
  | grep -E "Replicas|Conditions"
```

### Registro de encerramento no `#oncall-chronos`

```
✅ Incidente encerrado [HH:MM]
   Duração total: [N min]
   Causa identificada: [sim / não / parcialmente]
   Causa raiz: [deploy / vazamento / carga / dependência / desconhecida]
   Ação aplicada: [rollout restart / rollback rev. X / outra]
   Memória atual: [valor%] — todos os pods estáveis
   Post-mortem necessário: [sim / não]
```

> ⚠️ Se este alerta ocorreu **mais de 2 vezes na semana**, abra uma task de investigação de causa raiz. Este runbook é paliativo — a recorrência indica um problema estrutural que precisa ser endereçado no código ou na configuração de infraestrutura.

---

*Runbook mantido pelo time `@chronos-core` · Repositório: `hvt/chronos-api`*

---

## ⚖️ Campo 4: Justificativa
Na **Role** eu trouxe um pouco sobre o profissional que vai criar tal material, trazendo que é um engenheiro SRE com um determinado Background.
No **Input** eu trouxe um pouco de detalhes sobre o alerta e o cenário atual.
Em **Steps** eu trouxe algumas camadas do ambiente e especificações.
Em **Expectation** eu trouxe detalhes sobre o que é esperado e alguns requisitos, inclusive na parte de formatação do output esperado.