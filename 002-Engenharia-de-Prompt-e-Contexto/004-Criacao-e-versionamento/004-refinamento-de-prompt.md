# Código da Aula

Esta aula demonstra como usar um meta-prompt para refinar um prompt já existente — em vez de gerar do zero. O instrutor parte do prompt de auditoria de Dockerfile construído nas aulas anteriores e pede à IA para refiná-lo com objetivos específicos: reduzir consumo de tokens e ajustar o output esperado.
Meta-Prompt — Engenheiro de Prompt (refinador)

Meta-prompt em formato persona que recebe um prompt existente e instruções de melhoria, e devolve a versão refinada em Markdown. Cole o prompt original no campo [prompt original a ser refinado] e ajuste a seção Melhorias conforme o que precisa mudar (custo, formato, escopo, persona, etc.).


Você deve atuar como um engenheiro de prompt responsável por aprimorar
prompts já construídos.

Receba o prompt, faça uma análise do conteúdo desse prompt e analise
o que o usuário quer que seja alterado.

Depois dessa reflexão, eu quero que você retorne um prompt atendendo
às expectativas do usuário em um formato de arquivo Markdown.

Prompt:
[prompt original a ser refinado]

Melhorias:
- Eu quero que o consumo de tokens seja reduzido. Hoje consome
  aproximadamente 2 mil tokens de input e 8 mil tokens de output.
- Além da análise, deve retornar um Dockerfile seguindo as boas práticas.

Output:
Retorne apenas o prompt alterado em um arquivo Markdown.







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






- Testando

Você deve atuar como um engenheiro de prompt responsável por aprimorar
prompts já construídos.

Receba o prompt, faça uma análise do conteúdo desse prompt e analise
o que o usuário quer que seja alterado.

Depois dessa reflexão, eu quero que você retorne um prompt atendendo
às expectativas do usuário em um formato de arquivo Markdown.

Prompt:
[You are an expert DevOps engineer and security specialist with deep knowledge of Docker best practices, Python application deployment, and container security hardening.

You will be analyzing a Dockerfile for a Python application. Here is the Dockerfile:

<dockerfile>
{{DOCKERFILE}}
</dockerfile>

Your task is to analyze this Dockerfile and identify security vulnerabilities and performance optimization opportunities. Your analysis should be thorough and actionable for backend developers who will implement these changes.

Before providing your final recommendations, use a scratchpad to systematically analyze the Dockerfile:

<scratchpad>
In your scratchpad, work through the following analysis:

1. **Security Issues**: Examine the Dockerfile for:
   - Base image vulnerabilities (outdated versions, unofficial images)
   - Running as root user
   - Exposed secrets or credentials
   - Unnecessary packages or dependencies
   - Missing security updates
   - Insecure file permissions
   - Exposed ports that shouldn't be public

2. **Performance Opportunities**: Look for:
   - Layer optimization (combining RUN commands, order of operations)
   - Build cache inefficiencies
   - Unnecessary files in the image
   - Multi-stage build opportunities
   - Large base images that could be replaced with slim/alpine variants
   - Missing .dockerignore patterns
   - Inefficient COPY/ADD operations

3. **Prioritization**: For each issue found, assess:
   - Severity (Critical/High/Medium/Low)
   - Impact on security or performance
   - Ease of implementation

Organize your findings and determine the priority order.
</scratchpad>

After your analysis, provide your recommendations in the following format:

<recommendations>
Present a prioritized list of recommendations, ordered from most critical to least critical. For each recommendation, include:

**[Priority Level] - [Brief Title]**
- **Issue**: Describe what the current problem is
- **Risk/Impact**: Explain why this matters (security risk, performance impact, or both)
- **Recommendation**: Provide the specific change to make
- **Implementation**: Give concrete code example or specific instruction

Use these priority levels:
- 🔴 CRITICAL: Severe security vulnerabilities or major performance bottlenecks
- 🟠 HIGH: Significant security concerns or notable performance issues
- 🟡 MEDIUM: Moderate improvements to security or performance
- 🟢 LOW: Minor optimizations or best practice alignments

Number each recommendation (1, 2, 3, etc.) in priority order.
</recommendations>

Your final output should contain only the <recommendations> section with the prioritized list. Do not include the scratchpad in your final response.]

Melhorias:
- Eu quero que o consumo de tokens seja reduzido. Hoje consome
  aproximadamente 2 mil tokens de input e 8 mil tokens de output.
- Além da análise, deve retornar um Dockerfile seguindo as boas práticas.

Output:
Retorne apenas o prompt alterado em um arquivo Markdown.











**O que mudou e por quê:**

- **Corte de tokens de output**: removi a exigência de mostrar o scratchpad (mesmo "oculto" no final, ele ainda é gerado como tokens), limitei os achados a no máximo 6, e troquei o formato de bullet multi-linha (Issue/Risk/Recommendation/Implementation) por **uma linha compacta por achado**. O campo "Implementation" com código foi eliminado da seção de achados — todo o detalhe de implementação agora vive só no Dockerfile final, evitando repetição de conteúdo.
- **Novo requisito atendido**: adicionei a seção 2 (`Optimized Dockerfile`), que obriga o modelo a retornar o Dockerfile corrigido completo, incorporando todos os fixes listados.
- **Restrições explícitas de concisão**: adicionei uma seção `Constraints` reforçando que a resposta deve ser um checklist, não um relatório, e proibindo duplicar detalhes entre as duas seções.

Isso deve reduzir bastante o output (menos texto redundante, achados resumidos, sem scratchpad visível) mantendo a qualidade da análise e ainda entregando o artefato prático (Dockerfile pronto).






- Resultado:

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






- Tokens
economizou
input em 889
output em 954


