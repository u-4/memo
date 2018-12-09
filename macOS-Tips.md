# macOS-Tips

macOS（OSX）で忘れがちな操作をメモ。

## ファイル選択ダイアログ関連

標準だと隠しフォルダとかファイルが選択できなくて結構不便だったりする。

### ファイルダイアログで隠しファイル・隠しフォルダを表示

ダイアログを開いた後に「`⌘+⇧+.`」（コマンド＋シフト＋ピリオド）を押すことで隠しファイル・フォルダも表示されるようになる。

### ファイルダイアログでパス指定で入力

ダイアログを開いた後に「`⌘+⇧+G`」を押すとパスを入力するダイアログが開くので、そこでパスを入力すれば隠しフォルダなどでも直接移動できる。

### ファイルダイアログで開いているディレクトリをFinderで開く

ダイアログで開いているフォルダを開いて操作したい場合は「`⌘+R`」でFinderで開くことができる。

### ドラッグ＆ドロップ

ダイアログにファイルやフォルダをドラッグ＆ドロップするとそのファイルやフォルダを選んだ状態になる。

## macOSでのスクリーンショット

[Mac でスクリーンショットを撮る方法 - Apple サポート](https://support.apple.com/ja-jp/HT201361)

基本的にデスクトップに「png」ファイルとして保存される。BetterTouchTool でも設定しておくと便利（5本指ForceTouchにしている）

### 基本操作

- ⌘+⇧+3：画面全体
- ⌘+⇧+4：範囲を選択
- ⌘+⇧+5：Mojave以降で動画含めた総合コントロールを表示

### ウインドウのスクリーンショット

⌘+⇧+4でポインタが出たら「スペースバー」を押すとポインタがカメラになるのでウインドウを選択

### メニューのスクリーンショット

メニューを選択して開いた状態で上記（範囲選択ないしウインドウ）

### Touch Barのスクリーンショット

⌘+⇧+6

### ファイルではなくクリップボードに保持

上記コマンドに「⌃」を追加すれば良いがちと押しにくい

VSCodeの[Paste Image](https://marketplace.visualstudio.com/items?itemName=mushan.vscode-paste-image)拡張機能でよく使うので、BetterTouchToolを使ってTouchBarにボタンを作成してある。

![Touchbar Button for Screenshot to Clipboard](fig/BTT_screenshot_to_clipboard.png)

## アプリケーションの言語設定

[OSXで特定のアプリケーションだけを英語で起動する - Qiita](https://qiita.com/isicava@github/items/ceaba68c3dc88bb00b59)

VSCodeとか、日本語メニューになると逆に面倒な場合に。利用が確定したら[dotfiles/defaults](https://github.com/u-4/dotfiles/blob/master/lib/defaults)に設定しておく。

```bash
defaults write -app アプリケーション名 AppleLanguages "(en, ja)"
```

設定を解除する場合には

```bash
defaults delete -app アプリケーション名 AppleLanguages
```