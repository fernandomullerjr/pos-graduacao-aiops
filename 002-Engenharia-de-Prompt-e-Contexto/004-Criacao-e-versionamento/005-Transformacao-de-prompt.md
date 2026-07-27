
# 005-Transformacao-de-prompt

Esta aula demonstra um meta-prompt que recebe um prompt-base e o reescreve aplicando uma ou mais técnicas (Chain-of-Thought, Tree-of-Thoughts, etc.), gerando versões comparáveis em arquivos Markdown separados. A vantagem é poder comparar técnicas executando a mesma tarefa com prompts equivalentes.

## Meta-Prompt de Transformação por Técnica

Meta-prompt em formato persona. Recebe o prompt-base (no caso da aula, o Dockerfile Auditor refinado na Aula 04) e uma lista de técnicas. Devolve um arquivo Markdown para cada técnica solicitada, mantendo o contexto e objetivo original mas estruturando o raciocínio de acordo com a técnica.

~~~~
Você é um especialista em engenharia de prompt e o seu papel é receber
determinado prompt inserido pelo usuário e fazer a transformação desse
prompt utilizando técnicas que o usuário vai solicitar.

Você deve sempre manter o contexto e o objetivo do prompt, mas
utilizando ele como base para criar as técnicas definidas para poder
gerar uma base de comparação e analisar qual vai ter uma melhor
performance.

[prompt-base a ser transformado]

Técnicas: Chain of Thoughts, Tree of Thoughts
Output: criar um arquivo markdown para cada técnica solicitada
 
~~~~






## Dockerfile de exemplo (input para validar o prompt gerado)

Dockerfile propositalmente problemático usado para testar o prompt gerado. Contém vários antipatterns típicos: imagem sem tag, credenciais em variáveis de ambiente, ausência de usuário não-root, ausência de multi-stage, instalação de dependências de build em runtime. Cole esse arquivo na variável {DOCKERFILE} (ou equivalente) do prompt gerado para reproduzir a demo.

~~~~dockerfile
FROM python
WORKDIR /app
COPY . /app
RUN apt-get update && apt-get install -y curl build-essential
RUN pip install -r requirements.txt
ENV DB_PASSWORD=admin123
ENV API_KEY=sk-production-key-9f2a3b4c5d6e
EXPOSE 5000
CMD python app.py
~~~~






- Criando o personalizado:


~~~~
Você é um especialista em engenharia de prompt e o seu papel é receber
determinado prompt inserido pelo usuário e fazer a transformação desse
prompt utilizando técnicas que o usuário vai solicitar.

Você deve sempre manter o contexto e o objetivo do prompt, mas
utilizando ele como base para criar as técnicas definidas para poder
gerar uma base de comparação e analisar qual vai ter uma melhor
performance.

[

# System Prompt: Dockerfile Security & Performance Reviewer

You are an expert DevOps engineer and security specialist with deep knowledge of Docker best practices, Python application deployment, and container security hardening.

You will analyze a Dockerfile for a Python application:

<dockerfile>
{{DOCKERFILE}}
</dockerfile>

## Task
Identify the **most impactful** security and performance issues (maximum 6 — only report what genuinely applies to this Dockerfile, do not pad the list), then produce a corrected, production-ready Dockerfile.

Focus areas (only mention the ones that actually apply):
- **Security**: base image (outdated/unofficial), running as root, exposed secrets/credentials, unnecessary packages, missing security updates, insecure file permissions, unnecessarily exposed ports
- **Performance**: layer/cache optimization, multi-stage build opportunities, slim/alpine base image, missing `.dockerignore`, inefficient `COPY`/`ADD`

Before answering, internally triage each issue by severity and ease of fix. Do **not** show this internal reasoning, a scratchpad, or any intermediate analysis in your response — go straight to the final output below.

## Output format
Respond with **exactly** these two sections and nothing else — no preamble, no closing remarks, no restating the task.

### 1. Findings
Ordered from most to least critical, one compact line per issue (no multi-paragraph explanations, no code snippets here — code belongs only in section 2):

`[Emoji] **Priority – Title**: Issue → Risk/Impact → Fix` (each part a short phrase, not a full sentence)

Priority emojis:
- 🔴 CRITICAL
- 🟠 HIGH
- 🟡 MEDIUM
- 🟢 LOW

### 2. Optimized Dockerfile
A single code block with the full corrected Dockerfile, implementing every fix listed in section 1. Add inline comments only where the change isn't self-explanatory.

## Constraints
- Be concise: findings should read like a checklist, not a report.
- Never duplicate implementation detail between the two sections — detailed fixes live only in the Dockerfile.
- Do not include a scratchpad, reasoning trace, or any text outside the two numbered sections.

]

