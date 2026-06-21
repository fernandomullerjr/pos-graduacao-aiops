# 01 — Zero-Shot: instrução sem exemplos

> **Zero = nenhum exemplo no prompt.** Você instrui — o modelo usa o que aprendeu no treinamento. Simples, direto, poderoso.

---

## 1. O que é Zero-Shot?

Zero-Shot é dar uma instrução ao modelo **sem fornecer nenhum exemplo** de como a resposta deve ser. Você descreve a tarefa e confia que o modelo já "viu" padrões semelhantes durante o treinamento.

```text
┌─────────┐      ┌─────────┐      ┌──────────┐
│ Prompt  │ ───► │   LLM   │ ───► │ Resposta │
│ (tarefa)│      │ (treino)│      │          │
└─────────┘      └─────────┘      └──────────┘
   sem exemplos
```

## 2. Por que funciona — o que significa "zero"

"Zero" se refere a **zero exemplos no prompt**, e não a zero conhecimento. O modelo já carrega:

- **Zero contexto** adicional fornecido por você;
- **Zero exemplos** de entrada/saída;
- **Zero formatação** demonstrada.

O conhecimento vem da **base de treinamento**: bilhões de textos, código, documentação e tutoriais. Quando você pede algo comum, o modelo recupera o padrão aprendido.

> **Regra de ouro:** Zero-Shot instrui — o modelo usa o treinamento. Por isso só é preciso a **instrução certa**.

## 3. Quando usar (e quando não usar)

| ✅ Use Zero-Shot quando | ⚠️ Evite Zero-Shot quando |
|------------------------|---------------------------|
| A tarefa é comum e bem conhecida | O formato de saída é muito específico/proprietário |
| Você quer rapidez e simplicidade | O modelo precisa imitar um estilo particular |
| O modelo provavelmente já viu o padrão | A consistência entre respostas é crítica |
| Protótipos e exploração inicial | A tarefa depende de convenções internas da empresa |

Quando Zero-Shot não basta, o próximo passo natural é o **Few-Shot** (aula 02).

---

## 4. Demo técnica — classificação de alerta em JSON

Caso clássico de Cloud/SRE: classificar a severidade de um alerta e devolver em JSON estruturado, **sem dar exemplos**.

```text
PROMPT
------
Classifique o alerta abaixo como critical, high, medium ou low.
Responda em JSON com os campos: severity, justification e
recommended_action.

Alerta: "Pod CrashLoopBackOff no deployment payment-service no
namespace production. Restarts: 15 nos últimos 30 minutos."
```

Saída esperada (o modelo monta o JSON sozinho, sem ter visto exemplo):

```json
{
  "severity": "critical",
  "justification": "O pod está em CrashLoopBackOff, indicando falha repetida na inicialização de um serviço de pagamento em produção.",
  "recommended_action": "1) Investigar logs do pod imediatamente (kubectl logs <pod>); 2) Verificar últimas alterações de deploy; 3) Avaliar rollback se necessário."
}
```

A instrução precisa **deixar claro o formato** (JSON, campos, valores possíveis). É isso que faz o Zero-Shot funcionar bem aqui.

---

## 5. Boas práticas — maximizar a qualidade do Zero-Shot

A qualidade de um Zero-Shot depende de quão **claro** é o input. Quanto mais claro o input → mais previsível e útil o output.

### Comparação: prompt vago vs. prompt detalhado

```text
❌ PROMPT VAGO — impreciso
   "Analise (esse) e diz o que tem de errado"
   → o modelo chuta o que "errado" significa

✅ PROMPT DETALHADO — preciso
   "Analise os logs do payment-service. Identifique a causa raiz
    do erro 500, correlacione os timestamps e recomende uma ação
    corretiva. Responda com: causa, evidência, ação."
```

### As 4 práticas — aplique agora

1. **Seja detalhado.** Defina formato, tom, escopo e o caminho até a resposta. Ambiguidade vira chute.
2. **Use Role Prompting.** Atribua um papel ao modelo ("Você é um SRE sênior...") para ativar o conhecimento certo e a linguagem de domínio.
3. **Defina o formato de saída.** Diga exatamente como quer (JSON, tabela, markdown, bullet points). Aumenta a previsibilidade.
4. **Combine com frameworks.** Zero-Shot é a base — pode ser ponto de partida para outras técnicas (CoT, Step-Back, etc.).

### Casos de uso reais — Zero-Shot em Cloud

- Criar manifests Terraform para um VPC com subnets públicas e privadas.
- Converter docker-compose em Deployment + Service do Kubernetes.
- Gerar pipeline GitHub Actions para build, lint e deploy.
- Escrever um script bash de rotina (backup, limpeza, healthcheck).

> **Resumo:** Zero-Shot funciona quando você faz o trabalho de pensar antes de enviar o prompt. A clareza da instrução é o que carrega o resultado.
