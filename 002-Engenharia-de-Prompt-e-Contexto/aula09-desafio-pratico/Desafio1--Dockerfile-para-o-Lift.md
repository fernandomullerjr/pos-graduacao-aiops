5.	Engenharia de Prompt e Contexto
# Desafio prático - Frameworks de Prompt Engineering 

Um dia na Hill Valley Tech
A Hill Valley Tech é uma empresa fictícia que serve de palco para este desafio. Tem cinco sistemas em produção, cada um com seu papel bem definido. O Chronos é o API gateway e a plataforma core, ponto de entrada de todo tráfego da empresa. Por trás dele, o Ledger é um data warehouse em PostgreSQL que guarda histórico de transações e eventos, enquanto o Reactor toca o processamento assíncrono por filas de mensagens. Em paralelo a tudo isso, o Beacon mantém a observabilidade do ambiente inteiro, métricas, logs e alertas, e é por ele que o plantão enxerga o que está acontecendo. Fora do core principal, o Lift é um produto em beta que o time vem amadurecendo à parte.
O time que toca essa operação também é enxuto. Doc Brown, o CTO, responde pela direção técnica. Jennifer Parker é a PM que prioriza o que o produto entrega. Lorraine Baines lidera a SRE e responde pelo plantão, é dela a cobrança por runbooks e procedimentos documentados. George McFly é o engenheiro sênior veterano que escreveu boa parte do sistema legado, e muita coisa ainda roda exatamente como ele deixou anos atrás. Goldie Wilson, a CEO, observa tudo pelo prisma de custo e crescimento. E Strickland, head de segurança e compliance, é quem bate carimbo nos padrões internos que todo código novo precisa seguir.
Nos próximos cenários você vai pegar algumas dessas demandas que chegam à mesa do time. Em cada questão, a entrega é um prompt de IA aplicando o framework indicado no enunciado, executado em um modelo, com o output registrado e a justificativa mostrando como os componentes do framework apareceram no prompt. A Questão 08 foge desse padrão: a escolha do framework fica por sua conta entre os cinco do capítulo, com comparação explícita contra duas alternativas.
________________________________________
# Questão 01 - Dockerfile para o Lift
O Lift vai sair das VMs onde vem rodando e entrar no cluster Kubernetes da empresa. O código já está pronto: uma API Python/Flask na porta 8080, dependências declaradas em requirements.txt, e duas variáveis de ambiente que precisam estar presentes no runtime, DATABASE_URL e API_KEY.
Estrutura do projeto:
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
Falta o Dockerfile. Seguir todas as boas práticas de criação.
Tarefa. Aplicando o framework R-T-F, escrever o prompt de IA que produza esse Dockerfile.
Entregue. Prompt, modelo, output e justificativa mostrando como Role, Task e Format aparecem no prompt.
________________________________________ 


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


RESPOSTA:

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
Dockerfile comentado, com cabeçalho explicando uso.
Variáveis de fácil manutenção e comentadas.
Multi-stage somente se fizer sentido.





EXEMPLOS:

1 - Com prompt simples:

crie dockerfile para que eu suba aplicação  API Python/Flask na porta 8080,   com gunicorn --bind 0.0.0.0:8080 --workers 4 app:app. 


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

Exemplo mínimo de `requirements.txt`:

```txt
flask
gunicorn
```

Build da imagem:

```bash
docker build -t flask-api .
```

Executar container:

```bash
docker run -d -p 8080:8080 --name flask-api flask-api
```

Se quiser, também posso te montar:

* versão otimizada para produção (multi-stage + usuário não-root)
* versão com healthcheck
* docker-compose.yml
* exemplo pronto para Kubernetes
* configuração com variáveis de ambiente e `.env`




2 - Com prompt usam R-T-F:

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
Dockerfile comentado, com cabeçalho explicando uso.
Variáveis de fácil manutenção e comentadas.
Multi-stage somente se fizer sentido.


