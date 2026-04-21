# 🌟 Prompt Engineering — Beginner to Pro Notes

---

# Lesson 1 — Introduction to Prompt Engineering

## Role

Senior Full-Stack Developer

## Context

This lesson is designed for a Full-Stack Developer who wants to start learning **Prompt Engineering**. The focus is on understanding what prompts are, why they matter, and how to write beginner-friendly prompts effectively.

---

## 1️⃣ What is Prompt Engineering

**Definition:**
Prompt Engineering is the skill of writing **clear, structured, and effective instructions** for AI models to generate accurate, useful, and context-aware outputs.

**Why it matters:**

- The **quality of AI output depends on the quality of your prompt**.
- Saves time and improves **code, documentation, and design tasks**.
- Enables developers to leverage AI in **coding, debugging, architecture, and documentation**.

**AI Tools to Practice:**

- [ChatGPT](https://chat.openai.com)
- [Claude AI](https://www.anthropic.com)
- [Google Gemini](https://www.google.com/ai/gemini)

---

## 2️⃣ What is a Prompt

A **Prompt** is the instruction or question you give to an AI system.

### Examples

**❌ Weak Prompt:**

```text
Explain JavaScript
```

**✅ Strong Prompt:**

## ROLE

You are a Senior Full Stack Engineer (10+ years experience) building a production-grade WhatsApp Clone backend. Your task is ONLY the Authentication System — clean, scalable, and complete. No frontend code.

## TECH STACK (backend only)

- Runtime : Node.js v20+
- Framework : Express.js
- Database : MongoDB + Mongoose
- Cache : Redis (token blacklist + OTP storage)
- Auth : JWT access token (15min) + Refresh token (7d, httpOnly cookie)
- OTP : Twilio SMS (or mock in dev via console.log)
- Validation : Zod (all inputs validated at route level)
- Security : bcrypt (saltRounds 12), helmet, cors, express-rate-limit
- Upload : Multer + Cloudinary (profile avatar on register)
- Env : dotenv + Zod env validation (crash on missing vars)

## BACKEND FOLDER STRUCTURE (follow exactly)

server/
├── src/
│ ├── config/
│ │ ├── db.config.js ← MongoDB connect + retry logic
│ │ ├── redis.config.js ← ioredis client singleton
│ │ └── env.config.js ← Zod env schema + validation
│ ├── modules/
│ │ └── auth/
│ │ ├── auth.routes.js ← Express Router (all auth endpoints)
│ │ ├── auth.controller.js← Thin: parse req → call service → send res
│ │ ├── auth.service.js ← All business logic lives here
│ │ ├── auth.middleware.js← verifyAccessToken, requireRole
│ │ └── auth.validator.js ← Zod schemas for every route
│ ├── models/
│ │ └── user.model.js ← Mongoose schema + indexes + toJSON
│ ├── utils/
│ │ ├── jwt.util.js ← signToken, verifyToken, decodeToken
│ │ ├── hash.util.js ← hashPassword, comparePassword
│ │ ├── otp.util.js ← generateOtp, sendOtpSms, verifyOtp
│ │ ├── cloudinary.util.js ← uploadAvatar, deleteAvatar
│ │ └── ApiResponse.util.js ← Consistent response shape
│ ├── middlewares/
│ │ ├── error.middleware.js ← Global error handler
│ │ └── rateLimiter.middleware.js ← Per-route rate limits
│ └── app.js ← Express setup + global middlewares
├── .env.example ← All required env variable names
└── package.json

## AUTH ENDPOINTS TO BUILD

POST /api/v1/auth/register ← phone + password + avatar upload
POST /api/v1/auth/verify-otp ← verify phone OTP after register
POST /api/v1/auth/resend-otp ← resend OTP (rate limited)
POST /api/v1/auth/login ← phone + password → tokens
POST /api/v1/auth/refresh-token ← rotate refresh token
POST /api/v1/auth/logout ← blacklist refresh token in Redis
POST /api/v1/auth/forgot-password ← send OTP to phone
POST /api/v1/auth/reset-password ← OTP + new password
GET /api/v1/auth/me ← get current user (protected)
PATCH /api/v1/auth/update-avatar ← update profile picture (protected)

## CODE QUALITY RULES (non-negotiable)

- Every async route wrapped in asyncHandler(fn) — no uncaught promise rejections
- Controller is THIN: only req/res/next, zero business logic
- Service layer owns ALL logic: DB queries, token ops, Redis ops, OTP
- Return shape always: { success, statusCode, message, data } via ApiResponse util
- Never expose: password, \_\_v, internalId in any API response
- Use Mongoose .select('-password') everywhere user is fetched
- Mongoose toJSON transform: remove \_\_v, rename \_id to id
- All errors thrown as: throw new ApiError(statusCode, message)
- Global error middleware catches ApiError + unknown errors
- Zod validation middleware runs BEFORE controller on every route
- bcrypt saltRounds = 12 (never hardcode, read from env)
- JWT secrets must be separate: ACCESS_TOKEN_SECRET, REFRESH_TOKEN_SECRET
- Redis OTP keys: format otp:{phone} with TTL of 5 minutes
- Redis blacklist keys: format blacklist:{token} with TTL = token expiry

## SECURITY REQUIREMENTS

- Refresh token stored in httpOnly, sameSite=strict, secure cookie
- Access token sent in response body only (never in cookie)
- Rate limits: login → 5 req/15min | register → 3 req/hour | otp → 3 req/5min
- Helmet enabled with sensible defaults
- CORS: only allow CLIENT_URL from env
- Input sanitization via Zod (trim + lowercase phone numbers)
- OTP: 6-digit numeric, expires in 5 min, single-use (delete after verify)
- Password: min 8 chars, at least 1 uppercase, 1 number, enforced in Zod

## GENERATE IN THIS EXACT ORDER

Step 1 → env.config.js (Zod env schema, validate all vars at startup)
Step 2 → db.config.js (Mongoose connect with retry + graceful shutdown)
Step 3 → redis.config.js (ioredis singleton with error handling)
Step 4 → ApiResponse.util.js (response class + ApiError class)
Step 5 → jwt.util.js (sign/verify/decode with JSDoc)
Step 6 → hash.util.js (hashPassword / comparePassword)
Step 7 → otp.util.js (generate, store in Redis, send SMS, verify+delete)
Step 8 → cloudinary.util.js (uploadAvatar with stream, deleteAvatar)
Step 9 → user.model.js (Mongoose schema, all fields, indexes, toJSON)
Step 10 → auth.validator.js (Zod schema for every single endpoint)
Step 11 → auth.service.js (complete business logic for all 10 endpoints)
Step 12 → auth.controller.js (thin wrappers calling service methods)
Step 13 → auth.middleware.js (verifyAccessToken, requireRole factory)
Step 14 → rateLimiter.middleware.js (per-route limiters)
Step 15 → error.middleware.js (global error handler, dev vs prod stack)
Step 16 → auth.routes.js (all routes wired with validators + middlewares)
Step 17 → app.js (Express app setup, all middlewares mounted)
Step 18 → .env.example (all required env keys with example values)

## USER MODEL FIELDS

\_id, phone (unique, indexed), countryCode, password (hashed), displayName,
avatar { url, publicId }, isVerified (bool), isActive (bool),
role (enum: user|admin, default: user), lastSeen, refreshTokenHash,
passwordChangedAt, createdAt, updatedAt

## OUTPUT FORMAT

For EACH file output:

1. A comment line at top: // src/path/to/filename.js
2. The COMPLETE file — no TODOs, no placeholders, no "add logic here"
3. Every non-obvious block has a comment explaining WHY (not what)
4. JSDoc on every exported function

Files must be 100% runnable. No partial code. No "similarly do this for X".
Start with Step 1 now and continue through all 18 steps without stopping.

```text
Teach me Node.js backend development step by step for building a scalable e-commerce API.
Include:
- Folder structure
- Authentication
- Product APIs
- Order APIs
```

---

## 3️⃣ GOLDEN FORMULA for Effective Prompts

**Role + Task + Context + Output Format**

**Explanation:**

1. **Role:** Who the AI should act as (e.g., Senior Developer, Teacher)
2. **Task:** What you want the AI to do (e.g., explain, generate code, debug)
3. **Context:** Relevant details or background (e.g., tech stack, project type)
4. **Output Format:** Specify how you want the answer (e.g., Markdown, JSON, step-by-step guide)

### Example Using GOLDEN FORMULA

```text
Role: Senior Backend Developer
Task: Create a full-featured REST API
Context: Using Node.js, Express, MongoDB, for an e-commerce site
Output Format: Step-by-step instructions with code snippets in Markdown
```

---

## 4️⃣ Beginner Tips for Prompt Engineering

- **Be Specific:** Include all important details.
- **Be Structured:** Use lists, sections, and bullet points.
- **Be Context-Aware:** Mention the framework, language, or version.
- **Test & Iterate:** Refine your prompts based on the AI output.
- **Use Examples:** Show the AI exactly what you want.

---

## ✅ Summary

- Prompt Engineering improves AI output quality.
- The **GOLDEN FORMULA** (Role + Task + Context + Output Format) is your cheat code.
- Clear, specific, and structured prompts save time and effort.
