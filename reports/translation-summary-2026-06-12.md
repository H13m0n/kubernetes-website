# Translation Summary - 2026-06-12

## 이번 주 선정 문서

- 카테고리: 개념
- 영어 문서: `content/en/docs/concepts/overview/kubectl.md`
- 한국어 문서: `content/ko/docs/concepts/overview/kubectl.md`
- 브랜치: `ko-translate/concepts/kubectl`

## 진행 중 여부 확인

- GitHub 검색:
  - `repo:kubernetes/website "content/ko/docs/concepts/overview/kubectl.md"`: 결과 없음
  - `repo:kubernetes/website "content/en/docs/concepts/overview/kubectl.md" ko OR Korean OR translation OR localization OR l10n`: 한국어 결과 없음
- 관련 PR:
  - 일본어 번역 PR(닫힘): <https://github.com/kubernetes/website/pull/54900>
- 관련 Issue:
  - 일본어 번역 이슈(닫힘): <https://github.com/kubernetes/website/issues/54899>
- 판단: 공식 저장소에서 한국어 번역 진행 중인 흔적은 발견되지 않았다.

## 참고한 번역 사례

- 유사 문서:
  - `content/ko/docs/reference/kubectl/_index.md`
  - `content/ko/docs/reference/using-api/_index.md`
  - `content/ko/docs/reference/using-api/health-checks.md`
  - `content/ko/docs/reference/access-authn-authz/service-accounts-admin.md`
- 중국어 번역:
  - `content/zh-cn/docs/concepts/overview/kubectl.md`는 없어 참고하지 못했다.
- 주요 리뷰 코멘트:
  - 이번 회차에서 `kubectl.md` 한국어 관련 리뷰 코멘트는 발견되지 않았다.

## 주요 용어 결정

| English | Korean | 근거 |
|---|---|---|
| command-line tool | 명령줄 도구 | 기존 `content/ko/docs/reference/kubectl/_index.md` 제목 |
| command | 커맨드 | 기존 kubectl 참조 문서의 설명 어휘 |
| Kubernetes API | 쿠버네티스 API | 기존 API 참조 번역 |
| API server | API 서버 | 기존 API 헬스 문서 번역 |
| control plane | 컨트롤 플레인 | 기존 서비스어카운트 문서 번역 |
| ServiceAccount | 서비스어카운트 | 기존 인증/인가 문서 번역 |
| declarative | 선언형 | 오브젝트 관리 문맥의 일반 번역 |
| imperative | 명령형 | 선언형과 대비되는 kubectl 커맨드 방식 |
| version skew | 버전 차이 | 릴리스 정책 링크 표현과 일치 |

## 애매했던 표현

- `cordon nodes`: 기존 한국어 문서에서 자주 쓰이는 명령어 문맥에 맞춰 `노드를 코든`으로 번역했다.
- `script and automate`: 단순 명사형보다 kubectl 사용 목적에 맞게 `스크립트와 자동화`로 처리했다.
- `plus-or-minus one minor version`: 정책 문서 제목과 맞춰 `마이너 버전 하나 위아래의 버전 차이`로 풀어 썼다.

## 추후 공식 PR 생성 시 주의할 점

- 공식 PR을 만들기 전, 같은 경로에 대한 최신 open PR/Issue를 다시 확인해야 한다.
- `content/ko/docs/reference/kubectl/_index.md`와 겹치는 설명이 있으므로 리뷰에서 중복성 질문이 나올 수 있다.
- `content/zh-cn` 대응 문서가 없어 다른 현지화의 구조 비교는 제한적이었다.
- `version-skew-policy` 한국어 경로가 실제 빌드에서 올바르게 해석되는지 링크 검사가 필요하다.
