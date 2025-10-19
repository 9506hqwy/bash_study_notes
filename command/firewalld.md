# `firewall-cmd` コマンド

ファイアウォールを制御する。

## 遮断したログを有効化

遮断したログを確認する。既定は `off` が設定されている。

```sh
firewall-cmd --set-log-denied=all
firewall-cmd --reload
```

*/var/log/messages* に遮断したログが出力される。
