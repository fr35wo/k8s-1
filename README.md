# 쿠버네티스는 인프라를 만들지 않는다.

kubelets, API Server, Scheduler, 워커노드, 마스터 노드 등 인프라를 구성하는 것은 사용자가 선택한 클라우드 프로바이더(AWS EKS) 나 직접 구성(Kubermatic) 해야한다. 

포드나 컨테니어를 모니터링하는 관리, 스케일링, 포드에 들어오고 나가는 트래픽에만 관심을 가진다.

**What Kubernetes Will Do**

- **객체 생성 및 관리 (예: 파드)**
    - **사용자는 쿠버네티스 API를 통해 파드와 같은 객체를 정의하고 생성합니다. 이러한 객체는 애플리케이션의 컨테이너를 실행하는 기본 단위입니다.**
- **파드 모니터링 및 재생성, 스케일링 등 수행**
    - **쿠버네티스는 파드의 상태를 지속적으로 모니터링합니다. 만약 파드가 실패하거나 비정상적으로 동작할 경우, 자동으로 재생성합니다. 또한, 필요에 따라 파드의 수를 조정하여 애플리케이션의 부하에 맞게 스케일링할 수 있습니다.**
- **쿠버네티스는 제공된 (클라우드) 리소스를 활용하여 구성/목표를 적용**
    - **쿠버네티스는 클라우드 제공자가 제공하는 리소스를 활용하여 사용자가 설정한 구성과 목표를 달성합니다. 예를 들어, 로드 밸런서, 스토리지, 네트워킹 리소스 등을 자동으로 관리하여 애플리케이션의 가용성과 성능을 최적화합니다.**

**What You Need To Do / Setup (i.e. what Kubernetes requires)**

**1. 클러스터 및 노드 인스턴스 생성**

- **클러스터 생성: 클라우드 제공자(예: AWS, GCP, Azure)의 관리 콘솔이나 CLI를 사용하여 쿠버네티스 클러스터를 생성합니다. 이 과정에서 마스터 노드와 워커 노드를 정의합니다.**
- **노드 인스턴스: 마스터 노드와 워커 노드를 생성합니다. 마스터 노드는 클러스터의 제어 평면을 관리하고, 워커 노드는 실제 애플리케이션을 실행하는 역할을 합니다.**

**2. API 서버, kubelet 및 기타 Kubernetes 서비스 설정**

- **API 서버: 클러스터의 중앙 관리 컴포넌트로, 모든 요청을 처리합니다. API 서버를 설정하고, 인증 및 권한 부여를 구성합니다.**
- **kubelet: 각 노드에서 실행되는 에이전트로, 컨테이너의 상태를 관리하고 API 서버와 통신합니다. kubelet을 설치하고 구성합니다.**
- **기타 서비스:**
    - **Controller Manager: 클러스터의 상태를 관리하고, 필요한 리소스를 조정합니다.**
    - **Scheduler: 파드를 적절한 노드에 배치합니다.**
    - **etcd: 클러스터의 상태 정보를 저장하는 분산 키-값 저장소입니다.**

**3. 필요한 클라우드 제공자 리소스 생성**

- **로드 밸런서: 외부 트래픽을 클러스터의 서비스로 분산시키기 위해 로드 밸런서를 설정합니다. 클라우드 제공자의 로드 밸런서 서비스를 사용하여 생성합니다.**
- **파일 시스템: 영구 스토리지를 위해 클라우드 제공자의 파일 시스템(예: AWS EBS, GCP Persistent Disk 등)을 생성하고, 이를 쿠버네티스에서 사용할 수 있도록 설정합니다.**

# k8s 로컬 설치

![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/621504a8-73f4-4a14-962c-b3160a44e406/530bbb86-2fdf-40c2-9f0c-e96507e38968/image.png)

kubectl: 해당 인프라(클러스터)와 통신하고 쿠버네티스 설정과 통신하기 위한 통신장치.

이걸 사용하여 직접 마스터 노드에 명령을 보낼 수 있다. 실제 명령 내리는데 사용되는 도구이다.

Chocolatey:  ****Windows 운영 체제에서 사용되는 패키지 관리자. 주로 소프트웨어 설치 및 관리를 자동화하는 데 사용되며, 다양한 써드파티 소프트웨어 패키지를 쉽게 설치하고 업데이트할 수 있도록 도와준다. 파워쉘(관리자권한)에서 설치

