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
