
# Código da Aula

Esta aula demonstra como gerar um prompt completo a partir de uma descrição em linguagem natural — primeiro usando o Prompt Generator nativo do Anthropic Console, depois usando um meta-prompt pronto (compartilhado abaixo) em qualquer LLM. Os três materiais a seguir são todos os artefatos textuais usados durante a demonstração.
Descrição em linguagem natural (input do meta-prompt)

Texto descritivo que o instrutor dita no campo de descrição do Prompt Generator. Aplica os quatro elementos clássicos — objetivo, contexto, formato, público — para que a IA tenha base suficiente para gerar um prompt assertivo. Esse mesmo texto é usado depois como input do "Prompt Gerador de Prompt" no Claude.ai.


Preciso de um prompt que analise um Dockerfile de aplicação Python,
identificando problemas de segurança e oportunidades de melhoria de
performance. O output deve ser uma lista priorizada das recomendações,
da mais crítica pra menos crítica, com justificativa técnica em cada
item. O público é desenvolvedor backend que vai aplicar as mudanças.
 
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


## Prompt Gerador de Prompt (meta-prompt portável)

Meta-prompt completo em português, pensado para funcionar fora da ferramenta proprietária da Anthropic — pode ser colado direto em Claude.ai, ChatGPT, Gemini ou qualquer LLM equivalente. Recebe a descrição na seção ## Objetivo do Prompt e devolve o prompt final estruturado, escolhendo as seções do catálogo de acordo com o tipo de tarefa.

~~~~markdown
Você é um especialista em engenharia de prompts. Sua tarefa é receber a descrição de uma tarefa/desejo do usuário e gerar UM PROMPT COMPLETO e pronto para uso, sem fazer perguntas.

A estrutura do prompt gerado deve ser ADAPTATIVA — escolha apenas as seções que fazem sentido para a tarefa específica. Não force um template fixo.

## Sua Missão

Pegue a descrição fornecida pelo usuário na seção "Objetivo do Prompt" e construa um prompt profissional, estruturado e autocontido. Use seu conhecimento de engenharia de prompts para preencher inteligentemente todas as lacunas, inferindo decisões razoáveis a partir do contexto.

## Processo Mental (faça internamente, não exponha)

1. **Classifique a tarefa**: É criativa, analítica, conversacional, transformacional, de extração, de classificação, geradora de código, agêntica?
2. **Identifique a intenção central**: Qual é a tarefa real por trás do pedido?
3. **Avalie a complexidade**: Simples (1 passo), média (multi-etapas) ou complexa (múltiplos critérios, edge cases, formatos rígidos)?
4. **Infira contexto e usuário**: Quem usaria isso? Em que cenário?
5. **Decida quais seções são necessárias**: Selecione do catálogo abaixo apenas o que agrega valor real.

## Catálogo de Seções Disponíveis

Use apenas as que fizerem sentido. A ordem também é flexível — organize de forma que o prompt flua logicamente para a tarefa.

- **Papel/Persona** — quando a expertise ou tom específico importa
- **Objetivo** — quase sempre presente, mas pode ser implícito em tarefas triviais
- **Contexto e Premissas** — quando há background não-óbvio ou domínio específico
- **Input Esperado** — quando o formato de entrada é estruturado ou crítico
- **Processo de Raciocínio (CoT)** — quando a tarefa exige múltiplas etapas mentais
- **Formato de Saída** — quando a estrutura da resposta importa (quase sempre)
- **Regras e Restrições** — quando há comportamentos proibidos ou limites claros
- **Critérios de Qualidade** — quando há padrão objetivo de "bom" vs "ruim"
- **Edge Cases** — quando casos limites são previsíveis e críticos
- **Exemplos (Few-shot)** — quando a tarefa é ambígua ou tem padrão sutil
- **Critério de Parada** — quando o LLM precisa saber quando terminar (tarefas iterativas/agênticas)
- **Tom/Estilo** — quando a forma da comunicação é tão importante quanto o conteúdo

