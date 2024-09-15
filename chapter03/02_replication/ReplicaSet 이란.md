### ReplicaSet
- N 개의 Pod 을 생성하기 위해 생성 명령을 N 번 실행할 필요가 없다.
- ReplicaSet 오브젝트를 정의하고 원하는 pod 의 개수를 replicas 속성으로 선언한다
- 클러스터 관리자 대신 Pod 수가 부족하거나, 넘치지 않게 Pod 수를 조정해준다

### 왜 필요한지에 대해서
- Pod 에 문제가 생겨서.
  - Pod 이 다 뒤졌을때. 클라이언트의 요청을 처리할 수 가 없다.
  - 누군가 클러스터의 상태를 계속 모니터링해야한다.
  - 다운타임이 발생한다.

### 소프트웨어가 내결함성을 가진다 - fault tolerance
- 소프트웨어나 하트웨어 실패가 발생하더라도 소프트웨어가 정상적인 기능을 수행할 수 있어야한다.
- 없으면 비즈니스적 요구사항을 달성하기 힘들고, 고객이 실망한다.

> 이를 보완하기위해서라도 ReplicaSet 을 사용한다.

### ReplicaSet 오브젝트의 표현방법
```yaml
apiVersion: apps/v1 # Kubernetes API 버전
kind: ReplicaSet # 오브젝트 타입
metadata: # 오브젝트를 유일하게 식별하기 위한 정보
  name: blue-app-rs # 오브젝트 이름
  labels:
    app: blue-app # 오브젝트 집합을 구할 때 사용할 이름표

spec: # 사용자가 원하는 Pod의 바람직한 상태
  selector: # ReplicaSet이 관리해야하는 Pod을 선택하기 위한 label query
  replicas: # 실행하고자 하는 Pod 복제본 개수 선언
  template: # Pod 실행 정보 - Pod Template과 동일 (metadata, spec, …)
```
### Pod 레플리케이션 설정 방법
- ReplicaSet 을 이용해서 Pod 복제본을 생성하고 관리한다
  - 여러 노드에 걸쳐 배포된 Pod Up/Down 상태를 감시하고 replicas 수만큼 실행을 보장한다.
- ReplicaSet 의 spec selector matchLabels 는 Pod Template 부분의 tspec.template.metadata.labels 와 같아야한다.
- spec.replicas 를 선언하지 않으면 기본값은 1이다.

### 실습
> kubectl get rs blue-replicaset -o wide <br/>
NAME              DESIRED   CURRENT   READY   AGE   CONTAINERS   IMAGES                   SELECTOR <br/>
blue-replicaset   3         3         3       18s   blue-app     yoonjeong/blue-app:1.0   app=blue-app <br/>

- rs: 조회할 리소스 타입을 지정. 여기서는 ReplicaSet을 의미한다
- blue-replicaset: 조회할 특정 ReplicaSet의 이름
- -o wide: 출력 형식을 지정한다. wide 옵션은 기본 출력에 더해 추가적인 정보를 포함한 넓은 형식으로 출력

> kubectl get pod -o wide <br/>
NAME                    READY   STATUS    RESTARTS   AGE    IP          NODE                                        NOMINATED NODE   READINESS GATES <br/>
blue-replicaset-pc5cb   1/1     Running   0          109s   10.4.2.28   gke-my-cluster-default-pool-34e6c418-cl3d   <none>           <none> <br/>
blue-replicaset-pg7r9   1/1     Running   0          109s   10.4.0.14   gke-my-cluster-default-pool-34e6c418-0m4r   <none>           <none> <br/>
blue-replicaset-tkmm2   1/1     Running   0          108s   10.4.2.27   gke-my-cluster-default-pool-34e6c418-cl3d   <none>           <none> <br/>

- NAME 을 잘 보면 blue-replicaset-pc5cb 

> kubectl describe rs blue-replicaset <- replicaset 에서 발생한 이벤트를 확인해보자 <br/>
> Events: <br/>
> Type    Reason            Age   From                   Message <br/>
>  ----    ------            ----  ----                   -------  <br/>
> Normal  SuccessfulCreate  4m4s  replicaset-controller  Created pod: blue-replicaset-pc5cb <br/>
  Normal  SuccessfulCreate  4m3s  replicaset-controller  Created pod: blue-replicaset-pg7r9 <br/>
  Normal  SuccessfulCreate  4m3s  replicaset-controller  Created pod: blue-replicaset-tkmm2 <br/>

> 3 개의 pod 을 replicaset-controller 가 생성했다는것을 확인했다. <br/>

### describe rs blue-replicaset 말고도 이벤트를 확인하는 방법이 있다.
- kubectl get events --sort-by=.metadata.creationTimestamp
