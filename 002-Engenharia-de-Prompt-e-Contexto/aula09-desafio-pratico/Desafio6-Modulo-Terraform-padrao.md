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

## Questão 06 — Módulo Terraform no padrão interno

**Strickland**, que fecha a ponta de segurança e compliance, publicou o padrão interno de IaC que todo módulo Terraform novo precisa seguir:

- **Tags obrigatórias** em todo recurso: `Owner`, `CostCenter`, `Environment`.
- **Prefixo `hvt-`** nos nomes de recursos.
- Todo **bucket S3** com:
  - encryption habilitada (SSE-S3 mínimo);
  - versioning ativo;
  - block public access total;
  - logging configurado.
- **Variáveis de entrada** em `variables.tf` com `description` e `type` obrigatórios.

Doc Brown pediu um **módulo Terraform reutilizável** pra criar buckets S3 aderentes a esse padrão. O módulo vai ser consumido por todos os times da empresa, então precisa vir com **exemplo de uso**.

**Como referência de estilo**, o módulo de VPC que já existe na empresa:

```hcl
variable "environment" {
  description = "Nome do ambiente (dev, staging, production)"
  type        = string
}

locals {
  common_tags = {
    Owner       = var.owner
    CostCenter  = var.cost_center
    Environment = var.environment
  }
}

resource "aws_vpc" "this" {
  cidr_block = var.cidr_block
  tags = merge(local.common_tags, {
    Name = "hvt-vpc-${var.environment}"
  })
}
```

> **Tarefa.** Aplicando o framework **C-A-R-E**, escrever o prompt de IA que produza o módulo Terraform S3 aderente ao padrão, no mesmo estilo do exemplo.
>
> **Entregue.** Prompt, modelo, output e justificativa mostrando como **Context**, **Action**, **Result** e **Example** aparecem no prompt.

---

# RESPOSTA

---

## 💻 Campo 1: Prompt

[CONTEXT]
Head de segurança e compliance publicou o padrão interno de IaC que todo módulo deve seguir na empresa.

[ACTION]
Necessário criar um **módulo Terraform reutilizável** pra criar buckets S3 aderentes a esse padrão. 
O módulo vai ser consumido por todos os times da empresa, então precisa vir com **exemplo de uso**.

[RESULT]
Módulo Terraform precisa seguir as seguintes regras:
- **Tags obrigatórias** em todo recurso: `Owner`, `CostCenter`, `Environment`.
- **Prefixo `hvt-`** nos nomes de recursos.
- Todo **bucket S3** com:
  - encryption habilitada (SSE-S3 mínimo);
  - versioning ativo;
  - block public access total;
  - logging configurado.
- **Variáveis de entrada** em `variables.tf` com `description` e `type` obrigatórios.

[EXAMPLE]
**Como referência de estilo**, o módulo de VPC que já existe na empresa:

```hcl
variable "environment" {
  description = "Nome do ambiente (dev, staging, production)"
  type        = string
}

locals {
  common_tags = {
    Owner       = var.owner
    CostCenter  = var.cost_center
    Environment = var.environment
  }
}

resource "aws_vpc" "this" {
  cidr_block = var.cidr_block
  tags = merge(local.common_tags, {
    Name = "hvt-vpc-${var.environment}"
  })
}
```

---

## 🤖 Campo 2: Modelo
Gemini 1.5 Flash

---

## 📄 Campo 3: Output
Excelente iniciativa. Padronizar a infraestrutura via módulos reutilizáveis é o melhor caminho para garantir compliance sem travar as entregas dos times de engenharia.

Seguindo o padrão de estilo adotado no módulo de VPC e as diretrizes do Head de Segurança, desenhei a estrutura do módulo do S3 utilizando os recursos granulares da AWS (padrão recomendado a partir do Terraform AWS Provider v4+).

Aqui está a estrutura completa do módulo, dividida em arquivos, seguida por um exemplo prático de consumo.

---

## 1. `variables.tf`

Definição clara das variáveis de entrada, todas com tipos e descrições explícitas.

```hcl
variable "owner" {
  description = "Time responsável pela criação e manutenção do recurso (ex: sRE, DataPlatform)"
  type        = string
}

variable "cost_center" {
  description = "Centro de custo associado ao recurso para controle orçamentário"
  type        = string
}

variable "environment" {
  description = "Nome do ambiente (dev, staging, production)"
  type        = string
}

variable "bucket_name_suffix" {
  description = "Sufixo do nome do bucket. O prefixo 'hvt-' e o ambiente serão adicionados automaticamente"
  type        = string
}

variable "log_target_bucket" {
  description = "ID (nome) do bucket S3 centralizado onde os logs de acesso serão armazenados"
  type        = string
}

variable "log_target_prefix" {
  description = "Prefixo opcional para os arquivos de log dentro do bucket de destino"
  type        = string
  default     = "s3-access-logs/"
}

```

