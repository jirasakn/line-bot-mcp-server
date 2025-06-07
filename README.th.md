[English](README.md) | [日本語](README.ja.md)

# LINE Bot MCP Server - พร้อมรองรับการตอบกลับ (Reply)

[![npmjs](https://badge.fury.io/js/%40line%2Fline-bot-mcp-server.svg)](https://www.npmjs.com/package/@line/line-bot-mcp-server)

เซิร์ฟเวอร์ [Model Context Protocol (MCP)](https://github.com/modelcontextprotocol) ที่ใช้สำหรับเชื่อมต่อ AI Agent เข้ากับ LINE Official Account ผ่าน LINE Messaging API รองรับทั้งการส่งข้อความแบบ Push และการตอบกลับข้อความของผู้ใช้โดยตรง (Reply)

![](/assets/demo.png)

> [!NOTE]
> Repository นี้เป็นเวอร์ชัน Preview สำหรับการทดลองใช้งาน ซึ่งอาจมีฟังก์ชันบางอย่างไม่ครบถ้วนสมบูรณ์

## ฟีเจอร์เด่น

- **ส่งข้อความ (Push Message)**: ส่งข้อความไปหาผู้ใช้ LINE ได้โดยตรง
- **บรอดแคสต์ (Broadcast)**: ส่งข้อความหาผู้ติดตามทั้งหมดพร้อมกัน
- **ตอบกลับ (Reply)**: ตอบกลับข้อความจากผู้ใช้ได้ทันที
- **ดึงข้อมูลโปรไฟล์**: ดูข้อมูลโปรไฟล์ของผู้ใช้
- **ตรวจสอบโควต้า**: เช็คจำนวนข้อความที่ใช้ไปและที่เหลืออยู่

## เครื่องมือ (Tools)

1.  **`push_text_message`**
    - **หน้าที่**: ส่งข้อความแบบตัวอักษรธรรมดา (Plain Text) ไปยังผู้ใช้
    - **พารามิเตอร์**:
      - `userId` (string, optional): ระบุ User ID ของผู้ใช้ที่ต้องการส่งข้อความถึง (หากไม่ระบุ จะใช้ค่าจาก `DESTINATION_USER_ID`)
      - `message.text` (string): ข้อความที่ต้องการส่ง

2.  **`push_flex_message`**
    - **หน้าที่**: ส่งข้อความแบบ Flex Message ที่สามารถออกแบบหน้าตาได้อย่างอิสระ
    - **พารามิเตอร์**:
      - `userId` (string, optional): ระบุ User ID ของผู้ใช้ที่ต้องการส่งข้อความถึง (หากไม่ระบุ จะใช้ค่าจาก `DESTINATION_USER_ID`)
      - `message.altText` (string): ข้อความที่จะแสดงเมื่ออุปกรณ์ไม่รองรับ Flex Message
      - `message.contents` (object): โครงสร้างของ Flex Message ในรูปแบบ JSON

3.  **`reply_text_message`**
    - **หน้าที่**: ตอบกลับข้อความของผู้ใช้ด้วยข้อความแบบตัวอักษรธรรมดา
    - **พารามิเตอร์**:
      - `replyToken` (string): `replyToken` ที่ได้จาก Webhook Event เมื่อผู้ใช้ส่งข้อความมา (มีอายุสั้น)
      - `message.text` (string): ข้อความที่ต้องการตอบกลับ

4.  **`reply_flex_message`**
    - **หน้าที่**: ตอบกลับข้อความของผู้ใช้ด้วย Flex Message
    - **พารามิเตอร์**:
      - `replyToken` (string): `replyToken` ที่ได้จาก Webhook Event (มีอายุสั้น)
      - `message.altText` (string): ข้อความที่จะแสดงเมื่ออุปกรณ์ไม่รองรับ Flex Message
      - `message.contents` (object): โครงสร้างของ Flex Message ในรูปแบบ JSON

5.  **`broadcast_text_message`**
    - **หน้าที่**: บรอดแคสต์ข้อความแบบตัวอักษรธรรมดาไปยังผู้ติดตามทั้งหมด
    - **พารามิเตอร์**:
      - `message.text` (string): ข้อความที่ต้องการบรอดแคสต์

6.  **`broadcast_flex_message`**
    - **หน้าที่**: บรอดแคสต์ Flex Message ไปยังผู้ติดตามทั้งหมด
    - **พารามิเตอร์**:
      - `message.altText` (string): ข้อความที่จะแสดงเมื่ออุปกรณ์ไม่รองรับ Flex Message
      - `message.contents` (object): โครงสร้างของ Flex Message ในรูปแบบ JSON

7.  **`get_profile`**
    - **หน้าที่**: ดึงข้อมูลโปรไฟล์ของผู้ใช้ (ชื่อ, รูปโปรไฟล์, ข้อความสถานะ, และภาษา)
    - **พารามิเตอร์**:
      - `userId` (string, optional): User ID ของผู้ใช้ที่ต้องการดูข้อมูล (หากไม่ระบุ จะใช้ค่าจาก `DESTINATION_USER_ID`)

8.  **`get_message_quota`**
    - **หน้าที่**: ตรวจสอบโควต้าการส่งข้อความของ LINE Official Account
    - **พารามิเตอร์**: ไม่มี

## การใช้งานข้อความตอบกลับ (Reply Message)

การตอบกลับด้วย `replyToken` เป็นวิธีที่แนะนำมากกว่าการส่งข้อความแบบ Push Message เนื่องจาก:

1.  **ไม่มีค่าใช้จ่าย**: ไม่ถูกนับรวมในโควต้าการส่งข้อความรายเดือน
2.  **ประสบการณ์ที่ดีกว่า**: ผู้ใช้จะเห็นว่าเป็นการตอบกลับข้อความของตนเองโดยตรง
3.  **ต่อเนื่อง**: ช่วยให้บทสนทนาเป็นไปอย่างต่อเนื่อง

**ขั้นตอนการใช้งาน:**

1.  ตั้งค่า Webhook URL ใน LINE Official Account ของคุณเพื่อรับ Event ต่างๆ (ดูวิธีได้ที่: [Setting up your webhook URL](https://developers.line.biz/en/docs/messaging-api/building-bot/#setting-webhook-url))
2.  เมื่อได้รับ Webhook Event จากผู้ใช้ ให้ดึงค่า `replyToken` ออกมา
3.  ใช้เครื่องมือ `reply_text_message` หรือ `reply_flex_message` พร้อมกับ `replyToken` ที่ได้มา

> **ข้อควรระวัง**: `replyToken` มีอายุการใช้งานสั้นมาก (ประมาณ 1 นาที) จึงควรตอบกลับทันทีหลังจากได้รับ Event

## วิธีการติดตั้ง

### วิธีที่ 1: ติดตั้งผ่าน npx

**สิ่งที่ต้องมี:**
- Node.js v20 ขึ้นไป

**ขั้นตอน:**

1.  **สร้าง LINE Official Account**: หากยังไม่มี สามารถสร้างได้ที่ [LINE for Business](https://www.linebiz.com/th/service/line-official-account/) และเปิดใช้งาน Messaging API ตาม [คำแนะนำนี้](https://developers.line.biz/th/docs/messaging-api/getting-started/#using-oa-manager)
2.  **ตั้งค่า AI Agent**: เพิ่มการตั้งค่าสำหรับ AI Agent ของคุณ (เช่น Claude Desktop, Cline) โดยกำหนดค่า Environment Variables ดังนี้:
    - `CHANNEL_ACCESS_TOKEN` (จำเป็น): Channel Access Token ของคุณ (ดูวิธีได้ที่: [Channel access tokens](https://developers.line.biz/en/docs/basics/channel-access-token/#long-lived-channel-access-token))
    - `DESTINATION_USER_ID` (ทางเลือก): User ID เริ่มต้นสำหรับส่งข้อความ หากไม่ได้ระบุ `userId` ในแต่ละคำสั่ง (ดูวิธีได้ที่: [Getting user IDs](https://developers.line.biz/en/docs/messaging-api/getting-user-ids/#get-own-user-id))

**ตัวอย่างการตั้งค่า:**

```json
{
  "mcpServers": {
    "line-bot": {
      "command": "npx",
      "args": [
        "@line/line-bot-mcp-server"
      ],
      "env": {
        "CHANNEL_ACCESS_TOKEN" : "YOUR_CHANNEL_ACCESS_TOKEN",
        "DESTINATION_USER_ID" : "YOUR_USER_ID"
      }
    }
  }
}
```

### วิธีที่ 2: ติดตั้งผ่าน Docker

**ขั้นตอน:**

1.  **สร้าง LINE Official Account**: (ดูขั้นตอนเดียวกับวิธีที่ 1)
2.  **สร้าง Docker Image**:
    ```bash
    # โคลนโปรเจกต์
    git clone git@github.com:line/line-bot-mcp-server.git
    
    # สร้าง Docker image
    docker build -t line/line-bot-mcp-server .
    ```
3.  **ตั้งค่า AI Agent**:
    - `CHANNEL_ACCESS_TOKEN` (จำเป็น)
    - `DESTINATION_USER_ID` (ทางเลือก)

**ตัวอย่างการตั้งค่า:**

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
        "line/line-bot-mcp-server"
      ],
      "env": {
        "CHANNEL_ACCESS_TOKEN" : "YOUR_CHANNEL_ACCESS_TOKEN",
        "DESTINATION_USER_ID" : "YOUR_USER_ID"
      }
    }
  }
}
```

## การกำหนดเวอร์ชัน (Versioning)

โปรเจกต์นี้ใช้ระบบ Semantic Versioning (ดูเพิ่มเติมที่ [semver.org](http://semver.org/))

## การมีส่วนร่วม (Contributing)

หากต้องการมีส่วนร่วมในการพัฒนา โปรดอ่าน [แนวทางการมีส่วนร่วม](./CONTRIBUTING.md)
