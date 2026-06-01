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

## Questão 05 — Modernizar deployment legado

Numa revisão de produção, Doc Brown puxou o manifest do **Chronos** e caiu neste deployment que o George escreveu três anos atrás. Desde então ninguém mexeu nele, e muita coisa que hoje é obrigatória no padrão da empresa ainda não está presente. **Modernizar caiu na sua mesa.**

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: chronos-api
  namespace: production
spec:
  replicas: 1
  selector:
    matchLabels:
      app: chronos-api
  template:
    metadata:
      labels:
        app: chronos-api
    spec:
      containers:
      - name: api
        image: chronos-api:latest
        ports:
        - containerPort: 8080
        env:
        - name: DB_PASSWORD
          value: "P@ssw0rd2023!"
        - name: JWT_SECRET
          value: "hvt-jwt-prod-secret"
```

A versão moderna precisa ter:

- **alta disponibilidade**;
- **imagem versionada** (nada de `latest`);
- **secrets fora do manifest**;
- **resource requests e limits**;
- **liveness e readiness probes**;
- **`securityContext` não-root**;
- demais práticas de produção que hoje são padrão na empresa.

> **Tarefa.** Aplicando o framework **B-A-B**, escrever o prompt de IA que, recebendo esse manifest, produza a versão modernizada.
>
> **Entregue.** Prompt, modelo, output e justificativa mostrando como **Before**, **After** e **Bridge** aparecem no prompt.

---

# RESPOSTA

---

## 💻 Campo 1: Prompt

[BEFORE]
Revisando ambiente de produção, verificando o manifest do **Chronos** e seu deployment, foi possível verificar que  muita coisa que hoje é obrigatória no padrão da empresa ainda não está presente.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: chronos-api
  namespace: production
spec:
  replicas: 1
  selector:
    matchLabels:
      app: chronos-api
  template:
    metadata:
      labels:
        app: chronos-api
    spec:
      containers:
      - name: api
        image: chronos-api:latest
        ports:
        - containerPort: 8080
        env:
        - name: DB_PASSWORD
          value: "P@ssw0rd2023!"
        - name: JWT_SECRET
          value: "hvt-jwt-prod-secret"
```

[AFTER]
A versão moderna precisa ter:

- **alta disponibilidade**;
- **imagem versionada** (nada de `latest`);
- **secrets fora do manifest**;
- **resource requests e limits**;
- **liveness e readiness probes**;
- **`securityContext` não-root**;
- demais práticas de produção que hoje são padrão na empresa.

[BRIDGE]
Crie um deployment atualizado seguindo as melhores práticas do mercado.
Adicione as práticas modernas adotadas pela empresa.
Forneça um plano de ação visando a substituição do Deployment antigo pelo moderno, possíveis problemas e formas de contornar.


---

## 🤖 Campo 2: Modelo

* **Modelo:** `ChatGPT GPT-5.5`
* **Justificativa de Escolha:** Costuma entregar manifesto Kubernetes completo numa rodada só (probes, securityContext, requests/limits, rollout) e ainda traz Secret/HPA/PDB como recursos auxiliares sem precisar pedir.

---

## 📄 Campo 3: Output
A versão atual apresenta diversos riscos comuns em ambientes de produção:

* Apenas 1 réplica (sem alta disponibilidade).
* Uso de `latest` (sem rastreabilidade).
* Segredos expostos em texto puro.
* Sem requests/limits.
* Sem probes.
* Executando potencialmente como root.
* Sem estratégia de rollout adequada.
* Sem controles de disponibilidade durante atualizações.
* Sem política de distribuição entre nós.

---

# Deployment Modernizado

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: chronos-api
  namespace: production
  labels:
    app: chronos-api
spec:
  replicas: 3

  revisionHistoryLimit: 10

  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxUnavailable: 0
      maxSurge: 1

  selector:
    matchLabels:
      app: chronos-api

  template:
    metadata:
      labels:
        app: chronos-api

    spec:
      automountServiceAccountToken: false

      securityContext:
        runAsNonRoot: true
        runAsUser: 10001
        runAsGroup: 10001
        fsGroup: 10001

      topologySpreadConstraints:
      - maxSkew: 1
        topologyKey: kubernetes.io/hostname
        whenUnsatisfiable: ScheduleAnyway
        labelSelector:
          matchLabels:
            app: chronos-api

      containers:
      - name: api

        image: registry.company.com/chronos-api:v2.4.3

        imagePullPolicy: IfNotPresent

        ports:
        - name: http
          containerPort: 8080

        env:
        - name: DB_PASSWORD
          valueFrom:
            secretKeyRef:
              name: chronos-api-secret
              key: db-password

        - name: JWT_SECRET
          valueFrom:
            secretKeyRef:
              name: chronos-api-secret
              key: jwt-secret

        resources:
          requests:
            cpu: 250m
            memory: 512Mi

          limits:
            cpu: "1"
            memory: 1Gi

        securityContext:
          allowPrivilegeEscalation: false
          readOnlyRootFilesystem: true

          capabilities:
            drop:
            - ALL

        startupProbe:
          httpGet:
            path: /health
            port: http
          failureThreshold: 30
          periodSeconds: 10

        readinessProbe:
          httpGet:
            path: /ready
            port: http
          initialDelaySeconds: 10
          periodSeconds: 10
          timeoutSeconds: 2
          failureThreshold: 3

        livenessProbe:
          httpGet:
            path: /health
            port: http
          initialDelaySeconds: 30
          periodSeconds: 15
          timeoutSeconds: 2
          failureThreshold: 3

      terminationGracePeriodSeconds: 30
