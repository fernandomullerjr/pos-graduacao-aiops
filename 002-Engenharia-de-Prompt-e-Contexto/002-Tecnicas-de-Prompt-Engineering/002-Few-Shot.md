# 02 — Few-Shot: ensinar pelo exemplo

> **Few-Shot ensina o que o modelo não conhece pelo exemplo.** Em vez de _descrever_ o padrão, você o **demonstra** com pares de entrada → saída dentro do próprio prompt.

---

## 1. Zero-Shot vs. One-Shot vs. Few-Shot

A diferença é simples — é a **quantidade de exemplos** que você passa no prompt:

| Variante | Exemplos no prompt | Quando usar |
|----------|--------------------|-------------|
| **Zero-Shot** | 0 exemplos | Instrução direta; o modelo já conhece a tarefa |
| **One-Shot** | 1 exemplo | Capturar o formato sem gastar muitos tokens |
| **Few-Shot** | 2+ exemplos | Padrão específico da empresa/pessoa que o modelo não conhece |

### Regra de decisão — quantos exemplos passar?

```text
            ┌─────────────────────────┐
            │ Quantos exemplos você    │
            │ vai precisar passar?     │
            └───────────┬─────────────┘
        nenhum (0)      │ apenas 1        2 ou mais (2+)
     ┌──────────────┐   │   ┌──────────┐   ┌──────────────┐
     │ Use Zero-Shot│◄──┴──►│Use One-Shot│  │ Use Few-Shot │
     └──────────────┘       └──────────┘   └──────────────┘
```

Exemplo de One-Shot (1 par demonstrado, modelo continua o padrão):

```text
PROMPT (One-Shot)
-----------------
[REGRA] Você é um SRE. Classifique a severidade.

Alerta: "CPU 94% no payment-service"
Severidade: High

Alerta: "Disk 78% no log-server"
Severidade: ___      ← o modelo completa seguindo o exemplo
```

---

## 2. In-Context Learning — como os exemplos influenciam o modelo

Few-Shot funciona por um mecanismo chamado **In-Context Learning (ICL)**: o modelo identifica o padrão dos exemplos **na hora** e o reaplica na nova entrada.

```text
SEM EXEMPLOS:
  prompt ─► Transformer ─► output variável (sem padrão de referência)

COM EXEMPLOS (Few-Shot):
  exemplos ─► Transformer identifica o padrão ─► output estruturado,
                                                  no MESMO formato dos exemplos
```

O Transformer não "treina" com seus exemplos — ele apenas os usa como **referência ativa** durante aquela resposta.

> ### ⚠️ Importante: In-Context Learning ≠ Fine-Tuning
> - Os **pesos do modelo NÃO mudam**.
> - O aprendizado vale **só na conversa atual**.
> - Nova sessão = começa do zero (o "aprendizado" é **temporário**, dura enquanto a sessão estiver ativa).

---

## 3. Quando usar Few-Shot — o padrão que o modelo não conhece

A pergunta-chave: **o padrão que você quer é público ou privado?**

```text
            ┌──────────────────────────┐
            │ O padrão que você quer    │
            │ é público ou privado?     │
            └────────────┬─────────────┘
       público/comum     │      privado/próprio
   ┌──────────────────┐  │  ┌──────────────────────┐
   │  Zero-Shot basta │◄─┴─►│ Few-Shot obrigatório │
   └──────────────────┘     └──────────────────────┘
```

**Casos em que Few-Shot é necessário:**

- Formato de saída **proprietário** (template interno de ticket, post-mortem da empresa).
- Estilo de escrita **específico** (tom da sua documentação, voz da marca).
- Convenções de nomenclatura/classificação **internas**.
- Taxonomia de severidade ou tags que **não são padrão de mercado**.

> Padrão de mercado? → Zero-Shot resolve.
> Padrão da sua empresa/pessoa? → Few-Shot obrigatório.

---

## 4. Demo — output sem Few-Shot vs. com Few-Shot

O exemplo da aula mostra a diferença ao pedir um artigo no **estilo específico** do autor (direto, crítico, sem "floreios"):

```text
PROMPT (Few-Shot) — exemplo fornecido define o estilo
-----------------------------------------------------
Estilo de referência (exemplo do texto desejado):

"Spoiler: não é. Mundo real fala de Kubernetes como se fosse a
 solução pra tudo. Se você tem 3 serviços rodando num VPS de 20
 dólares, colocar Kubernetes ali não é arquitetura — é cosplay de
 Big Tech. Pare. Um script bash resolve."

Agora escreva um artigo neste mesmo estilo sobre:
"Por que monitoramento não é só instalar Grafana"
```

Sem o exemplo, o modelo entrega um texto genérico e "corporativo". **Com** o exemplo, ele captura tom, ritmo e atitude — porque esse estilo é seu, não é um padrão de mercado.

---

## 5. Boas práticas

- **Use exemplos consistentes entre si.** Se eles divergem de formato, o modelo fica confuso sobre qual padrão seguir.
- **Cubra os casos de borda.** Inclua um exemplo "difícil" para ensinar como tratar exceções.
- **Mantenha rótulos balanceados.** Em classificação, não use só exemplos de uma classe — isso enviesa a saída.
- **Mais exemplos ≠ sempre melhor.** Após alguns exemplos o ganho satura e o custo em tokens cresce. Comece com 2–4.
- **Demonstre, não descreva.** A força do Few-Shot é mostrar o resultado, não explicar como chegar nele.

> **Resumo:** Few-Shot transforma exemplos em "regras vivas" durante a conversa. Use quando o padrão for seu — e lembre que ele desaparece ao fim da sessão.