https://chocolatey.org/install

kubectl 설치 (관리자권한)

https://kubernetes.io/docs/tasks/tools/install-kubectl-windows/#install-nonstandard-package-tools

minikube: 로컬 개발 환경에서 Kubernetes 클러스터를 쉽게 설정하고 관리할 수 있도록 도와주는 도구. 클러스터를 운영하는 데 필요한 기본적인 리소스들을 제공

kubectl은 minikube에 의해 자동으로 minikube에 연결되도록 구성되어있다.

```java
choco install minikube

minikube start --driver=hyperv// 드라이버 값은 사용하고자 하는 가상화 드라이버가 된다.
//Docker Desktop이 실행되고 있어야함.
```

로컬 개발 클러스터를 만들지만, 실제로는 워커와 마스터 노드가 리소스를 공유하는 하나의 노드만 설정한다. 

window에 hyper기능 지원이 안된다면 드라이버를 virtualbox로 사용해야함.

![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/621504a8-73f4-4a14-962c-b3160a44e406/3ca578fc-21b7-45ea-83d7-c6820de1ce7f/image.png)

minikube status 로 상태확인

minikube dashboard 로 클러스터를 살펴볼 수 있는 탭 열기

# Kubernets 객체

![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/621504a8-73f4-4a14-962c-b3160a44e406/6b7fdfa3-7013-4692-b3f6-891bc4e38fe8/image.png)

객체는 명령적, 선언적 접근 방식이 있다.

**Pod Object**

![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/621504a8-73f4-4a14-962c-b3160a44e406/dcfe5444-f013-4c12-aa10-7640801871fe/image.png)

보통 하나의 컨테이너를 가진다.

볼륨 같은 공유 리소스 갖고있다.

클러스터 내부 IP 주소가 있다.

**실제로 Pod를 생성하는 deployment 객체를 만드는 이유: 쿠버네티스가 배포도 관리하기 때문**

**Deployment Object**

![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/621504a8-73f4-4a14-962c-b3160a44e406/bc9a28f9-af15-4edd-b758-433e936b4f88/image.png)

여러개의 Pod를 제어한다.

- **원하는 상태 관리:**
    - **애플리케이션의 원하는 상태(예: Pod의 수, 사용할 컨테이너 이미지)를 정의합니다.**
    - **Kubernetes는 실제 상태가 원하는 상태와 일치하도록 지속적으로 작업합니다.**
- **Pod 및 컨테이너 사양:**
    - **실행할 Pod와 컨테이너를 정의하며, 이들의 구성, 리소스 요청 및 제한을 설정할 수 있습니다.**
    - **각 Pod의 인스턴스 수(복제본)를 지정하여 고가용성과 로드 밸런싱을 보장할 수 있습니다.**
- **배포 관리:**
    - **배포는 일시 중지할 수 있어, 실행 중인 애플리케이션에 영향을 주지 않고 수동으로 업데이트나 변경을 할 수 있습니다.**
    - **더 이상 필요하지 않은 경우 배포를 삭제할 수 있습니다.**
    - **업데이트로 문제가 발생할 경우, 이전 상태로 롤백할 수 있어 빠른 복구가 가능합니다.**
- **상태 변경:**
    - **Kubernetes는 애플리케이션의 실제 상태를 자동으로 변경하여 원하는 상태와 일치하도록 하며, 업데이트 및 스케일링을 처리합니다.**
- **스케일링:**
    - **원하는 Pod의 수를 언제든지 변경할 수 있어 수동 스케일링이 가능합니다.**
    - **또한, Horizontal Pod Autoscaler를 사용하여 리소스 사용량이나 다른 메트릭에 따라 배포를 동적으로 및 자동으로 스케일링할 수 있습니다.**

이를 쿠버네티스 클러스터에 전송하여, 쿠버네티스가 이를 수행하도록 한다.

# 명령적 접근 방식 사용

이미지 생성 docker build -t kub-first-app .

아 관리자권한으로 설치해서 vscode도 관리자로 열어야 되는듯 ;;

minikube status 해서 실행중인지 확인

minikube start —driver=hyperv로 실행

deployment 객체는 자동으로 k8s 클러스터로 전송 

—image 옵션은 이 deployment에서 생성된 pod의 컨테이너에 사용할 이미지 지정할 때 사용.

