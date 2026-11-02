# 07 — Step-Back: notas de revisão (teste da skill)

> **Origem:** material local existente da aula 007 (Word com capturas e Markdown do lab). A página da plataforma não pôde ser aberta nesta execução; nenhum teste técnico foi executado aqui.

## Ideia central

Step-Back divide o pedido em duas fases: **abstrair princípios** do domínio e **aplicá-los** ao caso. Na demonstração de Kubernetes, isso evita concluir de imediato que basta elevar o limite de memória: é preciso distinguir limite insuficiente de possível crescimento anormal do consumo.

## Fluxo reutilizável

1. Descreva o sintoma e os dados observados.
2. Pergunte quais princípios fundamentais governam o sistema.
3. Aplique cada princípio às evidências do caso.
4. Liste hipóteses, verificações e critérios para descartá-las.

## Exemplos observados no material local

- **CrashLoopBackOff / OOMKilled:** analisar `requests`, `limits`, OOM Killer e evolução do uso de memória antes de propor uma correção.
- **Conectividade em VPC:** percorrer DNS, rotas, NACL, Security Group e processo escutando na porta esperada. Ping bem-sucedido não demonstra que TCP/8080 está liberado.
- **Combinações:** usar Step-Back para definir os fundamentos, CoT para estruturar o caminho de análise e Self-Consistency para comparar conclusões independentes.

## Desafio para o lab — proposto, não executado

Reproduzir o cenário do pod com `OOMKilled` em ambiente descartável. Coletar `kubectl describe pod`, `kubectl logs --previous` e métricas de memória; comparar uma hipótese de limite baixo com uma de vazamento. Registrar comandos, saídas e conclusão somente depois da execução.

## Ponto de atenção

A técnica consome mais tokens e tempo. Reservá-la para diagnósticos, decisões com várias camadas ou casos em que uma resposta superficial tenha custo relevante.

## Fontes

- `007-Step-Back.docx` e `007-Step-Back.md` na pasta local da pós.
- `007-Step-Back.md` no lab WSL.
- Página da seção: https://plataforma.devopspro.com.br/courses/8aef63f6-2e58-48da-b33e-e76c4b87f33b/18/9/51/conteudos?capitulo=51&conteudo=1554