Técnicas: Chain of Thoughts, Tree of Thoughts
Output: criar um arquivo markdown para cada técnica solicitada
 
~~~~






Vou criar dois arquivos markdown, cada um aplicando a técnica solicitada (Chain of Thought e Tree of Thoughts) sobre o prompt original, mantendo o mesmo objetivo, escopo e formato de saída — apenas alterando o processo de raciocínio interno exigido do modelo.Prontos os dois arquivos. Resumo do que mudou em cada um:

- **CoT (`prompt_chain_of_thought.md`)**: transforma a "triagem interna" vaga do original em uma sequência linear e obrigatória de 8 passos (parsear → escanear segurança → escanear performance → consolidar → ranquear → limitar a 6 → desenhar fix → verificar). Bom para consistência e para não pular categorias de problema.

- **ToT (`prompt_tree_of_thoughts.md`)**: em vez de um caminho único, faz o modelo gerar 2-3 estratégias de correção (patch mínimo, rebuild multi-stage, rebuild hardened), avaliá-las por critérios explícitos (segurança, performance, compatibilidade, risco), podar as piores e mesclar/escolher a melhor. Mais indicado quando há trade-offs reais entre abordagens de correção.

Em ambos os casos o formato de saída (Findings + Dockerfile) e as regras de "nunca mostrar o raciocínio" foram mantidos idênticos ao prompt original — só o processo interno de análise mudou, para você poder comparar qual gera findings mais precisos/consistentes na prática.









# Técnica aplicada: Chain of Thought (CoT)

> **O que mudou em relação ao original:** o prompt original já pedia uma "triagem interna" antes de responder, mas de forma vaga ("internally triage each issue by severity and ease of fix"). Na versão CoT abaixo, essa triagem foi transformada em uma **sequência explícita e ordenada de passos de raciocínio** que o modelo deve seguir internamente, um de cada vez, antes de gerar a resposta final. Isso reduz a chance de o modelo pular etapas (ex: avaliar performance sem terminar de avaliar segurança) e melhora a consistência da priorização. O raciocínio continua **oculto** — apenas o resultado final é exposto, exatamente como no prompt original.

---

## Prompt transformado

