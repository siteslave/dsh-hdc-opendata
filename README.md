# HDC OpenData สำหรับ DeepSeek Harness

ปลั๊กอินแบบ Cordis bundle สำหรับเชื่อม DeepSeek Harness กับ HDC OpenData MCP ของ NuradaLab ผ่าน Streamable HTTP ที่ `https://hdc-opendata-mcp.nuradalab.com/mcp` โดยใช้ MCP client ที่มากับ Harness เพื่อค้นหาและเรียกเครื่องมือของเซิร์ฟเวอร์โดยอัตโนมัติ

## สิ่งที่ต้องมี

- DeepSeek Harness CLI รุ่นที่ MCP client รองรับ `expandTemplates` และโปรไฟล์มี credentials provider เช่นโปรไฟล์ Web มาตรฐาน

## ติดตั้งในโปรไฟล์ Web

หากยังไม่มีซอร์สโค้ด ให้โคลน repository ก่อน

```sh
git clone https://github.com/siteslave/dsh-hdc-opendata.git
cd dsh-hdc-opendata
```

รันคำสั่งต่อไปนี้จากโฟลเดอร์ที่มี `package.json` และ `cordis.patch.yml`

```sh
dsh plugin --profile web add "$PWD"
dsh --profile web --dump-config
```

ตรวจว่า config มี layer `dsh-hdc-opendata` และรายการ MCP client ชื่อ `mcp-hdc-opendata`

## ตั้งค่า API key และเปิด Web UI

รันใน Bash หรือ Zsh ตัวอย่างนี้รับ key แบบซ่อนข้อความและส่งผ่าน environment variable ให้ Harness

```sh
printf 'HDC MCP API key: '
IFS= read -rs HDC_MCP_API_KEY
printf '\n'
export HDC_MCP_API_KEY
dsh web
```

หาก Harness เปิดอยู่ ให้ปิดแล้วเปิดใหม่จาก shell ที่ตั้งค่า `HDC_MCP_API_KEY` ตัว bundle เก็บเพียงชื่ออ้างอิงของ credential และส่ง key เป็น Bearer token เมื่อเชื่อมต่อ MCP Server

## ทดลองใช้

เปิด Web UI ตาม URL ที่ CLI แสดง แล้วส่งข้อความ

> ใช้ HDC OpenData ค้นหาตัวชี้วัดเกี่ยวกับเบาหวาน 3 รายการ แล้วแสดงชื่อตัวชี้วัดและ table_name

ตรวจว่า Harness เรียก `mcp__hdc_opendata__search_indicators` สำเร็จ ตัวอย่างเครื่องมืออื่น ได้แก่ `get_report_schema`, `get_report_data` และ `get_last_updated` ภายใต้คำนำหน้าเดียวกัน

## แก้ปัญหาการเชื่อมต่อ

| อาการ | วิธีตรวจสอบ |
| --- | --- |
| ไม่พบเครื่องมือ HDC OpenData | รัน `dsh --profile web --dump-config` แล้วตรวจว่ามีรายการ `mcp-hdc-opendata` ในโปรไฟล์ Web |
| สถานะ `waiting-credentials` | ตั้งค่าและ export `HDC_MCP_API_KEY` ใน shell เดียวกับที่ใช้เปิด Harness แล้วเปิดใหม่ |
| เซิร์ฟเวอร์ตอบ `401 Unauthorized` | ตรวจว่า key ออกให้สำหรับ MCP Server และยังใช้งานได้ |

## ทดลองแบบชั่วคราวหรือถอนการติดตั้ง

เมื่อกำหนด `HDC_MCP_API_KEY` แล้ว ให้ใช้คำสั่งนี้เพื่อโหลดปลั๊กอินเฉพาะรอบที่เปิด Harness โดยรันจากโฟลเดอร์ปลั๊กอิน

```sh
dsh web --patch "$PWD/cordis.patch.yml"
```

ใช้วิธีนี้กับโปรไฟล์ที่ยังไม่มีรายการ `mcp-hdc-opendata` หากติดตั้งเป็น bundle แล้ว ให้เปิดด้วย `dsh web` ตามปกติ

ถอน bundle ออกจากโปรไฟล์ Web ด้วยคำสั่ง

```sh
dsh plugin --profile web remove dsh-hdc-opendata
```

[คู่มือการติดตั้ง bundle](https://deepseek-harness.github.io/deepseek-harness/en/develop/basic/publish) · [คู่มือ MCP client และ credentials](https://github.com/deepseek-ai/deepseek-harness/blob/master/packages/mcp/mcp-client/README.md)