```

---

# Secret Externo

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: chronos-api-secret
  namespace: production
type: Opaque
stringData:
  db-password: "<valor>"
  jwt-secret: "<valor>"
```

Em ambientes maduros o ideal é nem manter Secrets no Git.

Alternativas mais modernas:

* AWS Secrets Manager + External Secrets Operator
* Hashicorp Vault
* Azure Key Vault
* Google Secret Manager

Exemplo de fluxo recomendado:

```text
AWS Secrets Manager
        ↓
External Secrets Operator
        ↓
Kubernetes Secret
        ↓
Pod
```

---

# PodDisruptionBudget

Evita indisponibilidade durante manutenção dos nós.

```yaml
apiVersion: policy/v1
kind: PodDisruptionBudget
metadata:
  name: chronos-api
  namespace: production
spec:
  minAvailable: 2

  selector:
    matchLabels:
      app: chronos-api
```

---

# Horizontal Pod Autoscaler

Permite crescimento automático.

```yaml
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: chronos-api
  namespace: production
spec:
  minReplicas: 3
  maxReplicas: 10

  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: chronos-api

  metrics:
  - type: Resource
    resource:
      name: cpu
      target:
        type: Utilization
        averageUtilization: 70
```

---

# NetworkPolicy

Caso a empresa utilize isolamento de rede.

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: chronos-api
  namespace: production
spec:
  podSelector:
    matchLabels:
      app: chronos-api

  policyTypes:
  - Ingress
  - Egress

  ingress:
  - from:
    - namespaceSelector:
        matchLabels:
          name: ingress

  egress:
  - to:
    - namespaceSelector:
        matchLabels:
          name: database
```

---

# Plano de Migração

## 1. Levantamento

Mapear:

* endpoints de healthcheck existentes;
* dependências externas;
* consumo real de CPU/Memória;
* comportamento de startup da aplicação;
* necessidade de filesystem temporário.

---

## 2. Externalização dos Secrets

Antes da migração:

```text
Manifest atual
↓
Criar Secret
↓
Validar aplicação
↓
Remover valores hardcoded
```

Risco:

```text
Aplicação depende de variável diferente
```

Mitigação:

```text
Validar em homologação antes do deploy
```

---

## 3. Adicionar Readiness Probe

Primeiro implantar apenas readiness.

Risco:

```text
Probe retornando 500
```

Resultado:

```text
Pod nunca recebe tráfego
```

Mitigação:

```text
Validar endpoint manualmente
```

---

## 4. Adicionar Liveness

Somente após validar readiness.

Risco:

```text
Loop infinito de restart
```

Mitigação:

```text
Usar startupProbe
```

---

## 5. Aumentar Réplicas

Migrar:

```text
1 → 2 → 3 réplicas
```

Validar:

* balanceamento;
* sessões;
* conexões com banco;
* consumo de recursos.

---

## 6. Habilitar Rolling Update

Com:

```yaml
maxUnavailable: 0
maxSurge: 1
```

Garantia:

```text
Zero downtime deployment
```

---

## 7. Habilitar HPA

Após coletar métricas reais.

Risco:

```text
requests mal definidos
```

Resultado:

```text
autoscaling incorreto
```

Mitigação:

```text
coletar métricas por alguns dias
```

---

# Possíveis Problemas Durante a Migração

| Problema                                  | Causa                                  | Mitigação                        |
| ----------------------------------------- | -------------------------------------- | -------------------------------- |
| Pods reiniciando                          | Liveness incorreta                     | Adicionar StartupProbe           |
| Pods nunca ficam Ready                    | Endpoint inválido                      | Validar manualmente              |
| Aumento de custo                          | Mais réplicas                          | Ajustar requests/limits          |
| Banco sobrecarregado                      | Mais conexões simultâneas              | Implementar pool de conexões     |
| Deploy travado                            | Readiness falhando                     | Rollback automático              |
| Escalabilidade não funciona               | Metrics Server ausente                 | Validar stack de observabilidade |
| Aplicação quebra com filesystem read-only | Escrita em `/tmp` ou diretórios locais | Criar `emptyDir` específico      |

---

Se eu estivesse revisando esse ambiente como SRE/DevOps em uma fintech, ainda adicionaria **External Secrets Operator**, **Kyverno/Gatekeeper para enforcement dos padrões**, **monitoramento via OpenTelemetry**, **PodDisruptionBudget**, **HPA** e uma política de **GitOps com Argo CD** para garantir que nenhuma aplicação nova consiga entrar em produção sem atender esses requisitos mínimos.


---

## ⚖️ Campo 4: Justificativa

Usando o modelo B-A-B, no Before eu trouxe o cenário atual, no After eu trouxe o cenário esperado e no Bridge eu detalhei alguns pontos para efetuar tudo isto. 