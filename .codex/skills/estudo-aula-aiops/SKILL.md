---
name: estudo-aula-aiops
description: Acessar aulas da plataforma DevOps PRO e produzir material de estudo da pós AIOps, com Word de capturas e anotações Markdown no lab correspondente. Use quando o usuário indicar uma aula, um link da plataforma ou materiais locais dessa pós.
---

# Estudo de aula AIOps

Produza dois artefatos revisáveis para **uma aula por execução**: um `.docx` visual na pasta da pós no OneDrive e um `.md` técnico no lab WSL. Adapte a profundidade ao material disponível; não preencha lacunas com conteúdo inventado.

## Economia de tokens

- Planeje uma passada pela aula: leia primeiro título, transcrição e materiais complementares disponíveis; depois procure no vídeo apenas os trechos necessários para capturas relevantes.
- Extraia ou resuma por partes úteis. Evite despejar transcrições integrais, HTML extenso, árvores de arquivos ou saídas repetidas no contexto; filtre e limite a saída das ferramentas.
- Reaproveite informações e capturas já obtidas na execução. Evite revisitar a mesma tela ou repetir buscas e testes sem uma dúvida concreta a resolver.
- Faça uma seleção enxuta de capturas distintas e notas proporcionais ao conteúdo da aula. A economia não deve omitir conceitos, comandos, resultados ou ressalvas essenciais.
- Verifique os artefatos uma vez e corrija apenas problemas encontrados. Encerre quando ambos estiverem legíveis, fiéis à fonte e prontos para revisão.

## Pastas e convenções

- Word: `G:\OneDrive\Documents\IA-inteligencia-artificial\Pos-Graduacao__AiOps__DevOps-PRO\`.
- Lab: `\\wsl.localhost\Ubuntu\home\fernando\cursos\ia-inteligencia-artificial\pos-graduacao-aiops\`.
- As árvores usam nomes diferentes. Localize o módulo e a seção equivalentes pelo tema, número e arquivos vizinhos; não transforme caminhos por mera substituição de texto.
- Use o prefixo numérico e a grafia do diretório correspondente. Preserve arquivos existentes. Se a aula já tiver artefatos, faça um rascunho com nome distinto ou peça instrução quando a intenção de atualizar não estiver clara.

## Fontes e acesso

1. Inspecione exemplos próximos nas duas árvores para captar nomes e estilo. Abra o link da aula em um navegador disponível no ambiente do usuário. Confira visualmente que a página, o título e o conteúdo correspondem à aula pedida. Uma resposta HTTP 200 isolada não basta: a plataforma carrega conteúdo por JavaScript.
2. Priorize uma sessão já autenticada. Se aparecer login, permita que o usuário se autentique no navegador e continue após verificar a página da aula. Use credenciais fornecidas para essa finalidade apenas durante a sessão autorizada; nunca as registre no `SKILL.md`, em memória persistente, scripts, logs, Word ou Markdown. Deixe segundo fator e CAPTCHA para o usuário quando exigidos.
3. Navegue pelos capítulos, materiais e vídeo para obter os conceitos e as demonstrações da aula. Se houver vídeo, selecione capturas apenas de quadros legíveis e relevantes: conceitos, diagramas, comandos, demonstrações e resultados. Registre a ordem, o título do tópico e, quando possível, o tempo do vídeo. Evite quadros repetidos. Não contorne controles de acesso ou proteção do vídeo.
4. Se o navegador, a autenticação ou a captura estiverem indisponíveis, descreva o bloqueio concreto. Use materiais locais existentes apenas como fonte alternativa identificada; não apresente imagens reutilizadas como se fossem capturas novas da aula.

## Documento Word

- Monte um registro visual com título da aula, link/fonte, sequência das capturas e legendas curtas que expliquem por que cada quadro importa. Preserve texto, código e diagramas legíveis, sem esticar imagens ou cortar informação.
- Para criar ou editar `.docx`, aplique a skill `documents` disponível no ambiente. Renderize e inspecione todas as páginas antes de entregar.

## Markdown do lab

- Faça anotações úteis para revisão e prática: conceitos, quando usar, exemplos, prompts/comandos, passos das demos, observações, dúvidas e desafios técnicos quando houver fonte para isso. Siga a organização de aulas vizinhas sem impor seções vazias.
- Identifique a origem de resultados: **observado na aula**, **executado no lab** ou **exemplo proposto**. Só chame algo de teste executado se ele tiver sido rodado e o output tiver sido conferido. Preserve comandos e saídas literais em blocos de código.
- Se for executar uma demo, confira antes os pré-requisitos, o ambiente e o risco de efeitos externos. Evite comandos que alterem infraestrutura real sem autorização específica.

## Verificação e entrega

- Confirme que os dois arquivos correspondem à mesma aula, que os links funcionam e que os fatos e capturas têm origem clara. Informe o que está pronto para revisão e qualquer limite concreto de acesso ou execução.