---

## 2. `main.tf`

Aqui aplicamos as regras de negócio: criptografia SSE-S3, versionamento ativo, bloqueio total de acesso público, logs de auditoria e a concatenação forçada do prefixo `hvt-`.

```hcl
locals {
  common_tags = {
    Owner       = var.owner
    CostCenter  = var.cost_center
    Environment = var.environment
  }
  
  # Garante que o nome final siga o padrão: hvt-[sufixo]-[ambiente]
  bucket_name = "hvt-${var.bucket_name_suffix}-${var.environment}"
}

# 1. O Bucket S3
resource "aws_s3_bucket" "this" {
  bucket        = local.bucket_name
  force_destroy = var.environment == "production" ? false : true

  tags = merge(local.common_tags, {
    Name = local.bucket_name
  })
}

# 2. Ativação do Versionamento
resource "aws_s3_bucket_versioning" "this" {
  bucket = aws_s3_bucket.this.id
  versioning_configuration {
    status = "Enabled"
  }
}

# 3. Criptografia Lado Servidor (SSE-S3 por padrão)
resource "aws_s3_bucket_server_side_encryption_configuration" "this" {
  bucket = aws_s3_bucket.this.id

  rule {
    apply_server_side_encryption_by_default {
      sse_algorithm = "AES256"
    }
  }
}

# 4. Bloqueio Total de Acesso Público (Block Public Access)
resource "aws_s3_bucket_public_access_block" "this" {
  bucket = aws_s3_bucket.this.id

  block_public_acls       = true
  block_public_policy     = true
  ignore_public_acls      = true
  restrict_public_buckets = true
}

# 5. Configuração de Logging de Acesso
resource "aws_s3_bucket_logging" "this" {
  bucket        = aws_s3_bucket.this.id
  target_bucket = var.log_target_bucket
  target_prefix = "${var.log_target_prefix}${local.bucket_name}/"
}

```

---

## 3. `outputs.tf`

Exporta os atributos essenciais para que outros módulos ou recursos possam consumir o bucket criado.

```hcl
output "bucket_id" {
  description = "O nome (ID) do bucket S3 criado"
  value       = aws_s3_bucket.this.id
}

output "bucket_arn" {
  description = "O ARN do bucket S3 criado"
  value       = aws_s3_bucket.this.arn
}

output "bucket_domain_name" {
  description = "O nome de domínio do bucket (FQDN)"
  value       = aws_s3_bucket.this.bucket_regional_domain_name
}

```

---

## 4. Exemplo de Uso (Para os Times Consumidores)

Este exemplo pode ser colocado no `README.md` do repositório do módulo para orientar os desenvolvedores da empresa.

```hcl
# Exemplo de consumo do módulo de S3 corporativo compilacente com segurança

module "s3_app_data" {
  source = "git::https://github.com/sua-organizacao/terraform-aws-s3-compliant.git?ref=v1.0.0"

  # Variáveis de Contexto / Tags Obrigatórias
  owner       = "Plataforma"
  cost_center = "102040"
  environment = "dev"

  # Configuração do Nome
  # O nome final na AWS será: hvt-app-user-profiles-dev
  bucket_name_suffix = "app-user-profiles"

  # Destino dos Logs de Acesso (Exigência de Compliance)
  log_target_bucket = "hvt-central-logs-bucket-dev"
  log_target_prefix = "s3-audit/"
}

# Exemplo de como usar o output do módulo em outro recurso
resource "aws_iam_policy" "app_policy" {
  name        = "hvt-app-s3-policy-dev"
  description = "Politica de acesso ao bucket de profiles"

  policy = jsonencode({
    Version = "2012-10-17"
    Statement = [
      {
        Action   = ["s3:GetObject", "s3:PutObject"]
        Effect   = "Allow"
        Resource = "${module.s3_app_data.bucket_arn}/*"
      }
    ]
  })
}

```

### Destaques do Design:

* **Abstração do Prefixo:** O desenvolvedor não precisa lembrar de digitar `hvt-` ou o sufixo do ambiente. O módulo faz o `join` dinâmico no `local.bucket_name`, mitigando erro humano.
* **Granularidade (AWS Provider 4.x/5.x):** Usar recursos separados como `aws_s3_bucket_public_access_block` em vez de inline arguments dentro do `aws_s3_bucket` evita problemas de concorrência na API da AWS e facilita futuras atualizações de estado do Terraform.

---

## ⚖️ Campo 4: Justificativa
O **Context** traz um pouco sobre o cenário da abordagem com base nas definições do Head da empresa, no **Action** é detalhado o que será feito numa breve descrição, em **Result** são trazidos os detalhes das regras e especificações e em **Example** é trazido um exemplo de outro manifesto que vai facilitar o modelo entender as particularidades da empresa. 