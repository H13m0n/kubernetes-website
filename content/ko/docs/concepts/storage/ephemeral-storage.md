---
title: 로컬 임시 스토리지
content_type: concept
weight: 95
---

노드에는 로컬에 연결된 쓰기 가능 장치 또는, 때로는 RAM에 의해
지원되는 로컬 임시 스토리지가 있다.
"임시"는 내구성에 대한 장기간의 보증이 없음을 의미한다.

파드는 스크래치 공간, 캐싱 및 로그에 대해 임시 로컬 스토리지를 사용한다.
kubelet은 로컬 임시 스토리지를 사용하여 컨테이너에
[`emptyDir`](/ko/docs/concepts/storage/volumes/#emptydir)
{{< glossary_tooltip term_id="volume" text="볼륨" >}}을 마운트하기 위해 파드에 스크래치 공간을 제공할 수 있다.

kubelet은 이러한 종류의 스토리지를 사용하여
[노드-레벨 컨테이너 로그](/ko/docs/concepts/cluster-administration/logging/),
컨테이너 이미지 및 실행 중인 컨테이너의 쓰기 가능 계층을 저장한다.

{{< caution >}}
노드가 실패하면 임시 스토리지의 데이터가 손실될 수 있다.
애플리케이션은 로컬 임시 스토리지에서 성능에 대한 SLA(예: 디스크 IOPS)를
기대할 수 없다.
{{< /caution >}}

{{< note >}}
리소스 쿼터가 `ephemeral-storage` 에 대해 작동하도록 하려면 두 가지가 필요하다.

* 관리자는 네임스페이스에 `ephemeral-storage` 용 리소스 쿼터를 설정해야 한다.
* 사용자는 파드 스펙에 `ephemeral-storage` 리소스의 제한을 지정해야 한다.

사용자가 파드 스펙에 `ephemeral-storage` 리소스 제한을 지정하지 않으면,
리소스 쿼터는 `ephemeral-storage` 에 대해 적용되지 않는다.
{{< /note >}}

쿠버네티스는 파드가 소비할 수 있는 임시 로컬 스토리지 양을
추적하고 예약하며 제한할 수 있게 한다.

## 로컬 임시 스토리지 구성 {#configurations}

쿠버네티스는 노드에서 로컬 임시 스토리지를 구성하는 다음 방식을 지원한다.

{{< tabs name="local_storage_configurations" >}}
{{% tab name="단일 파일시스템" %}}
이 구성에서는 모든 종류의 임시 로컬 데이터(`emptyDir` 볼륨,
쓰기 가능 계층, 컨테이너 이미지, 로그)를 하나의 파일시스템에 배치한다.

kubelet은 또한
[노드-레벨 컨테이너 로그](/ko/docs/concepts/cluster-administration/logging/)를
기록하고 이를 임시 로컬 스토리지와 유사하게 처리한다.

kubelet은 구성된 로그 디렉터리 내의 파일에 로그를 기록한다(기본적으로
`/var/log`). 그리고 로컬에 저장된 다른 데이터에 대한 기본 디렉터리가 있다(기본적으로
`/var/lib/kubelet`).

일반적으로 `/var/lib/kubelet` 와 `/var/log` 는 모두 시스템 루트 파일시스템에 있으며,
kubelet은 이런 레이아웃을 염두에 두고 설계되었다.

노드는 쿠버네티스에서 사용하지 않는 다른 많은 파일시스템을
가질 수 있다.
{{% /tab %}}
{{% tab name="런타임 파일시스템" %}}
이 구성에서는 노드에서 실행 중인 파드의 임시 데이터(로그와 `emptyDir` 볼륨 등)를
위한 하나의 파일시스템을 사용한다. 이 파일시스템은 쿠버네티스와 관련 없는
시스템 로그와 같은 다른 데이터에도 사용할 수 있으며, 루트 파일시스템일 수도 있다.

kubelet은 또한
[노드-레벨 컨테이너 로그](/ko/docs/concepts/cluster-administration/logging/)를
첫 번째 파일시스템에 기록하고, 이를 임시 로컬 스토리지와 유사하게 처리한다.

또한 다른 논리 스토리지 장치가 지원하는 별도의 파일시스템을 사용한다.
이 구성에서 컨테이너 런타임은 두 번째 파일시스템에 컨테이너 이미지 계층과
쓰기 가능 계층을 모두 저장한다. 이 저장소 위치는 kubelet이 아니라
컨테이너 런타임에서 구성한다.

첫 번째 파일시스템에는 이미지 계층이나 쓰기 가능 계층이 없다.

노드는 쿠버네티스에서 사용하지 않는 다른 많은 파일시스템을
가질 수 있다.
{{% /tab %}}
{{% tab name="이미지 파일시스템 분리" %}}
이 구성에서는 컨테이너 이미지 계층이 별도의 파일시스템에 있고,
컨테이너 쓰기 가능 계층은 로그와 `emptyDir` 볼륨처럼 kubelet의 임시 데이터와
같은 파일시스템에 있다.

이 레이아웃은 `containerfs` 축출 신호를 지원해야 한다. 기능 게이트와
이 레이아웃을 지원하는 컨테이너 런타임에 대한 자세한 내용은
[노드-압박 축출](/ko/docs/concepts/scheduling-eviction/node-pressure-eviction/)을 참고한다.
{{% /tab %}}
{{< /tabs >}}

[노드-압박 축출](/ko/docs/concepts/scheduling-eviction/node-pressure-eviction/)
문서에서는 이렇게 관찰되는 파일시스템을 `nodefs`, `imagefs`, `containerfs` 로 부른다.
이 이름이 항상 서로 다른 마운트 지점을 의미하는 것은 아니다.

노드를 지원되는 로컬 임시 스토리지 구성 중 하나로 설정하면,
kubelet은 로컬 스토리지 사용량을 측정할 수 있다.

다른 구성을 사용하는 경우 kubelet은 임시 로컬 스토리지에 대한 리소스
제한을 적용하지 않는다.

{{< note >}}
kubelet은 `tmpfs` `emptyDir` 볼륨을 로컬 임시 스토리지가 아니라
컨테이너 메모리 사용량으로 추적한다.
{{< /note >}}

{{< note >}}
kubelet은 지원되는 레이아웃을 통해 관찰하는 파일시스템에서만 임시 스토리지를 추적할 수 있다.
이 레이아웃 바깥에서 `/var/lib/kubelet`, `/var/log`, 또는 컨테이너 런타임 저장소
디렉터리 아래에 추가 파일시스템을 마운트하면, kubelet이 임시 스토리지를
정확히 보고하지 못할 수 있다.
{{< /note >}}

## 로컬 임시 스토리지에 대한 요청 및 제한 설정 {#requests-limits}

로컬 임시 스토리지를 관리하기 위해 `ephemeral-storage` 를 지정할 수 있다.
파드의 각 컨테이너는 다음 중 하나 또는 모두를 지정할 수 있다.

* `spec.containers[].resources.limits.ephemeral-storage`
* `spec.containers[].resources.requests.ephemeral-storage`

`ephemeral-storage` 에 대한 제한 및 요청은 바이트 단위로 측정된다.
E, P, T, G, M, k와 같은 접미사 중 하나를 사용하여 스토리지를 일반 정수 또는
고정 소수점 숫자로 표현할 수 있다. Ei, Pi, Ti, Gi, Mi, Ki와 같은
2의 거듭제곱 접미사도 사용할 수 있다. 예를 들어, 다음 값은 거의 같은 값을 나타낸다.

- `128974848`
- `129e6`
- `129M`
- `123Mi`

접미사의 대소문자에 유의한다. `400m` 의 `ephemeral-storage` 를 요청하면,
이는 0.4 바이트를 요청하는 것이다. 아마도 400 메비바이트(`400Mi`) 또는
400 메가바이트(`400M`)를 의도했을 것이다.

다음 예에서 파드에는 두 개의 컨테이너가 있다.
각 컨테이너에는 2GiB의 로컬 임시 스토리지 요청이 있고,
4GiB의 로컬 임시 스토리지 제한이 있다. 따라서 파드는 4GiB의
로컬 임시 스토리지 요청과 8GiB의 로컬 임시 스토리지 제한을 가진다.
이 제한 중 최대 500Mi는 `emptyDir` 볼륨이 소비할 수 있다.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: frontend
spec:
  containers:
  - name: app
    image: images.my-company.example/app:v4
    resources:
      requests:
        ephemeral-storage: "2Gi"
      limits:
        ephemeral-storage: "4Gi"
    volumeMounts:
    - name: ephemeral
      mountPath: "/tmp"
  - name: log-aggregator
    image: images.my-company.example/log-aggregator:v6
    resources:
      requests:
        ephemeral-storage: "2Gi"
      limits:
        ephemeral-storage: "4Gi"
    volumeMounts:
    - name: ephemeral
      mountPath: "/tmp"
  volumes:
    - name: ephemeral
      emptyDir:
        sizeLimit: 500Mi
```

## `ephemeral-storage` 요청이 있는 파드의 스케줄링 방법

파드를 생성하면 쿠버네티스 스케줄러가 파드를 실행할 노드를 선택한다.
각 노드에는 파드에 제공할 수 있는 최대 로컬 임시 스토리지 양이 있다.
자세한 내용은
[노드 할당 가능](/docs/tasks/administer-cluster/reserve-compute-resources/#node-allocatable)을
참고한다.

스케줄러는 스케줄된 컨테이너들의 리소스 요청 총합이 노드의 용량보다 작도록 보장한다.

## 임시 스토리지 소비 관리 {#resource-emphemeralstorage-consumption}

kubelet이 로컬 임시 스토리지를 리소스로 관리하는 경우, kubelet은 다음의 스토리지
사용량을 측정한다.

- `_tmpfs_` `emptyDir` 볼륨을 제외한 `emptyDir` 볼륨
- 노드-레벨 로그를 저장하는 디렉터리
- 컨테이너의 쓰기 가능 계층

파드가 허용된 양보다 더 많은 임시 스토리지를 사용하면 kubelet은
파드 축출을 트리거하는 축출 신호를 설정한다.

컨테이너 수준 격리에서는 컨테이너의 쓰기 가능 계층과 로그 사용량이
스토리지 제한을 초과하면 kubelet이 해당 파드를 축출 대상으로 표시한다.

파드 수준 격리에서는 kubelet이 파드 안 컨테이너들의 제한을 합산해
전체 파드 스토리지 제한을 계산한다. 이 경우 모든 컨테이너의 로컬 임시 스토리지
사용량과 파드의 `emptyDir` 볼륨 사용량 합계가 전체 파드 스토리지 제한을
초과하면 kubelet도 해당 파드를 축출 대상으로 표시한다.

{{< caution >}}
kubelet이 로컬 임시 스토리지를 측정하지 않는다면,
로컬 스토리지 제한을 초과한 파드는 로컬 스토리지 리소스 제한 위반만으로는
축출되지 않는다.

하지만 컨테이너의 쓰기 가능 계층, 노드-레벨 로그, 또는 `emptyDir` 볼륨이 사용하는
파일시스템 공간이 부족해지면, 노드는 로컬 스토리지가 부족하다는
{{< glossary_tooltip text="테인트" term_id="taint" >}}를 설정하고,
이 테인트는 이를 명시적으로 허용하지 않는 파드의 축출을 유발한다.

임시 로컬 스토리지에 대해서는 지원되는 [구성](#configurations)을 참고한다.
{{< /caution >}}

kubelet은 파드 스토리지 사용량을 측정하는 여러 방식을 지원한다.

{{< tabs name="resource-emphemeralstorage-measurement" >}}
{{% tab name="주기적 스캔" %}}
kubelet은 각 `emptyDir` 볼륨, 컨테이너 로그 디렉터리, 컨테이너 쓰기 가능 계층을
스캔하는 정기 점검을 수행한다.

이 스캔은 사용 중인 공간의 양을 측정한다.

{{< note >}}
이 모드에서 kubelet은 삭제된 파일의 열린 파일 디스크립터를 추적하지 않는다.

사용자 또는 컨테이너가 `emptyDir` 볼륨 안에 파일을 만들고,
그 파일을 다른 무언가가 연 다음 파일이 열린 상태에서 삭제하면,
닫힐 때까지 삭제된 파일의 inode는 남아 있지만 kubelet은 그 공간을 사용 중으로 분류하지 않는다.
{{< /note >}}
{{% /tab %}}

{{% tab name="파일시스템 프로젝트 쿼터" %}}
{{< feature-state feature_gate_name="LocalStorageCapacityIsolationFSQuotaMonitoring" >}}

프로젝트 쿼터는 파일시스템의 스토리지 사용량을 관리하는 운영체제 수준 기능이다.
쿠버네티스에서는 프로젝트 쿼터를 활성화해 스토리지 사용량을 모니터링할 수 있다.
노드의 `emptyDir` 볼륨을 뒷받침하는 파일시스템이 프로젝트 쿼터를 지원하는지 확인한다.
예를 들어 XFS와 ext4fs는 프로젝트 쿼터를 지원한다.

{{< note >}}
프로젝트 쿼터는 스토리지 사용량을 모니터링할 수 있게 하지만,
제한을 강제하지는 않는다.
{{< /note >}}

쿠버네티스는 `1048576` 부터 시작하는 프로젝트 ID를 사용한다.
사용 중인 ID는 `/etc/projects` 와 `/etc/projid` 에 등록된다.
이 범위의 프로젝트 ID가 시스템의 다른 용도로 사용된다면,
쿠버네티스가 이를 사용하지 않도록 해당 ID도 `/etc/projects` 와
`/etc/projid` 에 등록해야 한다.

쿼터는 디렉터리 스캔보다 더 빠르고 정확하다.
디렉터리에 프로젝트가 할당되면, 그 디렉터리 아래에 생성되는 모든 파일도
그 프로젝트 안에 생성되며, 커널은 그 프로젝트에 속한 파일이
사용 중인 블록 수만 추적하면 된다. 파일이 생성되었다가 삭제되더라도
열린 파일 디스크립터가 남아 있으면 계속 공간을 소비한다.
쿼터 추적은 이 공간을 정확히 기록하지만, 디렉터리 스캔은 삭제된 파일이
사용하는 스토리지를 놓친다.

쿼터를 사용해 파드의 리소스 사용량을 추적하려면 파드가 사용자 네임스페이스에 있어야 한다.
사용자 네임스페이스 안에서는 커널이 파일시스템의 projectID 변경을 제한하므로,
쿼터로 계산한 스토리지 메트릭의 신뢰성을 보장한다.

프로젝트 쿼터를 사용하려면 다음을 수행해야 한다.

* [기능 게이트](/ko/docs/reference/command-line-tools-reference/feature-gates/) `LocalStorageCapacityIsolationFSQuotaMonitoring=true`
  를 활성화한다. 설정은 `featureGates` 필드를 사용하는
  [kubelet 구성](/ko/docs/tasks/administer-cluster/kubelet-config-file/)에서 한다.
* [기능 게이트](/ko/docs/reference/command-line-tools-reference/feature-gates/) `UserNamespacesSupport`
  가 활성화되어 있고, 커널, CRI 구현체, OCI 런타임이 사용자 네임스페이스를 지원하는지 확인한다.
* 루트 파일시스템(또는 선택적 런타임 파일시스템)에서 프로젝트 쿼터가 활성화되어 있는지 확인한다.
  모든 XFS 파일시스템은 프로젝트 쿼터를 지원한다. ext4 파일시스템의 경우 파일시스템이
  마운트되지 않은 상태에서 프로젝트 쿼터 추적 기능을 활성화해야 한다.

  ```bash
  # For ext4, with /dev/block-device not mounted
  sudo tune2fs -O project -Q prjquota /dev/block-device
  ```

* 루트 파일시스템(또는 선택적 런타임 파일시스템)이 프로젝트 쿼터를 활성화한 상태로
  마운트되어 있는지 확인한다. XFS와 ext4fs 모두 마운트 옵션 이름은 `prjquota` 다.

프로젝트 쿼터를 사용하지 않으려면 다음을 수행한다.

* `featureGates` 필드를 사용하는
  [kubelet 구성](/ko/docs/tasks/administer-cluster/kubelet-config-file/)에서
  [기능 게이트](/ko/docs/reference/command-line-tools-reference/feature-gates/)
  `LocalStorageCapacityIsolationFSQuotaMonitoring` 을 비활성화한다.
{{% /tab %}}
{{< /tabs >}}

## {{% heading "whatsnext" %}}

* XFS의 [프로젝트 쿼터](https://www.linux.org/docs/man8/xfs_quota.html)에 대해 읽어본다.
