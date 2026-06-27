# コンテンツ司令塔 — 引き継ぎ / 統括ドキュメント

VTuber **ルイク**さん（Little Nightmares 1・2 RTA 日本記録保持者）の個人コンテンツ管理ツール。
このファイルはプロジェクト全体の統括用。新しく作業を引き継ぐ人（含むAI）はまずこれを読む。

---

## 1. 何のプロジェクトか

単一HTMLファイルで動くPWA（タスク／スケジュール／アイデア／ショート管理＋AI相談）。
データは GitHub Gist で同期し、localStorage にオフライン保存。Claude API で相談機能。
直近の主目的は **YouTube ショート（RTA技解説）の制作パイプライン運用**。

## 2. ファイル / リポジトリ / デプロイ

- 本体: `index.html`（単一ファイル・約1800行。HTML/CSS/JS全部入り）
- ルート複製: `/Users/attsu/content-cmd-center.html`（編集後 `cp index.html ../content-cmd-center.html` で同期している）
- アイコン: `icon-192.png` / `icon-512.png` / `icon.svg`、`manifest.json`、`sw.js`(PWA)
- リポジトリ: `https://github.com/izumoriluik/content-cmd-center`（public）
- 公開URL: `https://izumoriluik.github.io/content-cmd-center/`（GitHub Pages）
- ローカル確認: `.claude/launch.json` の `content-cmd`（python http.server :8731）で preview 起動

### デプロイ手順
```
cd /Users/attsu/content-cmd-center
# index.html を編集
cp index.html ../content-cmd-center.html
git add -A && git commit -m "..." && git push
```
反映に GitHub Pages の伝播で1〜2分。**PWAキャッシュがあるので push 後は少し待って ⌘+Shift+R**。

## 3. 技術構成 / データモデル

- 外部依存: Tabler Icons CDN のみ。フレームワーク無し。
- `data = { tasks, schedules, ideas, shorts, nextId }` を localStorage(`content-cmd-v2`) と Gist に保存。
- 各エンティティの主なフィールド:
  - task: `id,title,tag,status(todo/wip/done),created,due,recur(weekly/biweekly/monthly)`
  - schedule: `id,title,date,tag,startTime,endTime,urgency(high/mid/low),sub,recur`
  - idea: `id,text,tag,created`
  - short: `id,tech,angle(shock/howto/tips),title,status(idea/film/edit/posted),script,views,retention,posted`
- 繰り返しスケジュールは**元データ1件のみ保存**し、表示時に `expandSchedules()` で動的展開（Googleカレンダー方式・無期限）。
- 旧データ互換のため load 時にフィールド補完（recur/tag/shorts）。

## 4. 機能（タブ別）

- **ダッシュボード**: 統計、未完了タスクTOP3、直近スケジュール
- **タスク**: CRUD・D&D並べ替え・インライン編集・編集モーダル・期限/繰り返し・リスト/カンバン切替
- **スケジュール**: カレンダー(デフォルト)/リスト/スケジュール表(VTuber週間表・画像出力)・時間・カテゴリ・繰り返し
- **アイデア**: カード・タスクへ昇格
- **ショート**: 2階層ドリルダウン（一覧→台本ビュー）。1技で複数パターン(A/B/C)をタブ切替。状態/再生数/維持率/台本を管理
- **AI相談**: Claude API(claude-sonnet-4-6)・チャット履歴
- **設定**: Gist連携・JSONエクスポート・全削除

## 5. Gist 同期と直接書き込み

- Gist ID: `e504727eaa8d82dc11f84e2bda847383`（ファイル名 `content-cmd.json`）
- **トークンはユーザー提供（repo/gistスコープのPAT）。リポジトリやこのファイルに絶対書かない。**
- AIがデータ（特にショート台本）を直接入れる時は GitHub API で `content-cmd.json` を読み→該当配列のみ差し替え→PATCH。
  - 必ず**他の配列(tasks/schedules/ideas)を保持**。書き込み前にユーザーへ「アプリで一度同期して最新化」を促す（loadが上書きするため）。
  - 使い終わったトークン入りスクリプトは即削除する。

## 6. コンテンツ戦略（重要）

