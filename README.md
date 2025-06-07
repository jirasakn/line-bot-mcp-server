[日本語版 READMEはこちら](README.ja.md) | [ภาษาไทย](README.th.md)

# LINE Bot MCP Server - with Reply Support

[![npmjs](https://badge.fury.io/js/%40jirasakn%2Fline-bot-mcp-server.svg)](https://www.npmjs.com/package/@jirasakn/line-bot-mcp-server)

[Model Context Protocol (MCP)](https://github.com/modelcontextprotocol) server implementation that integrates the LINE Messaging API to connect an AI Agent to the LINE Official Account. Supports both push messages and direct replies to user messages.

![](/assets/demo.png)

> [!NOTE]
> This repository is provided as a preview version. While we offer it for experimental purposes, please be aware that it may not include complete functionality or comprehensive support.

## Features

- Push messages to LINE users
- Broadcast messages to all followers
- Reply directly to user messages
- Get user profile information
- Check message quota and consumption

## Tools

1. **push_text_message**
   - Push a simple text message to a user via LINE.
   - **Inputs:**
     - `user_id` (string?): The user ID to receive a message. Defaults to DESTINATION_USER_ID. Either `user_id` or `DESTINATION_USER_ID` must be set.
     - `message.text` (string): The plain text content to send to the user.
2. **push_flex_message**
   - Push a highly customizable flex message to a user via LINE.
   - **Inputs:**
     - `user_id` (string?): The user ID to receive a message. Defaults to DESTINATION_USER_ID. Either `user_id` or `DESTINATION_USER_ID` must be set.
     - `message.altText` (string): Alternative text shown when flex message cannot be displayed.
     - `message.content` (any): The content of the flex message. This is a JSON object that defines the layout and components of the message.
     - `message.contents.type` (enum): Type of the container. 'bubble' for single container, 'carousel' for multiple swipeable bubbles.
3. **reply_text_message**
   - Reply to a user message with a simple text message via LINE.
   - **Inputs:**
     - `replyToken` (string): The reply token received via webhook when a user sends a message. Valid for a short period of time.
     - `message.text` (string): The plain text content to send to the user.
4. **reply_flex_message**
   - Reply to a user message with a highly customizable flex message via LINE.
   - **Inputs:**
     - `replyToken` (string): The reply token received via webhook when a user sends a message. Valid for a short period of time.
     - `message.altText` (string): Alternative text shown when flex message cannot be displayed.
     - `message.content` (any): The content of the flex message. This is a JSON object that defines the layout and components of the message.
     - `message.contents.type` (enum): Type of the container. 'bubble' for single container, 'carousel' for multiple swipeable bubbles.
5. **broadcast_text_message**
   - Broadcast a simple text message via LINE to all users who have followed your LINE Official Account.
   - **Inputs:**
     - `message.text` (string): The plain text content to send to the users.
6. **broadcast_flex_message**
   - Broadcast a highly customizable flex message via LINE to all users who have added your LINE Official Account.
   - **Inputs:**
     - `message.altText` (string): Alternative text shown when flex message cannot be displayed.
     - `message.content` (any): The content of the flex message. This is a JSON object that defines the layout and components of the message.
     - `message.contents.type` (enum): Type of the container. 'bubble' for single container, 'carousel' for multiple swipeable bubbles.
7. **get_profile**
   - Get detailed profile information of a LINE user including display name, profile picture URL, status message and language.
   - **Inputs:**
     - `user_id` (string?): The ID of the user whose profile you want to retrieve. Defaults to DESTINATION_USER_ID.
8. **get_message_quota**
   - Get the message quota and consumption of the LINE Official Account. This shows the monthly message limit and current usage.
   - **Inputs:**
     - None

## Using Reply Messages

Reply messages allow you to respond directly to user messages using a `replyToken`. This is more efficient than using push messages as:

1. It's free and doesn't count against your message quota
2. It provides a better user experience as replies are visually linked to the original message
3. It maintains conversation context

To use reply messages:

1. Set up a webhook endpoint to receive events from LINE (see [LINE Webhook Setup](https://developers.line.biz/en/docs/messaging-api/building-bot/#setting-webhook-url))
2. Extract the `replyToken` from incoming webhook events
3. Use either `reply_text_message` or `reply_flex_message` with the extracted `replyToken`

Note: Reply tokens are valid for only a short time (typically 1 minute), so replies should be sent promptly after receiving the webhook event.

## Installation (Using npx)

requirements:
- Node.js v20 or later

### Step 1: Create LINE Official Account

This MCP server utilizes a LINE Official Account. If you do not have one, please create it by following [this instructions](https://developers.line.biz/en/docs/messaging-api/getting-started/#create-oa). 

If you have a LINE Official Account, enable the Messaging API for your LINE Official Account by following [this instructions](https://developers.line.biz/en/docs/messaging-api/getting-started/#using-oa-manager).

### Step 2: Configure AI Agent

Please add the following configuration for an AI Agent like Claude Desktop or Cline. 

Set the environment variables or arguments as follows:

- `CHANNEL_ACCESS_TOKEN`: (required) Channel Access Token. You can confirm this by following [this instructions](https://developers.line.biz/en/docs/basics/channel-access-token/#long-lived-channel-access-token).
- `DESTINATION_USER_ID`: (optional) The default user ID of the recipient. If the Tool's input does not include `user_id`, `DESTINATION_USER_ID` is required. You can confirm this by following [this instructions](https://developers.line.biz/en/docs/messaging-api/getting-user-ids/#get-own-user-id).

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

## Installation (Using Docker)

### Step 1: Create LINE Official Account

This MCP server utilizes a LINE Official Account. If you do not have one, please create it by following [this instructions](https://developers.line.biz/en/docs/messaging-api/getting-started/#create-oa).

If you have a LINE Official Account, enable the Messaging API for your LINE Official Account by following [this instructions](https://developers.line.biz/en/docs/messaging-api/getting-started/#using-oa-manager).


### Step 2: Build line-bot-mcp-server image

Clone this repository:

```
git clone git@github.com:jirasakn/line-bot-mcp-server.git
```

Build the Docker image:

```
docker build -t jirasakn/line-bot-mcp-server .
```

### Step 3: Configure AI Agent

Please add the following configuration for an AI Agent like Claude Desktop or Cline.

Set the environment variables or arguments as follows:

- `mcpServers.args`: (required) The path to `line-bot-mcp-server`.
- `CHANNEL_ACCESS_TOKEN`: (required) Channel Access Token. You can confirm this by following [this instructions](https://developers.line.biz/en/docs/basics/channel-access-token/#long-lived-channel-access-token).
- `DESTINATION_USER_ID`: (optional) The default user ID of the recipient. If the Tool's input does not include `user_id`, `DESTINATION_USER_ID` is required.
You can confirm this by following [this instructions](https://developers.line.biz/en/docs/messaging-api/getting-user-ids/#get-own-user-id).


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

## Versioning

This project respects semantic versioning

See http://semver.org/

## Contributing

Please check [CONTRIBUTING](./CONTRIBUTING.md) before making a contribution.