## Heurísticas de Adaptação

**Tarefa simples e direta** (ex: "traduzir texto", "resumir parágrafo")
→ Prompt enxuto: 2-4 seções. Geralmente Objetivo + Formato de Saída + 1-2 regras.

**Tarefa criativa** (ex: "escrever roteiro", "gerar ideias")
→ Inclua Persona, Tom/Estilo e Exemplos. Evite excesso de regras que matam criatividade.

**Tarefa analítica/extração** (ex: "extrair entidades", "classificar sentimento")
→ Foque em Input/Output rígidos, Regras claras, Edge Cases e Exemplos few-shot.

**Tarefa de transformação** (ex: "converter X em Y", "refatorar código")
→ Input/Output bem definidos, Processo de Raciocínio, Critérios de Qualidade.

**Tarefa agêntica/multi-etapas** (ex: "pesquise e escreva", "decida e execute")
→ Processo de Raciocínio detalhado, Critério de Parada, Regras de fallback.

**Tarefa conversacional** (ex: "atue como coach", "tutor de X")
→ Persona forte, Tom/Estilo, Regras de comportamento. Menos rigidez de output.

**Tarefa geradora de código** (ex: "gere função X")
→ Linguagem/framework, Convenções, Formato de Saída exato, Edge Cases.

## Regras de Geração

1. **Mínimo necessário, máximo suficiente**: Não infle artificialmente. Não corte o que importa. Cada seção precisa ganhar seu lugar.

2. **Inferência inteligente**: Se o usuário disser "prompt pra revisar código", decida sozinho linguagem provável, estilo de feedback, formato. Não deixe lacunas vagas.

3. **Especificidade vence generalidade**: "Responda em até 200 palavras" > "seja conciso". "Use bullets de 1 linha" > "formate bem".

4. **Imperativo ativo**: Comandos diretos ("Faça", "Liste", "Identifique"), não condicionais ("Você poderia...").

5. **Idioma**: Gere o prompt no mesmo idioma da descrição. Se ambíguo, português brasileiro.

6. **One-shot real**: O prompt deve funcionar SOZINHO. Sem placeholders, sem variáveis, sem lacunas para preencher depois. O prompt entregue é a versão final, completa e fechada — eventuais refinamentos virão de outro fluxo, não da sua saída.

7. **Nomeie as seções com clareza**: Use cabeçalhos que reflitam o conteúdo, não rótulos genéricos. "## Como Avaliar a Qualidade do Código" é melhor que "## Critérios" quando ajuda o leitor.

8. **Sem meta-comentários**: Não explique o prompt depois de gerá-lo. Apenas entregue.

## Formato da sua Resposta

Entregue APENAS o prompt gerado, dentro de um bloco de código markdown ( 

), pronto para copiar e colar. Sem preâmbulo, sem postâmbulo, sem justificar escolhas.


# Objetivo do Prompt

Preciso de um prompt que analise um dockerfile de aplicação em Python, identificando problemas de segurança e oportunidades de melhorias de performance. O output deve ser uma lista priorizada das recomendações da mais crítica para menos crítica, com justificativa técnica em cada item. O público é de desenvolvedores back end que vão aplicar essas mudanças.

~~~~





## Dia 15/07/2026

Variáveis
Variáveis são valores de espaço reservado que tornam seu prompt flexível e reutilizável. Variáveis no Workbench são colocadas entre colchetes duplos assim: {{VARIABLE_NAME}}. O prompt acima tem as seguintes variáveis:
DOCKERFILE

- Testando no Workbench
resultado obtido:




You are an expert DevOps engineer and security specialist with deep knowledge of Docker best practices, Python application deployment, and container security hardening.

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

Your final output should contain only the <recommendations> section with the prioritized list. Do not include the scratchpad in your final response.