# coding-styleguide

個人開発用またAIプロンプトのためコーディング規約・スタイルガイドを残します。

## 規約の構成

- `common-style.md`: 全言語共通のスタイルガイド（ログ出力文章の構文統一、ログレベルの厳格な運用など）
- `"LANGUAGE"-style.md`: 言語別のスタイルガイド

## Clone

WSL 環境のプロジェクトルートにて、以下のコマンドを実行することで、VSCode Copilot の指示書としてインポートすることが可能です。

Pythonの例：
```bash
mkdir -p .github && : > .github/copilot-instructions.md
curl -s https://raw.githubusercontent.com/taketake-dev/coding-styleguide/main/docs/common-style.md > .github/copilot-instructions.md
curl -s https://raw.githubusercontent.com/taketake-dev/coding-styleguide/main/docs/python-style.md >> .github/copilot-instructions.md
```
