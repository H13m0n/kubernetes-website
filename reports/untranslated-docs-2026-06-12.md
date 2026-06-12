# Untranslated Korean Docs Report - 2026-06-12

## 기준

- 기준 브랜치: `upstream/main` (`195d341244`)
- 비교 방식: `content/en/**/*.md`에는 있으나 동일 상대 경로의 `content/ko/**/*.md`가 없는 파일
- 제외/낮은 우선순위: `_index.md`, 자동 생성 reference, 오래된 blog, 특수 include/search 파일
- 전체 후보 수: 1,805개 (`_index.md` 제외)

## 카테고리별 후보 수

| 카테고리 | 후보 수 | 비고 |
|---|---:|---|
| 시작하기 | 4 | kubeadm/setup 중심 |
| 개념 | 37 | 핵심 개념 문서 다수 |
| 태스크 | 63 | 운영/디버그/파드 구성 문서 |
| 튜토리얼 | 11 | 클러스터 관리 튜토리얼 |
| 레퍼런스 | 922 | feature gates 및 generated kubeadm 문서 다수, 자동 생성 문서 우선순위 낮음 |
| 블로그 | 741 | 오래된 글 다수, 주간 번역 우선순위 낮음 |
| 릴리스 / 변경사항 | 4 | 릴리스 다운로드/패치 문서 |
| 커뮤니티 | 17 | 기여자 문서 중심 |
| 기타 | 6 | include/search/README 등 |

## 우선 검토 후보

| 카테고리 | 영어 문서 경로 | 예상 한국어 문서 경로 | 문서 제목 | 난이도 | 예상 분량 | 우선순위 | 진행 상태 | 관련 PR / Issue |
|---|---|---|---|---|---|---|---|---|
| 개념 | `content/en/docs/concepts/overview/kubectl.md` | `content/ko/docs/concepts/overview/kubectl.md` | The kubectl command-line tool | 보통 | 보통 | 높음 | 미진행 | 한국어 관련 없음. 일본어: <https://github.com/kubernetes/website/issues/54899>, <https://github.com/kubernetes/website/pull/54900> |
| 개념 | `content/en/docs/concepts/overview/working-with-objects/owners-dependents.md` | `content/ko/docs/concepts/overview/working-with-objects/owners-dependents.md` | Owners and Dependents | 보통 | 보통 | 보통 | 확인 필요 | 닫힌 한국어 이슈: <https://github.com/kubernetes/website/issues/36418> |
| 개념 | `content/en/docs/concepts/workloads/pods/static-pods.md` | `content/ko/docs/concepts/workloads/pods/static-pods.md` | Static Pods | 낮음 | 짧음 | 높음 | 미진행 | 한국어 관련 없음. 일본어: <https://github.com/kubernetes/website/issues/56010>, <https://github.com/kubernetes/website/pull/56022> |
| 개념 | `content/en/docs/concepts/scheduling-eviction/gang-scheduling.md` | `content/ko/docs/concepts/scheduling-eviction/gang-scheduling.md` | Gang Scheduling | 보통 | 짧음 | 보통 | 확인 필요 | 이번 회차 미확인 |
| 태스크 | `content/en/docs/tasks/configure-pod-container/enforce-standards-admission-controller.md` | `content/ko/docs/tasks/configure-pod-container/enforce-standards-admission-controller.md` | Enforce Pod Security Standards by Configuring the Built-in Admission Controller | 보통 | 짧음 | 높음 | 확인 필요 | 이번 회차 미확인 |
| 태스크 | `content/en/docs/tasks/debug/debug-cluster/kubectl-node-debug.md` | `content/ko/docs/tasks/debug/debug-cluster/kubectl-node-debug.md` | Debugging Kubernetes Nodes With Kubectl | 보통 | 짧음 | 보통 | 확인 필요 | 이번 회차 미확인 |
| 시작하기 | `content/en/docs/setup/production-environment/tools/kubeadm/dual-stack-support.md` | `content/ko/docs/setup/production-environment/tools/kubeadm/dual-stack-support.md` | Dual-stack support with kubeadm | 보통 | 보통 | 보통 | 확인 필요 | 이번 회차 미확인 |

## 이번 주 최종 선정 문서

- 카테고리: 개념
- 영어 문서: `content/en/docs/concepts/overview/kubectl.md`
- 한국어 문서: `content/ko/docs/concepts/overview/kubectl.md`
- 선정 이유:
  - 핵심 도구인 `kubectl` 개념 문서로 사용자 가치가 높다.
  - 한국어 대응 문서가 없다.
  - 공식 저장소의 한국어 진행 PR/Issue가 발견되지 않았다.
  - 기존 `content/ko/docs/reference/kubectl/_index.md`와 용어를 맞추기 쉽다.
  - 분량이 주간 단위 작업에 적절하다.
