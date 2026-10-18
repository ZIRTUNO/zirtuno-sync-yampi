# Projeto exportado e prévia local

O ZIP baixado pela extensão é um projeto completo: o código do tema, a cópia original usada na comparação e as ferramentas para ver a loja no computador.

## Estrutura

| Pasta ou arquivo | Conteúdo |
|---|---|
| `tema/` | O código do tema, para editar |
| `.yampi-sync/baseline/` e `.yampi-sync/manifest.json` | A cópia original, com tamanho e SHA-256 de cada arquivo |
| `.yampi-sync/tools/` | Ferramentas da prévia (Twig, Sass, Vue 2), integridade e retorno |
| `preview/` | Captura da vitrine, leituras públicas, recursos, manifesto e relatório |
| `local.data.json` | Ajustes nos dados da prévia; começa vazio |
| `local.config.json` | Templates, estilos, mapa de seções e porta da prévia |

Edite só `tema/`. A extensão compara o projeto com `.yampi-sync/`, por isso essa pasta precisa ficar como veio.

## Comandos

Com Node.js 24, no terminal da pasta do projeto:

```powershell
npm ci
npm run check:integrity
npm run check
npm run dev
```

| Comando | O que faz |
|---|---|
| `npm run dev` | Abre a prévia em `http://127.0.0.1:5182`, com as páginas capturadas nos mesmos caminhos da loja; `/__local/` lista capturas e templates |
| `npm run check` | Compila todas as páginas, confere se cada seção visível foi renderizada e compara o CSS compilado com o publicado; grava `.cache/yampi-check.json` |
| `npm run check:integrity` | Confere o SHA-256 e o tamanho dos originais e lista o que mudou; com `-- --original`, falha se algo mudou |
| `npm run pack` | Gera um ZIP de retorno e lista os arquivos alterados e novos; `npm run check:integrity -- --return-zip <arquivo>` confere esse ZIP |

O envio normalmente é feito escolhendo a pasta do projeto na extensão. O ZIP de `npm run pack` é a alternativa quando a pasta não pode ser selecionada.

## Captura da vitrine

Com **Incluir a vitrine publicada** ligado, a extensão lê os objetos públicos da vitrine (`window.themeConfig`, `window.data` e `window.merchant`) de forma estática, sem executar a página e sem cookies, e grava o resultado sem alteração em `preview/raw/`. Segredos (`token`, `session`, `password`, chaves de API) e IDs de rastreamento são removidos. Nome, documento, contatos e endereço da empresa, e-mails, CPF e CNPJ viram `"redacted"`.

`local-runtime/contract.mjs` transforma a captura nas variáveis do tema (`header`, `footer`, `mainSections`, `pageConfig.data`, `customData` e `context`). A mesma função serve a captura, `npm run check`, `npm run dev` e os testes. `get_section_file` procura `sections/<página>/<alias>.twig`, depois sem o prefixo `main_`, depois `sections/global/`; páginas institucionais usam `sections/content/`. Um alias sem arquivo é erro explícito.

A captura começa por uma página de cada tipo (home, categoria, produto, institucional, promoção e busca). Coleções, banners e produtos pedidos pelas seções visíveis são lidos uma vez na API pública, sem credenciais, e reproduzidos na prévia com filtro por `id[]`, `slug` e `limit`. Uma rota não gravada gera erro com o caminho.

Recursos seguem uma ordem: CSS e fontes (WOFF2, `latin` e `latin-ext`), depois logotipo, ícones, banners e produtos. PNG, JPEG, WebP, SVG, CSS e fontes são validados, identificados por SHA-256 e deduplicados. URLs privadas, credenciais em URL, redirecionamentos, tipos desconhecidos e SVG ativo são recusados.

| Limite | Valor |
|---|---|
| Páginas | 8 |
| Recursos | 200, até 12 MiB no total e 2 MiB cada |
| Leituras públicas | 40, até 4 MiB |
| Tempo por leitura | 15 segundos |
| Tema | 3.000 arquivos, 32 MiB, 2 MiB por arquivo |

## Diagnóstico

`preview/report.json` separa código, configuração, contrato, recursos, compilação e visual. Na prévia, uma barra avisa quando falta seção, imagem ou fonte, e `/__local/diagnostic.json` traz o mesmo diagnóstico. Uma página que abre não prova que a aparência está igual à da loja.

## Limitações

- A prévia é uma amostra datada da vitrine: os preços são os da data da captura. Carrinho, checkout, pagamentos, formulários e gravações ficam bloqueados.
- O catálogo e as configurações da loja não são exportados.
- Na prévia, `assets_url` e `asset()` apontam para `tema/assets/`; na loja, as imagens novas ficam em `assets/images`.
- A prévia usa Vue 2, fora de manutenção, e reproduz o Twig da Yampi de forma compatível, não idêntica. Use projetos confiáveis e mantenha o servidor em `127.0.0.1`.

Para levar um projeto exportado por uma versão antiga às ferramentas atuais, veja [Atualizar um projeto antigo](ATUALIZAR-PROJETO.md).
