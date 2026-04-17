# Day 12 Lab - Mission Answers

## Student Information
- Student Name: Nguyễn Đăng Hải
- Student ID: 2A202600390
- Date: 2026-04-17

## Part 1: Localhost vs Production

### Exercise 1.1: Anti-patterns found
1. Hardcoded secret trong source code: `OPENAI_API_KEY` và `DATABASE_URL`.
2. Bật `DEBUG=True` cố định, không tách môi trường.
3. In log lộ secret (`print` key ra console).
4. Không có health check endpoint cho platform monitor/restart.
5. Port hardcode 8000, không đọc từ biến môi trường `PORT`.
6. Bind `localhost` thay vì `0.0.0.0` nên không phù hợp container/cloud.
7. `reload=True` trong ngữ cảnh production.

### Exercise 1.3: Comparison table
| Feature | Develop | Production | Why Important? |
|---------|---------|------------|----------------|
| Config | Hardcode trong code | Đọc từ env vars qua settings | Dễ deploy nhiều môi trường, không sửa code |
| Secrets | Có secret trong source/log | Không hardcode secret | Tránh lộ key, an toàn bảo mật |
| Host/Port | `localhost:8000` cố định | `0.0.0.0` + `PORT` env | Chạy được trên Docker/Railway/Render |
| Logging | `print()` | Structured JSON logging | Dễ truy vết, giám sát tập trung |
| Health | Không có | Có `/health`, `/ready` | Orchestrator biết lúc nào restart/route |
| Shutdown | Không có flow rõ ràng | Graceful shutdown bằng signal/lifespan | Tránh rớt request khi deploy/restart |

---

## Part 2: Docker

### Exercise 2.1: Dockerfile questions
1. Base image (basic): `python:3.11`.
2. Working directory: `/app`.
3. Copy `requirements.txt` trước để tận dụng Docker layer cache, không phải cài lại dependencies mỗi lần đổi code.
4. `CMD` là lệnh mặc định khi chạy container (dễ override), còn `ENTRYPOINT` thường cố định executable chính.

### Exercise 2.3: Image size comparison
- Develop image: `agent-develop:latest` = **1.66 GB**.
- Advanced/final multi-stage image: `06-lab-complete-agent:latest` = **307 MB**.
- Difference: giảm khoảng **81.5%**.

Cách tính: `(1.66GB - 0.307GB) / 1.66GB ≈ 81.5%`.

---

## Part 3: Cloud Deployment

### Exercise 3.1: Railway deployment
- Platform: Railway
- URL: [Điền URL Railway public, ví dụ `https://your-app.up.railway.app`]
- Screenshot:
  - [Link dashboard Railway]
  - [Link app running]
  - [Link test endpoint]

Các bước đã chuẩn bị:
1. Cài Railway CLI: `npm i -g @railway/cli`
2. Login Railway CLI
3. `railway init`
4. `railway up`

---

## Part 4: API Security

### Exercise 4.1-4.3: Test results
Kết quả test local (PowerShell):
1. Health check thành công:
   - `GET /health` trả `status: ok`.
2. Auth fail đúng chuẩn:
   - Gửi `X-API-Key: wrong-key` vào `/ask` trả **401**.
3. Auth success:
   - Gửi key hợp lệ vào `/ask` trả **200** và có `answer`.
4. Rate limit:
   - Burst request cho cùng API key nhận **429** khi vượt ngưỡng.

### Exercise 4.4: Cost guard implementation
Cách làm:
1. Theo dõi usage theo ngày bằng biến/timestamp reset theo ngày.
2. Tính chi phí ước lượng theo input/output token.
3. Nếu vượt budget ngày thì block request (trả lỗi service/budget exhausted).
4. Cấu hình budget bằng env (`DAILY_BUDGET_USD`) để thay đổi theo môi trường.

---

## Part 5: Scaling & Reliability

### Exercise 5.1-5.5: Implementation notes
1. Health check và readiness:
   - Implement `GET /health` và `GET /ready`.
2. Graceful shutdown:
   - Bắt `SIGTERM`, dùng lifecycle để shutdown an toàn.
3. Stateless design:
   - Tách state khỏi process memory, dùng Redis cho state/session.
4. Load balancing:
   - Dùng reverse proxy/load balancer trong kiến trúc Docker stack.
5. Stateless test:
   - Có script test stateless và xác nhận conversation/session không phụ thuộc single instance.

---

## Part 6: Final Project Summary

Hoàn thiện project production-ready trong `06-lab-complete` với các thành phần:
- Multi-stage Dockerfile
- Docker Compose stack
- Env-based config (12-factor)
- API key auth
- Rate limiting
- Cost guard
- Health/readiness endpoints
- Graceful shutdown
- Structured logging
- Gemini-oriented config (`GEMINI_API_KEY`)

Validation:
- Chạy `python check_production_ready.py` thành công (exit code 0).
- Chạy local stack thành công bằng Docker Compose.

---

## Notes
- Không commit file `.env.local`/secret thực tế.
- Trước khi nộp cần bổ sung URL public và screenshot vào báo cáo.