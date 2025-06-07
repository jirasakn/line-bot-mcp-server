[English](README.md) | [ภาษาไทย](README.th.md)

# LINE Bot MCP Server - 返信機能付き

[![npmjs](https://badge.fury.io/js/%40jirasakn%2Fline-bot-mcp-server.svg)](https://www.npmjs.com/package/@jirasakn/line-bot-mcp-server)

LINE公式アカウントとAI Agentを接続するために、LINE Messaging APIを統合する[Model Context Protocol (MCP)](https://github.com/modelcontextprotocol) Serverです。プッシュメッセージとユーザーメッセージへの直接返信の両方をサポートしています

![](/assets/demo.ja.png)

> [!NOTE]
> このリポジトリはプレビュー版として提供されています。実験的な目的で提供されており、完全な機能や包括的なサポートが含まれていないことにご注意ください。

## 機能

- LINEユーザーへのメッセージ送信
- すべてのフォロワーへのブロードキャスト
- ユーザーメッセージへの直接返信
- ユーザープロフィール情報の取得
- メッセージクォータと消費量の確認

## Tools

1. **push_text_message**
   - LINEでユーザーにシンプルなテキストメッセージを送信する。
   - **入力:**
     - `user_id` (string?): メッセージ受信者のユーザーID。デフォルトはDESTINATION_USER_ID。`user_id`または`DESTINATION_USER_ID`のどちらか一方は必ず設定する必要があります。
     - `message.text` (string): ユーザーに送信するテキスト。
2. **push_flex_message**
   - LINEでユーザーに高度にカスタマイズ可能なフレックスメッセージを送信する。
   - **入力:**
     - `user_id` (string?): メッセージ受信者のユーザーID。デフォルトはDESTINATION_USER_ID。`user_id`または`DESTINATION_USER_ID`のどちらか一方は必ず設定する必要があります。
     - `message.altText` (string): フレックスメッセージが表示できない場合に表示される代替テキスト。
     - `message.content` (any): フレックスメッセージの内容。メッセージのレイアウトとコンポーネントを定義するJSONオブジェクト。
     - `message.contents.type` (enum): コンテナのタイプ。'bubble'は単一コンテナ、'carousel'は複数のスワイプ可能なバブルを示す。
3. **reply_text_message**
   - ユーザーからのメッセージに対して、LINEでシンプルなテキストメッセージで返信する。
   - **入力:**
     - `replyToken` (string): ユーザーがメッセージを送信した際にWebhookから受け取る返信トークン。短時間のみ有効。
     - `message.text` (string): ユーザーに送信するテキスト。
4. **reply_flex_message**
   - ユーザーからのメッセージに対して、LINEで高度にカスタマイズ可能なフレックスメッセージで返信する。
   - **入力:**
     - `replyToken` (string): ユーザーがメッセージを送信した際にWebhookから受け取る返信トークン。短時間のみ有効。
     - `message.altText` (string): フレックスメッセージが表示できない場合に表示される代替テキスト。
     - `message.content` (any): フレックスメッセージの内容。メッセージのレイアウトとコンポーネントを定義するJSONオブジェクト。
     - `message.contents.type` (enum): コンテナのタイプ。'bubble'は単一コンテナ、'carousel'は複数のスワイプ可能なバブルを示す。
5. **broadcast_text_message**
   - LINE公式アカウントと友だちになっているすべてのユーザーに、LINEでシンプルなテキストメッセージを送信する。
   - **入力:**
     - `message.text` (string): ユーザーに送信するテキスト。
6. **broadcast_flex_message**
   - LINE公式アカウントと友だちになっているすべてのユーザーに、LINEで高度にカスタマイズ可能なフレックスメッセージを送信する。
   - **入力:**
     - `message.altText` (string): フレックスメッセージが表示できない場合に表示される代替テキスト。
     - `message.content` (any): フレックスメッセージの内容。メッセージのレイアウトとコンポーネントを定義するJSONオブジェクト。
     - `message.contents.type` (enum): コンテナのタイプ。'bubble'は単一コンテナ、'carousel'は複数のスワイプ可能なバブルを示す。
7. **get_profile**
   - LINEユーザーの詳細なプロフィール情報を取得する。表示名、プロフィール画像URL、ステータスメッセージ、言語を取得できる。
   - **入力:**
      - `user_id` (string?): プロフィールを取得したいユーザーのユーザーID。デフォルトはDESTINATION_USER_ID。`user_id`または`DESTINATION_USER_ID`のどちらか一方は必ず設定する必要があります。
8. **get_message_quota**
   - LINE公式アカウントのメッセージ容量と消費量を取得します。月間メッセージ制限と現在の使用量が表示されます。
   - **入力:**
     - なし

## 返信メッセージの使用方法

返信メッセージを使用すると、`replyToken`を使用してユーザーメッセージに直接返信できます。プッシュメッセージよりも効率的な理由は以下の通りです：

1. 無料で、メッセージクォータにカウントされません
2. 返信が元のメッセージに視覚的にリンクされるため、ユーザーエクスペリエンスが向上します
3. 会話のコンテキストを維持します

返信メッセージを使用するには：

1. LINEからイベントを受信するためのWebhookエンドポイントを設定します（[LINE Webhook設定](https://developers.line.biz/ja/docs/messaging-api/building-bot/#setting-webhook-url)を参照）
2. 受信したWebhookイベントから`replyToken`を抽出します
3. 抽出した`replyToken`を使用して、`reply_text_message`または`reply_flex_message`を使用します

注意：返信トークンは短時間（通常1分）のみ有効なので、Webhookイベントを受信した後すぐに返信を送信する必要があります。

## インストール (npxを使用)

要件:
- Node.js v20 以上

### Step 1: LINE公式アカウントを作成

このMCP ServerはLINE公式アカウントを利用しています。公式アカウントをお持ちでない場合は、[こちらの手順](https://developers.line.biz/ja/docs/messaging-api/getting-started/#create-oa)に従って作成してください。

LINE公式アカウントをお持ちであれば、[こちらの手順](https://developers.line.biz/ja/docs/messaging-api/getting-started/#using-oa-manager)に従ってMessaging APIを有効にしてください。

### Step 2: AI Agentを設定

Claude DesktopやClaudeなどのAI Agentに次の設定を追加してください。

環境変数や引数は次のように設定してください:

- `CHANNEL_ACCESS_TOKEN`: (必須) チャネルアクセストークン。これを取得するには、[こちらの手順](https://developers.line.biz/ja/docs/basics/channel-access-token/#long-lived-channel-access-token)に従ってください。
- `DESTINATION_USER_ID`: (オプション) デフォルトのメッセージ受信者のユーザーID。Toolの入力に`user_id`が含まれていない場合、`DESTINATION_USER_ID`は必ず設定する必要があります。これを確認するには、[こちらの手順](https://developers.line.biz/ja/docs/messaging-api/getting-user-ids/#get-own-user-id)に従ってください。

```json
{
  "mcpServers": {
    "line-bot": {
      "command": "npx",
      "args": [
        "@jirasakn/line-bot-mcp-server"
      ],
      "env": {
        "CHANNEL_ACCESS_TOKEN" : "FILL_HERE",
        "DESTINATION_USER_ID" : "FILL_HERE"
      }
    }
  }
}
```

## インストール (Dockerを使用)

### Step 1: LINE公式アカウントを作成

このMCP ServerはLINE公式アカウントを利用しています。公式アカウントをお持ちでない場合は、[こちらの手順](https://developers.line.biz/ja/docs/messaging-api/getting-started/#create-oa)に従って作成してください。

LINE公式アカウントをお持ちであれば、[こちらの手順](https://developers.line.biz/ja/docs/messaging-api/getting-started/#using-oa-manager)に従ってMessaging APIを有効にしてください。

### Step 2: line-bot-mcp-serverをインストール

このリポジトリをクローンします:

```
git clone git@github.com:jirasakn/line-bot-mcp-server.git
```

Dockerイメージをビルドします:
```
docker build -t jirasakn/line-bot-mcp-server .
```

### Step 3: AI Agentを設定

Claude DesktopやClaudeなどのAI Agentに次の設定を追加してください。

環境変数や引数は次のように設定してください:

- `mcpServers.args`: (必須) `line-bot-mcp-server`へのパス。
- `CHANNEL_ACCESS_TOKEN`: (必須) チャネルアクセストークン。これを取得するには、[こちらの手順](https://developers.line.biz/ja/docs/basics/channel-access-token/#long-lived-channel-access-token)に従ってください。
- `DESTINATION_USER_ID`: (オプション) デフォルトのメッセージ受信者のユーザーID。Toolの入力に`user_id`が含まれていない場合、`DESTINATION_USER_ID`は必ず設定する必要があります。これを確認するには、[こちらの手順](https://developers.line.biz/ja/docs/messaging-api/getting-user-ids/#get-own-user-id)に従ってください。

```json
{
  "mcpServers": {
    "line-bot": {
      "command": "docker",
      "args": [
        "run",
        "-i",
        "--rm",
        "-e",
        "CHANNEL_ACCESS_TOKEN",
        "-e",
        "DESTINATION_USER_ID",
        "jirasakn/line-bot-mcp-server"
      ],
      "env": {
        "CHANNEL_ACCESS_TOKEN" : "FILL_HERE",
        "DESTINATION_USER_ID" : "FILL_HERE"
      }
    }
  }
}
```
