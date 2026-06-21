# 06 — Self-Consistency: validar pela convergência

> **Self-Consistency gera múltiplas análises independentes e compara onde convergem (confiança alta) e onde divergem (precisa investigar mais).** É o "buscar uma segunda (e terceira) opinião" aplicado ao modelo.

---

## 1. O que é Self-Consistency?

A técnica pede ao modelo **várias análises independentes** do mesmo problema e depois compara os resultados:

```text
              ┌─► Análise 1 ─┐
problema ─────┼─► Análise 2 ─┼─► comparar ─► CONVERGÊNCIA = confiança alta
              └─► Análise 3 ─┘              DIVERGÊNCIA  = investigar mais
```

- **Convergência** entre análises independentes → sinal forte de que a conclusão está correta.
- **Divergência** → ponto de incerteza que merece investigação adicional.

> **⚠️ Importante:** para resultados mais fiéis à técnica, execute cada análise em **conversas separadas** ou peça explicitamente ao modelo para **não enviesar** as respostas entre si ("não considere as outras análises").

## 2. Por que funciona

Um raciocínio único pode seguir um caminho errado e parecer convincente. Ao gerar **caminhos independentes**, erros aleatórios tendem a divergir, enquanto a resposta correta tende a se repetir. A **convergência espontânea** entre perspectivas diferentes é o sinal mais robusto.

## 3. Quando usar

Diagnósticos críticos, decisões de alto impacto, análise de causa raiz, situações onde você precisa **medir confiança**, não só obter uma resposta. Muito útil em **war rooms** e revisões de arquitetura.

---

## 4. Demo não-técnica — diagnóstico do carro (3 mecânicos)

Aplica "buscar segunda opinião" a um problema cotidiano. Mostra a essência: convergência = confiança, divergência = investigar.

```text
PROMPT
------
Meu carro está fazendo um barulho metálico ao frear, principalmente em
baixa velocidade. O carro tem 4 anos e 60.000 km rodados. Nunca troquei
as pastilhas.

Gere 3 diagnósticos independentes, como se fossem 3 mecânicos diferentes
analisando o problema separadamente. Cada mecânico deve raciocinar passo
a passo e chegar ao seu próprio diagnóstico principal, sem considerar a
opinião dos outros.

Depois, compare os 3 diagnósticos e indique: onde convergem (confiança
alta) e onde divergem (precisa investigar mais).
```

---

## 5. Demo técnica 1 — migração com 3 perfis profissionais

Três perfis (Developer, Sysadmin, Arquiteto Cloud) analisam o mesmo cenário de forma independente. A convergência espontânea entre perspectivas diferentes é o sinal mais robusto.

```text
PROMPT
------
Temos uma aplicação monolítica em Java (Spring Boot), rodando em 2
servidores on-premise, com PostgreSQL.

Equipe de 3 devs, prazo de 4 meses, budget limitado. A aplicação tem 80k
usuários/mês e picos sazonais.

Precisamos migrar pra AWS. Gere 3 análises independentes dos seguintes
perfis, cada um avaliando o cenário separadamente:

- Developer: foco em código, frameworks, refatoração e ciclo de desenvolvimento
- Sysadmin: foco em infraestrutura, operação, monitoramento e estabilidade
- Arquiteto Cloud: foco em serviços gerenciados, escalabilidade e custo a longo prazo

Cada profissional deve raciocinar passo a passo considerando:
- Equipe
- Prazo
- Budget
- Perfil da aplicação
- Recomendar a melhor estratégia de migração

Não considere as outras análises.

Depois, compare as 3 recomendações e indique: onde convergem (confiança
alta) e onde divergem (precisa investigar mais).
```

---

## 6. Demo técnica 2 — war room de incidente em produção

O prompt mais poderoso da aula: simula um war room real. A convergência entre Dev, Sysadmin e Arquiteto entrega não só a causa raiz, mas o **plano completo** (fix imediato + paliativo + redesign).

```text
PROMPT
------
Incidente em produção — API REST com latência degradada.

Cenário:
- API REST em Java (Spring Boot) rodando em 3 containers no ECS
- Banco de dados PostgreSQL no RDS (db.r5.large, max_connections=200)
- Deploy realizado há 2 horas com nova feature de relatórios
- Latência normal: p95 ~200ms. Latência atual: p95 ~5.000ms
- Alerta disparou há 30 minutos

Logs:
[ver bloco LOGS abaixo]

Gere 3 diagnósticos independentes dos seguintes perfis, cada um
analisando os logs separadamente:

- Desenvolvedor sênior: foco em código, queries, lógica de aplicação e o deploy recente
- Sysadmin sênior: foco em recursos, capacidade, connection pool e saúde da infraestrutura
- Arquiteto Cloud: foco em design do sistema, gargalos arquiteturais e padrões de escalabilidade

Cada profissional deve raciocinar passo a passo e chegar à sua hipótese
principal para a causa raiz, sem considerar as outras análises.

Depois, compare os 3 diagnósticos e indique: onde convergem (confiança
alta) e onde divergem (precisa investigar mais).
```

### LOGS do incidente (latência degradada)

```log
[2024-03-15 14:32:10] WARN app-01: Slow query detected: SELECT * FROM orders JOIN order_items ON... (duration: 4.2s)
[2024-03-15 14:32:15] WARN app-02: Slow query detected: SELECT * FROM orders JOIN order_items ON... (duration: 3.8s)
[2024-03-15 14:32:18] WARN app-03: HikariPool - Connection pool exhausted, waiting for available connection
[2024-03-15 14:32:22] ERROR app-01: Request timeout after 5000ms - GET /api/v2/reports/monthly
[2024-03-15 14:32:25] WARN app-02: HikariPool - Connection pool exhausted, waiting for available connection
[2024-03-15 14:32:30] INFO rds-monitor: CPU utilization at 94%, active connections: 195/200
[2024-03-15 14:32:35] ERROR app-03: Request timeout after 5000ms - GET /api/v2/reports/monthly
[2024-03-15 14:32:40] WARN app-01: Thread pool near capacity: 48/50 active threads
[2024-03-15 14:32:45] ERROR alb: 5xx errors spike — 23% of requests returning 503
[2024-03-15 14:33:00] INFO on-call: PagerDuty alert triggered — P1 incident opened
```

**Convergência esperada:** a nova feature de relatórios (`/api/v2/reports/monthly`) introduziu uma query lenta (`SELECT *` com JOIN), que segura conexões, satura o HikariPool e leva o RDS a 94% de CPU e 195/200 conexões. Os três perfis tendem a convergir na query do deploy recente como gatilho — alta confiança.

---

## 7. Boas práticas

- **Isole as análises** (conversas separadas ou instrução de não enviesar).
- **Use perfis/perspectivas diferentes** para tornar a convergência mais significativa.
- **Trate divergência como sinal**, não ruído — é onde está a incerteza real.
- **Custo:** gera mais tokens/chamadas; reserve para decisões que justificam o esforço.
- Combine com **CoT** dentro de cada análise para raciocínio rastreável.