```
# System Prompt: Dockerfile Security & Performance Reviewer

You are an expert DevOps engineer and security specialist with deep knowledge of Docker best practices, Python application deployment, and container security hardening.

You will analyze a Dockerfile for a Python application:

<dockerfile>
{{DOCKERFILE}}
</dockerfile>

## Reasoning Process (Chain of Thought — internal only, never shown)

Before producing any output, work through the following steps **in order**, one at a time, without skipping ahead:

1. **Parse structure**: Read the Dockerfile top to bottom. Note base image, stages (if any), instruction order, and any environment/build context clues.
2. **Scan for security issues**: Go through each security focus area one by one (base image, root user, exposed secrets, unnecessary packages, missing security updates, insecure file permissions, exposed ports). For each, decide "applies" or "does not apply" and note the concrete evidence (line/instruction) that justifies the decision.
3. **Scan for performance issues**: Repeat the same one-by-one check for each performance focus area (layer/cache optimization, multi-stage opportunity, slim/alpine base, missing `.dockerignore`, inefficient `COPY`/`ADD`). Note evidence for each.
4. **Consolidate**: Merge all issues marked "applies" into a single candidate list. Discard anything that doesn't have concrete evidence in the actual Dockerfile — do not invent issues to fill space.
5. **Rank**: Order the candidate list by severity (CRITICAL > HIGH > MEDIUM > LOW). Where two issues share severity, rank the easier/faster fix first.
6. **Cap the list**: Keep only the top 6 most impactful issues. If fewer than 6 genuinely apply, keep only those — never pad.
7. **Design the fix**: For the retained issues, draft the corrected Dockerfile step by step, confirming each fix doesn't conflict with another (e.g., a multi-stage build fix must not reintroduce a root-user issue).
8. **Verify before answering**: Check that every issue in the final list has a corresponding fix in the Dockerfile, and that the output matches the required format exactly.

Do **not** show this reasoning, a scratchpad, or any intermediate analysis in your response — go straight to the final output below.

## Output format
Respond with **exactly** these two sections and nothing else — no preamble, no closing remarks, no restating the task.

### 1. Findings
Ordered from most to least critical, one compact line per issue (no multi-paragraph explanations, no code snippets here — code belongs only in section 2):

`[Emoji] **Priority – Title**: Issue → Risk/Impact → Fix` (each part a short phrase, not a full sentence)

Priority emojis:
- 🔴 CRITICAL
- 🟠 HIGH
- 🟡 MEDIUM
- 🟢 LOW

### 2. Optimized Dockerfile
A single code block with the full corrected Dockerfile, implementing every fix listed in section 1. Add inline comments only where the change isn't self-explanatory.

## Constraints
- Be concise: findings should read like a checklist, not a report.
- Never duplicate implementation detail between the two sections — detailed fixes live only in the Dockerfile.
- Do not include a scratchpad, reasoning trace, or any text outside the two numbered sections.
```

---

## Por que essa técnica ajuda aqui
- **Reduz omissões**: forçar uma varredura item a item de cada área (segurança, depois performance) evita que o modelo se prenda a 1-2 problemas óbvios e ignore outros menos visíveis.
- **Melhora a consistência da priorização**: o critério de ranking (severidade → facilidade de fix como desempate) fica explícito, então execuções repetidas tendem a convergir para a mesma ordem.
- **Reduz alucinação de findings**: o passo 4 exige evidência concreta antes de aceitar um item, o que ajuda a cumprir a regra "não preencher a lista" do prompt original.
- **Custo**: mais tokens de raciocínio interno (não visíveis), mas resposta final continua igual em tamanho/formato.













- Testando


- Saída do COT:

1. Findings

🔴 CRITICAL – Hardcoded Secrets: DB_PASSWORD/API_KEY baked into image layers → credentials leak via image history/registry → inject at runtime via secrets manager or --env-file, never ENV in Dockerfile.
🟠 HIGH – Container Runs as Root: No USER instruction → full root access if compromised → add dedicated non-root user and switch with USER.
🟠 HIGH – Unpinned Bloated Base Image: FROM python uses latest full-size tag → large attack surface, non-reproducible builds → pin to specific slim version (e.g. python:3.12-slim).
🟡 MEDIUM – No Multi-Stage Build: build-essential/curl persist in final image → unnecessary size and tooling for attackers → separate builder stage, copy only artifacts to runtime stage.
🟡 MEDIUM – Poor Layer Caching: COPY . /app before dependency install → full rebuild on any code change → copy requirements.txt first, install deps, then copy rest.
🟢 LOW – Shell-Form CMD & No Apt Cleanup: CMD python app.py doesn't handle signals properly; apt lists left in layer → zombie processes on SIGTERM, bloated layer → use exec-form CMD and rm -rf /var/lib/apt/lists/*.

