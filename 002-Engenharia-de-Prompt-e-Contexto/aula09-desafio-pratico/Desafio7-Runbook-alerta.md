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