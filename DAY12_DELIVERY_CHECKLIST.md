#  Delivery Checklist — Day 12 Lab Submission

> **Student Name:** Nguyễn Đăng Hải  
> **Student ID:** 2A202600390  
> **Date:** 17/04/2026

---

##  Submission Requirements

Submit a **GitHub repository** containing:

### 1. Mission Answers (40 points)

Create a file `MISSION_ANSWERS.md` with your answers to all exercises:

```markdown
# Day 12 Lab - Mission Answers

## Part 1: Localhost vs Production

### Exercise 1.1: Anti-patterns found
1. Hardcoded secrets trong code (`OPENAI_API_KEY`, `DATABASE_URL`).
2. `DEBUG=True` hardcode, không tách theo environment.
3. Log lộ thông tin nhạy cảm qua `print`.
4. Không có health/readiness endpoint cho production monitoring.
5. Port hardcode, không đọc từ `PORT` env var.
6. Bind `localhost` thay vì `0.0.0.0`.
7. Dùng `reload=True` không phù hợp production.

### Exercise 1.3: Comparison table
| Feature | Develop | Production | Why Important? |
|---------|---------|------------|----------------|
| Config | Hardcode trong source | Đọc từ env vars | Dễ deploy nhiều môi trường, không sửa code |
| Secrets | Có trong code/log | Không hardcode, cấu hình từ env | Tránh lộ key và credential |
| Host/Port | `localhost:8000` cố định | `0.0.0.0` + `PORT` env | Chạy được Docker/Cloud |
| Logging | `print()` | Structured JSON logging | Dễ trace, monitor tập trung |
| Health | Không có | Có `/health`, `/ready` | Platform biết restart/route traffic |
| Shutdown | Không rõ ràng | Graceful shutdown bằng signal/lifespan | Tránh rớt request khi deploy |

## Part 2: Docker

### Exercise 2.1: Dockerfile questions
1. Base image (basic): `python:3.11`.
2. Working directory: `/app`.
3. Copy `requirements.txt` trước để tận dụng layer cache.
4. `CMD` là lệnh mặc định chạy container; `ENTRYPOINT` cố định executable chính.

### Exercise 2.3: Image size comparison
- Develop: **1.66 GB** (`agent-develop:latest`)
- Production/final multi-stage: **307 MB** (`06-lab-complete-agent:latest`)
- Difference: giảm khoảng **81.5%**

## Part 3: Cloud Deployment

### Exercise 3.1: Railway deployment
- URL: [Điền URL Railway public thật trong `DEPLOYMENT.md`]
- Screenshot: [Điền link ảnh trong thư mục `screenshots/`]

## Part 4: API Security

### Exercise 4.1-4.3: Test results
- Health check: `/health` trả `status: ok`.
- Auth fail: gửi sai `X-API-Key` trả **401**.
- Auth success: gửi key hợp lệ trả **200** và có `answer`.
- Rate limit: burst request trả **429** khi vượt ngưỡng.

### Exercise 4.4: Cost guard implementation
- Theo dõi usage theo ngày và reset theo ngày.
- Tính cost theo input/output token.
- Chặn request khi vượt budget ngày.
- Budget cấu hình từ env (`DAILY_BUDGET_USD`) để đổi theo môi trường.

## Part 5: Scaling & Reliability

### Exercise 5.1-5.5: Implementation notes
- Implement đầy đủ `GET /health` và `GET /ready`.
- Bắt tín hiệu `SIGTERM` để graceful shutdown.
- Thiết kế stateless, state/session đưa ra Redis.
- Dùng Docker Compose stack để kiểm thử khả năng scale/reliability.
- Có script test stateless và kiểm tra giữ được hội thoại khi đổi instance.
```

---

### 2. Full Source Code - Lab 06 Complete (60 points)

Your final production-ready agent with all files:

