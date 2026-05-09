# ⚠️ Hướng dẫn cài đặt bảo mật (KHÔNG commit credentials)

## Nguyên tắc
**KHÔNG BAO GIỜ** lưu email/password DeepSeek trong code repo (public).
Tất cả credentials phải đặt trong **Vercel Environment Variables** (private).

---

## Bước 1: Clone repo về máy local (không push credentials lên)

```bash
git clone https://github.com/CTKiet2006/ds2api.git
cd ds2api
```

## Bước 2: Tạo config với credentials (local only, không commit)

Tạo file `config-private.json` trên máy bạn ( KHÔNG push lên git):

```json
{
  "keys": ["kiet-deepseek-free-key"],
  "api_keys": [
    {
      "key": "kiet-deepseek-free-key",
      "name": "Kiet Free Key",
      "remark": "Dùng cho tất cả project"
    }
  ],
  "accounts": [
    {
      "name": "Kiet Account",
      "email": "YOUR_DEEPSEEK_EMAIL@gmail.com",
      "password": "YOUR_DEEPSEEK_PASSWORD"
    }
  ],
  "model_aliases": {
    "gpt-4o": "deepseek-v4-flash",
    "claude-sonnet-4-6": "deepseek-v4-flash",
    "claude-opus-4-6": "deepseek-v4-pro",
    "gemini-1.5-flash": "deepseek-v4-flash",
    "deepseek-v4-flash": "deepseek-v4-flash",
    "deepseek-v4-pro": "deepseek-v4-pro"
  },
  "responses": { "store_ttl_seconds": 900 },
  "current_input_file": { "enabled": true, "min_chars": 0 },
  "thinking_injection": { "enabled": true, "prompt": "" },
  "embeddings": { "provider": "deterministic" },
  "admin": { "jwt_expire_hours": 168 },
  "runtime": {
    "account_max_inflight": 2,
    "account_max_queue": 5,
    "global_max_inflight": 10,
    "token_refresh_interval_hours": 6
  },
  "auto_delete": { "mode": "none" }
}
```

**Lưu ý:** Thay `YOUR_DEEPSEEK_EMAIL@gmail.com` và `YOUR_DEEPSEEK_PASSWORD` bằng credentials thật.

## Bước 3: Tạo Base64 và điền vào Vercel

### Trên Windows (PowerShell):
```powershell
$config = Get-Content -Path "config-private.json" -Raw
$bytes = [System.Text.Encoding]::UTF8.GetBytes($config)
$base64 = [Convert]::ToBase64String($bytes)
Write-Output $base64
```

### Trên Linux/macOS/WSL:
```bash
cat config-private.json | base64 -w0 | tr -d '\n'
```

Copy kết quả Base64.

## Bước 4: Import repo vào Vercel + Set Environment Variables

1. Vào https://vercel.com → Add New Project → Import `CTKiet2006/ds2api`
2. Chọn **Framework: Other**
3. Mục **Environment Variables**, thêm:

| Name | Value (paste Base64 từ Bước 3) |
|------|------|
| `DS2API_CONFIG_JSON` | *(chuỗi Base64 dài)* |
| `DS2API_ADMIN_KEY` | `kiet2026` |

4. Click **Deploy**

## Bước 5: Verify

```bash
curl https://ds2api-xxxx.vercel.app/healthz
```

Nếu trả về `OK` → Deploy thành công.

---

## ⚠️ Lưu ý bảo mật
- File `config-private.json` phải nằm trong `.gitignore` (đã có sẵn trong repo)
- KHÔNG bao giờ push file chứa credentials lên git
- Nếu lỡ push rồi → Đổi password DeepSeek ngay + revoke token