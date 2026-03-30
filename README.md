# 微分音ピアノロール — セットアップガイド

## ファイル構成

```
piano-roll/
├── index.html           ← ピアノロール本体（このファイルをブラウザで開くだけでも動く）
├── vercel.json          ← Vercel デプロイ設定
├── supabase_schema.sql  ← Supabase テーブル作成 SQL
└── README.md
```

---

## ① Supabase のセットアップ

### 1. プロジェクト作成
- https://supabase.com にアクセス → 「New Project」
- プロジェクト名・パスワードを設定して作成

### 2. テーブル作成
- Dashboard の左メニュー → **SQL Editor**
- `supabase_schema.sql` の内容を貼り付けて **Run**

### 3. API キーを取得
- Dashboard → **Settings** → **API**
- `Project URL` と `anon public` キーをコピー

### 4. `index.html` を編集
```html
<!-- ファイルの先頭付近にある以下の2行を書き換える -->
const SUPABASE_URL      = "https://YOUR_PROJECT.supabase.co";   // ← Project URL
const SUPABASE_ANON_KEY = "YOUR_ANON_KEY";                       // ← anon public キー
```

---

## ② Vercel へのデプロイ

### 方法A: GitHub 経由（推奨）
1. このフォルダを GitHub リポジトリに push
2. https://vercel.com → 「New Project」→ リポジトリを選択
3. Framework Preset: **Other**（設定不要）
4. Deploy → 完了

### 方法B: Vercel CLI
```bash
npm i -g vercel
cd piano-roll/
vercel
```

---

## ③ ローカルで動かす（Supabase なしで確認）

```bash
# Python がある場合
python3 -m http.server 8080
# → http://localhost:8080/index.html を開く
```

または `index.html` をダブルクリックするだけでも動きます
（Supabase への送信は URL/KEY を設定しないとエラーになりますが、
 ピアノロール自体は動作します）

---

## 収集されるデータ（sessions テーブル）

| カラム | 型 | 内容 |
|---|---|---|
| `user_id` | text | ユーザーID |
| `task_type` | text | タスク種別（melody/scale/free） |
| `bpm` | integer | テンポ |
| `notes` | jsonb | 配置されたノート一覧 |
| `pitch_offsets` | jsonb | 音程別チューニング値（セント） |
| `note_hz` | jsonb | 各ノートの絶対周波数（Hz） |
| `note_cents_from_c` | jsonb | 基準Cからの絶対セント値 |
| `offset_cents` | jsonb | 12平均律からのオフセット |
| `intervals` | jsonb | 隣り合う音との音程差 |
| `duration_sec` | float | 制作時間（秒） |
| `slider_ops` | integer | チューニング操作回数 |

---

## 操作方法

| 操作 | 動作 |
|---|---|
| **スペースキー** | 再生 / 停止 |
| グリッド 左クリック | ノート配置 |
| グリッド 左ドラッグ | ドラッグした長さでノート配置 |
| ノートの右端をドラッグ | ノートの長さを変更 |
| グリッド 右クリック | ノート削除 |
| **鍵盤の境界線をドラッグ** | その音程のピッチをチューニング（±200¢） |
| 境界線をダブルクリック | チューニングをリセット（0¢に戻す） |
| 鍵盤クリック | その音を試聴 |
| **💾 データ保存ボタン** | Supabase にデータを送信 |