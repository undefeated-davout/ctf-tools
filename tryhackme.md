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
