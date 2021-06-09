# ECS
     
## Elastic Container Service
- Cluster에서 Docker Container를 쉽게 실행 및 관리할 수 있도록 하는 컨데이너 오케스트레이션 서비스
- 2가지 유형
	- Fargate : 관리하는 서버를 사용하지 않는 인프라에서 사용
	- EC2 : 관리하는 서버 인스턴스를 두고 사용
      
## ECS 사용 시 장점
- 간단한 API 호출 또는 GUI 환경에서 컨테이너 기반 애플리케이션을 관리 가능
- 중앙 집중식 서비스를 사용하여 클러스터 상태 확인 가능
- 같은 클러스터에 많은 서비스(ex. EC2)를 띄워 사용 가능
- 일관된 배포 및 구축 환경 생성
     
## 보통 ECS를 쓰는 경우
- 테스트(staging) 서버를 돌릴 때
- 충분히 작은 서비스를 돌릴 때
- 이미 Docker 이미지가 있을 때
     
## ECS 컴포넌트
- Task Definition(작업 정의)
	- Docker 컨테이너를 위해 정의한 set
	- 작업의 IAM, 네트워크, CPU/MEM 크기와 Docker 컨테이너의 옵션값들 정의 가능
	- 하나 이상의 Docker 컨테이너에 대해 정의 가능하고, 같은 Task Definition 내부에 정의된 컨테이너는 link 설정으로 연결 가능
     
- Task
	- Task Definition에 정의된대로 배포된 Constainer Set
	- Cluster에 속한 컨테이너 인스턴스에 배포
	- 각 Task는 메모리, CPU, 네트워크 인터페이스를 다른 Task와 공유하지 않음
     
- Service
	- Task들의 LIfe-cycle을 관리하는 부분
	- Task를 ELB와 연동할 때 사용
	- 실행 중인 Task가 작동이 중지될떄 이를 감지해 새로운 Task를 배포하는 정책 등 관리
     
- Container Instance
	- ECS에서 배포되는 Task는 EC2 instance에 올라간다. 이때 생성되는 EC2 instance를 Container instance라고 함
     
- Cluster
	- Task가 배포되는 Container instance들은 논리적인 그룹으로 묶이는데 이를 Cluster라 함