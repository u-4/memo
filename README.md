# `memo`コマンドによるメモ帳

[Memo Life For You](https://github.com/mattn/memo)のGo言語の`memo`コマンドおよびそれにインスパイアされたVSCodeで利用しやすい[vscode-memo-life-for-you](https://marketplace.visualstudio.com/items?itemName=satokaz.vscode-memo-life-for-you)（[日本語ReadMe](https://github.com/satokaz/vscode-memo-life-for-you/blob/master/README_ja.md)）による各種メモ取りライフ。個別のメモから日付形式によるlogまで対応し、ripgrepで検索をかけてくれる。howmとかPalmWiki的に使いたい。

## 方針

- 技術メモは基本的に全てここに
- Rの分をどうするか、ファイルは.mdか.rmdか


## バックアップ・バージョン管理・公開のバランス

- 技術系のメモは全て`memo`で取りたい
- バックアップはしっかりしたいし、ローカルでもwebでも見たい
- しっかりまとめたメモについてはバージョン管理をしたいし、場合により人に見せたい
- 環境再構築の際はWebで見られると便利
- まとまっていないメモは検索にかかればいいし、人にはとても見せられない
- blogもhtmlもわからないので選んで公開とかは無理

### 解決方法

- `memo config`/`Memo: Confit`の`memodir`をDropbox内に置くことでちょっとバージョン管理こみのバックアップおよび同期
- `memodir`はgit管理して[GitHub](https://github.com/u-4/memo)も利用
- `memo new`/`Memo: New`で作成される`YYYY-MM-DD-*.md`および`Memo: Today's quick Memo`で作成される`YYYY-MM-DD.md`がgit管理から除外されるように[`.gitignore`](./.gitignore)を設定
- ある程度まとまったメモについてのみタイトルから`YYYY-MM-DD-`を外すか適当にrenameしてgit管理下に置くようにすれば公開されることになる

### [`.gitignore`](./.gitignore)の設定

```bash :.gitignore
# ignore YYYY-MM-DD*.md files
[0-9][0-9][0-9][0-9]-[0-9][0-9]-[0-9][0-9]*.*md
```
