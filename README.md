# seira-pages

[seira](https://github.com/FullStackShell/seira) のドキュメント生成機能を使い、GitHub Pages にデプロイする GitHub Action です。

## Usage

リポジトリの `.github/workflows/` に以下のようなワークフローを追加してください。

```yaml
name: Deploy Documentation

on:
  push:
    branches: [main]
  workflow_dispatch:

permissions:
  contents: read
  pages: write
  id-token: write

concurrency:
  group: "pages"
  cancel-in-progress: false

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Build documentation
        uses: FullStackShell/seira-pages@main
        with:
          title: "My Project Docs"

  deploy:
    needs: build
    runs-on: ubuntu-latest
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    steps:
      - name: Deploy to GitHub Pages
        id: deployment
        uses: actions/deploy-pages@v4
```

## Inputs

| Input | Required | Default | Description |
|-------|----------|---------|-------------|
| `entrypoint` | No | `""` | シェルスクリプトのエントリポイント (`.seirarc.json` がある場合は省略可) |
| `title` | No | `""` | ドキュメントのタイトル (デフォルト: リポジトリ名) |
| `format` | No | `html` | 出力形式: `html` または `markdown` |
| `seira-version` | No | `main` | インストールする seira のバージョン (Git ref: ブランチ, タグ, コミットSHA) |
| `go-version` | No | `1.25` | seira のビルドに使用する Go のバージョン |
| `artifact-name` | No | `github-pages` | Pages アーティファクト名 |
| `retention-days` | No | `1` | アーティファクトの保持日数 |

## Outputs

| Output | Description |
|--------|-------------|
| `artifact-id` | アップロードされた Pages アーティファクトの ID |

## Examples

### `.seirarc.json` を使う場合

プロジェクトルートに `.seirarc.json` があれば、`entrypoint` の指定は不要です。

```yaml
- uses: FullStackShell/seira-pages@main
```

### エントリポイントを明示的に指定

```yaml
- uses: FullStackShell/seira-pages@main
  with:
    entrypoint: src/main.sh
    title: "My Shell Library"
```

### Markdown 形式で出力

```yaml
- uses: FullStackShell/seira-pages@main
  with:
    format: markdown
```

## Requirements

- リポジトリの Settings > Pages で Source を **GitHub Actions** に設定してください。
- ワークフローに `pages: write` と `id-token: write` の permissions が必要です。

## License

MIT