```java
 kubectl create deployment first-app --image=kub-first-app
```

minikube 클러스터의 deployment 표시

```java
kubectl get deployments
```

![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/621504a8-73f4-4a14-962c-b3160a44e406/c0b9b161-c8e1-4c02-bc90-ed8f3f82cd08/image.png)

deployment에서 생성된 것을 볼 수 있다.

```java
kubectl get pods
```

![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/621504a8-73f4-4a14-962c-b3160a44e406/ae5d1dde-32af-436e-87d4-16e774f8bc32/image.png)

이미지 pull 오류인데 kub-first-app 이미지는 로컬 머신엔 존재하지만 쿠버네티스 클러스터는 찾을 수 없다. 쿠버네티스 클러스터는 로컬의 가상머신 에서 작동하고 있기 때문.

deployment 삭제

```java
kubectl delete deployment first-app
```

도커 허브에 리포지토리 생성

도커 허브에 이미지 푸쉬

docker tag kub-first-app seokhyeonhong/kub-first-app

docker push seokhyeonhong/kub-first-app

도커 허브의 이미지는 클러스터 또한 접근이 가능하다.

 kubectl create deployment first-app --image=seokhyeonhong/kub-first-app

minikube dashboard로 확인가능하다.

### kubectl create deployment first-app --image=seokhyeonhong/kub-first-app 명령으로 일어나는 일

deployment 객체 생성한 다음 자동으로 쿠버네티스 클러스터에 있는 마스터 노드, 즉 컨트롤 플레인으로 전송한다. 이 마스터 노드는 클러스터에 필요한 모든 것을 생성한다. ex) 워커 노드에 pod 배포

마스터 노드의 스케줄러가 실제로 실행중인 pod 분석하여 새로 생성한 pod에 가장 적합한 node 찾는다. 새로 생성된 pod는 워커 노드의 하나로 보내진다.

워커 노드에서 kubelet 서비스를 얻게 된다. 워커 노드에서 pod 관리, 컨테이너 시작, pod 모니터링

- **Deployment 객체 생성: `kubectl create deployment` 명령을 실행하면, 쿠버네티스 API 서버에 Deployment 객체가 생성됩니다. 이 객체는 애플리케이션의 원하는 상태(예: 몇 개의 복제본이 필요한지, 어떤 이미지를 사용할지 등)를 정의합니다.**
- **마스터 노드(컨트롤 플레인): Deployment 객체가 생성되면, 이 정보는 쿠버네티스 클러스터의 마스터 노드(컨트롤 플레인)로 전송됩니다. 마스터 노드는 클러스터의 상태를 관리하고, 필요한 리소스를 생성 및 조정하는 역할을 합니다.**
- **스케줄링: 마스터 노드의 스케줄러는 클러스터 내의 워커 노드들을 분석하여 새로 생성된 Pod를 배치할 가장 적합한 노드를 찾습니다. 이 과정에서 각 노드의 리소스 사용량, 노드의 태그, 제약 조건 등을 고려합니다.**
- **Pod 배포: 스케줄러가 선택한 워커 노드에 Pod가 생성됩니다. 이 Pod는 Deployment에 정의된 대로 설정됩니다.**
- **Kubelet 서비스: 워커 노드에서는 kubelet이 실행되고 있습니다. kubelet은 해당 노드에서 Pod를 관리하고, 컨테이너를 시작하며, Pod의 상태를 모니터링합니다. kubelet은 마스터 노드와 지속적으로 통신하여 Pod의 상태를 보고하고, 필요한 경우 상태를 조정합니다.**

# [The “Service” Object](https://www.notion.so/10a97d6ce88f8032beddc218539bff2d?pvs=21)

![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/621504a8-73f4-4a14-962c-b3160a44e406/b3c3181d-fb9e-4b45-8a53-21596fb729c9/image.png)

pod와 pod에서 실행되는 컨테이너에 접근하려면 service가 필요하다.

클러스터의 다른 pod에 pod를 노출한다. 외부에서도 pod에 접근하게 해줌.

pod엔 이미 내부 ip 주소가 있다. 

내부 ip 주소에는 문제점이 있다.

- 클러스터 외부에서 pod에 액세스 하는데 사용할 수 없다.
- pod 교체마다 변경됨

