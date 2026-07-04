# AI外部記憶システム 導入プロンプト（Codex版 / Mac）

以下をまるごとコピーして、Codex（デスクトップアプリ）に貼り付けてください。

---

あなたに「外部記憶システム」を導入してもらいます。
Obsidian（ローカルのメモアプリ）を外部記憶として使い、セッションをまたいで記憶を引き継げるようにする仕組みです。

以下の手順を上から順番に実行してください。コマンドを実行する前に何をするか一言説明し、エラーが出たら止まって私に報告してください。

## Step 1: Obsidianの確認とインストール

1. `/Applications/Obsidian.app` が存在するか確認する
2. なければ `brew install --cask obsidian` でインストールする
3. Homebrewが入っていなければ、その旨を私に伝えて https://obsidian.md からの手動ダウンロードを案内する

## Step 2: 記憶用フォルダ（Vault）の作成

1. 以下のフォルダを作成する：

```
~/Documents/Claude-Brain/
├── daily/      （日次の作業ログ）
├── todos/      （todoリスト）
├── ideas/      （アイデア・構想）
├── projects/   （プロジェクトの現在地）
├── decisions/  （判断・ルールの記録）
├── mistakes/   （ミス記録）
└── sessions/   （セッション記録）
```

※ すでにClaude Code側で作成済みの場合はスキップしてOK（共有できるのがこの仕組みの強み）

2. `~/Documents/Claude-Brain/README.md` がなければ作成し、以下の内容を書き込む：

- このVaultはAIの外部記憶として機能すること
- 各フォルダの役割（上記の通り）
- 更新ルール：
  - セッション開始時：README + daily最新3件 + projectsを読む
  - 記録タイミング：タスク完了・情報更新・テーマ切替・節目ごとに許可不要で即記録
  - daily/への書き込みは追記（上書きしない）

## Step 3: MCPサーバーの接続

`~/.codex/config.toml` に以下のセクションを追記する（既存の設定は消さないこと）：

```toml
[mcp_servers.obsidian-brain]
command = "npx"
args = ["-y", "@modelcontextprotocol/server-filesystem", "/Users/ユーザー名/Documents/Claude-Brain"]
```

※ `/Users/ユーザー名/` の部分は実際のホームディレクトリの絶対パスに置き換えること（`echo $HOME` で確認できる）。チルダ（~）は使わない。

## Step 4: AGENTS.mdにルールを追記

`~/.codex/AGENTS.md` （なければ作成）に、以下のセクションを追記する：

```markdown
## 外部記憶（Claude-Brain）ルール ⭐ 最重要

Vault場所: `~/Documents/Claude-Brain/`

### セッション開始時（必須）
新しいセッションが始まったら必ず以下を読む：
1. `~/Documents/Claude-Brain/README.md`
2. `~/Documents/Claude-Brain/daily/` の最新3件
3. `~/Documents/Claude-Brain/projects/` の関連ファイル

### 自動記録ルール（許可不要・常時）
以下のタイミングで、ユーザーに確認せず即座にClaude-Brainに書き込む：

| タイミング | 書き込み先 |
|---|---|
| 新しい情報・決定・方針が出た | daily/YYYY-MM-DD.md に追記 |
| タスクが完了した | daily/YYYY-MM-DD.md に追記 |
| 会話の節目（テーマが変わる時） | daily/YYYY-MM-DD.md に追記 |
| ミスを指摘された | mistakes/YYYY-MM-DD-[内容].md |
| 重要な判断をした | decisions/YYYY-MM-DD-[内容].md |
| セッション終了時 | daily/YYYY-MM-DD.md に総まとめ |

- 許可を取らない。書いたら返答の末尾に（📝 記録済み）と添えるだけでOK
- daily/への書き込みは上書きでなく追記
```

## Step 5: 完了報告

すべて終わったら、以下を私に報告してください：

1. 実行した内容のまとめ
2. 私が手動でやること：
   - Obsidianを起動 →「保管庫としてフォルダを開く」→ `書類/Claude-Brain` を選択（Claude Code側で設定済みならスキップ）
   - Codexを再起動（MCP反映のため）
3. 再起動後のテスト方法：新しいセッションで「Claude-Brainを確認して」と送る
