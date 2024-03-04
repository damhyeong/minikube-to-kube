# `쿠버네티스 명령 튜토리얼`

---

### 1. `minikube` 대신 `Docker Desktop` 을 이용합니다.

### 2. `minikube`의 편의성 기능을 직접 적용합니다.

> `minikube dashboard` 와 같은 기능을 직접 적용합니다.

### 3. `Control Plane` 노드는 `Docker Desktop`입니다.

### 4. `k8s`는 `kubernetes`의 또다른 의미입니다.

---

# `본인 환경`

<br>

1. Mac M1 ==> `arm64`
2. minikube ==> `Docker Desktop K8s`
3. container runtime ==> `Docker`

---

# `kubectl 다운로드 방법`

## [쿠버네티스 공식 다운로드 링크](https://kubernetes.io/releases/download/)

맥북일 경우 brew를 통한 다운로드를 추천합니다

### [Homebrew Package Manager](https://brew.sh/)

<br>

쿠버네티스 자체 local 환경은 `minikube` or `Docker Desktop`을 통해 마련할 수 있습니다.

---

# `k8s 실행 방법`

<br>

* `Docker Desktop` 대시보드에서 맨 위 오른쪽 환경설정 클릭
* `Kubernetes` 탭 클릭 후 `Enable Kubernetes` 클릭
* `Docker Container` 로고 외 `Kubernetes`로고가 보인다.
* `Kubernetes` 로고가 노란색에서 초록색이 되면 실행이 되고 있는 것.

# `k8s 실행 여부 확인`

```bash
kubectl cluster-info
```

Result :
```
Kubernetes control plane is running at https://kubernetes.docker.internal:6443
CoreDNS is running at https://kubernetes.docker.internal:6443/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy
```
이렇게 나오면 성공입니다.