IP가 항상 바뀌기 때문에 ip로 pod를 찾는것은 어렵다

service는 pod를 그룹화하고, 공유 주소, 공유 ip 주소를 제공한다.

이 주소는 변경되지 않는다. 

service 를 사용하여 여러 pod를 해당 service로 이동하고 그 service에서 변경할 수 없는 ip주소에 연결할 수 있게 해준다.

클러스터 내부만이 아닌, 외부에서도 변경되지 않는 이 주소를 노출하도록 service에 지시가 가능하다. 그로 인해 클러스터 외부에서도 pod에 액세스가 가능하다.

디폴트는 내부 전용이지만 service로 덮어쓰기 가능하다. 따라서 service가 없다면 외부에서 접근할 수 없다.

# Service 로 Deployment 노출하기

service를 생성하여 deployment에 의해 생성된 pod를 노출한다.

```java
kubectl expose deployment first-app --type=LoadBalancer --port=8080
```

컨테이너화 된 앱이 수신하는 포트 설정 

유형 설정 - ClusterIP: 클러스터 내부에서 접근 가능

유형 설정 - Nodeport: 이 deployment가 실행 중인 워커 노드의 IP주소를 통해 노출됨. 외부 접근 가능

유형 설정 - LoadBalancer: 클러스터가 실행되는 인프라에 존재해야 하는 LoadBalancer 활용. 실제로 이 service에 대한 고유한 주소 생성. 들어오는 트래픽을 이 service의 일부인 모든 pod에 고르게 분산

단, 클러스터와 클러스터가 실행되는 인프라가 지원하는 경우에만 사용 가능하다.

생성한 service 보기

```java
kubectl get services
```

![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/621504a8-73f4-4a14-962c-b3160a44e406/e51ad69c-9ac1-479f-9576-d1bdd725c7f9/image.png)

클라우드 프로바이더에 배포되면 External-IP 에 실제 IP가 생성되어 보인다.

 Minikube 환경에서 `first-app`이라는 이름의 서비스를 클러스터 외부에서 접근할 수 있도록 하는 명령. 실제 배포시엔 실제 ip로 접근하면 된다.

```java
minikube service first-app
```

이 애플리케이션은 우리가 생성하여 쿠버네티스 클러스터로 보낸 deployment를 기반으로 쿠버네티스에 의해 생성한 pod에서 실행된다.

![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/621504a8-73f4-4a14-962c-b3160a44e406/be953e54-bdcd-4c3d-904f-4a888b17924c/image.png)

# 컨테이너 재시작

/error api로 요청을 보내보자.

무한루프를 방지하기 위해 쿠버네티스는 재시작할 때 점점 더 오래대기한다.

Pod와 Pod의 컨테이너가 모니터링 되는 중이며 실패하면 재시작됨을 알 수 있다.

![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/621504a8-73f4-4a14-962c-b3160a44e406/158818b5-733d-4518-8662-09439b993bb6/image.png)

# 스케일링

복제본을 늘릴 수 있다. 리소스종류(deployment 등)와 리소스이름(deployment의 이름) 지정. —replicas 옵션으로 복제본 수 지정

옵션 숫자를 줄여 복제본 수를 줄일수도 있다.

```java
kubectl scale deployment/first-app --replicas=3
```

요청이 고르게 들어간다. /error 반복 실행 결과

![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/621504a8-73f4-4a14-962c-b3160a44e406/93d39441-4adc-4609-8131-8ab6410c8112/image.png)

# deployment 업데이트

새 이미지 사용하도록 deployment를 업데이트 한다. 먼저, 코드를 변경하고 이미지 재빌드.

그 후 도커에 푸쉬

 리소스종류(deployment 등)와 리소스이름(deployment의 이름) 지정

쿠버네티스 대시보드에서 현재 컨테이너 이름 지정(이전에 사용한 이미지 이름). 새 이미지이름 지정

```java
kubectl set image deployment/first-app kub-first-app=seokhyeonhong/kub-first-app
```

이래도 바뀌는게 없다. 새 이미지에 다른 태그가 있는 경우에만 다운로드 되기 떄문이다.

프로덕션에서는 태그와 이미지 버전을 업데이트 하는것이 좋겠다.

재빌드.

```java
 docker build -t seokhyeonhong/kub-first-app:2 .
```

재푸쉬

```java
docker push seokhyeonhong/kub-first-app:2
```

