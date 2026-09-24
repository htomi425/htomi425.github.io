# インデントにスペースが必要なファイルとタブを使えるファイル

ファイル形式ごとに、インデントへスペースまたはタブを使用できるかを整理した一覧です。

## 結論

このリポジトリや一般的な開発環境では、次の方針が安全です。

- **YAML はスペース必須**
- **Makefile のレシピ行はタブ必須**
- **Markdown はスペース推奨**
- **JavaScript / TypeScript / JSON などはタブ使用可**
- **Biome と Prettier を同じファイルへ適用しない**

## スペースが必須または実質必須のファイル

| ファイル種別 | 推奨インデント | 理由 |
| --- | --- | --- |
| `.yml` | スペース | YAML の構造インデントにタブを使用できない |
| `.yaml` | スペース | YAML の構造インデントにタブを使用できない |
| GitHub Actions workflow | スペース | GitHub Actions の定義は YAML 形式 |
| `dependabot.yml` | スペース | Dependabot の設定は YAML 形式 |
| Kubernetes マニフェスト | スペース | Kubernetes の設定は YAML 形式 |
| Ansible playbook | スペース | Ansible の設定は YAML 形式 |
| YAML front matter | スペース | front matter 内の YAML 構造にタブを使用できない |

このリポジトリでは、少なくとも次のファイルをスペースで統一します。

```text
.github/workflows/*.yml
.github/workflows/*.yaml
.github/dependabot.yml
```

YAML のインデントは、Prettier の既定値である **スペース 2 個**に任せるのが一般的です。

```yaml
jobs:
  summary:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout repository
        uses: actions/checkout@v4
        with:
          persist-credentials: false
```

## タブを使用できるファイル

以下のファイル形式では、通常、インデントにタブを使用できます。

| ファイル種別 | タブ使用 | 備考 |
| --- | --- | --- |
| `.js` | 可 | Biome のタブ設定と相性がよい |
| `.jsx` | 可 | Biome のタブ設定と相性がよい |
| `.ts` | 可 | Biome のタブ設定と相性がよい |
| `.tsx` | 可 | Biome のタブ設定と相性がよい |
| `.json` | 可 | JSON の空白としてタブを使用できる |
| `.jsonc` | 可 | JSONC の空白としてタブを使用できる |
| `.css` | 可 | 構文上、タブを使用できる |
| `.scss` | 可 | 構文上、タブを使用できる |
| `.less` | 可 | 構文上、タブを使用できる |
| `.c` | 可 | C の空白としてタブを使用できる |
| `.h` | 可 | C/C++ ヘッダーの空白としてタブを使用できる |
| `.ps1` | 可 | PowerShell の空白としてタブを使用できる |
| `.sh` | 可 | シェルスクリプトの空白としてタブを使用できる |
| `.bash` | 可 | Bash スクリプトの空白としてタブを使用できる |
| `.cmd` | 可 | バッチファイルの空白としてタブを使用できる |
| `.bat` | 可 | バッチファイルの空白としてタブを使用できる |
| `.html` | 可 | HTML の空白としてタブを使用できる |
| `.xml` | 可 | XML の空白としてタブを使用できる |
| `.svg` | 可 | XML と同様にタブを使用できる |
| `.sql` | 可 | SQL の空白としてタブを使用できる |
| `.toml` | 通常可 | プロジェクトの規約やツール設定を優先する |
| `.ini` | 通常可 | 使用するパーサーやプロジェクト規約を優先する |
| `.cfg` | 通常可 | 使用するパーサーやプロジェクト規約を優先する |
| `.conf` | 通常可 | 使用するパーサーやプロジェクト規約を優先する |

「タブを使用できる」は、必ずタブにすべきという意味ではありません。フォーマッターや既存コードの規約がある場合は、それに合わせます。

## タブが必須のもの

### Makefile のレシピ行

従来の `make` では、ターゲットに続くコマンド行の先頭にタブが必要です。

```makefile
build:
	echo "build"
```

このタブをスペースに置き換えると、次のようなエラーになることがあります。

```text
*** missing separator.  Stop.
```

ただし、Makefile のすべての行でタブが必須という意味ではありません。特に、変数定義やターゲット定義は通常の形式で記述します。

## Markdown の扱い

Markdown は仕様上タブを扱える箇所もありますが、タブの位置によってはコードブロックやネストしたリストとして解釈されます。

```markdown
- item
  - nested item
```

行頭のタブは、インデント付きコードブロックとして解釈される可能性があります。

```markdown
	code block
```

そのため、Markdown はスペースを推奨します。さらに、Prettier のバージョン差異によって折り返し・リスト・テーブルの整形結果が変わる場合は、Markdown に対する Prettier の検査を無効化する方が安定します。

```yaml
VALIDATE_MARKDOWN_PRETTIER: false
```

## フォーマッターの役割分担

Biome と Prettier が同じファイルを整形すると、設定差によって無限に差分が発生することがあります。

```text
Biome    -> タブへ変換
Prettier -> スペースへ変換
Biome    -> タブへ戻す
Prettier -> スペースへ戻す
```

この問題を避けるため、ファイル種別ごとに担当を分けます。

| ファイル | 推奨する担当 | インデント |
| --- | --- | --- |
| JavaScript / TypeScript | Biome | タブ |
| JSON / JSONC | Biome | タブ |
| YAML | Prettier または YAML 専用 lint | スペース |
| Markdown | 必要に応じて Markdown lint | スペース推奨 |
| PowerShell | PowerShell の既存規約 | タブ使用可 |
| C / C ヘッダー | C 用フォーマッター | タブ使用可 |

つまり、**JSON は Biome のみ、YAML は Prettier のみ、Markdown は Prettier を無効化**という構成が安全です。

## `.editorconfig` の例

エディター側でもファイル種別ごとの方針を統一する場合は、次のように設定できます。

```ini
root = true

[*]
indent_style = tab
indent_size = 4

[*.{yml,yaml}]
indent_style = space
indent_size = 2

[*.md]
indent_style = space
indent_size = 2

[Makefile]
indent_style = tab
```

## このリポジトリでの推奨一覧

### スペースで統一

```text
*.yml
*.yaml
.github/workflows/*
.github/dependabot.yml
*.md
```

### タブを使用可能

```text
*.json
*.jsonc
*.js
*.jsx
*.ts
*.tsx
*.css
*.scss
*.less
*.c
*.h
*.ps1
*.sh
*.bash
*.cmd
*.bat
*.html
*.xml
*.svg
*.sql
```

### タブ必須

```text
Makefile のレシピ行
```

## 最終方針

- YAML はスペース 2 個で統一する
- JavaScript / TypeScript / JSON は Biome のタブ設定を使用する
- Markdown はスペースを推奨し、Prettier のバージョン差異で問題が出る場合は検査を無効化する
- Makefile のレシピ行だけはタブを維持する
- Biome と Prettier を同一ファイルへ適用しない
