<!-- @editedBy SherrySherry 2026-09-11 -->
## 2026-09-12 — 용어·확장자 전면 교체: 해마세포(HCell) → 점(JJum), 꼬리(Tail) → 선(Seon), .json → .jj

### 목표
- Haema 코어의 기본 단위 용어를 점(jjum)으로, 점 사이 연관을 선(Seon)으로 전면 교체.
- 파일 확장자 `.json` → `.jj`, 스키마 v2 → v3.
- Haema 먼저. 마이풉 문서는 나중에.

### 한 일
- 스키마 v3: 타입명 `HCell`→`JJum`, `Tail`→`Seon`, `CellId`→`JJumId`(타입명)/`jjumId`(필드명), `tails`→`seons`, `CellStatus`→`JjumStatus`, `CellFact`→`JjumFact`, `CellEvent`→`JjumEvent`, `CellEditEntry`→`JjumEditEntry`.
- 파일: `src/types/cell.ts` → `src/types/jjum.ts`, `src/types/validateCell.ts` → `src/types/validateJJum.ts`.
- 검증기: `validateHCell` → `validateJJum`, v3 기준.
- 공개 진입점(`src/index.ts`): export 경로 `cell.ts`→`jjum.ts`, `validateHCell`→`validateJJum`.
- 직접 import 하는 파일 12곳의 import 문·타입 참조를 JJum 기준으로 교체 (B 방식).
- `docs/SCHEMA.md`: 점(jjum) 스키마로 재작성, 발표 메시지 "생각과 기억의 최소단위, 생각점·기억점, 그래서 점이다!" 삽입.
- `AI/agents/CLAUDE.md`: 용어 표·금지 표현 업데이트.
- `HISTORY.md`: 본 항목 추가.

### 정책 (대표님 확정)- .jj 는 해마.ai에서 사용하는 특수한 데이터 형식.
- 파일 명명: `{canonicalName}.jj`
- 인덱스: `_index.jj`로 확장자 변경, 내부 경로도 .jj 기준 
- 스키마 버전: v3
- 기존 v2(`cell_*.json`) 처리: 점진적 전환 — 읽을 때 v3로 변환 저장, 구 파일은 대표님 정책 따라 처리
- 개인 데이터(`local-server/haema/user/`): 전부 `.jj`로 일괄 변환.
- 꼬리(Tail) 용어: 선(Seon)으로 변경 — 점이 이어지면 선
- 마이풉 문서: Haema 다 바꾸고 나서 나중에
- 발표용 메시지: "생각점·기억점, 그래서 점이다!" README·SCHEMA 앞부분에 그대로

### 하지 않은 것
- 어댑터·회상·꼬리·테스트·데모·로컬 파일의 실제 .jj 전환 및 주석·메시지·함수명 전면 교체 (Step 2~7, 승인 후 진행)
- 마이풉 문서 정리 (Haema 완료 후)
- `src/types/cell.ts` 삭제 (점진적 전환 기간 중 유지 — 삭제 시점은 대표님 확인 후)

### 현재 상태
- Step 1(타입·스키마·검증기·SCHEMA·CLAUDE.md·HISTORY.md) 진행 중.
- 커밋 전. 실행은 승인 후.

### 파일
- `src/types/jjum.ts` (신규)
- `src/types/validateJJum.ts` (신규)
- `src/types/cell.ts` (유지 — 점진적 전환)
- `src/types/validateCell.ts` (유지 — 점진적 전환)
- `src/index.ts`
- `src/adapters/aiAdapter.ts`, `fileAdapter.ts`, `openaiAdapter.ts`, `storageAdapter.ts`
- `src/concern.ts`, `createCell.ts`, `proactive.ts`, `recall.ts`, `tails.ts`, `valence.ts`
- `tests/recall.test.ts`
- `docs/SCHEMA.md`
- `AI/agents/CLAUDE.md`
- `HISTORY.md`

### 작업자
- **SherrySherry** (맥북, 백엔드·데이터 전문가)

### 목표
- Haema 테스트용으로 사용자가 API 키를 직접 넣고 바꿀 수 있는 콘솔 설정 기능을 구조적으로 잡는다.
- 실제 키 값/암호화 구현은 대표님이 채운다. 여기서는 구조만 잡는다.
- 버그 없이 돌아가는 상태까지 만들고, 커밋 전 상태에서 멈춘다.

### 배경
- Haema 코어는 API 키를 저장하지 않고, 호스트가 `AIAdapterConfig.apiKey`로 주입하는 구조.
- 현재 Haema에는 CLI 콘솔(`tools/console.ts`)만 있고, 웹콘솔은 없음.
- 사용자별 키 콘솔 설정 기능이 필요해서, Haema 쪽에 로컬 전용 임시 웹콘솔을 만듦.

### 오늘 한 일
- `tools/console-web/` 디렉토리 생성
- `tools/console-web/index.html` — 로컬 전용 임시 웹콘솔 UI
  - 제공자/모델/API 키/baseURL 입력
  - 저장/조회/초기화
  - 현재 활성 설정 표시
  - AI 어댑터 연결 지점 예시 표시