쿠버네티스가 이전과 사용된것과 다른 태그임을 감지 → 이미지 재다운, 컨테이너 재시작

![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/621504a8-73f4-4a14-962c-b3160a44e406/85f2e978-2892-4a72-9d96-7d1b3c590356/image.png)

대시보드에서도 확인가능

![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/621504a8-73f4-4a14-962c-b3160a44e406/1617fe05-dad4-4227-ac3f-e6a5dece0027/image.png)

# deployment 롤백

없는 태그로 deployment 업데이트 해보자.

 

롤아웃 진행 상황

```java
kubectl rollout status deployment/first-app
```

![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/621504a8-73f4-4a14-962c-b3160a44e406/dddc2739-6b00-4723-bb2e-4e552c49e8df/image.png)

새 Pod 가 시작되지 않아 오래된게 남아있다. 

![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/621504a8-73f4-4a14-962c-b3160a44e406/97695239-fe6f-4e21-ba50-144bc6ba277a/image.png)

롤백명령어

```java
kubectl rollout undo deployment/first-app
```

더 이전의 deployment로 돌아가려면 

```java
kubectl rollout history deployment/first-app
```

에서 revision 값을 얻어 

```java
kubectl rollout history deployment/first-app --revision=1
```

이전의 deployment로 돌아갈 수 있다.

```java
kubectl rollout undo deployment/first-app --to-revision=1
```

# 선언적 접근 방식

명령어를 길게 치지 말고 파일에 저장할 수 있다면 좋겠다. EX) docker-compose

![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/621504a8-73f4-4a14-962c-b3160a44e406/af6efcf3-24ec-4eb3-b423-13ad6da9d96b/image.png)

| **명령형 (Imperative)** | **선언형 (Declarative)** |
| --- | --- |
| **`kubectl create deployment …`** | **`kubectl apply –f config.yaml`** |
| **개별 명령이 실행되어 특정 Kubernetes 작업을 트리거합니다.** | **구성 파일이 정의되고 적용되어 원하는 상태를 변경합니다.** |
| **오직 `docker run`을 사용하는 것과 유사합니다.** | **Docker Compose와 구성 파일을 사용하는 것과 유사합니다.** |

# 배포 파일 구성하기

deployment.yaml 파일 생성

```yaml
apiVersion: apps/v1 # 사용자가 어떤 버전의 API를 사용하고 있는지
kind: Deployment # 생성하고자 하는 객체 지정(파일 이름으로 구분하는게 아니다)
metadata:  # 생성하는 객체의 이름 같은 중요데이터 포함
  name: second-app-deployment
spec:  # deployment의 사양
  replicas: 1 # 복제본 수
  selector: 
    matchLabels:
      app: second-app # deployment와 일치시키려는 pod레이블의 키-값 쌍 추가 
      tier : backend
  template: # pod 정의 (--image로 이 deployment에 연결된 pod에서 사용되는 컨테이너에 사용할 이미지 정의) deployment의 template는 pod이므로 kind 정의 필요없음
    metadata: # 생성하는 객체의 이름 같은 중요데이터 포함
      labels: 
        app: second-app # 값:app 키:second-app
        tier : backend
    spec: # pod의 사양
      containers: 
        - name: second-node # 동일한 pod에 여러 컨테이너를 가질 수 있다. - 로 목록 표시
          image: seokhyeonhong/kub-first-app:2 # 사용이미지 지정
        # - name: ...
        #   image: ...
# 다른 컨테이너를 지닌 다른 pod를 원하면 다른 deployment가 필요하다.      
```

deployment는 쿠버네티스 세계에서 동적 객체 또는 동적 사물이다. deployment는 모든 pod를 지속적으로 감시하며 제어해야하는 pod가 있는지 본다. 

selector로 제어할 pod 선택. 두가지 종류가 있다. selector는 이 리소스에게 제어되거나, 연결되어야하는 다른 리소스 식별

matchLabel로 label 일치 또는 matchExpressions로 표현일치

**1. `app` 레이블**

