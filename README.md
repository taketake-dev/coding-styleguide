# coding-styleguide

個人開発用またはAIプロンプトのためコーディング規約・スタイルガイドを残します。

## 規約の構成

- (./style/common/): 全言語共通のスタイルガイド（ログ出力文章の構文統一、ログレベルの厳格な運用など）
- (./style/`<Language>`): 言語別のスタイルガイド

## Clone

WSL 環境のプロジェクトルートにて、以下のコマンドを実行することで、指示書としてインポートすることが可能です。

以下のコマンドを実行すると、次の操作が行われます。

- `docs/coding-style.md` を作成
- コーディングスタイルを `docs/coding-style.md` に統合
- `AGENTS.md` に `docs/coding-style.md` へのリンクを追加

### `Python`

```bash:
mkdir -p docs

curl -fsSL https://raw.githubusercontent.com/taketake-dev/coding-styleguide/main/style/common/common-style-summary.md > docs/coding-style.md
curl -fsSL https://raw.githubusercontent.com/taketake-dev/coding-styleguide/main/style/python/python-style-summary.md >> docs/coding-style.md

cat >> AGENTS.md <<'EOF'
## Coding Style
- [Coding Style](docs/coding-style.md)
EOF
```