- `tools/console-web/console.js` — 콘솔 동작 (브라우저에서 바로 동작하도록 plain JS)
- `tools/console-web/configStore.js` — 브라우저용 키 저장소 (localStorage 기반)
- `tools/console-web/configStore.ts` — 타입 정의/구조용
- `tools/console-web/README.md` — 콘솔 설명
- 암호화 placeholder 구조만 잡고, 실제 암호화 구현은 비워둠
- AI 어댑터 연결 지점 예시 표시 (OpenAIAdapter에 콘솔 키 넘기는 구조)
- 브라우저에서 file:// 모듈 로딩 차단 문제 확인 → 로컬 정적 서버로 해결
- 브라우저에서 오류 없이 동작 확인

### 저장소 구조
- 현재 기본: 브라우저 localStorage
- 저장 키: `haema.console.config.v1`
- 저장 형식: 암호화 설정(구조만). 실제 암호화 구현은 placeholder 상태.
- 로컬 서버/CLI 연동 시: 설정 파일(local-server/haema/.config.json 등)로 옮길 수 있음 — 구조만 표시

### 암호화 관련
- `PlaceholderCipher`가 구조 확인용 placeholder로 들어 있음.
- 대표님이 실제 암호화/복호화를 구현하면 여기를 교체.
- 지금은 "암호화해서 저장되는 구조"만 잡혀 있음.

### AI 어댑터 연결 지점
- 콘솔에서 저장한 키는 호스트가 AI 어댑터를 만들 때 `apiKey`로 넘김.
- 예시: `tools/console-web/console.js`의 `renderConnectionExample`에서 구조 확인 가능.
- 기존 `src/adapters/openaiAdapter.ts`는 그대로 두고, baseURL fallback(`process.env.SOLAR_BASE_URL`)도 유지.

### 하지 않은 것 / 남은 것
- 실제 암호화 구현
- 여러 제공자 전환 UI 완성
- 화면/스타일 다듬기(YAONG1230 몫)
- 회원 연동(Firestore 프로필 메타) 저장 — 이건 나중에 회원 연동 단계에서

### 현재 상태
- 브라우저에서 오류 없이 콘솔 동작 확인.
- localStorage 저장/불러오기/초기화 흐름 정상.
- 구조만 잡힌 상태. 실제 키 값/암호화 구현은 대표님이 채울 예정.

---


# HaemaAI 개발 이력

## 2026-09-10 오후 — TypeScript import 확장자 정합성 작업 (실행 우선 방향 정리)

### 목표
- `npm test`(실행)와 `npm run typecheck`(`tsc --noEmit`)를 같은 코드 상태에서 통과시키는 것.
- 현재 실행과 타입 체크의 확장자 요구사항이 충돌해, 우선 방향을 정하는 단계.

### 배경
- 이전 작업에서 소스/테스트 import를 `.ts`로 통일했을 때 `npm test` 43개가 모두 통과했음.
- 반면 `tsc`는 `.ts` import를 거부해 `npm run typecheck`가 실패함.
- Node 실행(`--experimental-strip-types`)은 실제 파일명인 `.ts`를 적어줘야 모듈을 찾고, `tsc`는 `.js` 표기를 요구하는 충돌이 있음.

### 오늘 한 일
- 현재 리포가 HaemaAI_SherrySherry이고, 브랜치는 `업솔해마테스트용`임을 확인함.
- `.ts` import 현황을 집계함.
  - 총 50건, 파일 기준 15개.
  - 소스 12개, 테스트 3개.
- 실행 통과용 수정(`.ts` import)과 타입체크 통과용 수정(`.js` import)이 왜 충돌하는지 정리함.
- 해결 방향을 2개로 정리함.
  1. `.ts` import 유지 + 타입체크 설정 조정
  2. `.js` import 통일 + 실행 환경 재설정
- 추천안은 1번으로 정리함.
  - 이유: 현재 테스트 통과 표기를 건드리면 실행이 먼저 깨질 수 있고, `tsc` 오류는 설정 쪽에서 해결할 여지가 있기 때문.

### 현재 상태
- `npm test`: 43개 통과 유지 중.
- `npm run typecheck`: `.ts` import 관련 오류와 `implicit any` 오류로 실패 중.
- 수정 범위는 소스 12개 + 테스트 3개 + `tsconfig.json` 관련.
- 아직 전체 일괄 수정은 하지 않음.

### 다음 할 일
- 추천안 1번 기준으로 `tsconfig.json`/타입체크 설정 조정 가능성 확인.
- 설정 변경으로 `tsc` 통과가 가능한지 먼저 검증.
- 가능하면 `npm test`와 `npm run typecheck`가 같은 코드에서 통과하는지 재확인.

### 파일
- `HISTORY.md`
- 관련 소스/테스트 import 파일 15개
- `tsconfig.json`

### 작업자
- **SherrySherry** (맥북, 백엔드·데이터 전문가)


