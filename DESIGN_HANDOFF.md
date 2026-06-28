# SummitSirdar / PeakLeader — デザイン引き継ぎメモ

このファイルは「デザイン作業を別タブ／別エージェントに引き継ぐ」ための状態と指針。
作業前にまず `CLAUDE.md` を読み、その上でこれを読む。**作業中はこの先頭に「デザインタブ作業中」と一行書いて commit し、終わったら消す**（同じ `index.html` を2タブで同時編集すると競合するため）。

---

## ブランド指針

- ルイクさんのツールは2本立ての兄弟ブランド。**山モチーフで統一**。
  - **SummitSirdar（SS）**＝本リポジトリ（Mac作成・Win対応）。コンテンツ制作マネジメント／スケジュールの司令塔。略称「SS」。
  - **PeakLeader（PL）**＝別リポジトリ `luik-stream-bot`（Win）。配信サポート自動化。
- パレット:
  - SS … 深い紺→紫（`#534AB7`）＋ゴールド差し色（`#cf9f52` / soft `#e7c98a`）。夜のアルパイン＋オーロラ。
  - PL … ダーク＋ネオン（シアン `#34d0ff` / ミント `#5cffd6` / 紫 `#6a7bff`）。配信・夜向け。
- 画像は **AI生成しない**（本人の希望）。オリジナルのベクター（SVG）か、MIT/CC0等の権利クリーン素材のみ。アイコン基盤は Tabler Icons（MIT, CDN読み込み済み）。

## 現在のデザイン状態（適用済み）

- **アイコン v2 を SS に適用済み**: `icon.svg` / `icon-192.png` / `icon-512.png`。面分割の立体山＋頂の旗、星空・オーロラ・グレイン・ビネット。元SVGは作業時に再生成可（下記）。
  - PL素材一式は `…/Vtuber補佐プログラム/PeakLeader_brand/`（icon SVG/PNG・背景）。Win側で設定する想定。
- **フォント**: 見出し・数値＝`Outfit`、本文＝`Zen Kaku Gothic New`（Google Fonts、`<head>` で読み込み）。CSS変数 `--font-display` / `--font-ui`。
- **UIポリッシュ層**: `<style>` の**末尾**に「Polish layer」「背景」「入力＆追加バー」のブロックを追記している。基本ルールは触らず、ここで上書きする方針。
  - トークン再定義: `--radius:10px` / `--radius-lg:14px` / `--gold`。
  - ロゴはヘッダーで `icon-192.png` を表示。タブ active＝紫ピル。`.btn-primary`＝グラデ＋影。`.stat-card`＝上辺アクセント（紫→金）。入力フォーカス＝リング。
- **背景**: `bg.png`（山シーン）を `body` に固定（cover）＋暗オーバーレイ。`.app` は半透明＋`backdrop-filter: blur` のすりガラス（明 `rgba(255,255,255,.88)` / 暗 `rgba(20,18,40,.82)`）。**濃さはこの2つのalphaとbodyのlinear-gradientで調整**。
- **追加バー**: 日付2欄・時刻2欄をそれぞれ `.ctrl-group`（`〜` 区切り・nowrap）でまとめ、折り返しでバラけないようにした。高さ38pxで統一。時刻入力は `color-scheme` でダーク化。

## アーキテクチャ上の制約（デザイナー向け）

- 単一 `index.html`（約2400行・HTML/CSS/JS全部入り）。**CSSは末尾のPolish層で上書き**するのが安全。JS・DOM構造は意図がない限り変えない。
- ライト/ダーク両対応（`prefers-color-scheme`）。**全色が両モードで成立すること**。検証は主にダークで実施済み、ライトは未検証。
- SVG→PNG は sandbox の `cairosvg`（`pip install cairosvg --break-system-packages`）。

## デプロイ手順

```
cd /Users/attsu/content-cmd-center
# index.html / アイコン等を編集
cp index.html ../content-cmd-center.html
# sw.js の CACHE を summitsirdar-vN に上げる（PWAキャッシュ更新のため必須）
git add -A && git commit -m "..." && git push
```
push後、GitHub Pages 反映を1〜2分待って ⌘+Shift+R。公開URL: `https://izumoriluik.github.io/content-cmd-center/`。

## 残課題・デザイン候補

- 追加バーの並び・余白のさらなる最適化（要素数が多い）。
- カレンダーのセル密度・イベントピルの見た目・複数日バーの連続表現（現状は日ごと表示＋`↪`＋`n/N`）。
- ライトモードの仕上げ確認。
- （任意）PLの配信素材（バナー・オーバーレイ等）。

## 注意

- **トークンはコミットしない**。Gist同期のPATは本人提供・アプリ設定欄／キーチェーン保管。
- リポジトリ名・URL・Gistファイル名・localStorageキーの slug は旧名（content-cmd-center / content-cmd.json）据え置き。表示名のみ SummitSirdar。