2. Optimized Dockerfile
# ---------- Builder stage ----------
FROM python:3.12-slim AS builder
WORKDIR /app
# Install build deps only in builder stage (not shipped to final image)
RUN apt-get update && apt-get install -y --no-install-recommends \
    build-essential curl \
    && rm -rf /var/lib/apt/lists/*
# Copy only requirements first for better layer caching
COPY requirements.txt .
RUN pip install --no-cache-dir --user -r requirements.txt
# ---------- Final runtime stage ----------
FROM python:3.12-slim
WORKDIR /app
# Create non-root user
RUN useradd --create-home --shell /bin/bash appuser
# Copy installed packages from builder (no build tools in final image)
COPY --from=builder /root/.local /home/appuser/.local
# Copy application code (after deps for caching)
COPY --chown=appuser:appuser . /app
# Ensure user-installed packages are on PATH
ENV PATH=/home/appuser/.local/bin:$PATH
# Secrets injected at runtime, NOT hardcoded:
# docker run -e DB_PASSWORD=*** -e API_KEY=*** ...
# or use Docker secrets / a secrets manager
USER appuser
EXPOSE 5000
# Exec form for proper signal handling
CMD ["python", "app.py"]





- Saída do TOT:

1. Findings

🔴 CRITICAL – Hardcoded Secrets in ENV: DB_PASSWORD/API_KEY baked into image layers → credentials extractable via docker history/inspect → pass via runtime env vars or secrets manager, never bake into image.
🟠 HIGH – Container Runs as Root: No USER directive → full root access if container compromised → create/use unprivileged user.
🟠 HIGH – Unpinned Bloated Base Image: FROM python uses latest full-size tag → large attack surface, non-reproducible builds → pin to slim version-specific tag (e.g. python:3.12-slim).
🟡 MEDIUM – Unnecessary Build Tools & No Cleanup: curl/build-essential installed and apt cache left behind → bloats image, extra attack surface → install only if needed, use --no-install-recommends and clean apt lists.
🟡 MEDIUM – Poor Layer Caching / Missing .dockerignore: Full COPY . /app before installing deps invalidates cache on every code change, may leak local files (.git, venv) → copy requirements.txt first, add .dockerignore.
🟢 LOW – Shell-form CMD: CMD python app.py runs via shell, doesn't forward signals properly → use exec-form JSON array.

2. Optimized Dockerfile
# Pinned slim base image reduces attack surface and image size
FROM python:3.12-slim
# Create non-root user for runtime
RUN groupadd -r appuser && useradd -r -g appuser appuser
WORKDIR /app
# Install only required system deps (curl kept only if actually needed at runtime)
RUN apt-get update && apt-get install -y --no-install-recommends curl \
    && rm -rf /var/lib/apt/lists/*
# Copy dependency manifest first to leverage Docker layer caching
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt
# Copy application code (respect .dockerignore to exclude .git, venv, secrets, etc.)
COPY . /app
# Ensure app files owned by non-root user
RUN chown -R appuser:appuser /app
# Secrets must be injected at runtime, e.g.:
# docker run -e DB_PASSWORD=*** -e API_KEY=*** ...
# or via --env-file / orchestrator secrets manager
USER appuser
EXPOSE 5000
# Exec-form CMD for proper signal handling
CMD ["python", "app.py"]






- Verificando
testei os 2 prompts no workbench do claude, ambos tiveram outputs parecidos e parece nao ter usado muitas tecnicas.
AVALIE se houve algo errado ou tá certo isto






Não tem nada "quebrado" tecnicamente — mas o seu teste tem limitações metodológicas que explicam por que os outputs pareceram parecidos. Vamos por partes.

## 1. Na verdade, houve diferenças — só que sutis

Comparando linha a linha:

| Aspecto | CoT | ToT |
|---|---|---|
| Arquitetura da correção | **Multi-stage** (builder + runtime) | **Single-stage** |
| Tratamento do `build-essential`/`curl` | Removido completamente do runtime (fica só no builder) | Avaliou se `curl` é necessário em runtime e manteve |
| `.dockerignore` | Não virou finding próprio (ficou implícito em "layer caching") | Virou finding explícito, junto com layer caching |
| CMD/apt cleanup | Agrupou num único finding LOW | CMD ficou separado, cleanup de apt virou parte do finding de "unnecessary build tools" |

Isso **é** o comportamento esperado de cada técnica:
- O **CoT** seguiu o caminho linear e "óbvio" (multi-stage é a resposta padrão de manual para "tem build tools no final").
- O **ToT**, em teoria, deveria ter gerado as branches (patch mínimo / multi-stage / hardened), avaliado e escolhido — e o resultado sugere que ele **escolheu a branch "patch mínimo"** em vez da multi-stage, inclusive fazendo uma avaliação explícita ("curl kept only if actually needed at runtime") que é uma marca de comparação de trade-off, não de execução automática de checklist.

Ou seja: divergiu, só que num nível arquitetural (single vs multi-stage), não nos findings de topo — e isso é esperado.

## 2. Por que os top findings são idênticos (e isso é bom, não ruim)

🔴 Secrets, 🟠 root, 🟠 base image não pinado são os 3 problemas **objetivamente mais graves** de qualquer Dockerfile com esse padrão. Não importa a técnica de raciocínio usada — se o modelo divergisse nesses 3 achados entre CoT e ToT, isso seria sinal de **inconsistência ruim**, não de "uso de técnica". CoT/ToT influenciam mais a *exploração de soluções alternativas* e a *ordem/profundidade da análise*, não a detecção do óbvio.

## 3. O problema real do seu teste

Aqui estão os motivos prováveis pelos quais a diferença pareceu pequena:

1. **Você rodou só 1x cada.** CoT e ToT existem para melhorar variância entre execuções e reduzir erros em casos ambíguos — não dá pra avaliar isso com N=1. O teste correto é rodar cada prompt 5–10x com o mesmo Dockerfile e comparar consistência/cobertura de issues, não só olhar uma amostra.

2. **O raciocínio fica oculto por design** (ambos os prompts pedem "never show reasoning/branches"). Você não tem como confirmar se o ToT realmente gerou 2-3 branches internamente e descartou — só pode inferir pelo resultado final. Para depurar de verdade, vale criar uma **variante de debug** temporária que expõe o scratchpad, só para validar se o modelo está de fato seguindo os passos/branches instruídos, e depois remover essa exposição na versão de produção.

3. **O Dockerfile de teste é "genérico demais".** Ele tem um conjunto de problemas clássicos de manual (secrets em ENV, root, imagem não pinada, sem multi-stage). Para esse tipo de caso, praticamente qualquer técnica de prompting converge para a mesma resposta de best practice, porque não há trade-off real em jogo. **ToT só mostra valor de verdade quando existem caminhos genuinamente divergentes e válidos** — ex: um Dockerfile onde multi-stage vs. distroless vs. hardened teriam custos/benefícios diferentes reais (não apenas "sempre faça multi-stage").

4. **Claude Sonnet já "pensa" internamente por padrão.** Modelos atuais com raciocínio nativo (extended thinking) já fazem uma espécie de chain-of-thought implícito mesmo sem scaffold explícito no prompt. Isso reduz o ganho marginal de instruir CoT/ToT explicitamente — o efeito é mais visível em modelos mais fracos ou sem essa capacidade nativa.

## Recomendação para validar de verdade

- Rode cada prompt **múltiplas vezes** (mesmo Dockerfile) e compare taxa de acerto/cobertura, não uma única amostra.
- Teste com um Dockerfile **mais ambíguo/complexo**, onde exista mais de uma solução legitimamente boa (ex: app com múltiplas dependências pesadas, incerteza sobre se precisa de compilação nativa, ambiente onde secrets podem vir de fontes diferentes) — é aí que o ToT deveria brilhar mais.
- Compare contra uma **baseline sem técnica nenhuma** (zero-shot puro) — se CoT/ToT não superarem o zero-shot em nada mensurável nesse caso, isso é um resultado válido: significa que para *esse tipo de tarefa* (checklist determinístico), scaffolds de raciocínio explícito têm retorno marginal baixo, e isso é uma conclusão útil, não um erro seu.