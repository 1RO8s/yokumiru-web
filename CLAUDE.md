# yokumiru-web

iOSアプリ「ヨクミル」（リポジトリ: `1RO8s/yt-playlist`）の静的サイト。GitHub Pagesで `public/` 配下を `https://1ro8s.github.io/yokumiru-web/` として配信する。

## 構成

```
public/
├── video.html        # アプリ内WebViewが読み込むYouTubeプレイヤー
├── import.html       # セクション共有リンクのランディングページ（日英併記）
├── ja/
│   ├── term.html            # 利用規約（日本語＝正文）
│   └── privacy-policy.html  # プライバシーポリシー
└── en/               # 英語版（参考訳の位置づけ。日本語版が優先）
```

## デプロイ

`main` へのpushで GitHub Actions（`.github/workflows/static.yml`）が自動デプロイ。

- **初回デプロイが「Deployment failed, try again later.」で落ちることが多い**。中身の問題ではないので `gh run rerun <run-id>` でリランすれば通る
- 反映は即時。**アプリの既存ビルド（TestFlight配布済み含む)にもそのまま影響する**ので、後方互換を壊す変更は禁止

## アプリとの結合点（変更時は要注意）

1. **video.html**: アプリが `?v=<動画ID>&autoplay=1` 付きで読み込む。再生開始時に `postMessage` で `{ type: 'youtube_playback', event: 'playing', videoId, occurredAt }` をアプリへ通知する契約。クエリパラメータ名やメッセージ形式を変えるとアプリの再生・履歴記録が壊れる
2. **import.html**: `#d=<base64url(JSON)>` の共有リンクを受けてプレビューし、`ytplaylist://import?d=...` でアプリに引き渡す。**ペイロード形式と検証ルールはアプリ側と同期必須** — 仕様書は yt-playlist リポジトリの `docs/data-formats.md`
3. **法務ページのURL**: アプリの設定画面が `ja/`・`en/` 配下のパスを直接参照している。ファイル名・パスを変えるとアプリ側の `legalLinksByLanguage` の更新が必要

## 残タスク

- `en/privacy-policy.html` が日本語のまま（未翻訳）。英語ロケールでのリリース前に要対応
- `import.html` のApp Storeボタンはプレースホルダー。アプリ公開後に実URLへ差し替え
