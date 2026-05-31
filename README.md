# coding-styleguide

個人開発用またAIプロンプトのためコーディング規約・スタイルガイドを残します。

## 規約の構成

- `common-style.md`: 全言語共通のスタイルガイド（ログ出力文章の構文統一、ログレベルの厳格な運用など）
- `"LANGUAGE"-style.md`: 言語別のスタイルガイド

## Clone

WSL 環境のプロジェクトルートにて、以下のコマンドを実行することで、VSCode Copilot の指示書としてインポートすることが可能です。

共通の指示書をクローンする：
```bash
mkdir -p .github && : > .github/copilot-instructions.md
curl -s https://raw.githubusercontent.com/taketake-dev/coding-styleguide/main/docs/ai-behavior.md >> .github/copilot-instructions.md
curl -s https://raw.githubusercontent.com/taketake-dev/coding-styleguide/main/docs/design-policy.md >> .github/copilot-instructions.md
curl -s https://raw.githubusercontent.com/taketake-dev/coding-styleguide/main/docs/common-style.md >> .github/copilot-instructions.md
```

言語ごとの指示書を追加する：

- `Python`

```bash
curl -s https://raw.githubusercontent.com/taketake-dev/coding-styleguide/main/docs/python-style.md >> .github/copilot-instructions.md
```

## ⚠️ **注意：`.gitignore` への追加を推奨**

生成される `.github/copilot-instructions.md` は、ローカルの開発環境や使用する言語に合わせて動的に作成されるファイルです。チーム開発のリポジトリを汚さないよう、プロジェクト側の `.gitignore` に追加することを強く推奨します。

```bash
echo -e "\n# GitHub Copilot Instructions\n.github/copilot-instructions.md" >> .gitignore
```