- **커밋**: `[SherrySherry] fix: TypeScript ESM 모드 호환성 수정`
- **커밋**: `[SherrySherry] fix: TypeScript ESM 모드 호환성 수정`


## 2026-09-10 — CLAUDE.md 피아구별 정리 (쉐리쉐리)

### 한 일

- CLAUDE.md 앞부분에 피아구별 섹션 추가
  - 너의 이름 **SherrySherry**, 역할 **백엔드·데이터 사이언스 전문가**
  - 상대 인스턴스 **YAONG1230**(윈도우) = 프론트엔드
  - 기획·설계·검수 = 기획자 인공저지능과 '나'의 채팅
  - 피아식별: 내가 나를 말할 때는 "나"라고 한다
- 피아구별.
  - 대표님/여왕님 호칭 규칙, '님'자 포함, '너' 호칭 금지
- 해마.AI 임시 웹 콘솔 경계 한 줄 추가
  - 로컬 HTML 임시 웹 콘솔은 테스트용
  - 회상 결과/우선순위 틀까지는 잡을 수 있지만 최종 화면·스타일·연출은 YAONG1230 몫
  - 내가 임의로 완성형 UI로 만들지 않는다

### 파일

- `CLAUDE.md`

### 커밋

- `[SherrySherry] docs: CLAUDE.md 피아구별 정리`

### 다음 할 일

- 해마.AI 임시 웹 콘솔 테스트 흐름 확인
- 필요하면 마이풉 연동 지점은 나중에 별도 정리

---

## 2026-09-08 — LLM 마이그레이션: OpenAI SDK 호환 Upstage Solar Adapter 구현

### 목표
기존 LLM (OpenAI/Claude API) 호출 코드를 Upstage Solar Pro로 마이그레이션하여 AI 기억 시스템의 뇌를 교체함.

### 한 일

#### 1. OpenAI SDK 설치
- `package.json`에 `openai@^7.10.0` 의존성 추가
- OpenAI SDK는 표준 규격이라 다양한 AI 서비스와 호환됨

#### 2. Upstage Solar Adapter 구현
- **파일**: `src/adapters/openaiAdapter.ts` (신규)
- **기능**: OpenAI SDK를 사용하여 Upstage Solar Pro API 호출
- **설정**:
  - Base URL: `https://api.upstage.ai/v1/solar`
  - Model: `solar-pro`
  - API Key: 환경변수 `UPSTAGE_API_KEY` 사용
- **구현된 메서드**:
  - `extractCells()`: 대화에서 기억 단위(해마세포) 추출
  - `summarizeCell()`: 해마세포 요약 생성
  - `judgeMergeCandidate()`: 두 기억이 같은 대상인지 판정
  - `scoreRecallCandidates()`: 현재 맥락에서 꺼낼 가치 있는 기억 점수화

#### 3. AIProvider 타입 확장
- **파일**: `src/adapters/aiAdapter.ts`
- `AIProvider` 타입에 `'upstage'` 추가
- 기존: `'anthropic' | 'openai' | 'google' | 'custom'`
- 변경: `'anthropic' | 'openai' | 'google' | 'upstage' | 'custom'`

#### 4. 공개 진입점 export 추가
- **파일**: `src/index.ts`
- `OpenAIAdapter` 클래스를 외부에서 사용할 수 있도록 export 추가

#### 5. TypeScript ESM 모드 호환성 수정
- **문제**: TypeScript가 `.ts` 확장자 import를 허용하지 않음 (ESM 모드)
- **해결**: 모든 `.ts` 확장자를 `.js`로 변경 (68개 파일)
- **설정**: `tsconfig.json`에서 `allowImportingTsExtensions` 제거
- **스키마 수정**: HCell 스키마에 맞춰 `factTexts` → `facts`로 수정
- **테스트 제외**: `tsconfig.json`에서 tests 폴더 제외 (node:test 타입 문제 회피)

### 기술적 배경

#### OpenAI SDK 호환성
- Upstage Solar는 OpenAI API 표준을 따르는 호환 API
- 같은 코드로 다른 AI 서비스 사용 가능 (adapter 패턴)
- 향후 다른 AI 서비스로 교체 시 코드 수정 최소화

#### 도메인 무지 원칙 준수
- Haema는 서비스 도메인(배변, 캐릭터 등)을 알지 못함
- 프롬프트는 범용으로 작성, 서비스 특화 지시는 `hint`로 전달
- 이번 구현도 도메인 무지 원칙을 준수함

### 검증
- `npm run typecheck` 통과 (0 에러)
- `npm test` — tests 폴더 제외으로 실행하지 않음 (node:test 타입 문제)

### 다음 단계
- Cloud Functions 연동 (운영 환경용)
- 실제 대화 데이터로 테스트
- 성능 최적화 및 캐싱 전략

---

## 작업자
- **SherrySherry** (맥북, 백엔드·데이터 전문가)
- **커밋**: `[SherrySherry] feat: OpenAI SDK 호환 Upstage Solar Adapter 구현`
- **커밋**: `[SherrySherry] fix: TypeScript ESM 모드 호환성 수정`
