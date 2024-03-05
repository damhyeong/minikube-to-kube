# Kubernetes - LoadBalancer 배포하기

[원본 링크](https://minikube.sigs.k8s.io/docs/start/)

---

* 클라우드 환경에서는 주어지는 LoadBalancer를 통해 External-Ip가 따로 주어진다.
* 하지만, Minikube, Docker-Desktop을 통한 로컬 환경에서는 LoadBalancer 생성 시 자동으로 localhost ip로 할당한다.

### NodePort와 무슨 차이점이 있을까? - 튜토리얼 수준에서.

1. `NodePort`의 경우, 로컬 환경에서의 port 한 개, 클러스터 환경에서의 port를 직접 연결시키는 프로세스가 필요했다.
2. 이로 인해 `kubectl port-forward svc/hello-kubectl 7080:8080` 명령어를 입력 한 후 새로운 탭을 열어야 했다.
3.

|               |                                                                                                                                                                         |
|---------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `NodePort`    | 1. `NodePort`의 경우, 로컬 환경에서의 port 한 개, 클러스터 환경에서의 port를 직접 연결시키는 프로세스가 필요했다.<br/> 2. 이로 인해 `kubectl port-forward svc/hello-kubectl 7080:8080` 명령어를 입력 한 후 새로운 탭을 열어야 했다. |
| `LoadBalancer` | 1. `LoadBalancer`의 경우, 로컬 환경 ip와 내부 클러스터 Port를 통해 서비스에 직접 요청을 전달 및 답신 받을 수 있다. <br/> 2. 따로 로컬 환경의 포트와 클러스터 포트를 연결시키는 명령어 프로세서를 켜 놓을 필요가 없다.                             |
|               |                                                                                                                                                                         |

<br>

`NodePort`와 `LoadBalancer` 타입의 사용성이 약간 다르지만, 튜토리얼에서는 거의 비슷한 서비스 양상을 보인다.

하지만, `NodePort`는 **EXTERNAL-IP** 를 사용하지 않으며, 명령어 프로세스를 통해 직접 연결시켰다.

```
<< NodePort 예시 >>
http://{로컬 도메인 Ip}:7080 ==> http://{CLUSTER-IP}:{CLUSTER-PORT} ==> pod

$로컬 도메인 Ip = localhost
```

그렇다면, `LoadBalancer` 는 이러한 양상이다. (조금 틀릴 수도 있는 표현)
```
<< LoadBalancer 예시 >>
http://{EXTERNAL-IP}:{CLUSTER-PORT} ==> 로드 밸런서 ==> pod
```

---

직접 명령어를 쓰며 알아보자.

```bash
kubectl create deployment balanced --image=kicbase/echo-server:1.0
```

`Pod`을 관리하기 위한 Deployment를 만들어 준다.

그 다음, `LoadBalancer`의 역할을 보기 위해 `Pod`을 4개로 늘리자.

```bash
kubectl scale deployment/balanced --replicas=4
```
pod을 보는 명령어를 입력하면 **balanced** pod이 4개가 된 것을 볼 수 있다.

```bash
kubectl get pods
```
**Result** : 

```
NAME                       READY   STATUS    RESTARTS   AGE
balanced-dc9897bb7-48f9g   1/1     Running   0          4s
balanced-dc9897bb7-ck4lk   1/1     Running   0          4s
balanced-dc9897bb7-tsvth   1/1     Running   0          26s
balanced-dc9897bb7-xpgmm   1/1     Running   0          4s
```

기존 1개였던 `Pod`을 `Deployment`를 통해 레플리카 팟 개수를 4개로 늘리라 명령했고,

`Deployment`는 `Replica Set`을 통해 3개의 `Pod`을 더 생성 한 것이다.

위를 보면 `AGE` 칸에 하나만 오래 되어 있는 것을 볼 수 있다. 

---

그리고, `LoadBalancer` 타입의 `Service`를 만들기 위해 명령어를 입력한다.
```bash
kubectl expose deployment balanced --type=LoadBalancer --port=8080
```

그리고, 밑의 명령어를 입력하면 EXTERNAL-IP 를 확인할 수 있다.
```bash
kubectl get svc
```
```
Result : 
NAME         TYPE           CLUSTER-IP      EXTERNAL-IP   PORT(S)          AGE
balanced     LoadBalancer   10.104.193.30   localhost     8080:32553/TCP   4s
kubernetes   ClusterIP      10.96.0.1       <none>        443/TCP          13d
```

---

## 요청 보내고 응답 받기

`LoadBalancer` 타입의 서비스에게 요청을 보내기 위해서는,

curl http://`{EXTERNAL-IP}`:`{PORTS[0]}` 이다. ::`[0]`은 첫 번째 포트를 의미

```bash
curl http://localhost:8080
```
or [8080 포트 결과 홈페이지](http://localhost:8080)

```text
[Curl] Result : 

Request served by balanced-dc9897bb7-xpgmm

HTTP/1.1 GET /

Host: localhost:8080
Accept: */*
User-Agent: curl/8.4.0

--------------

[Web page] Result : 

Request served by balanced-dc9897bb7-48f9g

HTTP/1.1 GET /

Host: localhost:8080
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7
Accept-Encoding: gzip, deflate, br, zstd
Accept-Language: ko-KR,ko;q=0.9,en-US;q=0.8,en;q=0.7
Connection: keep-alive
Sec-Ch-Ua: "Chromium";v="122", "Not(A:Brand";v="24", "Google Chrome";v="122"
Sec-Ch-Ua-Mobile: ?0
Sec-Ch-Ua-Platform: "macOS"
Sec-Fetch-Dest: document
Sec-Fetch-Mode: navigate
Sec-Fetch-Site: none
Sec-Fetch-User: ?1
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 .....
```
이런 식으로 로드 밸런싱 서비스를 `8080`포트에 연결해 요청을 보낼 수 있다.

## 그런데,

해당 로드 밸런싱은 **8080 포트 외 7080 포트로 생성 시 서비스 응답이 오지 않는다.**

이상하다.

한번 로드 밸런싱을 제거 한 후, 7080포트로 매핑 해 보겠다.

```bash
kubectl delete svc/balanced && kubectl expose deployment balanced --type=LoadBalancer --port=7080
```
그리고, `Service`를 다시 보겠다.
```bash
kubectl get svc
```
```text
Result : 

NAME         TYPE           CLUSTER-IP      EXTERNAL-IP   PORT(S)          AGE
balanced     LoadBalancer   10.97.248.164   localhost     7080:32064/TCP   5s
kubernetes   ClusterIP      10.96.0.1       <none>        443/TCP          13d
```

### 그렇다면 이제 localhost:7080으로 요청 보내면 되나?

```bash
curl http://localhost:7080
```

```text
Result : 

curl: (52) Empty reply from server
```

## 왜 안될까?

응답으로 미루어 보아 요청은 갔으나, 응답이 서버로부터 오지 않음을 볼 수 있다.

로드밸런싱으로 따로 `7080`포트를 사용하여 `Pod`에 직접 요청을 보낼 줄 알았으나, 그것이 아니였다.

확인하기 위해 따로 `logs` 명령어를 이용해 보자.

```bash
kubectl logs svc/balanced
```
```text
Found 4 pods, using pod/balanced-dc9897bb7-tsvth
Echo server listening on port 8080.
```
* `deployment`명령으로 4개로 증가한 `Pod`을 볼 수 있다.
* 해당 `Echo Server`는 `8080`포트에서 리스닝을 하고 있다.

**결과적으로, 7080 포트를 `LoadBalancer`로 선언한 것이 8080 포트로 매핑되지 않고 있는 것이 실수인 것이다!**

그렇다면, 기존의 로드 밸런싱을 담당하는 서비스를 제거 한 후, 다시 생성 해 준다.

```bash
`kubectl delete svc balanced && kubectl expose deployment balanced --type=LoadBalancer --port=7080 --target-port=8080
```

`--target-port`가 설정되지 않아, 선언 된 `--port`명령어의 포트를 그대로 이어 7080 포트가 된 것이다.

그러므로, 8080포트로 `Pod`에 제대로 요청이 가지 않고, 응답이 오지 않았던 것이다.

* `--port` : 외부에서 인식하는 포트
* `--target-port` : 내부에서 인식 될 포트

7080포트로 요청을 받되, 내부에서는 8080포트로 인식하게끔 만들어 `LoadBalacer`를 완성 할 수 있다.

그리고 애초에 [kicbase/echo-server:1.0](https://hub.docker.com/layers/kicbase/echo-server/1.0/images/sha256-42a89d9b22e5307cb88494990d5d929c401339f508c0a7e98a4d8ac52623fc5b?context=explore)에 들어가 보면,

Port가 8080으로 매핑되어 있는 것을 볼 수 있다.