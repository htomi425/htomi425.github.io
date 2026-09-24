# ファイル種別ごとのインデント規約

ファイルの種類ごとに、インデントへスペースまたはタブを使用できるかを整理した一覧です。

> **表示上の注意**
>
> Markdown 上ではタブと複数のスペースが見分けにくいため、インデント例では次の記号を使います。
>
> - `·` = スペース 1 個
> - `→` = タブ 1 個
>
> 記号は説明用であり、そのままファイルへコピーするものではありません。

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

YAML のインデントは、Prettier の既定であるスペース 2 個が一般的です。

```text
# · はスペースを表す
name: example
jobs:
··build:
····runs-on: ubuntu-latest
····steps:
······-·name: Print message
········run: echo "hello"
```

実際の YAML では、`·` をスペースに置き換えます。

```yaml
name: example
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Print message
        run: echo "hello"
```

## タブを使用できるファイル

| ファイル種別 | タブ使用 | 備考 |
| --- | --- | --- |
| `.js` | 可 | Biome と相性がよい |
| `.jsx` | 可 | Biome と相性がよい |
| `.ts` | 可 | Biome と相性がよい |
| `.tsx` | 可 | Biome と相性がよい |
| `.json` | 可 | JSON の空白としてタブを使用できる |
| `.jsonc` | 可 | JSONC の空白としてタブを使用できる |
| `.css` | 可 | 構文上問題なし |
| `.scss` | 可 | 構文上問題なし |
| `.less` | 可 | 構文上問題なし |
| `.c` | 可 | C 言語の空白として使用可能 |
| `.h` | 可 | C/C++ ヘッダーとして使用可能 |
| `.ps1` | 可 | PowerShell の空白として使用可能 |
| `.sh` | 可 | シェルスクリプトで使用可能 |
| `.bash` | 可 | Bash スクリプトで使用可能 |
| `.cmd` | 可 | バッチファイルで使用可能 |
| `.bat` | 可 | バッチファイルで使用可能 |
| `.html` | 可 | HTML の空白として使用可能 |
| `.xml` | 可 | XML の空白として使用可能 |
| `.svg` | 可 | SVG は XML ベース |
| `.sql` | 可 | SQL の空白として使用可能 |
| `.toml` | 通常可 | プロジェクト規約とツール次第 |
| `.ini` | 通常可 | パーサー次第 |
| `.cfg` | 通常可 | パーサー次第 |
| `.conf` | 通常可 | パーサー次第 |

「タブを使用できる」は、必ずタブにすべきという意味ではありません。フォーマッターや既存コードの規約がある場合は、それに合わせます。

### タブを使ったコード例

次の例では `→` がタブです。

```text
function greet() {
→return "hello";
}
```

実際の JavaScript では、`→` をタブに置き換えます。

```javascript
function greet() {
	return "hello";
}
```

## タブが必須のもの

### Makefile のレシピ行

従来の `make` では、ターゲットに続くコマンド行の先頭にタブが必要です。

視認用の表記:

```text
build:
→echo "build"
```

実際の Makefile では、`→` をタブに置き換えます。

```makefile
build:
	echo "build"
```

このタブをスペースに置き換えると、次のようなエラーになることがあります。

```text
*** missing separator.  Stop.
```

## Markdown の扱い

Markdown はタブも技術的には使えますが、行頭のタブはコードブロックやネスト構造として解釈されることがあります。そのため、Markdown のインデントはスペースを推奨します。

視認用の例:

```text
-·item
··-·nested·item
```

実際の Markdown:

```markdown
- item
  - nested item
```

Prettier のバージョン差異によって折り返し・リスト・テーブルの整形結果が安定しない場合は、Markdown に対する Prettier の検査を無効化する方が安全です。

```yaml
VALIDATE_MARKDOWN_PRETTIER: false
```

## フォーマッターの役割分担

Biome と Prettier を同じファイル種別に同時適用すると、設定差によって無限に差分が発生することがあります。

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
- Markdown はスペースを推奨し、Prettier の差異で問題が出る場合は検査を無効化
- Makefile のレシピ行だけタブを維持
- Biome と Prettier を同一ファイルへ同時適用しない

## まとめ

インデントのルールはファイル種別で決めるのが安全です。YAML はスペース必須、Makefile のレシピ行はタブ必須です。その他のコード系はタブを使える場合が多く、Markdown はスペースに寄せるのが無難です。