- 애플리케이션 식별: `app` 레이블은 특정 애플리케이션을 식별하는 데 사용됩니다. 여러 리소스가 동일한 애플리케이션에 속할 때, 이 레이블을 통해 쉽게 그룹화할 수 있습니다.
- 리소스 관리: `app` 레이블을 사용하면 특정 애플리케이션에 관련된 모든 리소스를 쉽게 조회하고 관리할 수 있습니다. 예를 들어, `kubectl get pods -l app=second-app` 명령어를 사용하면 "second-app" 애플리케이션에 속하는 모든 Pod를 나열할 수 있습니다.
- 서비스 디스커버리: Kubernetes의 Service 리소스는 `app` 레이블을 사용하여 특정 애플리케이션의 Pod에 트래픽을 라우팅합니다. 이를 통해 클라이언트는 서비스 이름을 통해 애플리케이션에 접근할 수 있습니다.

**2. `tier` 레이블**

- 구성 요소 역할 정의: `tier` 레이블은 애플리케이션의 아키텍처에서 각 구성 요소의 역할을 정의합니다. 예를 들어, `tier: frontend`는 프론트엔드 구성 요소를, `tier: backend`는 백엔드 구성 요소를 나타냅니다. 이를 통해 애플리케이션의 구조를 명확히 할 수 있습니다.
- 리소스 분리 및 관리: `tier` 레이블을 사용하면 서로 다른 계층의 리소스를 쉽게 분리하고 관리할 수 있습니다. 예를 들어, 백엔드와 프론트엔드의 리소스를 별도로 모니터링하거나 업데이트할 수 있습니다.
- 정책 적용: 네트워크 정책이나 리소스 쿼터와 같은 Kubernetes의 다양한 정책을 적용할 때 `tier` 레이블을 활용할 수 있습니다. 예를 들어, 백엔드와 프론트엔드에 대해 서로 다른 네트워크 정책을 설정할 수 있습니다.

이 deployment를 어떻게 적용해야할까? 
클러스터가 이를 인식하여 deployment와 pod를 생성하고 컨테이너를 시작해야한다. 

-f로 파일 식별, 여러개도 가능

```java
kubectl apply -f=deployment.yaml
```

