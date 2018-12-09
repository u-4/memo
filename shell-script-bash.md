# shell-script

いわゆるシェルスクリプトについて。POSIXとか気にするべきことはあるようだが、基本的にはmacOSのbashで利用できることを最重視する。

[UNIX & Linux コマンド・シェルスクリプト リファレンス](https://shellscript.sunone.me/)

困ったらこれを見るのが良さそう。

> シェルスクリプトは他のプログラミング言語と違い、if や for といった制御文もコマンドであり、それらを含めてすべてコマンドの羅列にすぎない。シェルスクリプトの場合は、シンタックスエラーもコマンドエラーとして考えるとわかりやすいと思う。

この考え方はとても分かり易かった。だから変数を定める時の`=`の前後にスペースがあると変数名をコマンドとして実行しようとしてエラーになり、`[`コマンド内での`=`はパラメータなのでスペースが挟まると。

[初心者向け、「上手い」シェルスクリプトの書き方メモ - Qiita](https://qiita.com/m-yamashita/items/889c116b92dc0bf4ea7d)

[初心者向けシェルスクリプトの基本コマンドの紹介 - Qiita](https://qiita.com/zayarwinttun/items/0dae4cb66d8f4bd2a337)

[Bash の if 文（test文）のオプションを整理してみた - Qiita](https://qiita.com/wakayama-y/items/a9b7380263da77e51711)

## 初期設定

```bash
#!/bin/bash
set -eu
umask 0022
export LC_ALL='C'
export PATH="$(command -p getconf PATH):$PATH"
```

### ヘッダー

```bash
#!/bin/bash
#
# @(#) hoge.sh ver.1.0.0 2008.04.24
#
# Usage:
#   hoge.sh param1 param2
#     param1 - パラメータ1です.
#     param2 - パラメータ2です.
#
# Description:
#   hoge.shスクリプトです.
#
###########################################################################
```

[シェルスクリプト Tips | UNIX & Linux コマンド・シェルスクリプト リファレンス](https://shellscript.sunone.me/tips.html)

### shebang

一番最初の

```bash
#!/bin/sh
```

と書いてある部分をシバンというらしい。自分が描くときはPOSIX準拠かとか気にすることは（でき）ないのでbash前提で

```bash
#!/bin/bash
```

と書く方針で。

### `set`でエラーと未定義変数対応

[シェルスクリプトを書くときはset -euしておく - Qiita](https://qiita.com/youcune/items/fcfb4ad3d7c1edf9dc96)

```bash
set -eu
```

とする。作成中は`-x`もあっても良さそう。途中でエラーになった場合および未定義の変数（スペルミスとか）があった場合にスクリプト処理が止まるようになる。安全のためにも基本的には設定するようにしておく。shebangに引き続いて書くこともできるが、`bash ./test.sh`などとした時に無効になってしまうので`set -eu`の形が良い。途中で一時的に解除したい場合には

```bash
#!/bin/bash
set -eu

echo "通常の処理"

set +e

echo "一時的に除外したい処理"

set -e
```

のような形で行えば良い。

#### `set -e`

エラーがあったら（`exit 0`以外が返ったら）止まってくれる。ただし`command1 || command2`みたいなことができなくなるので、`set +e`してエラー絡みの処理してから再び`set -e`する方法もある。

#### `set -u`

未定義の変数を使おうとすると止めてくれる。

```bash
rm -rf $TMEP_DIR/
```

みたいな。これは恐ろしい。

#### `set -x`

実行したコマンドが全て標準エラー出力に投げてくれる。デバッグのためにはとても便利そう。以下のように都度行うこともできる。

```bash
bash -x XXXX.sh
```

また何もしない`:`コマンドを併用することで標準エラー出力にだけコメントを出力することができる。

```bash
: "記号利用の可能性も考えて常にダブルクォーテーションで括ってしまうのが良さそう"
```

### 作成ファイルのパーミッション設定

セキュリティのためにスクリプトで作成されるファイルのアクセス権限をあらかじめ制限しておくと良いとのこと。（[参照](http://myfuturesightforpast.blogspot.com/2016/12/how-to-initialize-posix-shell-script.html#20161219T2153_umask-0022)）

```bash
umask 0022
```

だと自分のみ読み書き可、他は読み込みのみ（`-rw-r--r--`）になる。本当に厳しくするときは`0077`を設定すれば`-rw-------`になる。

### （参考）パーミッション（権限）の設定

まあここからきちんと覚えていないのでついでにメモ。表現は`-rwxr--r--`のように10桁の文字列で行われ、内容は

略号  | 内容  | 数字
:---: | :---: | :---:
  r   | 読み  |   4
  w   | 書き  |   2
  x   | 実行  |   1

で、所有者（owner/**u**ser）、グループ（**g**roup）、その他（**o**ther）の順に並んでいる。全て（**a**ll）も指定のために用いられる。一応1文字目はファイル(`-`)、ディレクトリ（`d`）、シンボリックリンク（`l`）の3種類。

全て許可の場合は

- ファイル：`-rw-rw-rw-`（666）
- ディレクトリ：`drwxrwxrwx`（777）

#### `umask`

全て許可の場合から引き算をする形。

```bash
umask 022
```

とすると

- ファイル：`-rw-r--r--`（644）
- ディレクトリ：`drwxr-xr-x`（755）

になる。

#### `chmod`

```bash
chmod [対象ユーザ][変更方法][権限の表現] 対象ファイル
chmod 644 対象ファイル
```

で指定してファイル/ディレクトリの権限を変更する。対象ユーザは（`ugo/a`）で指定（組合せ可）、変更方法は追加（`+`）、剥奪（`-`）、設定（`=`）、表現は上記の`rwx`（組合せ可）で行う。

### 言語の設定

日本語環境だと帰ってくる値が変わったりするので設定してしまうらしい（[参照](http://myfuturesightforpast.blogspot.com/2016/12/how-to-initialize-posix-shell-script.html#20161219T2153_export-LC_ALL='C')）。

```bash
export LC_ALL='C'
```

### パスの設定

一応`PATH`が設定されていない環境ではエラーが出て止まる（[参照](http://myfuturesightforpast.blogspot.com/2016/12/how-to-initialize-posix-shell-script.html#20161219T2153_export-PATH=%22$(command--p-getconf-PATH):$PATH%22)）

```bash
export PATH="$(command -p getconf PATH):$PATH"
```

## 変数

[変数を使用する | UNIX & Linux コマンド・シェルスクリプト リファレンス](https://shellscript.sunone.me/variable.html)

[bash - シェルスクリプトのfor文で配列変数を使いたい。 - スタック・オーバーフロー](https://ja.stackoverflow.com/questions/24130/%E3%82%B7%E3%82%A7%E3%83%AB%E3%82%B9%E3%82%AF%E3%83%AA%E3%83%97%E3%83%88%E3%81%AEfor%E6%96%87%E3%81%A7%E9%85%8D%E5%88%97%E5%A4%89%E6%95%B0%E3%82%92%E4%BD%BF%E3%81%84%E3%81%9F%E3%81%84/24138)

## 配列

[配列を使用する | UNIX & Linux コマンド・シェルスクリプト リファレンス](https://shellscript.sunone.me/array.html)

## 位置パラメータ

[位置パラメーターの一括展開 $* $@ "$*" "$@" の違いを知れ!! - Shell Script Advent Calendar 2016 - ダメ出し Blog](https://fumiyas.github.io/2016/12/15/positional-parameters.sh-advent-calendar.html)

## ワイルドカード

いわゆる正規表現ではないことに注意。

## よく使うコマンド

### `:`ヌルコマンド

何の処理も行わず正常終了するだけのコマンド。パラメータとしてテキストを取ることが可能で、`set -x`でのデバッグ時などに利用できる。`true`を返して`exit status 0`相当になる？

### 今後まとめる

1. 制御・条件判定系: [, test, expr, true, false, yes, getopts
2. テキスト処理系: cat, awk, sed, tr, sort, uniq, grep, wc, head, tail, cut,
　paste, comm, join
3. ファイル検索系: find ( スペースなどを含むファイル名を正しく処理するため、
　find は -exec command {} + を推奨。
　他に -print0、xargs は -0 オプションという方法もあるが Solaris 非対応 )
4. ディレクトリ系: basename, dirname
5. 出力系: echo, printf
6. 対話コマンド制御系: expect
7. http / ftp の処理自動化: wget, curl

## よく使う表現

あちらこちらからの頂き物がメインに。可及的に参照先を記載すること。

### 一時ファイル/ディレクトリの作成 `mktemp`

[今だけ必要なファイル/ディレクトリを作る mktemp](https://qiita.com/m-yamashita/items/889c116b92dc0bf4ea7d#%E4%BB%8A%E3%81%A0%E3%81%91%E5%BF%85%E8%A6%81%E3%81%AA%E3%83%95%E3%82%A1%E3%82%A4%E3%83%AB%E3%83%87%E3%82%A3%E3%83%AC%E3%82%AF%E3%83%88%E3%83%AA%E3%82%92%E4%BD%9C%E3%82%8B-mktemp)

[安全な一時ファイルの作成と削除の方法 - 拡張 POSIX シェルスクリプト Advent Calendar 2013 - ダメ出し Blog](https://fumiyas.github.io/2013/12/06/tempfile.sh-advent-calendar.html)

適当に作ると他の処理とかぶったりする可能性があること、同様の処理は常に同様に行えるようにするために`mktemp`コマンドを利用する。

```bash
atexit() {
  [[ -n ${TEMP_FILE-} ]] && rm -f "$TEMP_FILE"
}

trap atexit EXIT
trap atexit HUP INT QUIT TERM

TEMP_FILE=$(mktemp --tmpdir ${0##*/}.tmp.XXXXXXXXXX)
```

#### `mktemp`コマンド詳細

ファイル名はデフォルトでは`tmp.XXXXXXXXXX`が使用される。ファイル名を使用するなら`任意のファイル名.XXXX`のように最後に3個以上の連続する`X`をつける必要がある。ファイルは`u+rw`から`umask`設定分を差し引いたもの、ディレクトリは`u+rwx`から`umask`設定分を差し引いたものが作成される。

作成されるディレクトリはデフォルトでは暗黙に引数なしの`--tmpdir`が指定されているため`$TMPDIR`以下に作られる。`$TMPDIR`はmacOSでは`/var/folders/XX/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/X/`のような文字列でかつ更新される（`/tmp`に決め打ちして攻撃されないように？）。

まあ実質的には`mktemp`でファイルが、`mktemp -d`でディレクトリが作られると思っておけば良いか……。

```bash
# 一時ディレクトリ/ファイルを作成する。
TEMP_FILE=$(mktemp)
TEMP_DIR=$(mktemp -d)
```

`$TMPDIR`に（シェルスクリプトのファイル名）.tmp.XXXXXXXXXXで作成するには

```bash
mktemp --tmpdir ${0##*/}.tmp.XXXXXXXXXX
```

### `trap`で終了時処理

[シグナルと trap コマンド | UNIX & Linux コマンド・シェルスクリプト リファレンス](https://shellscript.sunone.me/signal_and_trap.html)

```bash
trap `コマンド` シグナルリスト
```

シグナルリストにあるシグナルを検知したらコマンドを実行する。一時ファイルの消去など強制終了時処理に必要なシグナルリストは「`1 2 3 15`」でよい。略号なら`HUP INT QUIT TERM`。"コマンド"のなかに`exit`を明示的に入れない方が終了ステータスをそのまま送れる模様。変数で`$?`を拾って最後に`exit $変数`にしているコードが多いがテストした限りでは結局全部`0`になってしまっている気が……

```bash
trap "コマンド" EXIT
trap "コマンド" HUP INT QUIT TERM
```

シェルスクリプトが終了した時の共通処理を書きたければ

```bash
trap `終了時処理` EXIT
```

の様に`EXIT`と記載した方が`0`とシグナル番号だけ書くよりも意図が伝わりやすい。

#### 主要シグナル番号

番号 | 略号 | シグナルが持つ意味
 :---: | :---: | :---
0 | EXIT | プロセス終了時に自分自身に対して送られるEXITシグナル。
1 | HUP | Windowのクローズやデーモンのリセットに使用されるハングアップシグナル。
2 | INT | `Ctrl+C`や`Del`キーを押したときに発生する割り込みシグナル。
3 | QUIT | `Ctrl+\`を押したときに発生するクイットシグナル。
9 | KILL | プロセスを強制終了するためのキルシグナル。強制終了なのでtrapできない。
15 | TERM | プロセスを終了のためのシグナル。`kill`コマンドのデフォルトシグナル(`kill PID`は`kill -15 PID`と同じ)。

### 変数が定義されているかのチェック

[POSIXシェルで変数がセットされているか検査するイディオム - Qiita](https://qiita.com/blackenedgold/items/4f91a174e0f63ee42699)

### `[]`と`[[]]`の違い

[`test` と `[` と `[[` コマンドの違い - 拡張 POSIX シェルスクリプト Advent Calendar 2013 - ダメ出し Blog](https://fumiyas.github.io/2013/12/15/test.sh-advent-calendar.html)から

1. ワード分割とパス名展開がされない
2. 数値の比較演算子では左右の値が算術式展開される
3. 文字列の比較演算子 == の動作が異なる
4. 文字列の比較演算子の種類が多い
5. 論理演算子が異なる

空白文字の問題があるので`[`で変数使うときは`"`で囲むべき。

### スクリプト自身のファイル名・ディレクトリを取り出す

`${0}`でスクリプト名が得られる。ファイル名だけ・ディレクトリだけにするには`basename`・`dirname`コマンドを用いるか、

`bash`の機能である

- `##文字列`で変数内の文字列を先頭から最長一致で取り除く
- `%文字列`で変数内の文字列を後方から最短一致で取り除く

を用いて

ファイル名:
`${0##*/}`

ディレクトリ名:
`${0%/*}`

として得ることができる。suffixを取り除きたければ`%.sh`などとすることもできる。

### 標準入力の`IFS= read -r VAR`での読み込み

`$IFS`は単語の区切りとして認識する文字列（デリミタ）が入っていて、デフォルトでは``$' ¥t¥n'``（スペース、タブ、改行）が設定されている。このため`read`で受ける標準入力に空白文字などが含まれていると処理が意図せぬ形になる可能性がある。`IFS= read -r VAR`のように`IFS`に（この`read`コマンドの間だけ）空白文字を設定した上で実行することで、自動文字区切りを一時的に無効にすることができる。なおIFSは「Internal Field Separator」の略らしい。