# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Overview

Amazon Silk ブラウザ（Fire タブレット）向けのブラウザゲーム集。ビルドツール・パッケージマネージャー不要の単一 HTML ファイル構成。

## 開発・動作確認

ビルド不要。HTML ファイルをブラウザで直接開くだけで動作する。

```bash
# macOS でブラウザプレビュー
open zukei-game/zukeigame.html
```

ローカルサーバーが必要な場合:

```bash
python3 -m http.server 8080
# → http://localhost:8080/zukei-game/zukeigame.html
```

## Silk ブラウザ固有の制約

各ゲームを実装・修正する際に必ず考慮すること:

- **Web Speech API**: `voiceschanged` イベントが Silk で発火しない場合がある。`setTimeout` によるフォールバック（1.5秒）を必ず設ける
- **speechSynthesis**: `cancel()` 直後に `speak()` すると無視されることがある → 50ms の遅延を挟む
- **speechSynthesis**: `paused` 状態で固まることがある → `cancel()` 前に `paused` を確認して `resume()` する
- **画面向き**: `screen.orientation.lock('landscape')` で横向きをロック試行する（失敗は握りつぶす）
- **縦向き対応**: `@media (orientation:portrait)` でゲーム画面を隠し、回転促進メッセージを表示する
- **タッチ操作**: `touch-action:manipulation` と `-webkit-tap-highlight-color:transparent` を全要素に適用する

## ゲーム一覧

| ディレクトリ | ゲーム名 | 概要 |
|---|---|---|
| `zukei-game/` | ずけいであそぼ！ | 図形（まる・さんかく等）を音声とテキストで示し、正しいカードをタップする幼児向けゲーム |

## アーキテクチャパターン

各ゲームは **1つの HTML ファイルに CSS・JavaScript をインライン記述** するスタイル。外部依存ライブラリなし。

共通の画面構成:
- `#scr-title` — タイトル画面
- `#scr-game` — ゲーム画面
- `#scr-result` — リザルト画面
- `#rotate-msg` — 縦向き警告（portrait 時のみ表示）

サイズ単位は `vh`/`vw` を用い、タブレット横向き画面に自動フィットする設計。
