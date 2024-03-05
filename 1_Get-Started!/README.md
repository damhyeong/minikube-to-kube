# Kubernetes 시작하기

---

`Docker Desktop`, `Minikube`와 같은 프로그램은 쿠버네티스를 로컬 머신에서 쉽게 배우고 개발할 수 있습니다.

### `필요한 것`

* **컨테이너 런타임** : `Docker`
* **로컬 머신** : `2 CPU`, `2GB Memory`, `20GB disk` 스펙 이상을 갖추기
* **인터넷 환경 필수** : 튜토리얼을 위한 `image` 가져와야 됩니다.
* **도커와 컨테이너 환경에 대한 최소한의 이해** : 없다면 고생길이 펼쳐집니다.
---

# Kubernetes 시작하기

`Docker Desktop` 프로그램 시작 시 자동으로 `Kubernetes`가 시작됩니다.

이후, 
```bash
kubectl get nodes
```
명령어를 통해 `docker-desktop`이 올라와 있는 것을 볼 수 있습니다.

**Result** :
```
NAME             STATUS   ROLES           AGE     VERSION
docker-desktop   Ready    control-plane   6d21h   v1.29.1
```

---

# Cluster와 상호작용 해보기

```bash
kubectl get po -A
```
**Result** : 
```
NAMESPACE              NAME                                         READY   STATUS    RESTARTS       AGE
kube-system            coredns-76f75df574-g9l7h                     1/1     Running   8 (17h ago)    7d18h
kube-system            coredns-76f75df574-qv7wk                     1/1     Running   8 (17h ago)    7d18h
kube-system            etcd-docker-desktop                          1/1     Running   9 (17h ago)    7d18h
kube-system            kube-apiserver-docker-desktop                1/1     Running   9 (59m ago)    7d18h
kube-system            kube-controller-manager-docker-desktop       1/1     Running   9 (17h ago)    7d18h
kube-system            kube-proxy-xzqhs                             1/1     Running   8 (17h ago)    7d18h
kube-system            kube-scheduler-docker-desktop                1/1     Running   9 (17h ago)    7d18h
kube-system            storage-provisioner                          1/1     Running   16 (59m ago)   7d18h
kube-system            vpnkit-controller                            1/1     Running   8 (17h ago)    7d18h
kubernetes-dashboard   dashboard-metrics-scraper-5657497c4c-nhvt9   1/1     Running   7 (17h ago)    6d23h
kubernetes-dashboard   kubernetes-dashboard-78f87ddfc-87kkj         1/1     Running   7 (17h ago)    6d23h
```

위의 결과는 `NAMESPACE`, `NAME`, `READY`, `STATUS`, `RESTARTS`, `AGE` 컬럼이 존재합니다.

**kubernetes-dashboard** 네임스페이스는 웹에서 대시보드를 열기 위해 이미 yaml 파일을 적용 한 결과입니다.

**Minikube** 프로그램은 "minikube dashboard" 명령어를 통해 쉽게 클러스터를 볼 수 있지만, Dashboard 기능은 기본적으로 외부 기능이기 때문에 여러 절차가 필요합니다.

곧바로 대시보드를 통해 pod, deployment, svc를 보고 싶다면,

[쿠버네티스 대시보드 적용하기](https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/)

위 링크의 `kubectl apply -f https://......` 적용 후 대시보드 유저를 따로 만들어야 합니다.

[Creating Sample User](https://github.com/kubernetes/dashboard/blob/master/docs/user/access-control/creating-sample-user.md) 를 참조하기 바랍니다.

Dashboard 적용에 대한 내용은 처음부터 배우긴 어려우니, 기본적인 개념과 적용에 대해서 배우도록 하겠습니다.

대시보드 적용과 테스팅 유저 생성을 위한 yaml파일 작성, 접속 링크의 이해에 대해 다루겠습니다.
