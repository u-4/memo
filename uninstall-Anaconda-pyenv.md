# 2018-12-26 uninstall-Anaconda

macOSで[Homebrew](https://brew.sh/)使用中に[Anaconda](https://www.anaconda.com/)をアンインストールする方法についてメモ。Python環境を本格的に構築するにあたって、2018年現在ではAnacondaに頼らなくてもデータ解析関連のパッケージインストールができるようなので。またバージョン管理についてもPython3では標準の`venv`で十分そうなので、よくわかっていなかった`pyenv`も使用をやめることに。

[Uninstalling Anaconda — Anaconda 2.0 documentation](http://docs.continuum.io/anaconda/install/uninstall/)

の手順に従って行う。

## `anaconda-clean` -> `rm -rf anaconda3*`

```bash
conda install anaconda-clean
```

で`anaconda-clean`をインストールして実行

```bash
anaconda-clean
```

その上で自分の環境の場合は`~/.pyenv/versions/anaconda3.X.X/`を`rm -rf`で削除

## pyenvのuninstall

```bash
rm -rf $(pyenv root)
```

```bash
brew uninstall --force pyenv-virtualenv
brew uninstall --force pyenv
```

あとは`.bash_profile`などに設定してある`pyenv`や`anaconda`がらみの設定や`PATH`を削除する。以下は削除したものの覚書

```bash
# pyenv関連
export PYENV_ROOT="$HOME/.pyenv"
eval "$(pyenv init -)"
```

`pyenv`がらみの`brew doctor`エラーごまかしのために`PATH`付きで設定していたaliasもいらなくなるかな？まあパッケージ版の`R`を入れている限りエラーは続くと思われるが。

## アンインストール後の状況

`anaconda`と`pyenv`をアンインストールし、`Homebrew`による`python`（`python3`）と`python@2`（`python2`）がインストールされている状態。

```bash
$ python --version
Python 2.7.15
```

```bash
$ python3 --version
Python 3.7.1
```

となり、`python3`と明示する必要がある。`python@2`は`fontforge`パッケージなどで依存関係があるためアンインストールするわけにもいかない。`pip`も同様に`pip3`と`pip`で使い分ける必要がある模様。参考までにmacOS mojaveのプリインストール版は

```bash
$ /usr/bin/python --version
Python 2.7.10
```

となっている。