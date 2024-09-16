### Deployment 생성
- Deployment의 ReplicaSet 이벤트 확인
```text
> kubectl describe deployment deployment my-app
Name:                   my-app
Namespace:              default
CreationTimestamp:      Mon, 16 Sep 2024 10:48:13 +0900
Labels:                 <none>
Annotations:            deployment.kubernetes.io/revision: 1
Selector:               app=my-app
Replicas:               2 desired | 2 updated | 2 total | 2 available | 0 unavailable
StrategyType:           RollingUpdate
MinReadySeconds:        0
RollingUpdateStrategy:  25% max unavailable, 25% max surge
Pod Template:
  Labels:  app=my-app
  Containers:
   my-app:
    Image:      yoonjeong/my-app:1.0
    Port:       8080/TCP
    Host Port:  0/TCP
    Limits:
      cpu:         50m
      memory:      64Mi
    Environment:   <none>
    Mounts:        <none>
  Volumes:         <none>
  Node-Selectors:  <none>
  Tolerations:     <none>
Conditions:
  Type           Status  Reason
  ----           ------  ------
  Available      True    MinimumReplicasAvailable
  Progressing    True    NewReplicaSetAvailable
OldReplicaSets:  <none>
NewReplicaSet:   my-app-794cd5f5bd (2/2 replicas created)
Events:
  Type    Reason             Age    From                   Message
  ----    ------             ----   ----                   -------
  Normal  ScalingReplicaSet  3m22s  deployment-controller  Scaled up replica set my-app-794cd5f5bd to 2
Error from server (NotFound): deployments.apps "deployment" not found

```
> 마지막 이벤트를 보면 아, deploycontroller 가 replicaset app-794cd5f5bd 79 로 시작하는 리플리카 셋을 2로 스케일 업 했구나 . <br/>
> kubectl get rs <br/>
NAME                         DESIRED   CURRENT   READY   AGE </br>
my-app-794cd5f5bd            2         2         2       6m17s </br>
nginx-deployment-f48cb95c4   0         0         0       14h </br>

> kubectl rollout status deployment/my-app <br/>
deployment "my-app" successfully rolled out <br/>


### 정리
- Deployment 를 통한 배포 과정
- pod Template 이 변경 될때 Deployment 의 롤아웃 기능이 트리거 된다.
- pod Template 이 변경되면 pod-template-hash 값이 변경된다.
- 변경된 pod-template-hash 값은 Replicaset 과 pod 에 모두 반영된다.