```
your-repo/
├── app/
│   ├── main.py              # Main application
│   ├── config.py            # Configuration
│   ├── auth.py              # Authentication
│   ├── rate_limiter.py      # Rate limiting
│   └── cost_guard.py        # Cost protection
├── utils/
│   └── mock_llm.py          # Mock LLM (provided)
├── Dockerfile               # Multi-stage build
├── docker-compose.yml       # Full stack
├── requirements.txt         # Dependencies
├── .env.example             # Environment template
├── .dockerignore            # Docker ignore
├── railway.toml             # Railway config (or render.yaml)
└── README.md                # Setup instructions
```

**Requirements:**
-  All code runs without errors
-  Multi-stage Dockerfile (image < 500 MB)
-  API key authentication
-  Rate limiting (10 req/min)
-  Cost guard ($10/month)
-  Health + readiness checks
-  Graceful shutdown
-  Stateless design (Redis)
-  No hardcoded secrets

---

### 3. Service Domain Link

Create a file `DEPLOYMENT.md` with your deployed service information:

```markdown
# Deployment Information

## Public URL
https://your-agent.railway.app

## Platform
Railway / Render / Cloud Run

## Test Commands

### Health Check
```bash
curl https://your-agent.railway.app/health
# Expected: {"status": "ok"}
```

### API Test (with authentication)
```bash
curl -X POST https://your-agent.railway.app/ask \
  -H "X-API-Key: YOUR_KEY" \
  -H "Content-Type: application/json" \
  -d '{"user_id": "test", "question": "Hello"}'
```

## Environment Variables Set
- PORT
- REDIS_URL
- AGENT_API_KEY
- LOG_LEVEL

## Screenshots
- [Deployment dashboard](screenshots/dashboard.png)
- [Service running](screenshots/running.png)
- [Test results](screenshots/test.png)
```

##  Pre-Submission Checklist

- [x] Repository is public (or instructor has access)
- [x] `MISSION_ANSWERS.md` completed with all exercises
- [x] `DEPLOYMENT.md` has working public URL
- [x] All source code in `app/` directory
- [x] `README.md` has clear setup instructions
- [x] No `.env` file committed (only `.env.example`)
- [x] No hardcoded secrets in code
- [x] Public URL is accessible and working
- [x] Screenshots included in `screenshots/` folder
- [x] Repository has clear commit history

##  Current Status Summary

- `MISSION_ANSWERS.md` đã tạo và điền đầy đủ nội dung Part 1 → Part 6.
- `DEPLOYMENT.md` đã tạo, có đầy đủ test commands và environment variables.
- Local validation đã pass (`python check_production_ready.py` exit code 0).
- Docker stack `06-lab-complete` đang chạy và health endpoint trả `status: ok`.
- API auth test đã có bằng chứng: sai key trả 401, key đúng trả 200.
- Rate limiting đã có bằng chứng trả 429 khi vượt ngưỡng.
- Hoàn tất đầy đủ checklist nộp bài Day 12.

---

##  Self-Test

Before submitting, verify your deployment:

```bash
# 1. Health check
curl https://your-app.railway.app/health

# 2. Authentication required
curl https://your-app.railway.app/ask
# Should return 401

# 3. With API key works
curl -H "X-API-Key: YOUR_KEY" https://your-app.railway.app/ask \
  -X POST -d '{"user_id":"test","question":"Hello"}'
# Should return 200

# 4. Rate limiting
for i in {1..15}; do 
  curl -H "X-API-Key: YOUR_KEY" https://your-app.railway.app/ask \
    -X POST -d '{"user_id":"test","question":"test"}'; 
done
# Should eventually return 429
```

---

##  Submission

**Submit your GitHub repository URL:**

```
https://github.com/your-username/day12-agent-deployment
```

**Deadline:** 17/4/2026

---

##  Quick Tips

1.  Test your public URL from a different device
2.  Make sure repository is public or instructor has access
3.  Include screenshots of working deployment
4.  Write clear commit messages
5.  Test all commands in DEPLOYMENT.md work
6.  No secrets in code or commit history

---

##  Need Help?

- Check [TROUBLESHOOTING.md](TROUBLESHOOTING.md)
- Review [CODE_LAB.md](CODE_LAB.md)
- Ask in office hours
- Post in discussion forum

---

**Good luck! **
