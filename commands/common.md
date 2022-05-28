# 共通コマンド

```bash
# socat
socat tcp-listen:4444,fork,reuseaddr exec:"echo hello"
# nc
nc localhost 4444
```
