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

## Questão 02 — Script de backup do Ledger

Lorraine chegou à conclusão de que o **Ledger** — o PostgreSQL que o George levantou na EC2 anos atrás — nunca teve rotina de backup automatizada. Hoje isso é uma dependência aberta no radar da SRE, e ela quer fechar com uma cron diária.

**Ambiente onde o script vai rodar:**

| Item | Valor |
| --- | --- |
| Host | `ledger-db.internal.hvt.io` |
| Porta | `5432` |
| Banco | `ledger_prod` |
| Usuário de backup | `backup_user` |
| Senha | variável de ambiente `PGPASSWORD`, populada pelo AWS Secrets Manager via IAM role da instância |
| Região AWS | `us-east-1` |
| SO da instância | Ubuntu 22.04 LTS |
| Diretório de trabalho | `/var/backups/ledger` (80 GB livres) |
| Tamanho médio atual do dump compactado | ~12 GB |

**Requisitos do script:**

- fazer o dump com `pg_dump`;
- compactar com `gzip`;
- subir o arquivo pro bucket S3 `hvt-ledger-backups` via `aws s3 cp`;
- manter **30 dias de retenção** no S3 (removendo os mais antigos);
- registrar cada execução em `/var/log/ledger-backup.log` com timestamp;
- sair com **exit code adequado** em caso de falha.

> **Tarefa.** Aplicando o framework **R-T-F**, escrever o prompt de IA que produza esse script bash.
>
> **Entregue.** Prompt, modelo, output e justificativa mostrando como **Role**, **Task** e **Format** aparecem no prompt.

---

# RESPOSTA

---

## 💻 Campo 1: Prompt

```text
# Role
Você é um DBRE especialista.

# Task
Criar Script de Backup que rode diariamente.
O ambiente onde o script vai rodar:
- Host: ledger-db.internal.hvt.io
- Porta: 5432
- Banco: ledger_prod
- Usuário de backup: backup_user
- Senha: variável de ambiente PGPASSWORD, populada pelo AWS Secrets Manager via IAM role da instância
- Região AWS: us-east-1
- SO da instância: Ubuntu 22.04 LTS
- Diretório de trabalho com 80 GB livres: /var/backups/ledger
- Tamanho médio atual do dump compactado: ~12 GB

O script precisa fazer o dump com pg_dump, compactar com gzip, subir o arquivo pro bucket S3 hvt-ledger-backups via aws s3 cp, manter 30 dias de retenção no S3 (removendo os mais antigos), registrar cada execução em /var/log/ledger-backup.log com timestamp, e sair com exit code adequado em caso de falha.
Adicionar etapas de validações e tratamento de erros.
Guardar logs de execução no /var/log.

# Format
Script em bash.
No começo do arquivo trecho comentado com no máximo 3 linhas, explicando sobre o script.
Utilizar variáveis de fácil manutenção logo no começo.
Script deve conter comentários sem exageros.
Logs de execução também jogar na tela.
```

---

## 🤖 Campo 2: Modelo

* **Modelo:** `ChatGPT GPT-5`
* **Justificativa de Escolha:** Pra scripts de shell costuma trazer estrutura organizada (variáveis no topo, funções de log, tratamento de erro por etapa), que é exatamente o que esse tipo de rotina de backup precisa.

---

## 📄 Campo 3: Output

