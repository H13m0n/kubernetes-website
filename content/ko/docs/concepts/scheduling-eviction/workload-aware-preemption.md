---
title: 워크로드 인식 선점
content_type: concept
weight: 80
---

<!-- overview -->
{{< feature-state feature_gate_name="WorkloadAwarePreemption">}}

워크로드 인식 선점은 파드그룹(PodGroup)을 위해 특별히 설계된 선점 메커니즘을 도입한다.
파드그룹을 스케줄링할 수 없을 때, 스케줄러는 이 파드그룹의 스케줄링이 가능해지도록 시도하는
선점 로직을 사용한다. 이 접근 방식은 파드그룹 스케줄링 중에만 사용되며,
해당 파드그룹에 속한 파드에는 기본 선점 메커니즘 대신 적용된다.

이 기능이 활성화되면, 스케줄러는 파드그룹의 개별 파드를 따로 평가하지 않고
파드그룹을 하나의 선점자 단위로 취급한다. 그룹의 보류 중인 파드를 위한 공간을 만들기 위해
전체 클러스터에서 피해자를 찾으며, 다른 파드그룹의 중단 모드에 따라 이를 피해자로 취급하고 선점할 수 있다.

이 기능은 [갱 스케줄링](/docs/concepts/scheduling-eviction/gang-scheduling/)과
[워크로드 API](/docs/concepts/workloads/workload-api/)에 의존한다.
클러스터에서 [`GenericWorkload`](/docs/reference/command-line-tools-reference/feature-gates/#GenericWorkload)와
[`GangScheduling`](/docs/reference/command-line-tools-reference/feature-gates/#GangScheduling) 기능 게이트,
그리고 `scheduling.k8s.io/v1alpha2` {{< glossary_tooltip text="API 그룹" term_id="api-group" >}}이 활성화되어 있는지 확인한다.

<!-- body -->

## 동작 방식

워크로드 인식 선점 과정은 [기본 선점](/docs/concepts/scheduling-eviction/pod-priority-preemption/#선점)과
같은 원칙을 따르지만, 몇 가지 차이가 있다.

1. 클러스터 전체 도메인: 스케줄러는 노드별로 선점을 평가하는 대신,
   전체 클러스터를 하나의 도메인으로 평가한다.
   여러 노드에 걸쳐 제거할 수 있는 피해자 집합을 선택하여 선점자 파드그룹이
   스케줄링될 수 있는 충분한 공간을 만든다.

2. 피해자 중요도 계층: 스케줄러는 엄격한 계층을 사용해 어떤 선점 단위
   (개별 파드 또는 파드그룹)가 더 중요하고 선점에서 제외되어야 하는지 결정한다.
   * 우선순위: 우선순위가 더 높은 단위가 항상 더 중요하다.
   * 워크로드 유형: 같은 우선순위라면 파드그룹이 개별 파드보다 더 중요하다고 간주된다.
   * 그룹 크기(파드그룹): 두 단위가 모두 파드그룹이면, 구성원이 더 많은 그룹(더 큰 크기)이 더 중요하다고 간주된다.
   * 시작 시간: 더 일찍 시작한 단위가 더 중요하다.

3. 파드그룹 우선순위와 중단: 스케줄러는 파드그룹의 구체적인
   [우선순위와 중단 모드](/docs/concepts/workloads/workload-api/disruption-and-priority/)를 고려하여
   선점 이벤트 중 해당 파드를 선점할 수 있는지, 선점할 수 있다면 어떻게 선점할지를 평가한다.

{{< note >}}
단일 파드를 스케줄링할 때는 기본 파드 선점이 적용된다.
1.36 기준으로, 스케줄러가 단일 파드에 대해 기본 선점을 수행하면서
파드그룹에 속한 파드를 선점하려고 시도하는 경우,
해당 파드그룹의 `priority` 또는 `disruptionMode` 필드를 **고려하지 않는다**.
{{< /note >}}

## {{% heading "whatsnext" %}}

* [파드그룹 우선순위와 중단](/docs/concepts/workloads/workload-api/disruption-and-priority/)에 대해 더 알아본다.
* [워크로드 API](/docs/concepts/workloads/workload-api/)에 대해 알아본다.
* [갱 스케줄링](/docs/concepts/scheduling-eviction/gang-scheduling/)에 대해 더 읽어본다.
