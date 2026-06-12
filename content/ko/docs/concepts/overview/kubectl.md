---
title: kubectl 명령줄 도구
content_type: concept
description: >
  kubectl은 쿠버네티스 클러스터와 통신하기 위한 주된 명령줄 도구이다. 이 페이지에서는 kubectl의 개요와 쿠버네티스 생태계에서 kubectl이 맡는 역할을 설명한다.
weight: 50
card:
  name: concepts
  title: kubectl
  weight: 40
---

<!-- overview -->

{{< glossary_definition prepend="쿠버네티스는 다음을 제공한다: " term_id="kubectl" length="short" >}}

`kubectl` 도구는 [쿠버네티스 API](/ko/docs/concepts/overview/kubernetes-api/)를 통해 클러스터와 통신한다.
구성을 위해, `kubectl`은 `$HOME/.kube` 디렉터리에서 `config`라는 이름의 파일을 찾는다.
`KUBECONFIG` 환경 변수를 설정하거나
[`--kubeconfig`](/ko/docs/concepts/configuration/organize-cluster-access-kubeconfig/) 플래그를 설정하여
다른 [kubeconfig](/ko/docs/concepts/configuration/organize-cluster-access-kubeconfig/) 파일을 지정할 수 있다.

<!-- body -->

## kubectl의 역할

`kubectl` 도구는 쿠버네티스 오브젝트를 생성, 검사, 업데이트, 삭제하기 위한 주된 인터페이스이다.
이는 클러스터 안에서 실행되는 [쿠버네티스 컴포넌트](/ko/docs/concepts/overview/components/)와
그 컴포넌트가 구현하는 [쿠버네티스 API](/ko/docs/concepts/overview/kubernetes-api/)를 보완한다.
`kubectl`을 노트북에서 실행하든 클러스터 안의 파드에서 실행하든, `kubectl`은 API 서버로 요청을 보낸다.
[클라이언트 라이브러리](/ko/docs/reference/using-api/client-libraries/)와 같은 다른 클라이언트와
[Headlamp](https://headlamp.dev/) 같은 웹 대시보드도 같은 API를 통해 통신한다.

## kubectl의 동작 방식

`kubectl` 도구는 API 서버에 연결하고
[kubeconfig](/ko/docs/concepts/configuration/organize-cluster-access-kubeconfig/) 파일에 정의된
클러스터, 사용자, 컨텍스트를 사용하여 인증한다.
클러스터 외부에서 `kubectl`을 실행하면, API 서버 주소와 자격 증명을 찾기 위해 kubeconfig 파일을 사용한다.
`kubectl`이 파드 안에서 실행되는 경우(예: CI/CD 파이프라인), 파드에 마운트된 서비스어카운트 토큰을 기반으로
클러스터 내 인증을 사용할 수 있다.

커맨드를 실행하면 `kubectl`은 사용자의 의도를 하나 이상의
[쿠버네티스 API](/ko/docs/concepts/overview/kubernetes-api/)에 대한 HTTP 요청으로 변환한다.
API 서버는 각 요청을 검증하고, {{< glossary_tooltip text="etcd" term_id="etcd" >}}에 저장된 클러스터 상태에 적용한 뒤,
결과를 반환한다. 즉, 디플로이먼트를 생성하든 로그를 읽든 모든 `kubectl` 작업은 같은 API 기반 경로를 따른다.

kubeconfig는 여러 클러스터, 사용자, 컨텍스트를 정의할 수 있으므로, 환경을 다시 구성하지 않고도
`kubectl`을 사용해 클러스터 사이를 전환할 수 있다. 활성 컨텍스트를 변경하려면
`kubectl config use-context`를 실행한다.

## kubectl로 할 수 있는 일

`kubectl` 도구는 여러 작업을 지원하며, 이는 다음과 같은 큰 범주로 나눌 수 있다.

* **리소스 관리** - 파드, 디플로이먼트, 서비스 같은 오브젝트를 생성, 업데이트, 삭제한다.
  구성 파일을 사용한 선언형 관리에는 `kubectl apply`를 사용한다.
* **클러스터 상태 검사** - 오브젝트를 나열하고 설명하며, 이벤트를 보고 리소스 사용량을 확인한다.
* **디버그** - 컨테이너 로그를 보거나, 실행 중인 컨테이너 안에서 커맨드를 실행하거나, 파드로 포트 포워딩한다.
* **클러스터 운영** - 유지 보수를 위해 노드를 드레인하고, 새 워크로드가 스케줄링되지 않도록 노드를 코든하며,
  클러스터 구성을 관리한다.
* **스크립트와 자동화** - 스크립트와 파이프라인에서 사용할 수 있도록 [JSONPath](/ko/docs/reference/kubectl/jsonpath/)를 사용해
  출력을 JSON, YAML 또는 사용자 정의 열 형식으로 포맷한다.

구문, 커맨드 참조, 예제는 [kubectl 참조 문서](/ko/docs/reference/kubectl/)를 참고한다.

## 선언형과 명령형

프로덕션 워크로드에는 버전 관리되는 구성 파일과 함께 `kubectl apply`를 사용하는
[선언형 오브젝트 관리](/ko/docs/concepts/overview/working-with-objects/object-management/)를 선호한다.
선언형 관리는 변경 사항을 추적하고, 협업하며, GitOps 워크플로와 통합하는 데 도움이 된다.
명령형 커맨드(예: `kubectl create` 또는 `kubectl run`)는 개발과 실험에는 유용하지만,
재현하고 감사하기가 더 어렵다.

## 플러그인으로 kubectl 확장하기

새 하위 커맨드를 추가하는 [플러그인](/ko/docs/tasks/extend-kubectl/kubectl-plugins/)으로 `kubectl`을 확장할 수 있다.
플러그인은 `kubectl-<plugin-name>` 이름 지정 규칙을 따르는 독립 실행형 바이너리이다.
쿠버네티스 커뮤니티는 많은 플러그인을 유지 관리하며,
[Krew](https://krew.sigs.k8s.io/) 플러그인 관리자로 이를 관리할 수 있다.

## 버전 호환성

`kubectl` 도구는 클러스터의 컨트롤 플레인을 기준으로 마이너 버전 하나 위아래의 버전 차이를 지원한다.
예를 들어, `kubectl` v1.32는 v1.31, v1.32, v1.33 컨트롤 플레인과 함께 동작한다.
호환되는 버전을 사용하면 예상치 못한 동작을 피할 수 있다.
자세한 내용은 [버전 차이 정책](/ko/releases/version-skew-policy/)을 참고한다.

## {{% heading "whatsnext" %}}

* 구문과 커맨드 세부 사항은 [kubectl 참조](/ko/docs/reference/kubectl/)를 읽어본다.
* 사용하는 머신에 [kubectl을 설치](/ko/docs/tasks/tools/#kubectl)한다.
* `kubectl`이 사용하는 [쿠버네티스 API](/ko/docs/concepts/overview/kubernetes-api/)에 대해 알아본다.
* 클러스터를 구성하는 [쿠버네티스 컴포넌트](/ko/docs/concepts/overview/components/)를 살펴본다.
* [오브젝트 관리](/ko/docs/concepts/overview/working-with-objects/object-management/)와 선언형 구성을 알아본다.
* 지원되는 버전 조합은 [버전 차이 정책](/ko/releases/version-skew-policy/)에서 확인한다.
