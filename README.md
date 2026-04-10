# seira-actions

[seira](https://github.com/FullStackShell/seira) 用の GitHub Actions コレクションです。

## Actions

| Action | Description |
|--------|-------------|
| [`pages`](#pages) | seira doc でドキュメントを生成し GitHub Pages にデプロイ |
| [`build`](#build) | seira build でシェルスクリプトをバンドル |

---

## pages

シェルスクリプトのドキュメントを生成し、GitHub Pages アーティファクトとしてアップロードします。

```yaml
uses: FullStackShell/seira-actions/pages@main
```

### Usage

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
        uses: FullStackShell/seira-actions/pages@main
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

### Inputs

| Input | Required | Default | Description |
|-------|----------|---------|-------------|
| `entrypoint` | No | `""` | シェルスクリプトのエントリポイント (`.seirarc.json` がある場合は省略可) |
| `title` | No | `""` | ドキュメントのタイトル (デフォルト: リポジトリ名) |
| `format` | No | `html` | 出力形式: `html` または `markdown` |
| `seira-version` | No | `main` | インストールする seira のバージョン (Git ref) |
| `go-version` | No | `1.25` | Go のバージョン |
| `artifact-name` | No | `github-pages` | Pages アーティファクト名 |
| `retention-days` | No | `1` | アーティファクトの保持日数 |

### Outputs

| Output | Description |
|--------|-------------|
| `artifact-id` | アップロードされた Pages アーティファクトの ID |

### Requirements

- リポジトリの Settings > Pages で Source を **GitHub Actions** に設定してください。
- ワークフローに `pages: write` と `id-token: write` の permissions が必要です。

---

## build

シェルスクリプトの依存関係を解決し、スタンドアロンの実行可能ファイルにバンドルします。

```yaml
uses: FullStackShell/seira-actions/build@main
```

### Usage

```yaml
name: Build

on:
  push:
    branches: [main]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Bundle shell scripts
        uses: FullStackShell/seira-actions/build@main
        with:
          output: dist/app.sh
          mode: concat
          minify: "true"

      - name: Upload artifact
        uses: actions/upload-artifact@v4
        with:
          name: app
          path: dist/app.sh
```

### Inputs

| Input | Required | Default | Description |
|-------|----------|---------|-------------|
| `entrypoint` | No | `""` | シェルスクリプトのエントリポイント (`.seirarc.json` がある場合は省略可) |
| `output` | No | `output.sh` | 出力ファイルパス |
| `mode` | No | `""` | バンドルモード: `tarball` または `concat` (デフォルト: `.seirarc.json` の設定) |
| `shebang` | No | `""` | Shebang 行 |
| `minify` | No | `false` | ミニファイの有効化 |
| `treeshake` | No | `false` | ツリーシェイキングの有効化 |
| `strip-comments` | No | `false` | コメントの除去 |
| `type` | No | `""` | プロジェクトタイプ: `executable` または `library` |
| `seira-version` | No | `main` | インストールする seira のバージョン (Git ref) |
| `go-version` | No | `1.25` | Go のバージョン |

### Outputs

| Output | Description |
|--------|-------------|
| `output-path` | バンドルされた出力ファイルのパス |

---

## License

MIT
