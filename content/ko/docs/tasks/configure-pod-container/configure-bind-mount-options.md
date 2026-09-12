---
title: 볼륨 마운트에 바인드 마운트 옵션 설정
content_type: task
weight: 216
min-kubernetes-server-version: v1.37
---

<!-- overview -->

{{< feature-state feature_gate_name="VolumeBindMountOptions" >}}

이 페이지에서는 보안 관련 바인드 마운트 옵션(`noexec`, `nodev`, `nosuid`)을
파드의 볼륨 마운트에 적용하는 방법을 설명한다.

## {{% heading "prerequisites" %}}

{{< include "task-tutorial-prereqs.md" >}} {{< version-check >}}

API 서버와 kubelet **모두**에서 `VolumeBindMountOptions`
[기능 게이트](/docs/reference/command-line-tools-reference/feature-gates/)를
활성화해야 한다. 컨테이너 런타임도 CRI `Mount` 메시지의
`mount_options` 필드를 지원해야 한다.

<!-- steps -->

## 바인드 마운트 옵션을 사용하는 파드 생성 {#create-pod}

`.spec.containers[*].volumeMounts[*].bindMountOptions` 필드에는 바인드 마운트 플래그 목록을 지정할 수 있다.
허용되는 값은 `noexec`, `nodev`, `nosuid`이다.

예를 들어, `emptyDir` 볼륨을 `noexec`와 `nosuid` 옵션으로 `/tmp`에 마운트하면
해당 볼륨에서는 바이너리를 실행할 수 없고 set-user-ID 비트가 무시된다.

{{% code_sample file="pods/bind-mount-options.yaml" %}}

1. 클러스터에 파드를 생성한다.

   ```shell
   kubectl apply -f https://k8s.io/examples/pods/bind-mount-options.yaml
   ```

1. 파드가 실행 중인지 확인한다.

   ```shell
   kubectl get pod bind-mount-options-demo
   ```

1. 볼륨의 마운트 옵션을 확인한다.

   ```shell
   kubectl exec bind-mount-options-demo -- mount | grep /tmp
   ```

   출력된 마운트 옵션에는 `noexec`와 `nosuid`가 포함되어 있어야 한다.

1. `/tmp`에 마운트한 볼륨에서 바이너리 실행이 실패하는지 확인한다.

   ```shell
   kubectl exec bind-mount-options-demo -- sh -c 'cp /bin/ls /tmp/ls && /tmp/ls'
   ```

   출력은 다음과 비슷하다.

   ```none
   sh: /tmp/ls: Permission denied
   ```

1. 이 연습을 위해 생성한 파드를 삭제한다.

   ```shell
   kubectl delete pod bind-mount-options-demo
   ```

## {{% heading "whatsnext" %}}

- 볼륨의 [바인드 마운트 옵션](https://kubernetes.io/docs/concepts/storage/volumes/#bind-mount-options)에 대해 더 알아본다.
