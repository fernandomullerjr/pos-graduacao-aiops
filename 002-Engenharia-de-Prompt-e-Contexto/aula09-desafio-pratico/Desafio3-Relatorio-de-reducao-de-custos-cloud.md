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


## Questão 03 — Relatório de redução de custos cloud

Goldie apresentou a meta do próximo trimestre à diretoria: **15% de redução no custo cloud até o fim do período, sem degradar SLA**. Doc Brown pegou o breakdown de custos AWS do último mês e repassou a análise inicial para o time. O CSV está abaixo.

```csv
servico,categoria,custo_mensal_usd,uso_medio_pct,observacao
EC2 reservada,compute,4200,72,contrato de 1 ano
EC2 on-demand,compute,8200,45,workloads variaveis
EKS,compute,6700,58,3 clusters
RDS PostgreSQL,databases,8200,62,multi-AZ
ElastiCache Redis,databases,2100,40,cluster de producao
S3 Standard,storage,3100,,5 buckets principais
EBS gp3,storage,1600,68,volumes de producao
CloudWatch Logs,observability,2800,,retencao de 90 dias
CloudWatch Metrics,observability,900,,
Data Transfer Out,network,1900,,trafego entre regioes
NAT Gateway,network,1200,,3 gateways ativos
Lambda,compute,900,30,~12M invocacoes/mes
```

O relatório que vai voltar pra Goldie precisa trazer:

- **oportunidades de economia priorizadas por impacto**;
- **quanto cada uma representa em percentual** da conta total;
- **esforço de implementação** (baixo, médio, alto);
- **riscos ou pré-requisitos** envolvidos em cada uma.

> **Tarefa.** Aplicando o framework **T-A-G**, escrever o prompt de IA que, a partir do CSV acima, produza esse relatório alinhado à meta de 15%.
>
> **Entregue.** Prompt, modelo, output e justificativa mostrando como **Task**, **Action** e **Goal** aparecem no prompt.

---


# RESPOSTA

---

## 💻 Campo 1: Prompt

```text
Você atua como Engenheiro de Confiabilidade de Sites (SRE) Sênior com especialidade em FinOps na Hill Valley Tech. 

[TASK]
Sua tarefa é analisar o CSV de custos mensais da AWS abaixo e gerar um relatório estratégico de redução de despesas direcionado à nossa CEO, Goldie Wilson:

servico,categoria,custo_mensal_usd,uso_medio_pct,observacao
EC2 reservada,compute,4200,72,contrato de 1 ano
EC2 on-demand,compute,8200,45,workloads variaveis
EKS,compute,6700,58,3 clusters
RDS PostgreSQL,databases,8200,62,multi-AZ
ElastiCache Redis,databases,2100,40,cluster de producao
S3 Standard,storage,3100,,5 buckets principais
EBS gp3,storage,1600,68,volumes de producao
CloudWatch Logs,observability,2800,,retencao de 90 dias
CloudWatch Metrics,observability,900,,
Data Transfer Out,network,1900,,trafego entre regioes
NAT Gateway,network,1200,,3 gateways ativos
Lambda,compute,900,30,~12M invocacoes/mes

[ACTION]
Execute estritamente os seguintes passos para construir o relatório:
1. Calcule a soma total atual da fatura cloud.
2. Identifique oportunidades de economia focando em recursos subutilizados ou com configurações superestimadas.
3. Ordene essas oportunidades por impacto financeiro de forma decrescente.
4. Calcule o percentual exato que cada oportunidade de economia representa sobre a conta total.
5. Classifique o esforço de implementação de cada ação em Baixo, Médio ou Alto.
6. Mapeie os riscos técnicos ou pré-requisitos com foco no contexto da nossa equipe (interações com Lorraine em SRE, Strickland em compliance e George McFly em sistemas legados).

[GOAL]
O objetivo final é entregar um plano acionável focado na meta de Goldie Wilson: alcançar 15% de redução no custo cloud global para o próximo trimestre, garantindo categoricamente que nenhuma alteração degrade o SLA dos nossos sistemas core (Chronos, Ledger, Reactor, Beacon e Lift).

```

