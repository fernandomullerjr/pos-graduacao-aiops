Este documento serve como um guia rápido para consulta e revisão dos conceitos fundamentais de Engenharia de Prompt e dos frameworks abordados nas aulas.

---

# 🚀 Guia Rápido: Engenharia de Prompt

## 🦴 1. Anatomia de um Prompt Eficaz
Todo prompt de alta performance é construído sobre **4 pilares fundamentais**:

*   **Role (Persona):** Quem o modelo deve ser (ex: SRE Sênior, Alfaiate, Especialista em Frutas).
*   **Context (Contexto):** A situação atual, restrições e dados relevantes da aplicação ou cenário.
*   **Instruction (Instrução):** O que deve ser feito (use verbos de ação como "Compare", "Explique", "Crie").
*   **Output (Saída):** O formato esperado da resposta (Markdown, Tabela, JSON, Script).

---

## 🛠️ 2. Frameworks de Prompting

### **R-T-F (Role, Task, Format)**
*   **O que é:** O mais simples e direto, com zero overhead.
*   **Componentes:**
    *   **Role:** Persona e nível de expertise.
    *   **Task:** Ação específica a ser realizada.
    *   **Format:** Estrutura da saída (ex: e-mail, script Bash).
*   **Quando usar:** Tarefas diretas e previsíveis, como gerar scripts simples ou templates.

### **T-A-G (Task, Action, Goal)**
*   **O que é:** Inverte a lógica focado no **objetivo de negócio**.
*   **Componentes:**
    *   **Task:** O que precisa ser feito.
    *   **Action:** Como executar a tarefa.
    *   **Goal:** Objetivo mensurável a atingir (KPI, SLA, Meta).
*   **Quando usar:** Quando o resultado precisa atender a uma meta clara, como redução de custos ou aprovação em exames.

### **B-A-B (Before, After, Bridge)**
*   **O que é:** Framework de **transformação**. Define ponto de partida e destino.
*   **Componentes:**
    *   **Before:** Estado atual (problema ou limitações).
    *   **After:** Estado desejado (resultado ideal).
    *   **Bridge:** O pedido de transformação (o "caminho").
*   **Quando usar:** Migrações, refatoração de código legado ou planos de transição de carreira.

### **C-A-R-E (Context, Action, Result, Example)**
*   **O que é:** Focado em **precisão por referência**. O uso de exemplos (Few-shot) aumenta drasticamente a eficácia.
*   **Componentes:**
    *   **Context:** Cenário completo e restrições.
    *   **Action:** Ação específica.
    *   **Result:** Critérios de sucesso e formato.
    *   **Example:** Referência concreta do que você espera.
*   **Quando usar:** Quando há necessidade de consistência e replicabilidade (ex: seguir padrões de código da empresa).

### **R-I-S-E (Role, Input, Steps, Expectation)**
*   **O que é:** O mais estruturado. Define **processos e workflows** passo a passo.
*   **Componentes:**
    *   **Role:** Expertise e profundidade.
    *   **Input:** Dados concretos (logs, métricas, configs).
    *   **Steps:** Sequência lógica de execução.
    *   **Expectation:** Critérios de validação do resultado final.
*   **Quando usar:** Tarefas procedurais complexas, como runbooks de incidentes ou planos de mudança interestadual.

---

## 📊 3. Tabela Comparativa de Frameworks

| Framework | Diferencial | Melhor Cenário |
| :--- | :--- | :--- |
| **R-T-F** | Formato da saída | Tarefas simples e diretas |
| **T-A-G** | Objetivo de negócio | Resultados atrelados a metas/SLAs |
| **B-A-B** | Transformação | Migrações e refatorações |
| **C-A-R-E** | Exemplo de referência | Consistência e replicabilidade |
| **R-I-S-E** | Passos sequenciais | Processos complexos e workflows |



---

## 🧠 4. Fluxo de Decisão (Como Escolher)
1.  **A tarefa é simples?** ➔ Use **R-T-F**.
2.  **Existe uma meta ou KPI?** ➔ Use **T-A-G**.
3.  **Envolve sair de um estado A para B?** ➔ Use **B-A-B**.
4.  **Tem um exemplo para o modelo seguir?** ➔ Use **C-A-R-E**.
5.  **A tarefa exige um passo a passo rígido?** ➔ Use **R-I-S-E**.

**Regra de Ouro:** Não adicione complexidade desnecessária. Se o framework base já resolve, não precisa adicionar mais elementos.