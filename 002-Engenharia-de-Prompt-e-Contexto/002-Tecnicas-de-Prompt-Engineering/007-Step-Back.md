# 07 — Step-Back: abstrair princípios antes de aplicar

> **Step-Back = dar um passo atrás.** Antes de resolver o caso concreto, o modelo abstrai os **princípios fundamentais** do tema. Depois aplica esses princípios ao problema — diagnóstico fundamentado, não chute.

---

## 1. O que é Step-Back?

A técnica funciona em **duas etapas explícitas**:

```text
Etapa 1 ─► ABSTRAIR   "Quais são os princípios fundamentais de X?"
              │
              ▼
Etapa 2 ─► APLICAR    usar esses princípios para resolver o caso concreto
```

Em vez de pular direto para a solução, o modelo primeiro mapeia **como o domínio funciona** — e só então diagnostica. Isso evita respostas superficiais ("é só aumentar o limite") e leva a análises sistemáticas.

## 2. Por que funciona

- **Ativa o conhecimento certo** antes de aplicar (recupera os fundamentos do domínio).
- **Evita soluções de superfície** baseadas no sintoma, não na causa.
- **Permite eliminar hipóteses por camada**, em vez de tentativa e erro.

> **Dica prática:** rode o mesmo cenário **com e sem** a parte do Step-Back para sentir a diferença de profundidade na análise.

## 3. Quando usar

Diagnósticos técnicos profundos, problemas onde o sintoma engana, decisões que se beneficiam de fundamentos (TCO, redes, memória, performance). Sempre que "tratar o sintoma" for tentador mas arriscado.

---

## 4. Demo não-técnica — compra de carro com princípios de TCO

Antes de recomendar um modelo, o modelo mapeia os princípios de **Total Cost of Ownership (TCO)**. Resultado: recomendação baseada em fundamentos, não em popularidade.

```text
PROMPT
------
Preciso comprar um carro com orçamento de até R$100.000. É pra uso
urbano, faço uns 30km por dia no trânsito de São Paulo. Qual carro você
recomenda?

Antes de recomendar, dê um passo atrás e responda:
Quais são os princípios fundamentais para avaliar o custo total de
propriedade (TCO) de um veículo para uso urbano? Considere depreciação,
consumo, manutenção, seguro e revenda.

Depois, use esses princípios para recomendar o melhor carro para o meu
cenário. Considere carro a combustão e elétrico.
```

---

## 5. Demo técnica 1 — CrashLoopBackOff (memória em containers)

Pod em `CrashLoopBackOff` com `OOMKilled`. Antes de diagnosticar, o modelo abstrai os princípios de memória em K8s (requests vs limits, OOM Killer, cgroups). Resultado: ele questiona se é **under-provisioning** ou **memory leak**, em vez de só sugerir aumentar o limit.

```text
PROMPT
------
Um pod payment-service está em CrashLoopBackOff no Kubernetes. Os logs
mostram:

Container killed due to OOMKilled (exit code 137)
Last state: terminated with exit code 137
Restart count: 5
Resource limits: memory 256Mi
Current usage before crash: 312Mi

Antes de diagnosticar, dê um passo atrás e responda:
Quais são os princípios fundamentais de gerenciamento de memória em
containers Kubernetes? Como requests, limits e OOM Killer interagem?

Depois, use esses princípios para diagnosticar o problema e sugerir
soluções.
```

**Por que é melhor:** ao abstrair primeiro, o modelo distingue se 312Mi > 256Mi é só limite mal dimensionado **ou** crescimento anormal (leak) — duas soluções completamente diferentes.

---

## 6. Demo técnica 2 — conectividade em VPC (princípios de rede)

Dois serviços em subnets diferentes da mesma VPC não se conectam, mas o `ping` funciona. Antes de diagnosticar, o modelo mapeia as **camadas de controle de tráfego** em VPC. Resultado: diagnóstico sistemático por camada, eliminando hipóteses.

```text
PROMPT
------
O serviço order-service (subnet privada 10.0.1.0/24) não consegue se
conectar ao serviço inventory-service (subnet privada 10.0.2.0/24) na
porta 8080. Ambos estão na mesma VPC. O security group do
inventory-service permite inbound na porta 8080 de 10.0.1.0/24. O ping
entre as instâncias funciona normalmente.

Antes de diagnosticar, dê um passo atrás e responda:
Quais são as camadas de controle de tráfego em uma VPC AWS? Como
Security Groups, NACLs, Route Tables e DNS resolution interagem em
comunicação entre subnets?

Depois, use esses princípios para diagnosticar o problema de forma
sistemática.
```

**Pista do enunciado:** o `ping` (ICMP) funciona, mas a porta 8080 (TCP) não. Isso aponta para algo **stateful vs. stateless** — tipicamente uma **NACL** bloqueando a porta de retorno (efêmera) ou o SG de saída, não o ICMP. Conhecer as camadas leva direto à hipótese certa.

---

## 7. Boas práticas

- **Peça os princípios explicitamente** ("dê um passo atrás e responda: quais são os princípios fundamentais de...").
- **Liste as dimensões** que quer cobrir (no carro: depreciação, consumo, seguro; em K8s: requests, limits, OOM).
- **Force a ponte:** "depois, use esses princípios para..." — senão o modelo abstrai e não aplica.
- Combine com **CoT** na fase de aplicação para raciocínio rastreável.
- Excelente como **primeira camada** antes de Self-Consistency ou ToT em problemas difíceis.
