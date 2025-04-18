---
title: "Atcoder 環境構築 Python編"
emoji: "🐈"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: [Atcoder, 環境構築]
published: true
---

## はじめに

本記事は(2025/04/18)時点のものです。

本記事は、WSL2 + atcoder-cliとonline-judge-toolsを用いたpython用の環境構築を備忘録としてまとめた記事です。C++は一部置き換えることで同様に環境構築することができます。

WSL2 + Ubuntu 24.04.1 LTSで検証しています。WSL2は必須ではないですが、WSL2を使った方が分かりやすく、windowsの環境と競合しないため、オススメします。

online-judge-toolsは以下の機能を有するツールです。

- サンプルケースを取得
- システムケースを取得
- ログイン
- コードを提出
- テストを実行
- リアクティブ問題のテストを実行
- テストケース生成器からテストケースの入力を生成
- テストケースの入力と愚直解からテストケースの出力を生成

atcoder-cliは上記のojと組み合わせて、

- テンプレートの自動展開
- サンプルケースの自動ダウンロード
- その他AtCoderに特化した機能

を持ちます。

それではれっつごー！

## 前提条件

- Node.jsが導入済み
- pythonが導入済み
- python3 venvが導入済み

:::details pythonのインストール
### pythonのインストール

・WSLの場合
```bash:bash
sudo apt update
sudo apt install python3 python3-pip
```

```bash
python3 -V
```

```:結果
Python 3.12.3
```

:::

### 仮想環境の作成

OSによって管理するため、pipで環境を破壊しないためのルールとしてPEP 668というものがあります。そのため、pipを使うには仮想環境を作成して分ける必要があります。なので仮想環境の準備をします！

#### 必要なパッケージをインストール
```bash
sudo apt install python3-venv
```

#### 仮想環境の作成
```bash
python3 -m venv myenv
```

#### 仮想環境のアクティベート

```bash
source myenv/bin/activate
```

```:結果
(myenv) {ユーザ名}@{PC名}:~$
```

となれば成功です！

::::details Node.jsおよびnpmのインストール
### Node.jsおよびnpmのインストール