### ショート制作方針
- 1本目の制作時間が未計測 → **まず5本ストックを作る制作スプリント中**。投稿はストック後。
- 投稿頻度は週3目標だが**投稿開始してから稼働テストして確定**（スケジュールのGist登録は保留中）。
- 各技を **2〜3本（切り口: 衝撃版/解説版/コツ・失敗版）** 作る。同一技で切り口違い＝A/Bテスト。
- ショートは「解説系(要撮影・重編集・看板)」と「切り抜き系(VOD流用・撮影不要・量産)」で本数を埋める。撮影と編集は別日、解説系は配信直後にまとめ録り。

### 台本の確定仕様
- 口調は**普通（煽らない）**。「スーパープレイ」見せ＝緊張を作り記録保持者がサラッと決める落差で魅せる。
- **衝撃版テンプレ**: ビュン(最速フレーム)頭 → 「今の何?」 → スロー種明かし(浅め) → 締めCTA(解説版へ送客)。
- A/Bは「同じ技で開き方(編集パターン)だけ変える」＝変数1つに絞る。テロップ量/BGMは型確定後の第2実験。
- 元ネタ＝RTAマニュアル（下記）。

### 初動ラインナップ（マニュアル準拠・16本登録済み）
1. **Light Skip Zip**(メデューサの目=即死ギミックを発動前に回避 ☆4/7秒, Restricted禁止) — 衝撃版をA/B/C 3パターン制作済(A:ビュン頭/B:セットアップ頭/C:比較頭)＋解説/コツ
2. **Janitor Grab Bait**(管理人の掴みを餌に突破 ☆4/17秒) — 衝撃/解説/コツ
3. **ZEROhop**(スタミナ0が最速の逆説 ☆2/累計30秒) — 衝撃/解説/コツ
4. **Bed Skip**(鍵スキップ ☆1/22秒, 簡単×デカい) — 衝撃/解説/コツ
5. **Monkey Skip**(シーソー床をサルで傾ける ☆2/3秒) — 衝撃/コツ
- 注意: 「Zip＝壁抜け」は**誤り**。壁抜けしない。即死ギミック回避技。

### RTAマニュアル（台本の元ネタ）
- Google Doc（作成途中・随時更新）: `https://docs.google.com/document/d/1FWGzGTQiemHyhHEWpg7qpx5c00vOurelj6FPGcBqyvQ/edit`
- 読み方: `…/export?format=txt` のリダイレクト先を WebFetch（公開閲覧可）。
- 技ごとに難易度☆と短縮秒数が整理。詳細記載のある技だけ台本化可能（Kitchen Clip 等は未記載）。

### ガバルナイトメア（所属グループ）
- ルイクさんは3人組「ガバルナイトメア」に所属（アプリ表記は略称「ガバナイ」）。
- 隔週で **日曜=ラジオ配信(3人喋り)＋木曜=ゲームデー(3人ゲーム)**。グループ配信は本人の編集負荷が軽い。
- 週次設計は A週(ソロ:火木土ソロ配信) / B週(ガバナイ:火ソロ＋木ゲーム＋日ラジオ) のローテ。

## 7. 運用ループ

- **YouTubeアナリティクスはAIが直接取得不可（非公開）**。本人が投稿翌日に**視聴維持率グラフのスクショ**を共有 → AIが落ちポイント特定 → 次の台本修正。
- ショート成績(再生数/維持率)はアプリのショートタブに記録。
- スプレッドシート等の外部管理は廃案。**アプリ内に一元化**（台本もGist格納、AIが更新、本人は読むだけ）。

## 8. 現状と次の一手

- 済: PWA化・GitHub Pages公開・カテゴリ/時間/繰り返し・カレンダーUI・スケジュール表・ショートタブ(ドリルダウン)・Zip 3パターン台本
- 次の候補:
  - 残り4技(Janitor/ZEROhop/Bed/Monkey)の衝撃版台本 → ただし **Zip 3パターンの維持率で勝ち型が出てから**量産する段取り
  - Zipの解説版/コツ版台本（撮影まとめ録り用に先に書いてもよい）
  - 投稿開始後に週次ショート投稿スケジュールをGist登録

## 9. 開発上の注意

- 編集は `index.html` → `cp` でルート複製 → commit/push。
- 検証は preview_start(`content-cmd`) で localhost:8731、`switchTab()` 等を preview_eval で叩いて確認。**preview viewport が極端に細くなる事があるので preview_resize で width 指定**してからスクショ。
- node/jsc が無いので JS構文チェックはブラウザ(preview)で行う。
- 文字列出力は `esc()` でHTMLエスケープ（XSS対策）。
- コミット署名: `Co-Authored-By: Claude <noreply@anthropic.com>`。
