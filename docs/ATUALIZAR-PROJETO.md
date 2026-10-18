# Atualizar um projeto antigo

Projetos exportados por versões anteriores podem receber as ferramentas e a prévia atuais sem perder o que foi editado em `tema/`.

1. Faça uma nova exportação da mesma loja, **em outra pasta**.
2. Na pasta deste repositório, veja o plano e depois aplique:

```powershell
node scripts/update-project.mjs "C:/caminho/do/projeto" --preview "C:/caminho/nova-exportacao.zip"
node scripts/update-project.mjs "C:/caminho/do/projeto" --preview "C:/caminho/nova-exportacao.zip" --apply
```

3. No projeto atualizado, rode `npm ci`, `npm run check:integrity`, `npm run check` e `npm run pack`.

Para desfazer:

```powershell
node scripts/update-project.mjs "C:/caminho/do/projeto" --rollback .yampi-sync/updates/<data> --apply
```

## O que muda

Só `.yampi-sync/tools/`, `package.json`, `package-lock.json` e `preview/` são substituídos. `tema/`, os originais, o manifesto e `local.config.json` ficam como estão. Um `local.data.json` antigo, com a cópia completa dos dados, vai para o backup e dá lugar a ajustes vazios.

Sem `--preview`, só as ferramentas e o pacote mudam, e a captura existente é mantida. Prévias capturadas pela 0.3.x abrem em modo de compatibilidade, com dados incompletos: prefira uma nova exportação.

## Proteções

- Ferramentas editadas à mão, links e dependências em conflito com as ferramentas são recusados.
- Os bytes anteriores e o registro da atualização ficam em `.yampi-sync/updates/`.
- O rollback recusa arquivos alterados depois da atualização.
- Nenhum script do projeto é executado, e a Yampi não é chamada.