이 모든 구성이 [config](https://www.notion.so/k8s-10b97d6ce88f80acb532f4ac0d4ef3e5?pvs=21) 파일에 인코딩 된다.

# 선언적으로 [Service](https://www.notion.so/k8s-10b97d6ce88f80acb532f4ac0d4ef3e5?pvs=21) 만들기

service.yaml 파일 생성

```yaml
apiVersion: v1
kind: Service
metadata: 
  name: backend
spec:
  selector: # service의 일부가 되어야 하는 pod정의
    app: second-app # 이 레이블을 가진 모든 pod는 service가 제어한다. 이 pod가 service에 의해 노출된다. (expose 명령어 참고)
  ports:
    - protocol: 'TCP'
      port: 80 # 목표 포트에 노출하고자 하는 외부 세계 포트
      targetPort: 8080
    # - protocol: 'TCP'
    #   port: 443
    #   targetPort: 443
  type: LoadBalancer

```

service 생성

```yaml
kubectl apply -f service.yaml
```

서비스이름 추가하여 노출

```yaml
minikube service backend
```

# 리소스 업데이트 & 삭제

파일 변경하고 위의 apply 명령어 다시 입력하면 된다.

긴명령 다시 입력할 필요가 없다.

삭제는 위의 명령적 삭제 명령어로도 가능하다.

또한 해당 파일로 만들어진 리소스 모두를 삭제하는것도 가능하다. 여러개도 가능.

```yaml
kubectl delete -f=deployment.yaml
```

# 다중 vs 단일 Config 파일

master-deployment.yaml 생성

```yaml
apiVersion: v1
kind: Service
metadata: 
  name: backend
spec:
  selector: # service의 일부가 되어야 하는 pod정의
    app: second-app # 이 레이블을 가진 모든 pod는 service가 제어한다. 이 pod가 service에 의해 노출된다. (expose 명령어 참고)
  ports:
    - protocol: 'TCP'
      port: 80 # 목표 포트에 노출하고자 하는 외부 세계 포트
      targetPort: 8080
    # - protocol: 'TCP'
    #   port: 443
    #   targetPort: 443
  type: LoadBalancer
---
apiVersion: apps/v1 # 사용자가 어떤 버전의 API를 사용하고 있는지
kind: Deployment # 생성하고자 하는 객체 지정(파일 이름으로 구분하는게 아니다)
metadata:  # 생성하는 객체의 이름 같은 중요데이터 포함
  name: second-app-deployment
spec:  # deployment의 사양
  replicas: 1 # 복제본 수
  selector: 
    matchLabels:
      app: second-app # deployment와 일치시키려는 pod레이블의 키-값 쌍 추가 
      tier : backend
  template: # pod 정의 (--image로 이 deployment에 연결된 pod에서 사용되는 컨테이너에 사용할 이미지 정의) deployment의 template는 pod이므로 kind 정의 필요없음
    metadata: # 생성하는 객체의 이름 같은 중요데이터 포함
      labels: 
        app: second-app # 값:app 키:second-app
        tier : backend
    spec: # pod의 사양
      containers: 
        - name: second-node # 동일한 pod에 여러 컨테이너를 가질 수 있다. - 로 목록 표시
          image: seokhyeonhong/kub-first-app:2 # 사용이미지 지정
        # - name: ...
        #   image: ...
# 다른 컨테이너를 지닌 다른 pod를 원하면 다른 deployment가 필요하다.      
```

 - - - 으로 새 객체가 생성됨을 알린다.

service가 먼저 오는것이 좋다. 먼저 생성되어야 하기때문. 
애플리케이션에서 생성, 제거되는 모든 부분은 지속적으로 모니터링 되기 때문에 selector의 레이블과 일치하는 새로운 부분이 생기면 service에 추가된다. 따라서 반드시 먼저 올 필요는 없지만 권장사항이다.

```yaml
kubectl apply -f=master-deployment.yaml
```

# Label & Selector에 대한 추가 정보

matchExpressions: 더 많은 구성 옵션을 가진 항목을 선택하는 방법

```yaml
spec:  # deployment의 사양
  replicas: 1 # 복제본 수
  selector: 
    # matchLabels:
    #   app: second-app # deployment와 일치시키려는 pod레이블의 키-값 쌍 추가 
    #   tier : backend
    matchExpressions: 
      - {key: app, operator: In, values: [second-app, first-app]} # 일치하는 객체 찾기위해 모두 충족되어야 하는 표현식의 목록 가진다.
```

공식문서 참고.

kubectl delete deployments,services -l group=example : -l  deployments,services의 레이블별 객체 내리기 가능

```yaml
apiVersion: apps/v1 # 사용자가 어떤 버전의 API를 사용하고 있는지
kind: Deployment # 생성하고자 하는 객체 지정(파일 이름으로 구분하는게 아니다)
metadata:  # 생성하는 객체의 이름 같은 중요데이터 포함
  name: second-app-deployment
  labels:
    group: example
```

```yaml
apiVersion: v1
kind: Service
metadata: 
  name: backend
  labels:
    group: example
```

selector를 사용하면 name이 아닌 metadata에 정의된 레이블로만 선택가능하다.

# 활성 프로브(Liveness Probes)

쿠버네티스가 pod와 컨테이너가 정상인지 아닌지 여부를 확인할 수 있는 방법

deployment.yaml 참고

```yaml
    spec: # pod의 사양
      containers: 
        - name: second-node # 동일한 pod에 여러 컨테이너를 가질 수 있다. - 로 목록 표시
          image: seokhyeonhong/kub-first-app:2 # 사용이미지 지정
          livenessProbe:
            httpGet:
              path: /
              port: 8080
            periodSeconds: 10 # 작업 수행 빈도
            initialDelaySeconds: 5 # 쿠버네티스가 처음으로 상태 확일 할때까지 시간
```

# 더 많은 구성 설명

```yaml
    spec: # pod의 사양
      containers: 
        - name: second-node # 동일한 pod에 여러 컨테이너를 가질 수 있다. - 로 목록 표시
          image: seokhyeonhong/kub-first-app:2 # 사용이미지 지정
          livenessProbe:
            httpGet:
              path: /
              port: 8080
            periodSeconds: 10 # 작업 수행 빈도
            initialDelaySeconds: 5 # 쿠버네티스가 처음으로 상태 확일 할때까지 시간
```

image: seokhyeonhong/kub-first-app:latest 처럼 항상 [최근의 이미지를 가져오거나](https://www.notion.so/k8s-10b97d6ce88f80acb532f4ac0d4ef3e5?pvs=21)

imagePullPolicy: Always로 정의해도 항상 가져온다.

https://kubernetes.io/docs/concepts/overview/working-with-objects/common-labels/
