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
