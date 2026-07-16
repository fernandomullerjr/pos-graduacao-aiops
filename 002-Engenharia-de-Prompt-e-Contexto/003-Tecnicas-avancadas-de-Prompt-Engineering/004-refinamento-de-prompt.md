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