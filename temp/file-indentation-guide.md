# ファイル種別ごとのインデント規約

このドキュメントは、ファイルの種類ごとに「スペースが必要か」「タブで問題ないか」を整理した一覧です。

## 結論

- YAML (`.yml`, `.yaml`) はスペース必須
- Makefile のコマンド行はタブ必須
- Markdown はスペース推奨。Prettier が不安定な場合は検査を無効化する
- JavaScript / TypeScript / JSON / CSS / C / PowerShell などはタブを使える
- Biome と Prettier を同一ファイル種別に同時適用しない

## スペースが必須または実質必須のファイル

| ファイル種別 | 推奨インデント | 理由 |
| --- | --- | --- |
| `.yml` | スペース | YAML の構造インデントにタブは使えない |
| `.yaml` | スペース | YAML の構造インデントにタブは使えない |
| GitHub Actions workflow | スペース | YAML 形式 |
| `dependabot.yml` | スペース | YAML 形式 |
| Kubernetes マニフェスト | スペース | YAML 形式 |
| Ansible playbook | スペース | YAML 形式 |
| YAML front matter | スペース | YAML の構造記法にタブは使えない |

YAML のインデントは Prettier の既定である「スペース 2 個」になるのが一般的です。

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

| ファイル種別 | タブ使用 | 備考 |
| --- | --- | --- |
| `.js` | 可 | Biome と相性がよい |
| `.jsx` | 可 | Biome と相性がよい |
| `.ts` | 可 | Biome と相性がよい |
| `.tsx` | 可 | Biome と相性がよい |
| `.json` | 可 | JSON でもタブは有効 |
| `.jsonc` | 可 | JSONC でもタブは有効 |
| `.css` | 可 | 構文上問題なし |
| `.scss` | 可 | 構文上問題なし |
| `.less` | 可 | 構文上問題なし |
| `.c` | 可 | C 言語のインデントとして使用可能 |
| `.h` | 可 | C/C++ ヘッダーとして使用可能 |
| `.ps1` | 可 | PowerShell では使える |
| `.sh` | 可 | シェルスクリプトのインデントとして使える |
| `.bash` | 可 | Bash スクリプトのインデントとして使える |
| `.cmd` | 可 | バッチ系ファイルとして使える |
| `.bat` | 可 | バッチ系ファイルとして使える |
| `.html` | 可 | HTML でもタブは可能 |
| `.xml` | 可 | XML でもタブは可能 |
| `.svg` | 可 | SVG は XML ベース |
| `.sql` | 可 | SQL ではタブを使えることがある |
| `.toml` | 通常可 | プロジェクト規約とツール次第 |
| `.ini` | 通常可 | パーサー次第 |
| `.cfg` | 通常可 | パーサー次第 |
| `.conf` | 通常可 | パーサー次第 |

## タブが必須のファイル

### Makefile

`make` では、コマンド行の先頭にタブが必須です。

```makefile
build:
	echo "build"
```

スペースに変えると失敗します。

## Markdown の扱い

Markdown はタブも技術的には使えますが、行頭のタブはコードブロックやネスト構造として解釈されることがあり、読みにくさやバージョン差異の原因になります。

```markdown
- item
  - nested item
```

そのため、Markdown はスペースを推奨します。Prettier で差分が止まる場合は、検査自体を無効にする方が安定です。

```yaml
VALIDATE_MARKDOWN_PRETTIER: false
```

## フォーマッターの役割分担

Biome と Prettier を同じファイル種別に同時適用すると、無限ループが起きることがあります。

```text
Biome    -> タブへ変換
Prettier -> スペースへ変換
Biome    -> タブへ戻す
Prettier -> スペースへ戻す
```

そのため、役割を分けます。

| ファイル | 推奨する担当 | インデント |
| --- | --- | --- |
| JavaScript / TypeScript | Biome | タブ |
| JSON / JSONC | Biome | タブ |
| YAML | Prettier または YAML 専用 lint | スペース |
| Markdown | Markdown lint または無効化 | スペース推奨 |
| Makefile | make の規約に従う | タブ必須 |

## `.editorconfig` の例

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

## このリポジトリでの推奨方針

- YAML はスペース 2 個で統一
- JSON / JS / TS は Biome のタブ運用
- Markdown は Prettier を無効化することがある
- Makefile のレシピ行だけタブを維持
- Biome と Prettier を同一ファイルに同時適用しない

## まとめ

インデントのルールはファイル種別で決めるのが正しいです。YAML だけはスペース必須であり、Makefile だけはタブ必須です。その他のコード系はタブを使って問題ない場合が多く、Markdown はスペースに寄せるのが安全です。
