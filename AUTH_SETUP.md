# 로그인 & 회원관리 설정 가이드

기존 Supabase KPI/OKR 연동에 **로그인·비밀번호·회원관리**가 추가되었습니다.

---

## 1단계: 인증 SQL 실행 (필수)

Supabase → **SQL Editor** → `supabase/auth-schema.sql` **전체 실행**

이 SQL이 하는 일:
- `profiles` 회원 테이블 생성
- KPI/OKR **익명 접근 차단** → **로그인한 회원만** 접근
- 관리자 / 일반 회원 역할
- 최초 관리자 자동 설정 기능

> `schema.sql` 은 이미 실행했다면 **auth-schema.sql 만 추가 실행**하면 됩니다.

---

## 2단계: Supabase Auth 설정

Supabase → **Authentication** → **Providers** → **Email**

| 설정 | 권장값 | 이유 |
|------|--------|------|
| **Enable Email provider** | ON | 이메일 로그인 |
| **Confirm email** | **OFF** | 팀 내부용 — 가입 즉시 로그인 |
| **Secure email change** | ON | 보안 |

**Authentication** → **URL Configuration**

| 항목 | 값 |
|------|-----|
| **Site URL** | 대시보드 URL (예: `https://아이디.github.io/kpi-okr-dashboard/` 또는 `http://localhost:3000`) |
| **Redirect URLs** | 같은 URL 추가 |

---

## 3단계: 최초 관리자 만들기

### 방법 A — 대시보드에서 (권장)

1. 대시보드 접속
2. **「최초 관리자 설정」** 화면 표시 (회원 0명일 때)
3. 이름, 이메일, 비밀번호 입력 → **관리자 계정 만들기**

### 방법 B — Supabase에서 수동

1. **Authentication** → **Users** → **Add user** → 이메일/비밀번호 생성
2. SQL Editor에서 (이메일 수정):

```sql
UPDATE profiles SET role = 'admin' WHERE email = 'admin@yourcompany.com';
```

---

## 4단계: 팀원 추가 (관리자)

1. 관리자로 로그인
2. 사이드바 **「회원 관리」**
3. 이름, 이메일, 임시 비밀번호 입력 → **회원 등록**
4. 팀원에게 이메일/임시 비밀번호 전달
5. 팀원은 **내 계정**에서 비밀번호 변경

---

## 기능 요약

| 기능 | 위치 | 권한 |
|------|------|------|
| 로그인 | 첫 화면 | 전체 |
| 비밀번호 찾기 | 로그인 화면 | 전체 |
| 비밀번호 변경 | 내 계정 | 전체 |
| 이름 변경 | 내 계정 | 전체 |
| 회원 등록 | 회원 관리 | 관리자 |
| 역할 변경 (회원↔관리자) | 회원 관리 | 관리자 |
| 계정 정지/활성화 | 회원 관리 | 관리자 |
| 재설정 메일 발송 | 회원 관리 ✉ 버튼 | 관리자 |
| 로그아웃 | 사이드바 | 전체 |

---

## GitHub Pages / Netlify 배포 시

업로드 파일:
- `index.html`
- `supabase-config.js`
- `.nojekyll`

Supabase **Site URL** 과 **Redirect URLs** 에 배포 URL을 등록하세요.

---

## 문제 해결

### 로그인 후 「동기화 오류」
→ `auth-schema.sql` 실행 여부 확인

### 「Email not confirmed」
→ Supabase에서 **Confirm email** 끄기

### 비밀번호 재설정 메일이 안 옴
→ Supabase **Site URL** / **Redirect URLs** 확인  
→ 스팸함 확인

### 최초 관리자 화면이 안 나옴
→ 이미 회원이 있음 → 관리자에게 계정 요청  
→ 또는 SQL로 admin 지정

---

## 보안 변경 사항

**이전:** 링크만 있으면 누구나 KPI/OKR 수정 가능  
**이후:** 로그인한 활성 회원만 접근 가능

외부인은 계정 없이 **데이터를 볼 수 없습니다.**