---

## 🤖 Campo 2: Modelo

* **Modelo:** `Gemini 1.5 Pro`
* **Justificativa de Escolha:** Escolhido por sua excelente capacidade de raciocínio lógico-matemático estruturado e interpretação de dados tabulares (CSV) sem a necessidade de conversão prévia.
* **Pontos negativos:** Modelo teve dificuldade na formatação de tabela Markdown, para entregar o plano de ação detalhado.

---

## 📄 Campo 3: Output

### **Relatório de Otimização de Custos Cloud (FinOps) — Hill Valley Tech**

**Destinatária:** Goldie Wilson (CEO)

**Responsável:** Time de SRE / FinOps

---

#### **Diagnóstico Financeiro Atual**

* **Custo Mensal Consolidado:** USD 41.800,00
* **Meta de Redução do Trimestre (15%):** USD 6.270,00
* **Potencial de Economia Identificado:** **USD 8.120,00 (~19,42%)**

---

### **Plano de Ação Priorizado**

| ID | Oportunidade | Economia (USD) | % Total | Esforço |
| --- | --- | --- | --- | --- |
| 1 | Migração de EC2 On-Demand para Savings Plans | USD 2.870,00 | 6,87% | **Baixo** |
| 2 | Ajuste de Retenção do CloudWatch Logs | USD 1.680,00 | 4,02% | **Baixo** |
| 3 | Políticas de Ciclo de Vida no S3 Standard | USD 1.240,00 | 2,97% | **Baixo** |
| 4 | Rightsizing e Otimização do EKS | USD 1.340,00 | 3,21% | **Médio** |
| 5 | Downsize de ElastiCache Redis | USD 590,00 | 1,41% | **Médio** |
| 6 | Redução de NAT Gateways Ociosos | USD 400,00 | 0,96% | **Médio** |

---

### **Detalhamento Técnico e Riscos**

1. **EC2 & Savings Plans**
* **Ação:** Converter instâncias on-demand em planos de compromisso de 1 ano.
* **Risco:** Nenhum técnico. Requer apenas aprovação orçamentária de **Goldie**.


2. **CloudWatch Logs**
* **Ação:** Reduzir retenção de 90 para 14 dias; arquivar logs antigos no S3 Glacier.
* **Risco:** Logs antigos ficam fora do painel imediato do **Beacon**. Validar com **Strickland**.


3. **S3 Lifecycle**
* **Ação:** Mover dados não acessados para camadas Infrequent Access (IA).
* **Risco:** Pequeno aumento de latência no primeiro acesso. **George McFly** deve validar o legado.


4. **Otimização EKS**
* **Ação:** Consolidar nós dos 3 clusters e aplicar Spot no produto **Lift** (Beta).
* **Risco:** Possível instabilidade em deploys. Exige homologação de **Lorraine**.


5. **ElastiCache Redis**
* **Ação:** Reduzir tamanho dos nós para alinhar aos 40% de uso médio.
* **Risco:** Monitorar picos de IOPS via **Beacon** para evitar gargalos em produção.



---

#### **Conclusão Operacional**

Ao focar inicialmente nas ações de **Esforço Baixo** (IDs 1 a 3), garantimos **13,86%** de economia imediata. A inclusão da otimização do EKS (ID 4) nos leva a **17,07%**, superando a meta de Goldie Wilson sem comprometer o SLA dos sistemas core (**Chronos, Ledger e Reactor**).

---

## ⚖️ Campo 4: Justificativa

A estrutura baseou-se estritamente no framework **T-A-G**: a **Task** estabeleceu o papel profissional e o contexto do relatório direcionado à liderança; a **Action** delimitou os passos mecânicos e as amarrações de risco com os personagens da Hill Valley Tech; e o **Goal** fixou a restrição de sucesso (15% de corte financeiro sem violar os SLAs core).