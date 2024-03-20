# Dashboard 만들기

---

### 쿠버네티스의 대시보드란?

* 클러스터에서 생성되거나, 만들어진 모든 리소스를 웹 사이트 화면으로 볼 수 있게 해 준다.
* 예제로 만들었던, Pod, Service, Deployment, Ingress .. 등등에 대한 리소스를 UI로 보여준다.
* 해당 UI 웹사이트를 통해 리소스를 조작할 수 있다. - Pod, Service 등등..
* 쿠버네티스에서 공식적으로 배포하는 관리 UI 리소스를 적용 할 예정이다.
* Minikube는 개발 공부를 위해 사용되기에 대시보드가 기본 제공되지만, <br> 실제 쿠버네티스에서는 대시보드 기능이 제공되지 않는다. - 직접 적용해야 함.

---

[쿠버네티스 Dashboard 적용 공식 홈페이지](https://kubernetes.io/ko/docs/tasks/access-application-cluster/web-ui-dashboard/)
 
`공식 홈페이지를 통해 Dashboard를 적용하기 쉽지 않기에, 밑에 쉬운 적용법을 적는다.`

Minikube는 명령어 하나로 모든 리소스를 실시간으로 볼 수 있는 대시보드가 열린다. 하지만, 

실제 쿠버네티스의 경우, 미니큐브와 동일한 대시보드를 가져오기 위해 오픈소스에서 가져와야 하며,

대시보드 오픈소스가 적용 된 namespace에 ServiceAccount를 적용 시켜야 한다.

그 후, 적용 된 유저의 토큰을 발급하고 해당 토큰을 디코딩하여 터미널에 불러와야 한다.

디코딩 된 토큰은 대시보드에 접근하기 위한 비밀번호로 이용된다.

---

## `대시보드 적용하는 법`

* 쿠버네티스에서 배포하는 공식 오픈소스를 적용한다.

```bash
kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.6.0/aio/deploy/recommended.yaml
```
**Result :**
```text
namespace/kubernetes-dashboard created
serviceaccount/kubernetes-dashboard created
service/kubernetes-dashboard created
secret/kubernetes-dashboard-certs created
secret/kubernetes-dashboard-csrf created
secret/kubernetes-dashboard-key-holder created
configmap/kubernetes-dashboard-settings created
role.rbac.authorization.k8s.io/kubernetes-dashboard created
clusterrole.rbac.authorization.k8s.io/kubernetes-dashboard unchanged
rolebinding.rbac.authorization.k8s.io/kubernetes-dashboard created
clusterrolebinding.rbac.authorization.k8s.io/kubernetes-dashboard unchanged
deployment.apps/kubernetes-dashboard created
service/dashboard-metrics-scraper created
deployment.apps/dashboard-metrics-scraper created
```

공식문서에서는 배포한 대시보드를 `proxy` 형식으로 본다.

```bash
kubectl proxy
```

**Result :**

```text
Starting to serve on 127.0.0.1:8001
```
쿠버네티스의 api를 `localhost:8001` 주소에 curl 형식으로 요청할 수 있다.

그리고, 웹 사이트에 밑의 URL을 입력해 보자

[http://localhost:8001/api/v1/namespaces/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/](http://localhost:8001/api/v1/namespaces/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/)



---

### 대시보드를 적용하기 위한 필요 지식

### 1. `ClusterRoleBinding`

### 2. `ServiceAccount`

### 3. `namespace`
