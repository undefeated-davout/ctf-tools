# TryHackMe メモ

## 隠しページを見つける

```bash
sudo apt install gobuster

gobuster -u {対象サイトURL} -w /usr/share/wordlists/dirb/common.txt dir

# word listは以下にデフォルトで格納されている
ll /usr/share/wordlists/
```

## DNS情報取得

```bash
nslookup --type=CNAME {対象サイトドメイン名}
nslookup --type=TXT {対象サイトドメイン名}
nslookup --type=MX {対象サイトドメイン名}
nslookup --type=A {対象サイトドメイン名}
```

## SCP

```bash
scp {送信したいファイル} {ユーザー}@{サーバIP}:{サーバ内フルパス}
```

## バックグラウンド実行

```bash
# Ctrl + z or 末尾に &
# フォアグランドに戻す
fg
```

## systemctl

```bash
systemctl [option] [service name]
# option: start, stop, enabled
```

## Crontab

- ジェネレーター
  - https://crontab-generator.org/
  - https://crontab.guru/

```bash
# 編集
crontab -e
# 参照
crontab -l
```

## パッケージ管理

```bash
# SublimeTextインストールをしたい場合
wget -qO - https://download.sublimetext.com/sublimehq-pub.gpg | sudo apt-key add -
# ファイル作成しリポジトリ情報を書き込む
# /etc/apt/sources.list.d/sublime-text.list
sudo apt update
sudo apt install sublime-text

# パーッケージ削除方法
sudo add-apt-repository --remove [ppa:PPA_Name/ppa]
sudo apt remove [software-name-here]
```

## Windows CMD

```bash
hostname
whoami
ipconfig
# ヘルプ表示
ipconfig /?
# 画面クリア
cls
# プロトコル統計、ネットワーク接続表示
netstat
# net系コマンド
net [user | localgroup | use | share | session]
```
