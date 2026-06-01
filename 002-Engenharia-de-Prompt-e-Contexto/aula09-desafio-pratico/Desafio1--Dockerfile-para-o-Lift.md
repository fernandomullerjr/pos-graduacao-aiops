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

## Questão 01 — Dockerfile para o Lift

O Lift vai sair das VMs onde vem rodando e entrar no cluster Kubernetes da empresa. O código já está pronto: uma API **Python/Flask** na porta **8080**, dependências declaradas em `requirements.txt`, e duas variáveis de ambiente que precisam estar presentes no runtime: `DATABASE_URL` e `API_KEY`.

**Estrutura do projeto:**

```text
lift/
├── app.py
├── requirements.txt
├── lib/
│   ├── auth.py
│   └── storage.py
└── tests/
    └── test_app.py
```

**Conteúdo de `requirements.txt`:**

```text
Flask==3.0.0
gunicorn==21.2.0
requests==2.31.0
python-dotenv==1.0.0
psycopg2-binary==2.9.9
```

Em produção o serviço sobe com:

```bash
gunicorn --bind 0.0.0.0:8080 --workers 4 app:app
```

Falta o Dockerfile. Seguir todas as boas práticas de criação.

> **Tarefa.** Aplicando o framework **R-T-F**, escrever o prompt de IA que produza esse Dockerfile.
>
> **Entregue.** Prompt, modelo, output e justificativa mostrando como **Role**, **Task** e **Format** aparecem no prompt.

---

# RESPOSTA

---

## 💻 Campo 1: Prompt

```text
# Role
Você é um SRE senior especializado em Redes, Servidores e Kubernetes.

# Task
Crie um Dockerfile seguindo boas práticas.
O Dockerfile vai pegar código que já está pronto: uma API Python/Flask na porta 8080, dependências declaradas em requirements.txt, e duas variáveis de ambiente que precisam estar presentes no runtime, DATABASE_URL e API_KEY.

Considere a Estrutura do projeto:
lift/
├── app.py
├── requirements.txt
├── lib/
│   ├── auth.py
│   └── storage.py
└── tests/
    └── test_app.py

Conteúdo de requirements.txt:
Flask==3.0.0
gunicorn==21.2.0
requests==2.31.0
python-dotenv==1.0.0
psycopg2-binary==2.9.9

Em produção o serviço sobe com gunicorn --bind 0.0.0.0:8080 --workers 4 app:app.

Considere aspectos de Segurança, Manutenção, Performance ao elaborar o Dockerfile. Além do fato que a empresa vai sair das VMs onde vem rodando e entrar no cluster Kubernetes da empresa.

# Format
Dockerfile comentado, com cabeçalho explicando uso em no máximo 2 linhas.
Variáveis de fácil manutenção e comentadas.
Multi-stage somente se fizer sentido.
Não exagerar nos comentários, somente o necessário, para fácil leitura humana.
```

---

## 🤖 Campo 2: Modelo

* **Modelo:** `ChatGPT GPT-5`
* **Justificativa de Escolha:** Uso bastante para tarefas de containerização e Kubernetes, costuma cobrir bem as boas práticas (usuário não-root, cache de layers, healthcheck) sem precisar de muito refinamento.
* **Pontos negativos:** Na primeira execução com o R-T-F o modelo exagerou demais nos comentários, transformando o Dockerfile em quase um tutorial. Precisei ajustar o **Format** pedindo explicitamente "não exagerar nos comentários, somente o necessário, para fácil leitura humana". Daí o resultado ficou bem mais limpo.

---

## 📄 Campo 3: Output

### Iteração 1 - Prompt simples (sem framework)

**Prompt usado:**

```text
crie dockerfile para que eu suba aplicação API Python/Flask na porta 8080,
com gunicorn --bind 0.0.0.0:8080 --workers 4 app:app.
```

**Output:**

