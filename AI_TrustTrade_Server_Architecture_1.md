# AI TrustTrade - 서버 아키텍처 설계

## 📋 목차
1. [전체 시스템 아키텍처](#전체-시스템-아키텍처)
2. [Frontend 구조](#frontend-구조)
3. [Backend 구조](#backend-구조)
4. [Database 구조](#database-구조)
5. [인프라 구성](#인프라-구성)
6. [보안 및 인증](#보안-및-인증)
7. [API 설계](#api-설계)

---

## 1. 전체 시스템 아키텍처

```
┌─────────────────────────────────────────────────────────────────┐
│                        CLIENT LAYER                              │
├─────────────────────────────────────────────────────────────────┤
│  📱 Mobile App        💻 Web App          🖥️ Admin Panel        │
│  (Flutter)            (React/Next.js)     (React)               │
└────────────┬─────────────────┬────────────────────┬─────────────┘
             │                 │                    │
             └─────────────────┼────────────────────┘
                               │
                    ┌──────────▼──────────┐
                    │   API GATEWAY       │
                    │   (Kong / AWS)      │
                    │   - Rate Limiting   │
                    │   - Auth Check      │
                    │   - Load Balance    │
                    └──────────┬──────────┘
                               │
        ┌──────────────────────┼──────────────────────┐
        │                      │                      │
┌───────▼────────┐   ┌────────▼────────┐   ┌────────▼────────┐
│  Auth Service  │   │  Core Service   │   │  AI Service     │
│  (Node.js)     │   │  (Node.js)      │   │  (Python)       │
│  Port: 3001    │   │  Port: 3000     │   │  Port: 5000     │
└───────┬────────┘   └────────┬────────┘   └────────┬────────┘
        │                      │                      │
        └──────────────────────┼──────────────────────┘
                               │
        ┌──────────────────────┼──────────────────────┐
        │                      │                      │
┌───────▼────────┐   ┌────────▼────────┐   ┌────────▼────────┐
│  PostgreSQL    │   │  Redis Cache    │   │  MongoDB        │
│  (Main DB)     │   │  (Sessions)     │   │  (Chat/Logs)    │
└────────────────┘   └─────────────────┘   └─────────────────┘
        │
┌───────▼────────┐   ┌─────────────────┐   ┌─────────────────┐
│  AWS S3        │   │  Firebase       │   │  Elasticsearch  │
│  (Files)       │   │  (Push/Auth)    │   │  (Search)       │
└────────────────┘   └─────────────────┘   └─────────────────┘
```

---

## 2. Frontend 구조

### 2.1 모바일 앱 (Flutter)

```
📱 Mobile App (Flutter)
├── lib/
│   ├── main.dart
│   ├── core/
│   │   ├── theme/
│   │   ├── constants/
│   │   ├── network/
│   │   │   ├── api_client.dart          # Dio HTTP Client
│   │   │   ├── api_endpoints.dart       # API URLs
│   │   │   └── interceptors.dart        # Auth Token
│   │   └── utils/
│   ├── features/
│   │   ├── auth/
│   │   ├── home/
│   │   ├── provider/
│   │   └── payment/
│   └── config/
│       └── env.dart                      # API Base URL

API 통신:
- Base URL: https://api.aitrusttrade.ph
- Auth: Bearer Token (JWT)
- Timeout: 30 seconds
```

### 2.2 웹 애플리케이션 (Next.js)

```
💻 Web App (Next.js 14 - App Router)
├── app/
│   ├── (auth)/
│   │   ├── login/
│   │   └── register/
│   ├── (main)/
│   │   ├── home/
│   │   ├── providers/
│   │   ├── booking/
│   │   └── profile/
│   ├── api/                              # API Routes (Optional)
│   │   └── proxy/                        # Backend Proxy
│   ├── layout.tsx
│   └── page.tsx
├── components/
│   ├── ui/                               # Shadcn UI
│   ├── features/
│   └── layouts/
├── lib/
│   ├── api/
│   │   ├── client.ts                     # Axios Client
│   │   ├── endpoints.ts                  # API URLs
│   │   └── hooks/                        # React Query Hooks
│   ├── store/                            # Zustand State
│   └── utils/
├── public/
└── styles/

기술 스택:
- Framework: Next.js 14 (App Router)
- Styling: Tailwind CSS + Shadcn UI
- State: Zustand + React Query
- HTTP: Axios
- Auth: NextAuth.js
```

### 2.3 관리자 패널 (React)

```
🖥️ Admin Panel (React + Vite)
├── src/
│   ├── pages/
│   │   ├── Dashboard/
│   │   ├── Users/
│   │   ├── Providers/
│   │   ├── Transactions/
│   │   ├── Reports/
│   │   └── Settings/
│   ├── components/
│   │   ├── charts/                       # Recharts
│   │   ├── tables/                       # TanStack Table
│   │   └── forms/
│   ├── api/
│   │   └── client.ts
│   ├── store/
│   └── router/
└── index.tsx

기능:
- 사용자 관리
- 거래 모니터링
- 신뢰 점수 조정
- 분쟁 해결
- 통계 대시보드
```

---

## 3. Backend 구조

### 3.1 마이크로서비스 아키텍처

```
Backend Services
├── 1. Auth Service (Node.js + Express)
├── 2. Core Service (Node.js + Express)
├── 3. Payment Service (Node.js + Express)
├── 4. AI Service (Python + FastAPI)
├── 5. Notification Service (Node.js)
└── 6. File Service (Node.js)
```

---

### 📦 **1. Auth Service** (Port 3001)

```javascript
// 인증 및 권한 관리
auth-service/
├── src/
│   ├── controllers/
│   │   ├── auth.controller.js
│   │   │   - POST /auth/register          # 회원가입
│   │   │   - POST /auth/login             # 로그인
│   │   │   - POST /auth/refresh-token     # 토큰 갱신
│   │   │   - POST /auth/logout            # 로그아웃
│   │   │   - POST /auth/forgot-password   # 비밀번호 찾기
│   │   │   - POST /auth/verify-email      # 이메일 인증
│   │   └── oauth.controller.js
│   │       - GET /auth/google             # Google OAuth
│   │       - GET /auth/facebook           # Facebook OAuth
│   ├── middleware/
│   │   ├── auth.middleware.js             # JWT 검증
│   │   └── rate-limit.middleware.js       # Rate Limiting
│   ├── services/
│   │   ├── jwt.service.js                 # JWT 생성/검증
│   │   ├── password.service.js            # bcrypt
│   │   └── email.service.js               # 이메일 발송
│   ├── models/
│   │   └── user.model.js
│   └── utils/
│       └── validators.js
└── package.json

기술 스택:
- Framework: Express.js
- Auth: Passport.js + JWT
- Password: bcrypt
- Email: Nodemailer / SendGrid
- Validation: Joi

환경변수:
- JWT_SECRET
- JWT_EXPIRES_IN=7d
- REFRESH_TOKEN_EXPIRES_IN=30d
- GOOGLE_CLIENT_ID
- GOOGLE_CLIENT_SECRET
```

---

### 📦 **2. Core Service** (Port 3000)

```javascript
// 핵심 비즈니스 로직
core-service/
├── src/
│   ├── controllers/
│   │   ├── user.controller.js
│   │   │   - GET /users/profile           # 내 프로필
│   │   │   - PUT /users/profile           # 프로필 수정
│   │   │   - GET /users/:id               # 사용자 조회
│   │   │
│   │   ├── provider.controller.js
│   │   │   - GET /providers               # 고수 목록
│   │   │   - GET /providers/:id           # 고수 상세
│   │   │   - POST /providers              # 고수 등록
│   │   │   - PUT /providers/:id           # 고수 정보 수정
│   │   │   - GET /providers/search        # 검색
│   │   │
│   │   ├── booking.controller.js
│   │   │   - POST /bookings               # 견적 요청
│   │   │   - GET /bookings                # 내 견적 목록
│   │   │   - GET /bookings/:id            # 견적 상세
│   │   │   - PUT /bookings/:id/accept     # 견적 수락
│   │   │   - PUT /bookings/:id/reject     # 견적 거절
│   │   │   - PUT /bookings/:id/complete   # 완료 처리
│   │   │
│   │   ├── transaction.controller.js
│   │   │   - GET /transactions            # 거래 목록
│   │   │   - GET /transactions/:id        # 거래 상세
│   │   │   - POST /transactions/escrow    # 에스크로 생성
│   │   │   - PUT /transactions/:id/release # 에스크로 해제
│   │   │
│   │   ├── review.controller.js
│   │   │   - POST /reviews                # 리뷰 작성
│   │   │   - GET /reviews/provider/:id    # 고수 리뷰 조회
│   │   │   - PUT /reviews/:id             # 리뷰 수정
│   │   │   - DELETE /reviews/:id          # 리뷰 삭제
│   │   │
│   │   ├── category.controller.js
│   │   │   - GET /categories              # 카테고리 목록
│   │   │   - GET /categories/:id/providers # 카테고리별 고수
│   │   │
│   │   └── chat.controller.js
│   │       - GET /chats                   # 채팅방 목록
│   │       - GET /chats/:id/messages      # 메시지 조회
│   │       - POST /chats                  # 채팅방 생성
│   │       - POST /chats/:id/messages     # 메시지 전송
│   │
│   ├── services/
│   │   ├── user.service.js
│   │   ├── provider.service.js
│   │   ├── booking.service.js
│   │   ├── transaction.service.js
│   │   ├── review.service.js
│   │   └── notification.service.js        # 알림 큐 연동
│   │
│   ├── models/                            # PostgreSQL Models
│   │   ├── user.model.js
│   │   ├── provider.model.js
│   │   ├── booking.model.js
│   │   ├── transaction.model.js
│   │   └── review.model.js
│   │
│   ├── middleware/
│   │   ├── auth.middleware.js             # 토큰 검증
│   │   ├── permission.middleware.js       # 권한 체크
│   │   └── upload.middleware.js           # Multer
│   │
│   └── utils/
│       ├── pagination.js
│       ├── error-handler.js
│       └── logger.js
│
└── package.json

기술 스택:
- Framework: Express.js
- ORM: Sequelize / Prisma
- Database: PostgreSQL
- Cache: Redis
- File Upload: Multer + AWS S3
- Validation: Joi / Zod
- Logger: Winston
```

---

### 📦 **3. Payment Service** (Port 3002)

```javascript
// 결제 및 에스크로 처리
payment-service/
├── src/
│   ├── controllers/
│   │   ├── payment.controller.js
│   │   │   - POST /payments/create        # 결제 생성
│   │   │   - POST /payments/webhook       # PayMongo Webhook
│   │   │   - GET /payments/:id            # 결제 조회
│   │   │   - POST /payments/refund        # 환불
│   │   │
│   │   ├── escrow.controller.js
│   │   │   - POST /escrow/create          # 에스크로 생성
│   │   │   - POST /escrow/:id/release     # 에스크로 해제
│   │   │   - POST /escrow/:id/refund      # 에스크로 환불
│   │   │
│   │   └── payout.controller.js
│   │       - GET /payouts                 # 정산 내역
│   │       - POST /payouts/request        # 정산 요청
│   │       - POST /payouts/:id/approve    # 정산 승인
│   │
│   ├── services/
│   │   ├── paymongo.service.js            # PayMongo API
│   │   ├── gcash.service.js               # GCash API
│   │   ├── escrow.service.js              # 에스크로 로직
│   │   └── fee.service.js                 # 수수료 계산
│   │
│   ├── models/
│   │   ├── payment.model.js
│   │   ├── escrow.model.js
│   │   └── payout.model.js
│   │
│   └── utils/
│       ├── webhook-signature.js           # Webhook 검증
│       └── encryption.js                  # 결제 정보 암호화
│
└── package.json

API 연동:
- PayMongo: https://developers.paymongo.com/
- GCash: https://www.gcash.com/gcash-business-api/
- Bank Transfer: BancNet / InstaPay

보안:
- PCI-DSS 준수
- 결제 정보 암호화 (AES-256)
- Webhook Signature 검증
- 3D Secure 지원
```

---

### 📦 **4. AI Service** (Port 5000)

```python
# AI 신뢰 점수 및 매칭 엔진
ai-service/
├── app/
│   ├── main.py                            # FastAPI App
│   ├── routers/
│   │   ├── trust_score.py
│   │   │   - POST /ai/trust-score/calculate    # 신뢰점수 계산
│   │   │   - GET /ai/trust-score/:userId       # 점수 조회
│   │   │   - POST /ai/trust-score/predict      # 점수 예측
│   │   │
│   │   ├── matching.py
│   │   │   - POST /ai/matching/recommend       # 고수 추천
│   │   │   - POST /ai/matching/score           # 매칭 점수
│   │   │
│   │   └── nlp.py
│   │       - POST /ai/nlp/analyze              # 텍스트 분석
│   │       - POST /ai/nlp/sentiment            # 감정 분석
│   │
│   ├── services/
│   │   ├── trust_score_model.py           # ML 모델
│   │   ├── matching_engine.py             # 추천 알고리즘
│   │   └── nlp_processor.py               # NLP 처리
│   │
│   ├── models/
│   │   ├── trust_score.pkl                # 학습된 모델
│   │   └── matching.pkl
│   │
│   └── utils/
│       ├── feature_engineering.py
│       └── data_preprocessing.py
│
├── requirements.txt
│   - fastapi
│   - uvicorn
│   - scikit-learn
│   - tensorflow
│   - transformers
│   - pandas
│   - numpy
│
└── train/                                 # 모델 학습
    ├── train_trust_score.py
    └── train_matching.py

신뢰 점수 계산 로직:
1. 완료율 (30%)
2. 평균 응답 시간 (20%)
3. 리뷰 평점 (25%)
4. 계정 활동 기간 (15%)
5. 분쟁 발생률 (10%)

매칭 알고리즘:
1. 스킬 매칭 (40%)
2. 위치 근접성 (30%)
3. 신뢰 점수 (20%)
4. 가격 적합성 (10%)
```

---

### 📦 **5. Notification Service** (Port 3003)

```javascript
// 푸시 알림 및 이메일 발송
notification-service/
├── src/
│   ├── controllers/
│   │   ├── push.controller.js
│   │   │   - POST /notifications/push          # 푸시 발송
│   │   │   - POST /notifications/subscribe     # FCM 토큰 등록
│   │   │
│   │   ├── email.controller.js
│   │   │   - POST /notifications/email         # 이메일 발송
│   │   │
│   │   └── sms.controller.js
│   │       - POST /notifications/sms           # SMS 발송
│   │
│   ├── services/
│   │   ├── fcm.service.js                 # Firebase Cloud Messaging
│   │   ├── email.service.js               # SendGrid / AWS SES
│   │   ├── sms.service.js                 # Twilio / Semaphore
│   │   └── template.service.js            # 템플릿 관리
│   │
│   ├── workers/
│   │   └── notification.worker.js         # Bull Queue Worker
│   │
│   └── templates/
│       ├── email/
│       │   ├── welcome.html
│       │   ├── booking-confirmation.html
│       │   └── payment-success.html
│       └── push/
│           └── messages.json
│
└── package.json

메시지 큐:
- Redis + Bull
- 비동기 처리
- 재시도 로직
- 우선순위 큐
```

---

### 📦 **6. File Service** (Port 3004)

```javascript
// 파일 업로드 및 관리
file-service/
├── src/
│   ├── controllers/
│   │   └── upload.controller.js
│   │       - POST /files/upload           # 파일 업로드
│   │       - GET /files/:id               # 파일 조회
│   │       - DELETE /files/:id            # 파일 삭제
│   │
│   ├── services/
│   │   ├── s3.service.js                  # AWS S3
│   │   ├── image-processing.service.js    # Sharp (리사이징)
│   │   └── virus-scan.service.js          # ClamAV
│   │
│   └── middleware/
│       └── file-validation.middleware.js
│
└── package.json

파일 정책:
- 최대 크기: 10MB (이미지), 50MB (문서)
- 허용 형식: jpg, png, pdf, docx
- 이미지 최적화: WebP 변환
- CDN: CloudFront
```

---

## 4. Database 구조

### 4.1 PostgreSQL (주 데이터베이스)

```sql
-- 메인 데이터베이스 스키마

-- 1. Users Table (사용자)
CREATE TABLE users (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    email VARCHAR(255) UNIQUE NOT NULL,
    password_hash VARCHAR(255) NOT NULL,
    phone VARCHAR(20),
    full_name VARCHAR(255),
    user_type VARCHAR(20) CHECK (user_type IN ('consumer', 'provider', 'admin')),
    profile_image_url TEXT,
    email_verified BOOLEAN DEFAULT FALSE,
    phone_verified BOOLEAN DEFAULT FALSE,
    status VARCHAR(20) DEFAULT 'active',
    created_at TIMESTAMP DEFAULT NOW(),
    updated_at TIMESTAMP DEFAULT NOW(),
    last_login_at TIMESTAMP
);

-- 2. Providers Table (고수)
CREATE TABLE providers (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID REFERENCES users(id) ON DELETE CASCADE,
    category_id UUID REFERENCES categories(id),
    business_name VARCHAR(255),
    description TEXT,
    hourly_rate DECIMAL(10, 2),
    service_area TEXT[],  -- 활동 지역
    skills TEXT[],
    portfolio_url TEXT,
    trust_score INTEGER DEFAULT 0,
    response_time_avg INTEGER,  -- 분 단위
    completion_rate DECIMAL(5, 2) DEFAULT 0.00,
    total_jobs_completed INTEGER DEFAULT 0,
    total_reviews INTEGER DEFAULT 0,
    avg_rating DECIMAL(3, 2) DEFAULT 0.00,
    is_verified BOOLEAN DEFAULT FALSE,
    is_premium BOOLEAN DEFAULT FALSE,
    created_at TIMESTAMP DEFAULT NOW(),
    updated_at TIMESTAMP DEFAULT NOW()
);

-- 3. Categories Table (카테고리)
CREATE TABLE categories (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    name VARCHAR(100) NOT NULL,
    slug VARCHAR(100) UNIQUE NOT NULL,
    icon VARCHAR(50),
    parent_id UUID REFERENCES categories(id),
    created_at TIMESTAMP DEFAULT NOW()
);

-- 4. Bookings Table (견적 요청)
CREATE TABLE bookings (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    consumer_id UUID REFERENCES users(id),
    provider_id UUID REFERENCES providers(id),
    category_id UUID REFERENCES categories(id),
    title VARCHAR(255) NOT NULL,
    description TEXT,
    estimated_hours DECIMAL(5, 2),
    proposed_rate DECIMAL(10, 2),
    scheduled_date TIMESTAMP,
    status VARCHAR(20) DEFAULT 'pending' 
        CHECK (status IN ('pending', 'accepted', 'rejected', 'in_progress', 'completed', 'cancelled')),
    location TEXT,
    created_at TIMESTAMP DEFAULT NOW(),
    updated_at TIMESTAMP DEFAULT NOW()
);

-- 5. Transactions Table (거래)
CREATE TABLE transactions (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    booking_id UUID REFERENCES bookings(id),
    consumer_id UUID REFERENCES users(id),
    provider_id UUID REFERENCES providers(id),
    amount DECIMAL(10, 2) NOT NULL,
    platform_fee DECIMAL(10, 2),
    provider_amount DECIMAL(10, 2),
    payment_method VARCHAR(50),
    payment_provider VARCHAR(50),  -- PayMongo, GCash
    payment_id VARCHAR(255),  -- 외부 결제 ID
    escrow_status VARCHAR(20) DEFAULT 'pending'
        CHECK (escrow_status IN ('pending', 'held', 'released', 'refunded')),
    escrow_released_at TIMESTAMP,
    status VARCHAR(20) DEFAULT 'pending',
    created_at TIMESTAMP DEFAULT NOW(),
    updated_at TIMESTAMP DEFAULT NOW()
);

-- 6. Reviews Table (리뷰)
CREATE TABLE reviews (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    booking_id UUID REFERENCES bookings(id),
    reviewer_id UUID REFERENCES users(id),
    provider_id UUID REFERENCES providers(id),
    rating INTEGER CHECK (rating BETWEEN 1 AND 5),
    title VARCHAR(255),
    content TEXT,
    response TEXT,  -- 고수의 답변
    is_verified BOOLEAN DEFAULT FALSE,
    created_at TIMESTAMP DEFAULT NOW(),
    updated_at TIMESTAMP DEFAULT NOW()
);

-- 7. Trust Scores History (신뢰 점수 이력)
CREATE TABLE trust_scores_history (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID REFERENCES users(id),
    old_score INTEGER,
    new_score INTEGER,
    change_reason VARCHAR(255),
    factors JSONB,  -- 점수 계산 요인
    created_at TIMESTAMP DEFAULT NOW()
);

-- 8. Payments Table (결제 내역)
CREATE TABLE payments (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    transaction_id UUID REFERENCES transactions(id),
    payment_method VARCHAR(50),
    payment_provider VARCHAR(50),
    external_payment_id VARCHAR(255),
    amount DECIMAL(10, 2),
    currency VARCHAR(3) DEFAULT 'PHP',
    status VARCHAR(20),
    metadata JSONB,
    created_at TIMESTAMP DEFAULT NOW(),
    updated_at TIMESTAMP DEFAULT NOW()
);

-- 9. Notifications Table (알림)
CREATE TABLE notifications (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID REFERENCES users(id),
    type VARCHAR(50),  -- booking, payment, message, review
    title VARCHAR(255),
    message TEXT,
    data JSONB,  -- 추가 데이터
    is_read BOOLEAN DEFAULT FALSE,
    read_at TIMESTAMP,
    created_at TIMESTAMP DEFAULT NOW()
);

-- 10. Insurance Policies (마이크로보험)
CREATE TABLE insurance_policies (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    booking_id UUID REFERENCES bookings(id),
    provider_id UUID REFERENCES providers(id),
    policy_number VARCHAR(100),
    coverage_amount DECIMAL(10, 2),
    premium DECIMAL(10, 2),
    status VARCHAR(20),
    valid_from TIMESTAMP,
    valid_until TIMESTAMP,
    created_at TIMESTAMP DEFAULT NOW()
);

-- 11. Micro Loans (마이크로론)
CREATE TABLE micro_loans (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    provider_id UUID REFERENCES providers(id),
    amount DECIMAL(10, 2),
    interest_rate DECIMAL(5, 2),
    term_months INTEGER,
    status VARCHAR(20),
    approved_at TIMESTAMP,
    disbursed_at TIMESTAMP,
    due_date TIMESTAMP,
    created_at TIMESTAMP DEFAULT NOW()
);

-- Indexes
CREATE INDEX idx_providers_trust_score ON providers(trust_score DESC);
CREATE INDEX idx_providers_category ON providers(category_id);
CREATE INDEX idx_bookings_status ON bookings(status);
CREATE INDEX idx_bookings_consumer ON bookings(consumer_id);
CREATE INDEX idx_bookings_provider ON bookings(provider_id);
CREATE INDEX idx_transactions_status ON transactions(status);
CREATE INDEX idx_reviews_provider ON reviews(provider_id);
CREATE INDEX idx_notifications_user ON notifications(user_id, is_read);
```

---

### 4.2 Redis (캐시 & 세션)

```redis
# 캐시 구조

# 1. 세션 관리
session:{userId}
  - user_id
  - token
  - expires_at
TTL: 7 days

# 2. 고수 캐시
provider:{providerId}
  - profile data
  - trust_score
  - reviews_count
TTL: 1 hour

# 3. 검색 결과 캐시
search:providers:{query}:{location}:{category}
  - list of provider IDs
TTL: 15 minutes

# 4. 실시간 통계
stats:daily:{date}
  - total_bookings
  - total_transactions
  - active_users
TTL: 24 hours

# 5. Rate Limiting
rate_limit:{userId}:{endpoint}
  - request count
TTL: 1 minute

# 6. 알림 큐
queue:notifications
  - Bull Queue Jobs
```

---

### 4.3 MongoDB (채팅 & 로그)

```javascript
// Chat Database

// 1. Chat Rooms Collection
{
  _id: ObjectId,
  participants: [
    { userId: UUID, role: 'consumer/provider' }
  ],
  bookingId: UUID,
  lastMessage: {
    senderId: UUID,
    text: String,
    timestamp: Date
  },
  unreadCount: {
    consumerId: Number,
    providerId: Number
  },
  createdAt: Date,
  updatedAt: Date
}

// 2. Messages Collection
{
  _id: ObjectId,
  chatRoomId: ObjectId,
  senderId: UUID,
  messageType: 'text/image/file',
  content: String,
  fileUrl: String,
  isRead: Boolean,
  readAt: Date,
  createdAt: Date
}

// 3. Activity Logs Collection
{
  _id: ObjectId,
  userId: UUID,
  action: String,  // login, booking_create, payment_success
  resource: String,
  resourceId: String,
  ipAddress: String,
  userAgent: String,
  metadata: Object,
  createdAt: Date
}

// Indexes
db.messages.createIndex({ chatRoomId: 1, createdAt: -1 });
db.chatRooms.createIndex({ 'participants.userId': 1 });
db.activityLogs.createIndex({ userId: 1, createdAt: -1 });
db.activityLogs.createIndex({ createdAt: 1 }, { expireAfterSeconds: 7776000 }); // 90 days TTL
```

---

### 4.4 Elasticsearch (검색)

```json
// Provider Search Index
{
  "mappings": {
    "properties": {
      "id": { "type": "keyword" },
      "businessName": { "type": "text" },
      "description": { "type": "text" },
      "category": { "type": "keyword" },
      "skills": { "type": "keyword" },
      "location": { "type": "geo_point" },
      "trustScore": { "type": "integer" },
      "avgRating": { "type": "float" },
      "hourlyRate": { "type": "float" },
      "totalJobsCompleted": { "type": "integer" },
      "isVerified": { "type": "boolean" },
      "createdAt": { "type": "date" }
    }
  }
}

// 검색 쿼리 예시
{
  "query": {
    "bool": {
      "must": [
        { "match": { "description": "web developer" } }
      ],
      "filter": [
        { "term": { "category": "it-development" } },
        { "range": { "trustScore": { "gte": 800 } } },
        { "geo_distance": {
            "distance": "10km",
            "location": { "lat": 14.5995, "lon": 120.9842 }
          }
        }
      ]
    }
  },
  "sort": [
    { "trustScore": "desc" },
    { "_score": "desc" }
  ]
}
```

---

## 5. 인프라 구성

### 5.1 AWS 아키텍처

```
AWS Infrastructure
├── VPC
│   ├── Public Subnet
│   │   ├── Application Load Balancer
│   │   └── NAT Gateway
│   └── Private Subnet
│       ├── ECS Fargate (Backend Services)
│       ├── RDS PostgreSQL (Multi-AZ)
│       ├── ElastiCache Redis (Cluster)
│       └── DocumentDB (MongoDB-compatible)
│
├── S3 Buckets
│   ├── user-uploads-bucket
│   ├── static-assets-bucket
│   └── backup-bucket
│
├── CloudFront (CDN)
│   └── Distribution for S3 + API
│
├── Route 53 (DNS)
│   ├── api.aitrusttrade.ph
│   ├── www.aitrusttrade.ph
│   └── admin.aitrusttrade.ph
│
├── Certificate Manager
│   └── SSL Certificates
│
├── CloudWatch
│   ├── Logs
│   ├── Metrics
│   └── Alarms
│
└── Secrets Manager
    └── API Keys, DB Credentials
```

---

### 5.2 Docker Compose (개발 환경)

```yaml
# docker-compose.yml
version: '3.8'

services:
  # PostgreSQL
  postgres:
    image: postgres:15-alpine
    environment:
      POSTGRES_DB: aitrusttrade
      POSTGRES_USER: admin
      POSTGRES_PASSWORD: ${DB_PASSWORD}
    ports:
      - "5432:5432"
    volumes:
      - postgres_data:/var/lib/postgresql/data

  # Redis
  redis:
    image: redis:7-alpine
    ports:
      - "6379:6379"
    command: redis-server --requirepass ${REDIS_PASSWORD}

  # MongoDB
  mongodb:
    image: mongo:7
    ports:
      - "27017:27017"
    environment:
      MONGO_INITDB_ROOT_USERNAME: admin
      MONGO_INITDB_ROOT_PASSWORD: ${MONGO_PASSWORD}
    volumes:
      - mongo_data:/data/db

  # Auth Service
  auth-service:
    build: ./services/auth-service
    ports:
      - "3001:3001"
    environment:
      DATABASE_URL: ${DATABASE_URL}
      REDIS_URL: ${REDIS_URL}
      JWT_SECRET: ${JWT_SECRET}
    depends_on:
      - postgres
      - redis

  # Core Service
  core-service:
    build: ./services/core-service
    ports:
      - "3000:3000"
    environment:
      DATABASE_URL: ${DATABASE_URL}
      REDIS_URL: ${REDIS_URL}
      MONGODB_URL: ${MONGODB_URL}
    depends_on:
      - postgres
      - redis
      - mongodb

  # Payment Service
  payment-service:
    build: ./services/payment-service
    ports:
      - "3002:3002"
    environment:
      DATABASE_URL: ${DATABASE_URL}
      PAYMONGO_SECRET: ${PAYMONGO_SECRET}
    depends_on:
      - postgres

  # AI Service
  ai-service:
    build: ./services/ai-service
    ports:
      - "5000:5000"
    environment:
      DATABASE_URL: ${DATABASE_URL}
    depends_on:
      - postgres

  # Elasticsearch
  elasticsearch:
    image: docker.elastic.co/elasticsearch/elasticsearch:8.11.0
    environment:
      - discovery.type=single-node
      - xpack.security.enabled=false
    ports:
      - "9200:9200"
    volumes:
      - es_data:/usr/share/elasticsearch/data

volumes:
  postgres_data:
  mongo_data:
  es_data:
```

---

## 6. 보안 및 인증

### 6.1 인증 플로우

```
1. 로그인 플로우
   User → POST /auth/login → Auth Service
   ↓
   Verify Password (bcrypt)
   ↓
   Generate JWT (Access + Refresh Token)
   ↓
   Store Session in Redis
   ↓
   Return Tokens to Client

2. API 요청 플로우
   Client → Request with Bearer Token → API Gateway
   ↓
   Verify JWT Signature
   ↓
   Check Redis Session (Optional)
   ↓
   Forward to Backend Service
   ↓
   Return Response

3. 토큰 갱신 플로우
   Client → POST /auth/refresh-token → Auth Service
   ↓
   Verify Refresh Token
   ↓
   Generate New Access Token
   ↓
   Return New Token
```

---

### 6.2 보안 정책

```javascript
// JWT Payload
{
  "userId": "uuid",
  "email": "user@example.com",
  "userType": "provider",
  "iat": 1234567890,
  "exp": 1234571490  // 7 days
}

// API 보안 헤더
headers: {
  'Authorization': 'Bearer <access_token>',
  'X-API-Version': 'v1',
  'X-Device-ID': 'unique-device-id',
  'X-Request-ID': 'unique-request-id'
}

// Rate Limiting
- 인증 API: 5 requests/minute
- 검색 API: 30 requests/minute
- 일반 API: 100 requests/minute
- Webhook: 무제한 (Signature 검증)

// CORS 설정
allowedOrigins: [
  'https://aitrusttrade.ph',
  'https://www.aitrusttrade.ph',
  'https://admin.aitrusttrade.ph'
]
```

---

## 7. API 설계

### 7.1 RESTful API 구조

```
Base URL: https://api.aitrusttrade.ph/v1

# Auth Endpoints
POST   /auth/register
POST   /auth/login
POST   /auth/refresh-token
POST   /auth/logout
POST   /auth/forgot-password
POST   /auth/reset-password
GET    /auth/google
GET    /auth/facebook

# User Endpoints
GET    /users/me
PUT    /users/me
GET    /users/:id
POST   /users/avatar

# Provider Endpoints
GET    /providers
GET    /providers/:id
POST   /providers
PUT    /providers/:id
DELETE /providers/:id
GET    /providers/search?q=web&category=it&location=manila
GET    /providers/:id/reviews
GET    /providers/:id/portfolio

# Category Endpoints
GET    /categories
GET    /categories/:id/providers

# Booking Endpoints
GET    /bookings
POST   /bookings
GET    /bookings/:id
PUT    /bookings/:id
DELETE /bookings/:id
PUT    /bookings/:id/accept
PUT    /bookings/:id/reject
PUT    /bookings/:id/complete
PUT    /bookings/:id/cancel

# Transaction Endpoints
GET    /transactions
GET    /transactions/:id
POST   /transactions/escrow
PUT    /transactions/:id/release
PUT    /transactions/:id/refund

# Payment Endpoints
POST   /payments/create
GET    /payments/:id
POST   /payments/webhook
POST   /payments/refund

# Review Endpoints
GET    /reviews
POST   /reviews
GET    /reviews/:id
PUT    /reviews/:id
DELETE /reviews/:id

# Chat Endpoints
GET    /chats
POST   /chats
GET    /chats/:id
GET    /chats/:id/messages
POST   /chats/:id/messages

# AI Endpoints
POST   /ai/trust-score/calculate
GET    /ai/trust-score/:userId
POST   /ai/matching/recommend
POST   /ai/matching/score

# File Endpoints
POST   /files/upload
GET    /files/:id
DELETE /files/:id

# Notification Endpoints
GET    /notifications
PUT    /notifications/:id/read
PUT    /notifications/read-all
DELETE /notifications/:id
```

---

### 7.2 API Response 포맷

```json
// Success Response
{
  "success": true,
  "data": {
    "id": "uuid",
    "name": "Juan Dela Cruz",
    "email": "juan@example.com"
  },
  "meta": {
    "timestamp": "2025-01-15T10:30:00Z",
    "requestId": "req-12345"
  }
}

// Error Response
{
  "success": false,
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Invalid email format",
    "details": [
      {
        "field": "email",
        "message": "Email must be a valid email address"
      }
    ]
  },
  "meta": {
    "timestamp": "2025-01-15T10:30:00Z",
    "requestId": "req-12345"
  }
}

// Paginated Response
{
  "success": true,
  "data": [...],
  "pagination": {
    "page": 1,
    "limit": 20,
    "total": 150,
    "totalPages": 8,
    "hasNext": true,
    "hasPrev": false
  }
}
```

---

## 📊 예상 비용 (월간)

### AWS 인프라

| 서비스 | 사양 | 월 비용 (USD) | 월 비용 (PHP) |
|--------|------|--------------|--------------|
| **ECS Fargate** | 4 Services × 2 Tasks | $200 | ₱11,200 |
| **RDS PostgreSQL** | db.t4g.medium (Multi-AZ) | $150 | ₱8,400 |
| **ElastiCache Redis** | cache.t4g.medium | $80 | ₱4,480 |
| **DocumentDB** | db.t4g.medium | $120 | ₱6,720 |
| **S3** | 500GB storage | $15 | ₱840 |
| **CloudFront** | 1TB transfer | $85 | ₱4,760 |
| **Load Balancer** | 1개 | $25 | ₱1,400 |
| **Route 53** | Hosted Zone | $1 | ₱56 |
| **CloudWatch** | Logs + Metrics | $30 | ₱1,680 |
| **총 인프라 비용** | | **$706** | **₱39,536** |

### 외부 서비스

| 서비스 | 용도 | 월 비용 (PHP) |
|--------|------|--------------|
| **SendGrid** | 이메일 발송 | ₱2,000 |
| **Twilio** | SMS 발송 | ₱3,000 |
| **Firebase** | Push 알림 | ₱1,000 |
| **PayMongo** | 결제 수수료 (변동) | 변동 |
| **총 외부 서비스** | | **₱6,000** |

**총 월간 운영 비용: ₱45,536 ($813)**

---

## 🚀 배포 전략

### Phase 1: Development (1-2개월)
- 로컬 Docker Compose 환경
- PostgreSQL + Redis
- 기본 CRUD API

### Phase 2: Staging (1개월)
- AWS ECS 배포
- RDS + ElastiCache
- CI/CD 파이프라인 (GitHub Actions)

### Phase 3: Production (1개월)
- Multi-AZ 구성
- Auto-scaling 설정
- 모니터링 및 알림
- 백업 정책

---

## 📝 다음 단계

1. ✅ **Backend Repository 구조 생성**
2. ✅ **Docker 개발 환경 셋업**
3. ✅ **Database 스키마 마이그레이션**
4. ✅ **Auth Service 개발**
5. ✅ **Core Service 개발**
6. ✅ **Payment Service 개발**
7. ✅ **AI Service 개발**
8. ✅ **Frontend 연동**
9. ✅ **테스트 및 배포**

---

**문서 작성 완료**
작성일: 2025년 10월 25일
작성자: AI TrustTrade 백엔드팀
