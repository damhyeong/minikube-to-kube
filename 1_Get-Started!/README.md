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