```Dockerfile
# -----------------------------------------------------------------------------
# Dockerfile - API Python/Flask com Gunicorn
# -----------------------------------------------------------------------------
#
# Objetivo:
# Containerizar uma API Flask preparada para execução em Kubernetes,
# seguindo boas práticas de:
#
# - Segurança
# - Performance
# - Manutenção
# - Observabilidade básica
# - Redução de superfície de ataque
#
# Características:
#
# - Imagem base slim (menor footprint)
# - Execução com usuário não-root
# - Variáveis centralizadas
# - Layers otimizadas para cache
# - PYTHONUNBUFFERED para logs em tempo real
# - Healthcheck básico
# - Compatível com Kubernetes
#
# Estrutura esperada:
#
# lift/
# ├── app.py
# ├── requirements.txt
# ├── lib/
# └── tests/
#
# Build:
# docker build -t lift-api .
#
# Run local:
# docker run -p 8080:8080 \
#   -e DATABASE_URL=postgresql://user:pass@db:5432/app \
#   -e API_KEY=secret \
#   lift-api
#
# Kubernetes:
# As variáveis DATABASE_URL e API_KEY devem vir de:
# - Secrets
# - External Secrets
# - Vault
# - SealedSecrets
#
# -----------------------------------------------------------------------------

# -----------------------------------------------------------------------------
# Base Image
# -----------------------------------------------------------------------------
FROM python:3.12-slim

# -----------------------------------------------------------------------------
# Variáveis de ambiente
# -----------------------------------------------------------------------------
ENV PYTHONDONTWRITEBYTECODE=1 \
    PYTHONUNBUFFERED=1 \
    PIP_NO_CACHE_DIR=1 \
    APP_HOME=/app \
    GUNICORN_BIND=0.0.0.0:8080 \
    GUNICORN_WORKERS=4

# -----------------------------------------------------------------------------
# Diretório da aplicação
# -----------------------------------------------------------------------------
WORKDIR ${APP_HOME}

# -----------------------------------------------------------------------------
# Instalação de dependências do sistema
#
# gcc/libpq-dev:
# necessários para psycopg2 e libs PostgreSQL
#
# --no-install-recommends:
# reduz tamanho da imagem
# -----------------------------------------------------------------------------
RUN apt-get update && apt-get install -y --no-install-recommends \
    gcc \
    libpq-dev \
    && rm -rf /var/lib/apt/lists/*

# -----------------------------------------------------------------------------
# Criação de usuário não-root
#
# Segurança importante para Kubernetes/containers
# -----------------------------------------------------------------------------
RUN groupadd -r appgroup && useradd -r -g appgroup appuser

# -----------------------------------------------------------------------------
# Copia primeiro requirements.txt
#
# Melhor aproveitamento de cache nas builds
# -----------------------------------------------------------------------------
COPY requirements.txt .

# -----------------------------------------------------------------------------
# Instala dependências Python
# -----------------------------------------------------------------------------
RUN pip install --upgrade pip && \
    pip install -r requirements.txt

# -----------------------------------------------------------------------------
# Copia código da aplicação
# -----------------------------------------------------------------------------
COPY . .

# -----------------------------------------------------------------------------
# Ajusta ownership da aplicação
# -----------------------------------------------------------------------------
RUN chown -R appuser:appgroup ${APP_HOME}

# -----------------------------------------------------------------------------
# Troca para usuário não-root
# -----------------------------------------------------------------------------
USER appuser

# -----------------------------------------------------------------------------
# Exposição da porta da aplicação
# -----------------------------------------------------------------------------
EXPOSE 8080

# -----------------------------------------------------------------------------
# Healthcheck
#
# Idealmente sua API deve possuir endpoint:
# GET /health
#
# Exemplo Flask:
#
# @app.route("/health")
# def health():
#     return {"status": "ok"}, 200
#
# Kubernetes usará probes próprias,
# mas healthcheck local ajuda bastante.
# -----------------------------------------------------------------------------
HEALTHCHECK --interval=30s --timeout=5s --start-period=15s --retries=3 \
    CMD python -c "import urllib.request; \
    urllib.request.urlopen('http://127.0.0.1:8080/health')" || exit 1

# -----------------------------------------------------------------------------
# Comando de inicialização
#
# app:app =>
# arquivo app.py
# variável Flask "app"
#
# Ajustável via env vars futuramente
# -----------------------------------------------------------------------------
CMD ["sh", "-c", "\
gunicorn \
--bind ${GUNICORN_BIND} \
--workers ${GUNICORN_WORKERS} \
--access-logfile - \
--error-logfile - \
app:app"]
```

### Pontos importantes para Kubernetes

#### 1. Não armazenar secrets no Dockerfile

As variáveis abaixo devem vir via Secret:

* `DATABASE_URL`
* `API_KEY`

Exemplo Deployment:

```yaml
env:
  - name: DATABASE_URL
    valueFrom:
      secretKeyRef:
        name: api-secret
        key: database-url

  - name: API_KEY
    valueFrom:
      secretKeyRef:
        name: api-secret
        key: api-key
```

---

#### 2. Sugestão de melhorias futuras

Para ambiente produtivo mais maduro:

* usar `readinessProbe` e `livenessProbe`
* adicionar métricas Prometheus
* adicionar logs estruturados JSON
* adicionar timeout do gunicorn:

```bash
--timeout 30
```

* limitar workers conforme CPU:

Regra prática:

```text
workers = (2 x CPU) + 1
```

No Kubernetes normalmente vale mais:

* menos workers
* mais réplicas

---

#### 3. Sobre multi-stage

Neste caso específico:

* o ganho seria pequeno
* `python:slim` já é enxuta
* não há build de frontend/binários complexos

Então manter single-stage deixa:

* manutenção mais simples
* troubleshooting mais fácil
* build mais rápida

Se no futuro:

* houver wheels customizadas
* compilação pesada
* scanners rigorosos
* necessidade de imagem distroless

Aí multi-stage passa a fazer mais sentido.




- Fiz novo ajuste, devido o excesso de comentários:

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


- Entregou algo muito superior:

```Dockerfile
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