[公式のページ](https://nodejs.org/ja/download)に従って、fnmと呼ばれるNode.jsのバージョン管理ツールをインストールします。そのあと好きなバージョンのNode.jsとnpmをインストールします。

```bash
curl -o- https://fnm.vercel.app/install | bash
```

:::message alert
エラーがでた場合
:::

:::details 対処法
```bash
Checking dependencies for the installation script...
Checking availability of curl... OK!
Checking availability of unzip... Missing!
Not installing fnm due to missing dependencies.
```

NANI?!
どうやらunzipという依存パッケージが見つからないようなので、インストールしてあげます！

```bash
sudo apt install unzip
```

次に、再度fnmをインストールします。
:::

-----
無事完了したらPATHが追加されてるので、一度ターミナルを**再起動**したあと、fnmでNode.jsをインストールします！

```bash
fnm install 22
```

#### Node.jsとnpmのバージョン確認
```bash
node -v
npm -v
```

```結果
v22.14.0
10.9.2
```
::::
お疲れ様です！下準備の完了です！

### atcoder-cliのインストール

[atcoder-cli/わたしろぐ](http://tatamo.81.la/blog/2018/12/07/atcoder-cli/)を参考にしてインストールします。

```bash
npm install -g atcoder-cli
```

インストールできたか確認
```bash
acc -v
```

```:結果
2.2.0
```

### online-judge-toolsのインストール

[online-judge-tools](https://github.com/online-judge-tools/oj/blob/master/docs/getting-started.ja.md)を参考にしてインストールします。
PEP 668の影響で--userオプションはつけずに実行します。
```bash
pip3 install online-judge-tools
```

インストールできたか確認
```bash
oj --version
```

```:結果
online-judge-tools 11.5.1 (+ online-judge-api-client 10.10.1)
```

accから認識されているか確認
```bash
acc check-oj
```

```bash:結果
online-judge-tools is available. found at:
/home/{ユーザー名}/myenv/bin/oj
```
### setuptoolsのインストール

online-judge-toolsでは、python3.12以降で廃止されたdistutilsというモジュールを参照しています。そのため、python3.12でインストールして実行するとエラーがでます。その対策としてsetuptoolsをインストールします。

参考記事: [【inshellisense】ModuleNotFoundError: No module named 'distutils' の対処法](https://qiita.com/pitao/items/1740a62ddee797aed807)


### acloginのインストール

前まではユーザーIDとパスワードの組み合わせによる認証でしたが、現在(2025/4/18時点)AtCoderの仕様が少し変わり、認証に**Cloudflare Turnstile**が追加されました。
（これはJavaScriptを実行させてクライアントがユーザーらしい動きをしているかどうか判定するものっぽい）
これにより、accのログインなどのスクリプトが正常に機能しなくなっています。有志様による修正パッチスクリプト[aclogin](https://github.com/key-moon/aclogin)
をインストールします。

```bash
pip3 install aclogin
```

くぅ～疲れましたw
これにてインストール完結です！

## セットアップ

まず各ツールのログインを行います。
先述した通り、Cloudflare Turnstileの認証でacc loginが機能していないので、acloginを用いてログインします。

[aclogin](https://github.com/key-moon/aclogin)を参考に認証情報のクッキーをコピーします。

その後

```bash
aclogin
```

を実行してコピーしたクッキーを貼り付けます。
そして各ツールのログインを行います。

```bash
acc login
```

```bash
you logged-in already
OK
```

```bash
oj login https://atcoder.jp
```

```bash
[SUCCESS] You have already signed in.
```

### テンプレートファイルの追加
```.config\atcoder-cli-nodejs```以下に構成ファイルを作成していきます。

```bash
cd $(acc config-dir)
mkdir template_py
cd template_py
```

カレントディレクトリである```template_py```に、テンプレートとなる```template.json```, ```main.py```を作成します。
ちなみに、WSL2からエクスプローラーを起動するコマンドは次の通りです。

```bash
explorer.exe .
```

```json:template.json
{
    "task": {
        "program": [
            "main.py"
        ],
        "submit": "main.py"
    }
}
```

```python:main.py
import bisect
import math
import sys
import copy
#import numpy as np # Pypyでは使えない
from collections import deque,defaultdict,Counter
from itertools import permutations,combinations,product,accumulate
from array import array # 連続メモリ上の配列（数値型で使用，高速）
# al=[chr(ord('a') + i) for i in range(26)]
# Al=[chr(ord('A') + i) for i in range(26)]
DEBUG = False  # デバッグ時は True、本番環境では False
def debug_print(*args, **kwargs):
    if DEBUG:
        print(*args, **kwargs)
        
#n = int(input())
```

ここのファイルや名前を、main.cppに置き換えればcppの構成にすることも可能です。

#### accの設定

先ほどの```template_py```ディレクトリをテンプレートファイルとして登録します。

```bash
 acc config default-template template_py
```

次に、accを使ってあるコンテストの問題をダウンロードするときに、デフォルトで全問題をダウンロードするように変更します。

```bash
acc config default-task-choice all
```

早速動作するか確認してみましょう
問題を解く用のディレクトリを好きな場所に作成してください！
作成したらそのディレクトリをVScodeで開いてください。

本記事ではルート直下に```py-coder```を作成します。

```bash
cd
mkdir py-coder
cd py-coder
code .
```
などを実行してVScodeで開きます。

VScodeで開けたら、```ctl+@```を押してターミナルを起動します。
次のコマンドを実行して、コンテスト用ディレクトリの自動作成を行ってみましょう。

```bash
acc new abc321
```

![テスト](public\images\vscode_screenshot.png)


これでツールのセットアップは完了です！！！
これでほぼ完成まできました！！
あと少しだけです！

## ショートカットキー

次のVScode拡張機能を入れます。

- Command Runner/edonet
- WSL/Microsoft
- Python/Microsoft

```py-coder```直下に```.vscode```フォルダを作成します。

そこにsettings.jsonを作成します。内容は下記の通りです。
ここではcommand-runnerにコマンドのエイリアスを登録しています。

- ```oj test```は作業中の親ディレクトリに移動して、python3でojのテストコマンドを実行するエイリアスです。

- ```acc submit```は作業中の親のディレクトリ名に移動したのち、accの提出コマンドをpypyで実行し、確認の入力をechoで自動化したエイリアスです。

- ```execute python```は開いてるpythonファイルを実行するエイリアスです。

```json:settings.json
{
    "command-runner.commands": {
        "oj test": "cd ${fileDirname} && oj t -c \"python3 ${fileBasename}\" -d tests",
        "acc submit": "cd ${fileDirname} && echo abc$(basename $(pwd)) | acc s ${file} -- --guess-python-interpreter pypy",
        "execute python": "cd ${fileDirname} && python3 ${fileBasename}"
    }

}
```

次にVScodeで```ctl + k```+```ctl + s```を押してキーボードショートカットの設定画面に移ります！
下記の内容を追記してします。

```json:keybindings.json
    {
        "key": "ctrl+shift+t",
        "command": "command-runner.run",
        "args": {
          "command": "oj test"
        }
      },
      {
        "key": "ctrl+shift+oem_plus",
        "command": "command-runner.run",
        "args": {
          "command": "acc submit"
        }
      }
      ,{
        "key": "ctrl+shift+e",
        "command": "command-runner.run",
        "args":{
          "command": "execute python"
        }
      }
```

```main.py```を開いた状態で```ctl + shift + t```を押してみてください！
自動でサンプルケースのテストが行われるはずです！
テストの確認した後```ctl + shift + +```を押すと提出ができます！

これにて環境構築ほぼ終了です！あとはお好みにあわせて変更してください！
お疲れ様でした！！