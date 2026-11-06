# HDC OpenData สำหรับ DeepSeek Harness

ปลั๊กอินแบบ Cordis bundle สำหรับเชื่อม DeepSeek Harness กับ HDC OpenData MCP ของ NuradaLab ผ่าน Streamable HTTP ที่ `https://hdc-opendata-mcp.nuradalab.com/mcp` โดยใช้ MCP client ที่มากับ Harness เพื่อค้นหาและเรียกเครื่องมือของเซิร์ฟเวอร์โดยอัตโนมัติ

## สิ่งที่ต้องมี

- DeepSeek Harness CLI รุ่นที่ MCP client รองรับ `expandTemplates` และโปรไฟล์มี credentials provider เช่นโปรไฟล์ Web มาตรฐาน
- API key สำหรับ **MCP Server** ที่ออกจาก [หน้า Dashboard](https://hdc-opendata-mcp.nuradalab.com/admin/) ของเซิร์ฟเวอร์ โดยผู้ดูแลจะออก key ให้จากหน้ารายละเอียดผู้ใช้งาน

## ติดตั้งในโปรไฟล์ Web

รันจากโฟลเดอร์ปลั๊กอิน

```sh
dsh plugin --profile web add "$PWD"
dsh --profile web --dump-config
```

ตรวจว่ามี layer `dsh-hdc-opendata` จากนั้นตั้งค่า environment variable `HDC_MCP_API_KEY` ให้กับ process ที่จะเปิด Harness ตัวอย่างนี้ใช้ Bash หรือ Zsh และรับ key จากแป้นพิมพ์แบบซ่อนข้อความ

```sh
printf 'HDC MCP API key: '
IFS= read -rs HDC_MCP_API_KEY
printf '\n'
export HDC_MCP_API_KEY
dsh web
```

หากเปิด Harness อยู่แล้ว ให้ปิดแล้วเปิดจาก shell ที่ตั้งค่าตัวแปรนี้ เพื่อให้ Host ได้รับ key ตัว bundle เก็บเพียงชื่ออ้างอิงของ credential เมื่อไม่มี credential การเชื่อมต่อจะอยู่ในสถานะ `waiting-credentials`

## ทดลองใช้

เปิด Web UI ตาม URL ที่ CLI แสดง แล้วส่งข้อความ

> ใช้ HDC OpenData ค้นหาตัวชี้วัดเกี่ยวกับเบาหวาน 3 รายการ แล้วแสดงชื่อตัวชี้วัดและ table_name

ตรวจว่า Harness เรียก `mcp__hdc_opendata__search_indicators` สำเร็จ ตัวอย่างเครื่องมืออื่น ได้แก่ `get_report_schema`, `get_report_data` และ `get_last_updated` ภายใต้คำนำหน้าเดียวกัน หากเซิร์ฟเวอร์ตอบ `401` ให้ตรวจว่า key เป็นของ MCP Server และยังใช้งานได้

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
