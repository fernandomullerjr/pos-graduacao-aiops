# Aula 09 — Triagem de Incidente com GitHub e Kubernetes

> **Módulo:** Agentes de Codificação e SDD · **Capítulo:** MCP  
> **Fonte:** [aula na plataforma DevOps PRO](https://plataforma.devopspro.com.br/courses/8aef63f6-2e58-48da-b33e-e76c4b87f33b/18/9/55/conteudos?capitulo=55&conteudo=1668) (vídeo de 20:36, transcrição e material complementar).  
> **Estado deste arquivo:** notas da aula; nenhum comando deste roteiro foi executado no meu lab nesta elaboração.

## Objetivo da aula

Integrar um agente de codificação a **GitHub** e **Kubernetes** por meio de servidores MCP para tratar uma issue de incidente de ponta a ponta: ler o ticket, investigar o cluster, corrigir a configuração, validar a aplicação e registrar o resultado na issue. Na demonstração, o projeto é o `encontros-tech` e o cluster é da DigitalOcean; o instrutor afirma que o fluxo pode ser adaptado a outro provedor ou laboratório local.

## Fluxo observado no vídeo

| Tempo aproximado | Etapa | Evidência |
|---|---|---|
| 01:34 | Agente consulta GitHub e Kubernetes por MCP | Diagrama do fluxo |
| 04:29–07:13 | Criação de fine-grained PAT para o fork | Permissões e conexão do GitHub MCP |
| 08:27–09:26 | Build, push e deploy da aplicação | `docker compose`, `kubectl apply`, `kubectl get all` |
| 10:44–11:22 | Configuração do banco é alterada de propósito | Pods da aplicação não ficam prontos e começam a reiniciar |
| 12:37–13:17 | Issue “Aplicação fora do ar” é aberta | Ticket no fork do GitHub |
| 13:24–15:14 | Agente recebe a tarefa e investiga | Issue, manifesto, estado do cluster e logs |
| 16:27–17:57 | Escopo é esclarecido e o manifesto é corrigido | Reaplicação e reinício dos pods |
| 18:03–18:28 | Agente registra diagnóstico e validação | Issue fechada com relato do incidente |

## Preparação do cenário

O [repositório original](https://github.com/KubeDev/encontros-tech) é usado como base. A aula recomenda criar um **fork**, pois a issue e as alterações devem ocorrer no repositório do aluno. O material complementar mostra um GitHub MCP Server em container, começando com `GITHUB_READ_ONLY=1` e `GITHUB_TOOLSETS=issues,pull_requests`. O token é injetado por variável de ambiente; não deve ser gravado no JSON ou no repositório.

```json
{
  "mcpServers": {
    "github": {
      "command": "docker",
      "args": [
        "run", "-i", "--rm",
        "-e", "GITHUB_PERSONAL_ACCESS_TOKEN",
        "-e", "GITHUB_TOOLSETS=issues,pull_requests",
        "-e", "GITHUB_READ_ONLY=1",
        "ghcr.io/github/github-mcp-server"
      ],
      "env": {
        "GITHUB_PERSONAL_ACCESS_TOKEN": "${GITHUB_PERSONAL_ACCESS_TOKEN}"
      }
    }
  }
}
```

O fine-grained PAT da demo foi limitado ao fork e tinha validade de sete dias. O material lista `Administration`, `Contents`, `Issues` e `Pull requests` com leitura e escrita, além de `Metadata` somente leitura. Isso descreve a **demonstração**; para repetir o exercício, conceder apenas as permissões que as operações planejadas exigirem. A transição de leitura para escrita é uma decisão explícita antes de comentar, corrigir ou abrir PR.

## Deploy e falha simulada

O instrutor verifica que a imagem do `docker-compose.yml` e a referenciada no deployment correspondem. A sequência apresentada inclui:

```bash
docker compose build
docker compose push
kubectl apply -f k8s
kubectl get all
```

Depois do deploy, ele altera propositalmente uma configuração do banco no Secret/manifesto e reaplica o YAML. A transcrição menciona a alteração do host; o registro final da issue mostra ajustes de variáveis relacionadas ao PostgreSQL. O efeito observado é que os pods da aplicação podem aparecer como `Running` mas **não estão Ready** e passam a reiniciar. O pod do banco permanece pronto. A lição prática é conferir prontidão, logs e conectividade, além da fase do pod.

## Investigação e correção pelo agente

O pedido feito ao agente, construído ao vivo, equivale a:

> Verifique se há uma issue aberta no repositório. Se houver, investigue o incidente usando o acesso ao Kubernetes. Não altere código. Conclua apenas quando o problema estiver resolvido; registre na issue a causa raiz, a correção e a validação, e então feche a issue.

Durante a execução, o agente lê a issue, consulta o cluster e o manifesto, examina logs e encontra a falha de configuração do banco. Há uma mudança de escopo importante: o instrutor esclarece que **o manifesto pode ser alterado**, embora o código da aplicação não deva ser modificado. O agente então corrige a configuração, aplica a alteração, reinicia os pods e valida o serviço. O comentário final da issue documenta sintoma, diagnóstico, causa raiz, correção e verificações; a issue aparece fechada.

**Outputs observados na aula:** pods da aplicação sem prontidão antes da correção; no registro final, banco `1/1 Running`, aplicação `2/2 Running`, health checks e acesso externo HTTP 200. Estes resultados pertencem à demonstração do instrutor, não a uma execução no meu ambiente.

## Ponto de atenção

O fluxo mostra a utilidade de combinar servidores MCP, mas também o impacto das permissões. A investigação pode começar em modo somente leitura. Escritas no GitHub e alterações no cluster devem ser limitadas ao repositório e ao ambiente do exercício, com validação explícita antes de encerrar o ticket. O próprio instrutor apresenta esse prompt como uma demonstração simples e diz que um workflow mais estruturado virá em aulas seguintes.

## Desafio para o lab — proposto, não executado

1. Criar um fork de `encontros-tech` e um cluster descartável.
2. Configurar o GitHub MCP em modo somente leitura e o MCP de Kubernetes com acesso restrito ao cluster de teste. Guardar o token fora do repositório.
3. Fazer deploy e registrar `kubectl get all`, `kubectl describe pod` e logs da aplicação em estado saudável.
4. Introduzir uma falha reversível de configuração do banco, abrir uma issue no fork e pedir ao agente **primeiro apenas o diagnóstico**.
5. Revisar a proposta de mudança, permitir a correção somente no laboratório e validar pods Ready, health checks e resposta HTTP.
6. Registrar na issue sintoma, evidências, causa raiz, correção, validação e rollback. Guardar outputs reais aqui após executar.

## Arquivos relacionados

- Word visual: `G:\OneDrive\Documents\IA-inteligencia-artificial\Pos-Graduacao__AiOps__DevOps-PRO\Modulo003 - Agentes de Codificacao e SDD\003-MCP\009-Triagem-de-Incidente-com-GitHub-e-Kubernetes.docx`.
- Capturas: `G:\OneDrive\Documents\IA-inteligencia-artificial\Pos-Graduacao__AiOps__DevOps-PRO\Modulo003 - Agentes de Codificacao e SDD\003-MCP\capturas-009`.
