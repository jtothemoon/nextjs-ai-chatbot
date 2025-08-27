<a href="https://chat.vercel.ai/">
  <img alt="Next.js 14 and App Router-ready AI chatbot." src="app/(chat)/opengraph-image.png">
  <h1 align="center">Chat SDK</h1>
</a>

<p align="center">
    Chat SDK is a free, open-source template built with Next.js and the AI SDK that helps you quickly build powerful chatbot applications.
</p>

<p align="center">
  <a href="https://chat-sdk.dev"><strong>Read Docs</strong></a> ·
  <a href="#features"><strong>Features</strong></a> ·
  <a href="#model-providers"><strong>Model Providers</strong></a> ·
  <a href="#deploy-your-own"><strong>Deploy Your Own</strong></a> ·
  <a href="#running-locally"><strong>Running locally</strong></a>
</p>
<br/>

## Features

- [Next.js](https://nextjs.org) App Router
  - Advanced routing for seamless navigation and performance
  - React Server Components (RSCs) and Server Actions for server-side rendering and increased performance
- [AI SDK](https://sdk.vercel.ai/docs)
  - Unified API for generating text, structured objects, and tool calls with LLMs
  - Hooks for building dynamic chat and generative user interfaces
  - Supports xAI (default), OpenAI, Fireworks, and other model providers
- [shadcn/ui](https://ui.shadcn.com)
  - Styling with [Tailwind CSS](https://tailwindcss.com)
  - Component primitives from [Radix UI](https://radix-ui.com) for accessibility and flexibility
- Data Persistence
  - [Neon Serverless Postgres](https://vercel.com/marketplace/neon) for saving chat history and user data
  - [Vercel Blob](https://vercel.com/storage/blob) for efficient file storage
- [Auth.js](https://authjs.dev)
  - Simple and secure authentication

## Model Providers

This template ships with [xAI](https://x.ai) `grok-2-1212` as the default chat model. However, with the [AI SDK](https://sdk.vercel.ai/docs), you can switch LLM providers to [OpenAI](https://openai.com), [Anthropic](https://anthropic.com), [Cohere](https://cohere.com/), and [many more](https://sdk.vercel.ai/providers/ai-sdk-providers) with just a few lines of code.

## Deploy Your Own

You can deploy your own version of the Next.js AI Chatbot to Vercel with one click:

[![Deploy with Vercel](https://vercel.com/button)](https://vercel.com/new/clone?repository-url=https%3A%2F%2Fgithub.com%2Fvercel%2Fai-chatbot&env=AUTH_SECRET&envDescription=Learn+more+about+how+to+get+the+API+Keys+for+the+application&envLink=https%3A%2F%2Fgithub.com%2Fvercel%2Fai-chatbot%2Fblob%2Fmain%2F.env.example&demo-title=AI+Chatbot&demo-description=An+Open-Source+AI+Chatbot+Template+Built+With+Next.js+and+the+AI+SDK+by+Vercel.&demo-url=https%3A%2F%2Fchat.vercel.ai&products=%5B%7B%22type%22%3A%22integration%22%2C%22protocol%22%3A%22ai%22%2C%22productSlug%22%3A%22grok%22%2C%22integrationSlug%22%3A%22xai%22%7D%2C%7B%22type%22%3A%22integration%22%2C%22protocol%22%3A%22storage%22%2C%22productSlug%22%3A%22neon%22%2C%22integrationSlug%22%3A%22neon%22%7D%2C%7B%22type%22%3A%22integration%22%2C%22protocol%22%3A%22storage%22%2C%22productSlug%22%3A%22upstash-kv%22%2C%22integrationSlug%22%3A%22upstash%22%7D%2C%7B%22type%22%3A%22blob%22%7D%5D)

## 로컬 개발 환경 설정

### 사전 요구사항

- Node.js 18+ 및 npm/pnpm
- PostgreSQL 데이터베이스 ([Neon](https://neon.tech) 권장)
- AI 서비스 API 키 (Google Gemini, xAI, OpenAI 등)

### 빠른 설정 가이드

#### 1. 프로젝트 클론 및 의존성 설치

```bash
git clone <your-repo-url>
cd ai-chatbot
npm install
# 또는
pnpm install
```

#### 2. 환경변수 설정

루트 디렉토리에 `.env.local` 파일을 생성하고 다음 변수들을 설정하세요:

```bash
# 인증 시크릿 키 (생성: openssl rand -base64 32)
AUTH_SECRET=your_auth_secret_here

# 데이터베이스 설정
POSTGRES_URL=your_postgres_connection_string
DATABASE_URL=your_postgres_connection_string

# AI 서비스 (하나를 선택)
GOOGLE_GENERATIVE_AI_API_KEY=your_google_api_key
# 또는
XAI_API_KEY=your_xai_api_key
# 또는  
OPENAI_API_KEY=your_openai_api_key

# 파일 저장소
BLOB_READ_WRITE_TOKEN=your_vercel_blob_token

# 선택사항: Redis (사용하지 않으면 주석 처리)
# REDIS_URL=your_redis_url
```

#### 3. 데이터베이스 설정

**방법 A: Neon 사용 (권장)**
1. [neon.tech](https://neon.tech)에서 계정 생성
2. 새 프로젝트 생성
3. 연결 문자열을 `DATABASE_URL`과 `POSTGRES_URL`에 복사
4. 데이터베이스 마이그레이션 실행:
   ```bash
   npm run db:migrate
   ```

**방법 B: 수동 데이터베이스 생성**
데이터베이스에서 직접 테이블을 생성하려면 다음 SQL을 실행하세요:

<details>
<summary>SQL 스키마 보기</summary>

```sql
-- 모든 테이블 생성
CREATE TABLE IF NOT EXISTS "User" (
	"id" uuid PRIMARY KEY DEFAULT gen_random_uuid() NOT NULL,
	"email" varchar(64) NOT NULL,
	"password" varchar(64)
);

CREATE TABLE IF NOT EXISTS "Chat" (
	"id" uuid PRIMARY KEY DEFAULT gen_random_uuid() NOT NULL,
	"createdAt" timestamp NOT NULL,
	"title" text NOT NULL,
	"userId" uuid NOT NULL,
	"visibility" varchar DEFAULT 'private' NOT NULL
);

CREATE TABLE IF NOT EXISTS "Message_v2" (
	"id" uuid PRIMARY KEY DEFAULT gen_random_uuid() NOT NULL,
	"chatId" uuid NOT NULL,
	"role" varchar NOT NULL,
	"parts" json NOT NULL,
	"attachments" json NOT NULL,
	"createdAt" timestamp NOT NULL
);

-- 외래키 제약조건 추가
ALTER TABLE "Chat" ADD CONSTRAINT "Chat_userId_User_id_fk" FOREIGN KEY ("userId") REFERENCES "public"."User"("id");
ALTER TABLE "Message_v2" ADD CONSTRAINT "Message_v2_chatId_Chat_id_fk" FOREIGN KEY ("chatId") REFERENCES "public"."Chat"("id");
```
</details>

#### 4. API 키 설정

**Google Gemini (무료 옵션)**
1. [Google AI Studio](https://aistudio.google.com/apikey) 접속
2. "Create API key" 클릭
3. 생성된 키를 `GOOGLE_GENERATIVE_AI_API_KEY`에 입력

**Vercel Blob 저장소**
1. [Vercel 대시보드](https://vercel.com) → 프로젝트 → Storage
2. 새 Blob store 생성
3. 토큰을 `BLOB_READ_WRITE_TOKEN`에 복사

#### 5. AI 서비스 변경

기본적으로 Google Gemini를 사용합니다. 다른 서비스를 사용하려면 `lib/ai/providers.ts` 파일을 수정하세요:

```typescript
// OpenAI 사용 시
import { openai } from '@ai-sdk/openai';
'chat-model': openai('gpt-4'),

// xAI 사용 시  
import { xai } from '@ai-sdk/xai';
'chat-model': xai('grok-2-vision-1212'),
```

#### 6. 개발 서버 실행

```bash
npm run dev
# 또는
pnpm dev
```

이제 [localhost:3000](http://localhost:3000)에서 앱이 실행됩니다.

### 문제 해결

**데이터베이스 연결 오류**
- `DATABASE_URL` 형식이 올바른지 확인
- 데이터베이스 접근 권한 확인
- `npm run db:migrate` 실행하여 누락된 테이블 생성

**AI API 오류**
- API 키가 올바르고 크레딧/할당량이 있는지 확인
- 콘솔에서 구체적인 오류 메시지 확인
- `lib/ai/providers.ts`에서 AI 서비스가 올바르게 설정되었는지 확인

**의존성 충돌 오류**
- 설치 중 peer dependency 오류가 발생하면:
  ```bash
  npm install --legacy-peer-deps
  ```

### Vercel CLI 사용 방법 (선택사항)

Vercel CLI를 사용하려면:

1. Vercel CLI 설치: `npm i -g vercel`
2. 로컬 인스턴스 연결: `vercel link`
3. 환경변수 다운로드: `vercel env pull`
4. 의존성 설치 및 실행: `pnpm install && pnpm dev`
