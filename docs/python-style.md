# Python共通のスタイル

以下のスタイルに従うこと。例も載せている。

## 使用技術

- uv
- python 3.12 (基本)

## ディレクトリ構成

- .python-version
- pyproject.toml
- uv


## パッケージ化

### やるべき時

- ファイル数が4つ以上になり、自作ファイル間で import が頻繁に発生するとき（例：pipeline.py が draw_ai.py をインポートし、さらに双方が config.py をインポートするような、密結合な構造）
- ポートフォリオとして公開・配布する予定があるとき。企業やプロの現場の自作開発バックエンドは、ほぼ100%パッケージ化（src レイアウト）されている
- 将来的に、そのツールを他のプロジェクトでも使い回したいとき

### しなくていいとき

- 3ファイルほどの .py で import が多発しないとき
- 親プロセスが子プロセスを「独立したコマンド」として外から叩くだけの構造（疎結合）のとき

## 命名規則

- モジュール・パッケージ名：`snake_case`
- クラス名：`PascalCase`
- 関数・メソッド・変数名：`snake_case`
- 定数：`UPPER_SNAKE_CASE`
- プライベート（内部用）：先頭にアンダースコア `_private_variable`

## コード整形

- インデント：スペース4つ
- 空白行：クラスとクラス間は2行、関数と関数間は1行、関数内のロジック区切りは1行（PEP 8に従う）
- 一行の長さ：制限なし（個人開発かつ、制限すると見づらくなるため）

## Docstring

Googleスタイルにすること。以下につけること。

- ファイルの先頭
- クラス
- 関数

```py
def calc_total(amount: int, postage: int, tax_rate: float) -> int:
    """支払合計を計算する関数

    商品購入金額に送料と消費税を加えた支払総額を返します。

    Args:
        amount (int): 商品購入金額

    Raises:
        ValueError: amountやpostageが負の場合
        TypeError: amountやpostageが数値型でない場合

    Returns:
        int: 支払合計金額

    Examples: # 使い方の例
        >>> calc_total(1000, 200, 0.1)
        1320

    Note: # 注意点
        消費税は小数点以下切り上げで計算

    Todo: # 未完成の部分を記述
        割引計算を追加する
    """
```

## タイプアノテーション

```python
def f(price: int, quantity: int=1) -> int | str:

def f() -> int | None:

def process_data(data: list[str]) -> dict[str, int]:
    return {key: len(key) for key in data}
```

### 型一覧

| 型 | 書き |
| --- | --- |
| 整数 | int |
| 浮動小数点 | float |
| 文字列 | str |
| ブール | bool |
| None | None |
| リスト | list[型] |
| タプル | tuple[型, …] |
| 辞書 | dict[キー型, 値型] |
| セット | set[型] |
| 任意の型 | Any |
| 関数型 | Callable |
| ジェネレータ | Generator |
| イテレータ | Iterator |
| クラス名 | <クラス名をそのまま> |

## import

- ファイルの先頭に書くこと
- ライブラリの種類によって分けること
- アルファベット順にすること

```python
# 1. 標準ライブラリ（Pythonに最初から組み込まれているもの）
import os
import sys
from datetime import datetime

# 2. サードパーティ製ライブラリ（pip等でインストールしたもの）
import numpy as np
import requests
from flask import Flask

# 3. 自作モジュール / ローカルライブラリ（同じプロジェクト内のファイル）
from myproject import config
from utils import helper
```

## ロギング

`print()` ではなく `logging` モジュールを使う。

```python
import logging

logger = logging.getLogger(__name__)

def process_data(data):
    logger.info("処理を開始します")
    try:
        result = data / 0
        logger.debug(f"結果: {result}")
    except ZeroDivisionError as e:
        logger.error(f"ゼロ除算エラー: {e}")
    finally:
        logger.info("処理を終了します")
```

ロギングレベル：`DEBUG < INFO < WARNING < ERROR < CRITICAL`

## エラー処理

具体的な例外をキャッチすること。汎用 `Exception` は最後。`with` 構文を使うことで、`finally` での close 忘れを防ぐ。

```python
try:
    with open("file.txt") as file:
        data = file.read()
except FileNotFoundError:
    logger.error("ファイルが見つかりません")
except Exception as e:
    logger.error(f"予期しないエラー: {e}")
```

## 環境変数

### 設定値とパラメータの切り分け（CLI / config / .env）

1. CLI引数
   ユーザーが毎回の実行で変更したいもの
   - 投稿時間
   - Youtubeに投稿するか否か

2. config.pyに書く
   デフォルトの値を書く
   - デフォルトでYoutubeには投稿しない
3. .env
   - APIkey
   - 使いたいモデルのパス

### config.py

マジックナンバーはconfigにまとめること。configから.envを読み込むこと。

以下を入れること

- ディレクトリパス
- 数値、パラメータ
- ディレクトリの位置、output
- 出力ファイル名
- プロンプト
- その他

### .env

#### python-dotenvの場合

```py
from pathlib import Path
from dotenv import load_dotenv

# resolve() を挟むことで、実行環境によるパスのブレを完全に防ぐ
# .parents[1] など、階層に合わせて正確に指定する（例は1階層上のルート直下を想定）
BASE_DIR = Path(__file__).resolve().parent
ENV_FILE_PATH = BASE_DIR / ".env"

# .env ファイルが実際に存在する場合のみ読み込む（存在チェックを入れるのがプロの技）
if ENV_FILE_PATH.exists():
    load_dotenv(dotenv_path=ENV_FILE_PATH)
```

#### pydantic-settingsを使う場合

```py
from pathlib import Path
from pydantic_settings import BaseSettings, SettingsConfigDict

# プロジェクトのルートディレクトリ
BASE_DIR = Path(__file__).resolve().parent


class Settings(BaseSettings):
    """Pydanticが裏で自動的に .env を探してパースしてくれます。"""

    # ここに欲しい環境変数を型ヒント付きで定義するだけ
    DISCORD_WEBHOOK_URL: str
    SHUTDOWN_SECONDS: int = 60

    # 内蔵されている dotenv パース機能の定義
    model_config = SettingsConfigDict(
        # ここでパスを指定するだけで、存在チェックもパースも全部ツールが代行します
        env_file=BASE_DIR / ".env",
        env_file_encoding="utf-8",
        case_sensitive=True,
        extra="ignore",
    )


# シングルトンとしてエクスポート
config = Settings()
```

## CLI引数

### 小〜中規模（argparse）

```python
import argparse

parser = argparse.ArgumentParser(description="画像生成ツール")
parser.add_argument("input_file", type=str, help="入力ファイルのパス")
parser.add_argument("--output", "-o", type=str, default="output.png", help="出力ファイルのパス")
parser.add_argument("--strength", "-s", type=float, default=0.8, help="強度パラメータ")
parser.add_argument("--verbose", "-v", action="store_true", help="詳細ログを出力")

args = parser.parse_args()
```

### 大規模（Typer）

Python 3.10 以降は `Annotated` を使った現代的な書き方を推奨。

```python
from typing_extensions import Annotated
import typer

app = typer.Typer()

@app.command()
def process(
    input_file: Annotated[str, typer.Argument(help="入力ファイル")],
    output: Annotated[str, typer.Option("--output", "-o", help="出力ファイル")] = "output.png",
    strength: Annotated[float, typer.Option("--strength", "-s", help="強度パラメータ")] = 0.8,
):
    """画像生成ツール"""
    print(f"入力: {input_file}, 出力: {output}")

if __name__ == "__main__":
    app()
```