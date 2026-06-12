# Korean Kubernetes Localization Style Notes

## 문체

- 한국어 본문은 간결한 설명체를 사용하고, 독자에게 직접 말하는 `you`는 대부분 생략한다.
- `kubectl` 사용 설명에서는 명령 이름, 플래그, 환경 변수, 파일명은 번역하지 않는다.
- 기존 한국어 문서의 어투에 맞춰 문장 종결은 `한다`, `사용한다`, `참고한다` 형태를 기본으로 한다.

## 기술 용어

- 쿠버네티스 리소스명과 API 필드명은 원문을 유지한다.
- `Pod`, `Deployment`, `Service`, `ServiceAccount`, `kubeconfig`, `KUBECONFIG`, `--kubeconfig`는 코드/고유명으로 유지한다.
- 링크 대상은 가능한 한국어 경로(`/ko/...`)를 사용하되, 생성되지 않은 자동 생성 참조나 외부 사이트 링크는 원문 구조를 유지한다.
- Hugo shortcode와 glossary tooltip은 원문 구조를 유지하고, 표시 텍스트만 한국어 문맥에 맞게 번역한다.

## 이번 작업에서 확인한 사항

- `content/zh-cn/docs/concepts/overview/kubectl.md`는 아직 없어 보조 참고 자료로 사용할 수 없었다.
- 공식 GitHub 검색에서 `content/ko/docs/concepts/overview/kubectl.md` 관련 한국어 PR/Issue는 발견되지 않았다.
- 동일 영어 문서에 대한 일본어 번역 이슈/PR은 닫힌 상태로 확인했다: <https://github.com/kubernetes/website/issues/54899>, <https://github.com/kubernetes/website/pull/54900>.
