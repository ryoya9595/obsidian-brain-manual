# AI外部記憶システム 導入プロンプト（Claude Code版 / Mac）

以下をまるごとコピーして、Claude Codeに貼り付けてください。

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

2. `~/Documents/Claude-Brain/README.md` を作成し、以下の内容を書き込む：

- このVaultはAIの外部記憶として機能すること
- 各フォルダの役割（上記の通り）
- 更新ルール：
  - セッション開始時：README + daily最新3件 + projectsを読む
  - 記録タイミング：タスク完了・情報更新・テーマ切替・節目ごとに許可不要で即記録
  - daily/への書き込みは追記（上書きしない）
  - ミスが発生したらmistakes/に即座に記録
  - 重要な判断をしたらdecisions/に記録

## Step 3: MCPサーバーの接続

1. 以下のコマンドを実行する（ユーザー全体で使えるように --scope user を付ける）：

```
claude mcp add --scope user obsidian-brain npx -- -y @modelcontextprotocol/server-filesystem ~/Documents/Claude-Brain
```

2. `claude mcp list` を実行して `obsidian-brain: ✓ Connected` が出ることを確認する

※ 注意：settings.jsonに直接mcpServersを書く方法は反映されないことがあるので、必ず `claude mcp add` コマンドを使うこと。

## Step 4: CLAUDE.mdにルールを追記

`~/.claude/CLAUDE.md` （なければ作成）に、以下のセクションを追記する：

```markdown
## 外部記憶（Obsidian）ルール ⭐ 最重要

### Vault場所
`~/Documents/Claude-Brain/`

### セッション開始時（必須）
新しいセッションが始まったら、必ず最初に以下を読む：
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
- 書きすぎを恐れない。抜け漏れより過剰記録の方がよい
```

## Step 5: 完了報告

すべて終わったら、以下を私に報告してください：

1. 実行した内容のまとめ
2. 私が手動でやること：
   - Obsidianを起動 →「保管庫としてフォルダを開く」→ `書類/Claude-Brain` を選択
   - Claude Codeを再起動（MCP反映のため）
3. 再起動後のテスト方法：新しいセッションで「Claude-Brainを確認して」と送る
