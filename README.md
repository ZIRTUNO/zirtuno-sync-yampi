# Zirtuno Sync Yampi

Extensão para Chrome que copia o código do tema de uma loja Yampi para o seu computador e devolve as suas alterações ao editor de código da Yampi, com comparação, backup e conferência de cada item. Desenvolvida por **Zirtuno**.

**[Baixar a extensão](https://github.com/ZIRTUNO/zirtuno-sync-yampi/releases/latest/download/zirtuno-sync-yampi.zip)** · [Releases e SHA-256](https://github.com/ZIRTUNO/zirtuno-sync-yampi/releases/latest) · [Instalação passo a passo](docs/INSTALACAO.md)

Versão do código: **0.6.0**. O link baixa a última versão publicada em Releases. Antes de usar numa loja de cliente, leia o [estado da validação](#estado-da-validação).

## O que ela faz

- **Baixar o tema.** Copia os arquivos do editor de código (textos e imagens) num ZIP pronto para editar no VS Code, com uma prévia local da vitrine. Só lê: nada muda na loja.
- **Enviar alterações.** Compara o seu projeto com a cópia original e com a loja de agora e mostra o que vai mudar. Envia pelo próprio editor da Yampi: salva os arquivos alterados, cria os arquivos novos e envia as imagens novas. Cada item é conferido na Yampi depois de recarregar o editor.
- **Proteger a loja.** O backup é baixado antes da primeira mudança. O envio para no primeiro erro, e uma alteração feita na loja depois da sua cópia só é sobrescrita se você escolher. A extensão nunca publica, exclui ou renomeia.
- **Seguir em segundo plano.** Fechar o painel ou trocar de aba não interrompe nada. O ícone mostra o progresso, e um envio interrompido (aba fechada, erro, extensão atualizada) continua de onde parou.
- **Desfazer.** Histórico de envios por loja, backups e restauração que preserva o que mudou depois.

## Instalar

1. Baixe `zirtuno-sync-yampi.zip` e extraia numa pasta fixa. Não carregue o ZIP direto; o download **Source code** do GitHub não é a extensão pronta.
2. Abra `chrome://extensions`, ative **Modo do desenvolvedor**, clique em **Carregar sem compactação** e selecione a pasta que contém `manifest.json`.
3. Fixe o ícone da extensão na barra do Chrome. É preciso o Chrome 120 ou posterior.

Para atualizar, extraia a versão nova sobre a mesma pasta e clique em **Recarregar** no cartão da extensão. Usar outra pasta cria outra instalação e separa os backups. Não atualize no meio de um envio: a operação para, sem desfazer nada, e pode continuar depois. [Instruções completas](docs/INSTALACAO.md).

## Usar

Abra o editor de código da loja (`https://app.yampi.com.br/store/code-editor/`) e clique no ícone da extensão. O painel abre ao lado, na mesma aba, e não faz nada sozinho. A tela inicial tem três opções:

| Opção | Para quê |
|---|---|
| **Enviar alterações** | Levar para a loja o que você editou no computador |
| **Baixar tema** | Baixar uma cópia do código em ZIP |
| **Histórico e backups** | Desfazer um envio, baixar backups e registros |

Cada tela tem um título que diz o estado e uma ação principal na barra de baixo. **‹ Início** volta à tela inicial, e clicar no ícone da extensão também. O botão **…**, no alto, abre o histórico e os backups em qualquer tela. Fora do editor de código, o ícone abre uma orientação com o botão **Abrir o editor de código**.

### Baixar o tema

1. Escolha **Baixar tema** e confira o nome da loja no alto do painel.
2. Deixe **Incluir a vitrine publicada** ligado, para a prévia local ficar parecida com a loja, e clique em **Baixar ZIP**. O Chrome pede permissão para ler a vitrine; se você negar, o código é baixado com uma prévia de exemplo.
3. Imagens, fontes e a API pública de catálogo ficam em outros endereços. O painel lista esses endereços e espera **Autorizar e terminar o ZIP**, ou **Baixar sem esses recursos**.
4. O ZIP é baixado no fim, mesmo com o painel fechado. A tela final mostra quantos arquivos, imagens e páginas da prévia vieram, e **Mostrar o ZIP** abre a pasta do download.

Durante a cópia, nada é editado, salvo, criado, excluído, renomeado ou publicado na Yampi.

### Editar no computador

Extraia o ZIP numa pasta própria, fora deste repositório, e abra a pasta (ou o seu `.code-workspace`) no VS Code. Com Node.js 24, no terminal dessa pasta:

```powershell
npm ci
npm run check:integrity
npm run check
npm run dev
```

`npm run dev` abre a prévia em `http://127.0.0.1:5182`. As páginas capturadas abrem nos mesmos caminhos da loja, e `/__local/` lista as capturas e os templates. Edite só `tema/` e mantenha `.yampi-sync/`: é com ela que a extensão compara e envia.

| Pasta ou arquivo | Conteúdo |
|---|---|
| `tema/` | O código do tema, para editar |
| `.yampi-sync/baseline/` e `.yampi-sync/manifest.json` | A cópia original, com tamanhos e SHA-256, para comparar e enviar |
| `.yampi-sync/tools/` | Ferramentas da prévia (Twig, Sass, Vue 2), integridade e retorno |
| `preview/` | Captura da vitrine, leituras públicas, recursos, manifesto e relatório |
| `local.data.json` | Só os ajustes que você fizer nos dados da prévia (começa vazio) |
| `local.config.json` | Templates, estilos, mapa de seções e porta da prévia |

`npm run check:integrity` confere o SHA-256 e o tamanho dos originais e lista o que mudou no projeto; com `-- --original`, falha se algo mudou. `npm run pack` gera um ZIP de retorno e lista os arquivos alterados e novos; `npm run check:integrity -- --return-zip retorno-yampi.zip` confere se esse ZIP tem exatamente o manifesto, os originais e `tema/`.

### Enviar alterações

1. Escolha **Enviar alterações** e a pasta do projeto, ou solte a pasta no painel. Da segunda vez em diante, **Reler “nome da pasta”** faz isso num clique. Também vale o ZIP de `npm run pack`, em **Usar um ZIP de retorno**.
2. A comparação começa sozinha. Ela relê na loja só o que o projeto mudou e os textos que esses arquivos usam. Depois de mudar algo no projeto, **Comparar de novo** relê a pasta escolhida; com Alt+clique, relê também o tema inteiro na loja.
3. Confira o resumo (alterados, novos e imagens). O que precisa de você aparece em **Precisa da sua atenção**, uma linha por item, com a ação ao lado:
   - **Arquivo que mudou na loja e no projeto:** **Comparar e escolher** mostra as linhas que mudaram. **Usar a versão do projeto** envia a sua; a da loja fica no backup.
   - **Arquivo que usa outro que mudou só na loja:** **Ver a mudança** mostra o que mudou na loja. **Usar no projeto** grava essa versão no arquivo da sua pasta, com a sua permissão, e compara de novo; **Copiar** é para colar à mão.
4. Clique no botão de envio (por exemplo, **Enviar 3 itens para Minha Loja**) e confirme em **Enviar agora**. O backup é baixado antes da primeira mudança. Pode fechar o painel ou trocar de aba: o envio continua, o ícone mostra a porcentagem e, com o painel fechado, um aviso do sistema diz quando terminou.
5. No fim, **Abrir a prévia** abre o rascunho na Yampi. Gravar pelo editor muda só a prévia; os clientes veem as mudanças depois de **Publicar** na Yampi, sempre à mão. Se algo não estiver certo, use **Desfazer este envio**.

### O que é enviado

| Situação | Resultado |
|---|---|
| Mudou no projeto; a loja está como na cópia | Enviado |
| Projeto e loja já iguais | Nada a gravar |
| Mudou só na loja | A versão da loja fica |
| Mudou no projeto e na loja, de formas diferentes | Conflito: fica a versão da loja, a menos que você escolha **Usar a versão do projeto** |
| Ausente no projeto | Nada é excluído |
| Texto novo (`.twig`, `.vue`, `.scss`) numa pasta que existe na loja | Criado pela extensão e preenchido |
| Texto novo numa pasta nova, ou com nome que a Yampi recusa | Bloqueia todo o envio, com o motivo |
| Imagem nova | Enviada para `assets/images`; vinda de subpasta, com as referências ajustadas |
| Imagem existente alterada | Não enviada: a Yampi não substitui imagens |
| `elements/scripts/components.twig` | Nunca enviado: a Yampi gera esse arquivo |
| Usa um arquivo que não vai existir na loja | Bloqueado, com o nome do arquivo |
| Usa um arquivo que mudou só na loja ou está em conflito | Bloqueado até você levar a mudança ao projeto ou decidir o conflito |
| Arquivos alterados que usam uns aos outros | Enviados juntos |
| Diferença só de espaço em branco | Mostrada à parte, fora da seleção; pode ser descartada |

Nomes de textos novos aceitam letras, números, `_` e `-`. Imagens novas podem ser PNG, JPEG, WebP ou SVG de até 2 MB.

### Como o envio protege a loja

- **Antes de gravar:** o editor é recarregado, os arquivos que vão mudar são relidos e precisam estar como na comparação, e os caminhos novos precisam continuar livres. Qualquer diferença para tudo antes da primeira gravação.
- **Backup:** fica guardado no navegador e é baixado antes da primeira mudança. Sem ele, nada é gravado.
- **Ordem:** primeiro os textos novos, criados pelo **Adicionar arquivo** do editor; depois as imagens, pelo envio de imagens da Yampi. Cada grupo é conferido depois de recarregar o editor. Em seguida, cada texto é salvo, o editor é recarregado e o texto é relido antes do próximo. No fim, todos são relidos.
- **Erro:** o envio para no item em andamento e guarda o registro. **Continuar de onde parou** não refaz o que já foi conferido e para se alguém mudou um arquivo na loja durante a interrupção.
- **Desfazer:** volta só o que continua como foi enviado; o que mudou depois aparece como conflito. Arquivos criados voltam ao conteúdo inicial da Yampi; nada é excluído.
- **Nunca:** publicar, excluir, renomear, criar pastas ou substituir imagens.

Regras completas em [SPEC.md](docs/SPEC.md).

**Imagens.** A Yampi guarda imagens só direto em `assets/images`. Uma imagem nova de subpasta recebe um nome lá, e toda referência literal ao caminho antigo é ajustada no que vai para a loja; o seu projeto não muda, e a prévia continua usando as subpastas. Um caminho montado em tempo de execução, como `'images/hero/' ~ nome`, não pode ser ajustado e bloqueia o arquivo, com o motivo.

### Histórico, backups e restauração

**Histórico e backups**, na tela inicial ou no **…**, lista os envios desta loja. Para cada um, **Restaurar este envio** prepara a volta ao estado anterior (com a mesma revisão e confirmação de um envio), **Baixar o backup** baixa os textos de antes do envio e **Baixar o registro** baixa o que foi feito, item a item. **Apagar os dados locais** remove deste navegador as cópias originais, o projeto carregado e os backups guardados; os ZIPs já baixados continuam no computador.

## Estado da validação

- **Editor fictício:** baixar, comparar, enviar, retomar e restaurar, em testes automáticos que reproduzem a marcação do editor real, incluindo o painel fechado no meio do envio. A gravação na pasta do projeto foi testada com uma pasta simulada. [Resultados](docs/VALIDACAO.md).
- **Loja real (0.5.0, 16/10/2026):** **Adicionar arquivo** criou 18 textos novos (Twig, Vue e SCSS), cada um conferido após recarregar; uma imagem foi enviada para `assets/images` com os mesmos bytes do projeto; o backup veio antes da primeira gravação; e o envio parou no primeiro erro com o registro guardado. Esse erro, na conferência da imagem, foi corrigido na 0.5.1.
- **Ainda sem validação real:** a gravação de textos alterados, as demais imagens, o caminho que `assets_url` gera em `assets/images` e a restauração. Também tudo o que a 0.6.0 acrescentou: execução em segundo plano, painel fechado no meio do envio, retomada, conferência em grupo, comparação parcial e a gravação na pasta do projeto pelo seletor de pastas do Chrome.
- **Captura da vitrine:** a captura da 0.4.0 ainda não tem execução registrada numa vitrine real. Também sem validação: o formato da API pública além de coleções, banners e produtos, e as regras exatas do minificador da plataforma.

No primeiro envio real, acompanhe o painel e confira tudo na prévia da Yampi antes de publicar. Testes de gravação numa loja real dependem de ambiente e arquivo escolhidos pelo proprietário.

### Limitações

- A Yampi não oferece transação no servidor: uma edição feita por outra pessoa entre a leitura e o salvamento ainda pode acontecer. Não edite a loja ao mesmo tempo nem troque de loja ou de tema durante um envio.
- Com a aba do editor em segundo plano, o navegador desacelera a página: o envio continua, mais devagar. Fechar a aba do editor cancela a operação.
- A extensão exige o Chrome 120 ou posterior; outros navegadores não foram testados. A prévia local usa Vue 2, fora de manutenção: use projetos confiáveis e mantenha o servidor em `127.0.0.1`.
- Na prévia local, `assets_url` aponta para `tema/assets/`; o caminho de produção ainda não foi conferido.
- A prévia é uma amostra datada da vitrine (preços da data da captura). Não exporta o catálogo nem as configurações da loja, e carrinho, checkout, pagamentos, formulários e gravações ficam bloqueados.

## Prévia local e captura da vitrine

A captura lê os objetos públicos da vitrine (`window.themeConfig`, `window.data`, `window.merchant`) de forma estática: só literais, objetos, listas e JSON declarativo, sem executar a página e sem cookies. O resultado fica sem alteração em `preview/raw/`. Uma lista de bloqueio remove segredos (`token`, `session`, `password`, chaves de API) e IDs de rastreamento; nome, documento, contatos e endereço da empresa, e-mails, CPF e CNPJ viram `"redacted"`.

- **Contrato único.** `local-runtime/contract.mjs` transforma a captura nas variáveis do tema (`header`, `footer`, `mainSections`, `pageConfig.data`, `customData`, `context`). A mesma função serve a captura, `npm run check`, `npm run dev` e os testes.
- **Seções por alias.** `get_section_file` procura `sections/<página>/<alias>.twig`, depois sem o prefixo `main_`, depois `sections/global/`; páginas institucionais usam `sections/content/`. Um alias sem arquivo é erro explícito.
- **Páginas e leituras públicas.** Primeiro uma página de cada tipo (home, categoria, produto, institucional, promoção, busca). As coleções, banners e produtos pedidos pelas seções visíveis são lidos uma vez na API pública, sem credenciais, e reproduzidos com filtro por `id[]`, `slug` e `limit`. Uma rota não gravada gera erro com o caminho.
- **Recursos.** CSS servido e fontes primeiro (WOFF2, `latin` e `latin-ext`), depois logotipo, ícones, banners e produtos. PNG, JPEG, WebP, SVG, CSS e fontes são validados, identificados por SHA-256 e deduplicados. URLs privadas, credenciais em URL, redirecionamentos, tipos desconhecidos e SVG ativo são recusados.
- **Limites.** 8 páginas, 200 recursos, 12 MiB de recursos, 2 MiB por recurso e por página, 40 leituras públicas (4 MiB) e 15 segundos por leitura.
- **Diagnóstico.** `preview/report.json` separa código, configuração, contrato, recursos, compilação e visual. `npm run check` compila cada entrada Sass, compara com o CSS publicado e grava `.cache/yampi-check.json` com as seções renderizadas por página. A prévia mostra uma barra quando falta seção, imagem ou fonte, e `/__local/diagnostic.json` traz o mesmo diagnóstico. Uma página que abre com HTTP 200 não prova que a aparência está certa.

A cópia aceita temas de até 3.000 arquivos e 32 MiB, com até 2 MiB por arquivo.

## Atualizar projetos antigos

Faça uma nova exportação **em outra pasta**. Depois, na pasta deste repositório:

```powershell
node scripts/update-project.mjs "C:/caminho/do/projeto" --preview "C:/caminho/nova-exportacao.zip"
node scripts/update-project.mjs "C:/caminho/do/projeto" --preview "C:/caminho/nova-exportacao.zip" --apply
node scripts/update-project.mjs "C:/caminho/do/projeto" --rollback .yampi-sync/updates/<data> --apply
```

O primeiro comando só mostra o plano; o segundo aplica; o terceiro desfaz. Só `.yampi-sync/tools/`, `package.json`, `package-lock.json` e `preview/` são substituídos; `tema/`, os originais, o manifesto e `local.config.json` ficam como estão. Um `local.data.json` antigo (cópia completa dos dados) vai para o backup e dá lugar a ajustes vazios. Ferramentas editadas à mão, links e dependências em conflito são recusados. O registro e os bytes anteriores ficam em `.yampi-sync/updates/`, e o rollback recusa arquivos alterados depois da atualização. Nada é executado do projeto, e a Yampi não é chamada. Depois, rode `npm ci`, `npm run check:integrity`, `npm run check` e `npm run pack` no projeto.

Sem `--preview`, só as ferramentas e o pacote mudam. Prévias capturadas pela 0.3.x abrem em modo de compatibilidade, com dados incompletos: faça uma nova exportação.

## Privacidade e permissões

Sem telemetria. A extensão só fala com o editor da Yampi aberto na aba em que você clicou e com os endereços públicos que você autorizar.

| Permissão | Uso |
|---|---|
| `activeTab`, `scripting` | Ler e operar o editor só na aba em que você clicou no ícone |
| `sidePanel` | O painel ao lado do editor |
| `storage` | Sessões e estado das operações em andamento |
| `downloads` | Baixar o ZIP, os backups e os registros |
| `offscreen` | Executar as operações com o painel fechado |
| `notifications` | Avisar o fim de uma operação quando o painel está fechado |
| `https://*/*` (opcional) | Ler a vitrine e os seus recursos, pedida endereço por endereço e só ao baixar com a vitrine |

- **Pasta do projeto.** O Chrome pede acesso de leitura quando você escolhe a pasta. A única escrita fora da loja é **Usar no projeto**: o Chrome pede permissão de escrita, e só um arquivo de `tema/` que já existe é trocado, se continuar como na comparação. Nada é criado.
- **Isolamento.** O worker só aceita comandos de um painel ou de uma operação autenticados para aquela aba, confere a loja e o endereço do editor em cada comando e, nas leituras públicas, confere a loja antes e depois, sem cookies, sem redirecionamento e sem referrer.
- **Dados locais.** Originais, histórico e backups ficam no IndexedDB deste navegador, separados por loja, sem criptografia adicional. ZIPs e backups podem ter conteúdo privado: guarde-os fora deste repositório.
- **Fontes.** Atkinson Hyperlegible Next e Mono (SIL OFL 1.1) vêm no pacote; nada é carregado da internet. [Licenças](docs/TERCEIROS.md).

## Desenvolvimento

Com Node.js 24:

```powershell
npm ci
npm run check
npm run test:e2e
npm run test:preview
npm run test:compression
npm run audit:performance
npm run package
```

- `npm run check` roda TypeScript, os testes unitários e o build em `dist/`.
- `npm run test:e2e` instala a extensão num Chromium temporário, com painel lateral nativo e editor fictício: exportação, envio com o painel fechado no meio, restauração, reconexão e captura da vitrine. Nenhuma requisição chega à Yampi.
- `npm run test:preview` exporta três lojas fictícias, instala cada projeto com `npm ci` e confere a prévia em Chromium. `npm run test:compression` confere que o ZIP feito em segundo plano é igual ao síncrono e pode ser cancelado. `npm run audit:performance` mede o build num cenário sintético.
- `npm run dev` abre a demonstração fictícia em `http://127.0.0.1:5181/demo.html?demo=1`.
- `npm run package` atualiza a pasta pronta para carregar, `releases/zirtuno-sync-yampi/` (23 arquivos permitidos, sempre no mesmo lugar), e gera em `.cache/package/` os ZIPs `zirtuno-sync-yampi-<versão>.zip` e `zirtuno-sync-yampi.zip`, com SHA-256. Essas pastas ficam fora do Git; o instalador público vai em Releases.

Os testes usam só lojas e dados fictícios: o editor fictício reproduz a marcação do editor real, e duas vitrines fictícias são comparadas pixel a pixel. Gravação numa loja real nunca entra nos testes automáticos.

| Onde | O quê |
|---|---|
| `src/panel.ts`, `src/panel/` | O painel: telas, pasta do projeto, diferenças por linha e vidro das barras |
| `src/jobs/engine.ts`, `src/browser/runner.ts` | Operações em segundo plano (baixar, comparar, enviar) no documento offscreen |
| `src/browser/background.ts` | Worker: sessões por aba, bloqueios, permissões, downloads, ícone e avisos |
| `src/browser/bridge.ts`, `adapter.ts` | Leitura e gravação no editor da Yampi |
| `src/core/workflow.ts` | Captura, envio, conferência e retomada |
| `src/core/planner.ts`, `compare.ts`, `explain.ts` | Comparação original/projeto/loja, leitura parcial e motivos de bloqueio |
| `src/core/workspace.ts`, `src/storage.ts` | Dados por loja no IndexedDB |
| `src/core/preview.ts`, `src/browser/preview-capture.ts` | Formato, sanitização e captura da vitrine |
| `local-runtime/` | Ferramentas do projeto exportado: prévia, contrato, dependências, integridade e retorno |
| `scripts/` | Build, pacote, testes de ponta a ponta e atualização de projetos |

Documentos: [SPEC](docs/SPEC.md) (o contrato do envio), [INTERFACE](docs/INTERFACE.md) (desenho do painel), [VALIDACAO](docs/VALIDACAO.md) (evidências por versão), [JOURNAL](docs/JOURNAL.md) (decisões datadas), [ROADMAP](docs/ROADMAP.md) (o que falta), [INSTALACAO](docs/INSTALACAO.md) e [TERCEIROS](docs/TERCEIROS.md).

## Versões

- **0.6.0** — Operações em segundo plano com o painel fechado, progresso com tempo restante, retomada de envios interrompidos, envio mais rápido com a mesma conferência e comparação que relê só o que mudou. Painel refeito, com uma tela por etapa, diferenças por linha e bloqueios explicados, além de **Usar no projeto** para trazer uma mudança da loja.
- **0.5.1** — A conferência de imagens decodifica os endereços `data:` do editor, que a política de segurança da página não deixa baixar.
- **0.5.0** — Envio automático: textos novos criados pelo **Adicionar arquivo**, imagens novas enviadas pelo editor, conflito resolvido arquivo a arquivo.
- **0.4.0** — Prévia local fiel à vitrine: contrato único, captura anônima, leituras públicas e diagnóstico. [Relatório](docs/RELATORIO-0.4.0.md).

As versões anteriores estão no [diário](docs/JOURNAL.md) e no histórico do Git.

## Nome e repositório

O nome no Chrome e no painel é **Zirtuno Sync Yampi**, e o pacote é `zirtuno-sync-yampi`. O ícone usa a logo fornecida pelo proprietário, cuja versão original está em `assets/zirtuno-sync-yampi-logo.png`. O nome interno `yampi-theme-sync` continua nos formatos de exportação e armazenamento, para abrir projetos e históricos anteriores. As versões são publicadas em [ZIRTUNO/zirtuno-sync-yampi](https://github.com/ZIRTUNO/zirtuno-sync-yampi). O repositório original, [JonathanDelmonte/yampi-virtual-store-code-extractor-synchronizer](https://github.com/JonathanDelmonte/yampi-virtual-store-code-extractor-synchronizer), guarda o histórico até a 0.4.0.

Este repositório público contém só a ferramenta e exemplos fictícios. Temas, exportações, backups e dados de lojas ficam fora dele.

Referências: [editor de código da Yampi](https://docs.yampi.com.br/editor-codigo/intro), [painel lateral do Chrome](https://developer.chrome.com/docs/extensions/reference/api/sidePanel), [documentos offscreen](https://developer.chrome.com/docs/extensions/reference/api/offscreen), [File System Access](https://developer.chrome.com/docs/capabilities/web-apis/file-system-access).
