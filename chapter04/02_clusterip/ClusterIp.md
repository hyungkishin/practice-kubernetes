### ClusterIP 타입의 Service 를 생성하여 파드 집합을 단일 앤드포인트로 노출 할 수 있다.
- Service ClusterIP 생성방법
  - Service 오브젝트 선언
- ClusterIP 의 역할과 특징
> Service: order <- 서비스를 order 라 명칭 <br/>
> Type: ClusterIP <- 타입을 ClusterIP 라고 선언하게 되면 <br/> 
> Endpoints: order <- 서비스의 셀렉터를 이용해서 pod 들의 주소목록을 결정짓는 Endpoints Object 를 생성하게 된다. <br/>

### 클러스터 내부에서 Service IP 혹은 Service 이름으로 다른 Pod 와 통신할 수 있다.
- 환경변수 : Service IP 로 파드 집합에 접근하는 방법
- 쿠버네티스 DNS 서버: Service 이름으로 파드집합에 접근하는 방법
- MSA 환경에서 다른 네임스페이스에 있는 Service 를 도메인 이름으로 접근하는 방법