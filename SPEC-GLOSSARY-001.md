# SPEC-GLOSSARY-001: AI 에이전트 용어사전 웹사이트 구축

## 개요
Claude Code에서 직접 수정하고 Vercel로 자동 배포되는 정적 HTML 용어사전 사이트.  
용어 추가/수정 시 Claude Code로 `index.html`의 데이터만 편집 → GitHub push → Vercel 자동 배포.

---

## 디렉토리 구조

```
ai-glossary/
├── index.html        ← 전체 앱 (단일 파일)
├── README.md
└── .gitignore
```

단일 HTML 파일 구조. 외부 의존성 없음. 빌드 과정 없음.

---

## T-01: GitHub 저장소 생성

### 작업 내용
1. 로컬에 `ai-glossary` 디렉토리 생성
2. 아래 `index.html` 파일 배치 (현재 완성된 용어사전 코드 그대로 사용)
3. `README.md` 생성
4. GitHub에 `ai-glossary` 퍼블릭 저장소 생성 후 push

### README.md 내용
```markdown
# AI 에이전트 용어사전

비개발자를 위한 AI 에이전트 관련 용어 정리 사이트.

## 용어 추가/수정 방법
`index.html` 내 `const T = [...]` 배열에 항목 추가.

## 배포
Vercel 자동 배포 연동. main 브랜치 push 시 자동 반영.
```

### .gitignore 내용
```
.DS_Store
```

---

## T-02: index.html 데이터 구조 명세

Claude Code가 용어를 추가/수정할 때 반드시 아래 구조를 따를 것.

### 용어 객체 스키마
```javascript
{
  id: "고유_영문_소문자",       // 예: "cicd", "rag", "token"
  cat: "env | func | agent",   // 카테고리 (환경 | 기능/확장 | 에이전트 구조)
  tag: "환경 | 확장 | 에이전트", // 표시용 태그 텍스트
  tc: "tag-env | tag-tool | tag-agent", // 태그 CSS 클래스
  title: "용어명",
  s: "한 줄 부제",              // 20자 이내 권장
  d: "개념 설명",               // 2~3문장, 비개발자 기준
  e: "예: 실제 사용 예시",       // '예:' 로 시작
  r: ["관련용어id", "..."]      // 관련 용어 id 배열, 최대 3개
}
```

### 카테고리별 tag/tc 매핑
| cat | tag | tc |
|-----|-----|----|
| env | 환경 | tag-env |
| func | 확장 | tag-tool |
| agent | 에이전트 | tag-agent |

---

## T-03: Vercel 배포 연동

### 작업 내용 (수동 1회 설정 — 건멍님이 직접 진행)
1. [vercel.com](https://vercel.com) 접속 → GitHub 계정으로 로그인
2. "Add New Project" → `ai-glossary` 저장소 선택
3. 설정값:
   - Framework Preset: **Other**
   - Root Directory: `/`
   - Build Command: **(비워두기)**
   - Output Directory: **(비워두기)**
4. Deploy 클릭

### 결과
- 배포 URL 자동 생성: `https://ai-glossary-xxx.vercel.app`
- 이후 `main` 브랜치에 push할 때마다 자동 재배포 (약 10~20초 소요)

---

## T-04: Claude Code 작업 흐름 (운영 단계)

### 용어 추가 요청 예시
```
"RAG와 파인튜닝의 차이 설명 추가해줘"
"'에이전트 루프' 용어 새로 만들어줘, 카테고리는 에이전트"
```

### Claude Code 처리 절차
1. `index.html` 열기
2. `const T = [...]` 배열에 새 항목 추가 또는 기존 항목 수정
3. 스키마 유효성 확인 (id 중복 없음, r 배열의 id가 실존하는지 확인)
4. 저장 → `git add . && git commit -m "용어 추가: [용어명]" && git push`
5. Vercel 자동 배포 완료 확인

### 주의사항
- `T` 배열 외 HTML/CSS/JS 구조는 수정하지 않음
- id는 영문 소문자+숫자만 사용, 공백·특수문자 금지
- `r` 배열에 존재하지 않는 id 참조 금지

---

## 완료 기준 (Definition of Done)

- [ ] GitHub 저장소에 `index.html` push 완료
- [ ] Vercel에서 배포 URL 발급됨
- [ ] URL 접속 시 용어사전 정상 표시
- [ ] 테스트: 용어 1개 추가 후 push → Vercel 자동 반영 확인

---

## 참고

- 현재 용어 수: 18개
- 추후 용어가 100개 이상이 되면 카테고리 필터 또는 검색 기능 추가 고려
- 커스텀 도메인 연결도 Vercel에서 무료로 지원 (필요 시 별도 진행)