```bash
#!/usr/bin/env bash

# Backup diário PostgreSQL com upload para S3 e retenção de 30 dias.
# Requer: awscli, pg_dump, gzip e variável PGPASSWORD já carregada no ambiente.
# Logs são enviados para tela e /var/log/ledger-backup.log.

set -o pipefail

# -----------------------------------------------------------------------------
# Variáveis
# -----------------------------------------------------------------------------

DB_HOST="ledger-db.internal.hvt.io"
DB_PORT="5432"
DB_NAME="ledger_prod"
DB_USER="backup_user"

AWS_REGION="us-east-1"
S3_BUCKET="s3://hvt-ledger-backups"

BACKUP_DIR="/var/backups/ledger"
LOG_FILE="/var/log/ledger-backup.log"

RETENTION_DAYS=30

TIMESTAMP=$(date +"%Y%m%d_%H%M%S")
BACKUP_FILE="ledger_prod_${TIMESTAMP}.sql"
COMPRESSED_FILE="${BACKUP_FILE}.gz"

BACKUP_PATH="${BACKUP_DIR}/${BACKUP_FILE}"
COMPRESSED_PATH="${BACKUP_DIR}/${COMPRESSED_FILE}"

# -----------------------------------------------------------------------------
# Logging
# -----------------------------------------------------------------------------

log() {
    local MESSAGE="$1"

    echo "$(date '+%Y-%m-%d %H:%M:%S') - ${MESSAGE}" | tee -a "${LOG_FILE}"
}

# -----------------------------------------------------------------------------
# Validações iniciais
# -----------------------------------------------------------------------------

check_command() {
    command -v "$1" >/dev/null 2>&1 || {
        log "ERRO: comando '$1' não encontrado."
        exit 1
    }
}

check_required_env() {
    if [[ -z "${PGPASSWORD}" ]]; then
        log "ERRO: variável PGPASSWORD não definida."
        exit 1
    fi
}

check_disk_space() {
    AVAILABLE_GB=$(df -BG "${BACKUP_DIR}" | awk 'NR==2 {gsub("G","",$4); print $4}')

    if [[ "${AVAILABLE_GB}" -lt 20 ]]; then
        log "ERRO: espaço insuficiente em disco. Disponível: ${AVAILABLE_GB}G"
        exit 1
    fi
}

# -----------------------------------------------------------------------------
# Pré-checks
# -----------------------------------------------------------------------------

mkdir -p "${BACKUP_DIR}"

touch "${LOG_FILE}" || {
    echo "ERRO: não foi possível escrever em ${LOG_FILE}"
    exit 1
}

check_command pg_dump
check_command gzip
check_command aws

check_required_env
check_disk_space

# -----------------------------------------------------------------------------
# Início
# -----------------------------------------------------------------------------

log "Iniciando backup do banco ${DB_NAME}"

# -----------------------------------------------------------------------------
# Dump PostgreSQL
# -----------------------------------------------------------------------------

pg_dump \
    --host="${DB_HOST}" \
    --port="${DB_PORT}" \
    --username="${DB_USER}" \
    --dbname="${DB_NAME}" \
    --format=plain \
    --no-owner \
    --no-privileges \
    --verbose \
    > "${BACKUP_PATH}"

PG_DUMP_EXIT_CODE=$?

if [[ ${PG_DUMP_EXIT_CODE} -ne 0 ]]; then
    log "ERRO: falha no pg_dump."
    rm -f "${BACKUP_PATH}"
    exit 1
fi

log "Dump concluído: ${BACKUP_PATH}"

# -----------------------------------------------------------------------------
# Compressão
# -----------------------------------------------------------------------------

gzip "${BACKUP_PATH}"

GZIP_EXIT_CODE=$?

if [[ ${GZIP_EXIT_CODE} -ne 0 ]]; then
    log "ERRO: falha ao compactar backup."
    rm -f "${BACKUP_PATH}"
    exit 1
fi

log "Compressão concluída: ${COMPRESSED_PATH}"

# -----------------------------------------------------------------------------
# Upload S3
# -----------------------------------------------------------------------------

aws s3 cp \
    "${COMPRESSED_PATH}" \
    "${S3_BUCKET}/${COMPRESSED_FILE}" \
    --region "${AWS_REGION}"

AWS_UPLOAD_EXIT_CODE=$?

if [[ ${AWS_UPLOAD_EXIT_CODE} -ne 0 ]]; then
    log "ERRO: falha no upload para S3."
    exit 1
fi

log "Upload concluído para ${S3_BUCKET}"

# -----------------------------------------------------------------------------
# Retenção S3
# Remove backups com mais de 30 dias
# -----------------------------------------------------------------------------

log "Executando política de retenção (${RETENTION_DAYS} dias)"

aws s3 ls "${S3_BUCKET}/" --recursive --region "${AWS_REGION}" | while read -r LINE; do

    FILE_DATE=$(echo "${LINE}" | awk '{print $1}')
    FILE_TIME=$(echo "${LINE}" | awk '{print $2}')
    FILE_PATH=$(echo "${LINE}" | awk '{print $4}')

    [[ -z "${FILE_PATH}" ]] && continue

    FILE_EPOCH=$(date -d "${FILE_DATE} ${FILE_TIME}" +%s)
    CURRENT_EPOCH=$(date +%s)

    AGE_DAYS=$(( (CURRENT_EPOCH - FILE_EPOCH) / 86400 ))

    if [[ "${AGE_DAYS}" -gt "${RETENTION_DAYS}" ]]; then
        log "Removendo backup antigo do S3: ${FILE_PATH}"

        aws s3 rm \
            "s3://hvt-ledger-backups/${FILE_PATH}" \
            --region "${AWS_REGION}"
    fi
done

# -----------------------------------------------------------------------------
# Limpeza local
# -----------------------------------------------------------------------------

find "${BACKUP_DIR}" \
    -type f \
    -name "*.gz" \
    -mtime +2 \
    -delete

log "Limpeza local concluída"

# -----------------------------------------------------------------------------
# Finalização
# -----------------------------------------------------------------------------

log "Backup finalizado com sucesso"

exit 0
```

---

## ⚖️ Campo 4: Justificativa

Aplicando o R-T-F, no **Role** defini o perfil (DBRE especialista) pra puxar conhecimento de operação de banco e cuidados de backup. Na **Task** descrevi o ambiente completo (host, porta, banco, usuário, região, SO, diretório, tamanho do dump) e listei explicitamente o que o script precisa fazer (`pg_dump`, `gzip`, upload S3, retenção de 30 dias, log com timestamp e exit code adequado), pedindo validações e tratamento de erro. No **Format** delimitei a saída como script bash com variáveis nomeadas no topo, cabeçalho curto, comentários comedidos e logs duplicados em tela e arquivo, pra que o operador consiga acompanhar a execução sem precisar abrir o log.
