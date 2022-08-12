# atcoder-cli の設定例

## このリポジトリの目的
**`atcoder-cli`** のインストール、初回作業、設定例を示す。

## `atcoder-cli`とは
`AtCoder`のコードをローカルの`VSCode`で書き、デバッグし、提出するまでの流れを補助してくれるコマンドラインツール。
[この記事](https://qiita.com/Adaachill/items/3d4ddad56c5c2cc372cd)が参考になる。

[atcoder初心者こそ環境構築しよう！\(atcoder\-cli,online\-judge\-toolsのインストール、使い方） \- Qiita](https://qiita.com/Adaachill/items/3d4ddad56c5c2cc372cd)

## 前提条件
- `MacOS` これを書いている今は Monterey を使用中
- `VSCode` 
- `homebrew`インストール済み
- `Python` インストール済み
- `Node.js` インストール済み

## インストール
`atcoder-cli`を使う場合、`online-judge-tools`も一緒にインストールするとよい。

- `online-judge-tools`のインストールと確認
`oj`は`online-judge-tools`のコマンド名。
```zsh
$ pip3 install online-judge-tools 
$ oj -h
```
- `atcoder-cli`のインストールと確認
`acc`は`atcoder-cli`のコマンド名。
```zsh
$ npm -g install atcoder-cli
$ acc -h
```

## 初回作業
コマンドから出力されるメッセージは省略。
### `acc`と`oj`の連携確認
- `acc`…`atcoder-cli`コマンド名
- `oj`…`online-judge-tools`コマンド名
```zsh
$ acc check-oj
online-judge-tools is available. found at:
/Users/username/.pyenv/shims/oj
```
### `acc`で`AtCoder`にログイン
```zsh
$ acc login
? username: aromarious
? password: [hidden]
OK
$
```

### `oj`で`AtCoder`にログイン
```zsh
$ oj login https://atcoder.jp/
Username: aromarious
Password: 
$
```

### `atcoder-cli`を設定しておく
まず現在の設定を見ておく。
```zsh
$ acc config
oj-path: /Users/username/.pyenv/shims/oj
default-contest-dirname-format: {ContestID}
default-task-dirname-format: {tasklabel}
default-test-dirname-format: tests
default-task-choice: inquire
default-template:
```
- `oj-path`<br>…`acc check-oj` コマンドで設定される。（それぞれの環境によって違う）
- `default-ナンチャラ-dirname-format` <br>…これはコンテスト、問題、テストデータのディレクトリを作る時にどんな名前で作るかを設定。デフォルトで構わない。
- `default-task-choice`<br>…コンテストを取得する時に、その中に問題（タスク）が複数ある。どれを取ってくるか設定する。
  - `inquire`…メニュー形式で問い合わせてくるので選ぶ
  - `all`…全タスクを取得する
  - `none`…タスクは取ってこない（コンテスト情報を取得し、コンテスト用ディレクトリを作成するだけ）
  - `rest`…未取得のタスクを全部とってくる
  - `next`…未取得のタスクのうち、一番若い番号のものをとってくる。

```
$ acc config-dir
/Users/username/Library/Preferences/atcoder-cli-nodejs
$ cd $(acc config-dir)
$ pwd
/Users/username/Library/Preferences/atcoder-cli-nodejs
```

↑ここのディレクトリをこのリポジトリに置いた。
```
/Users/username/Library/Preferences/atcoder-cli-nodejs
├── config.json … acc config で確認・設定できる
├── python3/ …テンプレート名でディレクトリを作る。
│   ├── template.json …解答コード用のファイルはこれ、提出用のファイルはこれ、という設定をする
│   └── solve.py* …解答コード用のファイルを作るときの原本
└── session.json …ログイン情報が入っているのでいじらない
```
`oj test` で取得したテストケースを実行するが、その時にスクリプトファイルを直接実行してもらうために
- she bang (1行目に`#!/usr/bin/env python`)を書く
- ファイルに実行フラグを立てる（`chmod +x`しておく）
ということをしている。

`oj`のコマンドラインオプションで`-c "python solve_ナンチャラ.py` と明示指定する場合はこの小細工は必要ない。

## コンテストごとにやる作業
### コンテスト用のディレクトリを作成
```zsh
$ acc new abc081
```

これを実行すると、次のディレクトリやファイルができる。
```
abc081/ …コンテスト用ディレクトリ
├── a/ …タスク用ディレクトリ
│   ├── solve_abc081_a.py* …解答用ファイル
│   └── tests/ …テストデータ用ディレクトリ
│       ├── sample-1.in
│       ├── sample-1.out
│       ├── sample-2.in
│       └── sample-2.out
└── contest.acc.json …このコンテストの取得状況等
```

### 解答用ファイルにコードを書く
`VSCode`などで好きに書く。

### テストデータを流す
```
$ cd タスク用ディレクトリ
$ oj test -c 解答用ファイル名 -d tests
```

### 提出する
```zsh
$ cd タスク用ディレクトリ
$ acc submit 解答用ファイル
```

### 次のタスクをとってくる
```zsh
$ cd コンテスト用ディレクトリ ←タスク用ディレクトリでも構わない
$ acc add
```

