# Deployment Information

## Public URL
[Điền URL deploy thật, ví dụ `https://your-agent.up.railway.app`]

## Platform
Railway

## Runtime Summary
- Local validation: passed
- Docker stack: running
- Health endpoint: returns `status: ok`
- Auth endpoint `/ask`: requires `X-API-Key`
- LLM provider in config: Gemini (`GEMINI_API_KEY`)

## Test Commands (PowerShell)

### 1. Health Check
```powershell
Invoke-RestMethod http://localhost/health | ConvertTo-Json -Depth 5
```
Expected:
- `status = ok`

### 2. Authentication required
```powershell
$headers=@{'X-API-Key'='wrong-key';'Content-Type'='application/json'}
$body=@{question='hello'} | ConvertTo-Json
Invoke-RestMethod -Uri 'http://localhost/ask' -Method Post -Headers $headers -Body $body
```
Expected:
- HTTP 401

### 3. API Test (with valid key)
```powershell
$headers=@{'X-API-Key'='my-local-agent-key';'Content-Type'='application/json'}
$body=@{question='What is deployment?'} | ConvertTo-Json
Invoke-RestMethod -Uri 'http://localhost/ask' -Method Post -Headers $headers -Body $body | ConvertTo-Json -Depth 6
```
Expected:
- HTTP 200
- JSON chứa `answer`

### 4. Rate limiting test
```powershell
$headers=@{'X-API-Key'='my-local-agent-key';'Content-Type'='application/json'}
$body=@{question='rate limit test'} | ConvertTo-Json
$codes=@(); 1..25 | ForEach-Object {
  try {
    Invoke-RestMethod -Uri 'http://localhost/ask' -Method Post -Headers $headers -Body $body -ErrorAction Stop | Out-Null
    $codes += 200
  } catch {
    $codes += [int]$_.Exception.Response.StatusCode.value__
  }
}
$codes | Group-Object | Sort-Object Name | Select-Object Name,Count
```
Expected:
- Có status 429 khi vượt ngưỡng

## Environment Variables Set
- HOST
- PORT
- ENVIRONMENT
- DEBUG
- APP_NAME
- APP_VERSION
- GEMINI_API_KEY
- OPENAI_API_KEY
- LLM_MODEL
- AGENT_API_KEY
- JWT_SECRET
- RATE_LIMIT_PER_MINUTE
- DAILY_BUDGET_USD
- REDIS_URL
- ALLOWED_ORIGINS

## Screenshots
- [Deployment dashboard](screenshots/dashboard.png)
- [Service running](screenshots/running.png)
- [Health check and API tests](screenshots/test-results.png)

## Notes
- Chỉ commit `.env.example`, không commit `.env.local` chứa secret.
- Sau khi deploy Railway, thay tất cả local URL trong file này bằng URL public thực tế.