```dockerfile
# Imagem base Python
FROM python:3.12-slim

# Evita geração de arquivos .pyc e melhora logs
ENV PYTHONDONTWRITEBYTECODE=1
ENV PYTHONUNBUFFERED=1

# Diretório da aplicação
WORKDIR /app

# Instala dependências
COPY requirements.txt .

RUN pip install --no-cache-dir -r requirements.txt

# Copia arquivos da aplicação
COPY . .

# Expõe a porta da aplicação
EXPOSE 8080

# Inicializa aplicação com Gunicorn
CMD ["gunicorn", "--bind", "0.0.0.0:8080", "--workers", "4", "app:app"]
```

Resultado básico, sem usuário não-root, sem healthcheck, sem nada das boas práticas que o cenário pede.

---

### Iteração 2 - R-T-F primeira versão

Resultado funcional, mas com **excesso de comentários**. Virou quase um documento didático, dificultando leitura humana. Não vou colar aqui inteiro pra não inflar. Ajustei o Format e refiz.

---

### Iteração 3 - R-T-F refinado (versão final)

```dockerfile
# Dockerfile para API Flask com Gunicorn otimizado para Kubernetes.
# Build: docker build -t lift-api . | Run: docker run -p 8080:8080 -e DATABASE_URL=... -e API_KEY=... lift-api

FROM python:3.12-slim

# -----------------------------------------------------------------------------
# Variáveis de ambiente
# -----------------------------------------------------------------------------

# Evita geração de .pyc
ENV PYTHONDONTWRITEBYTECODE=1

# Logs sem buffer (melhor para kubectl logs)
ENV PYTHONUNBUFFERED=1

# Diretório da aplicação
ENV APP_HOME=/app

# Configuração do Gunicorn
ENV GUNICORN_BIND=0.0.0.0:8080
ENV GUNICORN_WORKERS=4

# Variáveis obrigatórias no runtime:
# - DATABASE_URL
# - API_KEY

WORKDIR ${APP_HOME}

# Dependências mínimas necessárias
RUN apt-get update && apt-get install -y --no-install-recommends \
    libpq-dev \
    gcc \
    && rm -rf /var/lib/apt/lists/*

# Usuário não-root
RUN groupadd -r app && useradd -r -g app app

# Copia dependências primeiro para melhor cache
COPY requirements.txt .

# Instala dependências Python
RUN pip install --no-cache-dir --upgrade pip && \
    pip install --no-cache-dir -r requirements.txt

# Copia aplicação
COPY . .

# Permissões
RUN chown -R app:app ${APP_HOME}

USER app

EXPOSE 8080

# Healthcheck simples
HEALTHCHECK --interval=30s --timeout=5s --retries=3 CMD \
    python -c "import urllib.request; urllib.request.urlopen('http://127.0.0.1:8080/health')"

# Inicialização da API
CMD ["sh", "-c", "\
gunicorn \
--bind ${GUNICORN_BIND} \
--workers ${GUNICORN_WORKERS} \
--access-logfile - \
--error-logfile - \
app:app"]
```

Cobre o que o cenário pediu: imagem slim, usuário não-root, layers otimizadas para cache, healthcheck, variáveis nomeadas pra facilitar manutenção, e secrets (`DATABASE_URL`/`API_KEY`) ficando fora do Dockerfile pra virem via Secret do Kubernetes.

---

## ⚖️ Campo 4: Justificativa

Aplicando o R-T-F, no **Role** defini o perfil (SRE sênior com foco em Kubernetes) pra puxar boas práticas de container nesse contexto específico. Na **Task** descrevi o serviço (Flask/gunicorn, porta 8080, estrutura do projeto, variáveis obrigatórias) e pedi os eixos a considerar (segurança, manutenção, performance, ida pra K8s). No **Format** restringi a saída pra Dockerfile comentado com cabeçalho curto, variáveis nomeadas e multi-stage só se necessário. O contraste com o prompt simples (sem framework) mostrou o ganho. A versão simples entregou um Dockerfile genérico, sem usuário não-root e sem healthcheck.
