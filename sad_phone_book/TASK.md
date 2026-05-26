# TASK.md — 전화번호 관리 시스템 구현 태스크

## Phase 1: 프로젝트 초기 설정

### TASK-01: Next.js 프로젝트 생성
- `npx create-next-app@latest phone-book --typescript --tailwind --app --no-src-dir` 실행
- 불필요한 보일러플레이트 파일 제거
- 완료 기준: `npm run dev` 로컬 실행 성공

### TASK-02: Supabase 프로젝트 설정
- Supabase 콘솔(https://supabase.com)에서 새 프로젝트 생성
- `contacts` 테이블 생성 SQL 실행:
```sql
create table contacts (
  id uuid primary key default gen_random_uuid(),
  name text not null,
  phone text not null,
  created_at timestamp with time zone default now(),
  updated_at timestamp with time zone default now()
);

-- RLS 비활성화 (로그인 없는 공개 접근)
alter table contacts disable row level security;
```
- API URL 및 anon key 복사
- 완료 기준: Supabase 테이블 생성 확인

### TASK-03: 환경 변수 설정
- 프로젝트 루트에 `.env.local` 파일 생성:
```
NEXT_PUBLIC_SUPABASE_URL=your_supabase_url
NEXT_PUBLIC_SUPABASE_ANON_KEY=your_anon_key
```
- `.env.local`을 `.gitignore`에 추가 확인
- 완료 기준: 환경 변수 로드 확인

### TASK-04: Supabase 클라이언트 설치 및 설정
- `npm install @supabase/supabase-js` 실행
- `lib/supabase.ts` 파일 생성 (싱글턴 클라이언트)
- 완료 기준: import 오류 없이 클라이언트 초기화

---

## Phase 2: 백엔드 API 구현

### TASK-05: GET /api/contacts — 전체 조회 API
- `app/api/contacts/route.ts` 생성
- Supabase에서 `contacts` 전체 조회 (created_at 내림차순)
- 응답: `{ data: Contact[] }`
- 완료 기준: curl 테스트 성공

### TASK-06: POST /api/contacts — 추가 API
- `app/api/contacts/route.ts`에 POST 핸들러 추가
- body: `{ name: string, phone: string }`
- 유효성 검사: name, phone 필수값 체크
- 응답: `{ data: Contact }`
- 완료 기준: 데이터 삽입 및 응답 확인

### TASK-07: PUT /api/contacts/[id] — 수정 API
- `app/api/contacts/[id]/route.ts` 생성
- body: `{ name?: string, phone?: string }`
- updated_at 자동 갱신
- 응답: `{ data: Contact }`
- 완료 기준: 특정 레코드 수정 확인

### TASK-08: DELETE /api/contacts/[id] — 삭제 API
- `app/api/contacts/[id]/route.ts`에 DELETE 핸들러 추가
- 응답: `{ success: true }`
- 완료 기준: 특정 레코드 삭제 확인

---

## Phase 3: 프론트엔드 구현

### TASK-09: 타입 정의
- `types/contact.ts` 생성
```ts
export interface Contact {
  id: string;
  name: string;
  phone: string;
  created_at: string;
  updated_at: string;
}
```

### TASK-10: 연락처 추가 폼 컴포넌트
- `components/AddContactForm.tsx` 생성
- 입력 필드: 이름, 전화번호
- 추가 버튼 클릭 시 POST /api/contacts 호출
- 성공 후 입력 필드 초기화 및 목록 갱신
- 완료 기준: UI에서 추가 후 목록에 반영

### TASK-11: 연락처 목록 컴포넌트
- `components/ContactList.tsx` 생성
- GET /api/contacts 데이터 렌더링
- 각 행: 이름, 전화번호, 수정 버튼, 삭제 버튼
- 로딩 상태 표시
- 완료 기준: 목록 정상 출력

### TASK-12: 인라인 수정 기능
- 수정 버튼 클릭 시 해당 행이 입력 폼으로 전환
- 저장 버튼 클릭 시 PUT /api/contacts/[id] 호출
- 취소 버튼으로 편집 모드 해제
- 완료 기준: 수정 후 목록 즉시 반영

### TASK-13: 삭제 기능
- 삭제 버튼 클릭 시 확인 다이얼로그 표시
- 확인 시 DELETE /api/contacts/[id] 호출
- 목록에서 즉시 제거
- 완료 기준: 삭제 후 목록 갱신 확인

### TASK-14: 검색 기능
- 검색 입력창 추가
- 클라이언트 사이드 필터: 이름 또는 전화번호로 검색
- 실시간 필터링 (onChange)
- 완료 기준: 입력 시 목록 즉시 필터링

### TASK-15: 메인 페이지 조합
- `app/page.tsx`에 모든 컴포넌트 통합
- 상태 관리: contacts 배열, 검색어, 로딩 상태
- 완료 기준: 전체 CRUD 흐름 동작 확인

---

## Phase 4: 배포

### TASK-16: Vercel 배포
- GitHub 저장소 생성 및 코드 push
- Vercel 콘솔에서 프로젝트 import
- 환경 변수 설정 (NEXT_PUBLIC_SUPABASE_URL, NEXT_PUBLIC_SUPABASE_ANON_KEY)
- 배포 실행
- 완료 기준: 배포된 URL에서 전체 기능 동작 확인

### TASK-17: 최종 검증
- 연락처 추가 테스트
- 연락처 수정 테스트
- 연락처 삭제 테스트
- 검색 기능 테스트
- 모바일 반응형 확인
- 완료 기준: 모든 기능 정상 동작

---

## 파일 구조 (최종)

```
phone-book/
├── app/
│   ├── api/
│   │   └── contacts/
│   │       ├── route.ts          # GET, POST
│   │       └── [id]/
│   │           └── route.ts      # PUT, DELETE
│   ├── globals.css
│   ├── layout.tsx
│   └── page.tsx                  # 메인 페이지
├── components/
│   ├── AddContactForm.tsx
│   ├── ContactList.tsx
│   └── SearchBar.tsx
├── lib/
│   └── supabase.ts
├── types/
│   └── contact.ts
├── .env.local
├── .gitignore
├── next.config.js
├── package.json
├── tailwind.config.ts
└── tsconfig.json
```
