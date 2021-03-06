# DevOps 개요 및 DevOps 엔지니어의 역할

## DevOps
- 개발(Dev)과 운영(Ops)의 합성어
- 분리되어 있는 개발조직과 운영조직을 하나의 팀으로 통합하고자 하는 문화이자 패러다임
- DevOps의 목표는 '개발과 운영의 벽을 허물어 더 빨리 자주 배포하자'
- 2009년 O'Relly Velocity 컨퍼런스에서 <하루에 10회 이상 배포하기: Flickr에서 Dev와 Ops의 협업> 발표에서 개발팀과 운영팀의 불협화음에 대해 소개하면서 DevOps라는 단어가 만들어지는데 영향을 줌
	- 당시에는 클라우드 서비스가 많이 도입되지 않았기 때문에 대부분의 회사에서는 직접 서버를 구축하고 직접 배포하는 방식이었음
	- 서비스 개발팀과 프로덕션으로 배포하는 운영팀이 분리가 되어있었음
	- 개발팀은 운영팀에게 개발결과물을 줘야하고, 운영팀에서는 해당 결과물에 대해 이해를 했어야 하는데, 이 과정에서 커뮤니케이션 오버헤드가 상당했음
- 일반적인 개발 라이프사이클: design -> develop -> test -> deploy -> operate -> support
	- 각 프로세스별로 전문가로 구성된 기능 조직을 운영할 수 있지만, 그럴 경우 의사소통의 필요성 및 중요성이 높아지기 때문에 의사소통 문제가 발생할 가능성이 높아짐
- DevOps는 design, develop한 개발자가 test와 operate에 직접적으로 참여한다면 더 효율적이지 않을까 라는 질문에서 시작됨
- 넷플릭스는 개발자가 소프트웨어의 모든 단계에 참여한다는 Full-Cycle Developer라는 모델을 제시함 
- AWS에서 제공하는 DevOps 가이드: 
	- 지속적 통합 (Continuous Integration, CI) : 개발한 변경사항에 대해 빌드 및 테스트 진행 & 코드저장소에 merge
	- 지속적 배포 (Continuous Delivery, CD) : 개발 결과물을 dev 환경이나 prod 환경에 배포
	- 마이크로서비스 (Micro-services) : 어느 정도 규모가 있는 서비스를 쪼개서 여러 마이크로 서비스로 쪼개서 관리
	- IaC (Infastructure as Code) : 인프라를 코드로 관리. 서비스 운영에 필요한 인프라 변경사항을 적용하는데 있어 자동화를 적용하기 위함
	- 모니터링과 로깅 (Monitoring & Logging) : 제품의 metric과 로그 데이터를 제공한다면 개발자들이 운영에 참여하기 용이해짐
	- 소통 및 협업 (Communication & Collaboration) : 조직 내 소통 및 협업 개선으로도 DevOps 문화를 정착시키는데 큰 도움이 됨. EX) 슬랙, 지라
     
     
## DevOps 엔지니어의 역할
### DevOps vs DevOps 엔지니어
- 데브옵스 엔지니어는 조직에 데브옵스 문화를 정착시키는데 도움을 주는 역할
- 개발자가 개발 뿐 아니라 운영에도 참여할 수 있는 환경을 구축하는 것 ( != 개발자가 환경을 직접 구축하는것)
- 예를 들면, 테스트를 위한 환경, 배포를 위한 환경, 모니터링을 위한 환경을 구축하는 것을 개발자가 직접하지 않고 데브옵스 개발자가 구축해주는 것
- 데브옵스 팀은 개발자의 생산성을 높이기 위한 조직이라고 생각하면 됨

### 데브옵스 팀의 업무 도메인
- 조직의 규모나 구성에 따라서 달라질 수 있음
- 데브옵스 팀의 업무를 행위 기반으로도 분류할 수 있는데, 시스템을 구축/설정/운영하고, 기본적인 사용법에 대해서 교육하는 것에 대한 책임 있고, 문서화도 당연히 담당하게 됨
     
- **네트워크 (Network)**
	- 가상 네트워크 및 물리 네트워크 구성
	- 프록시 / VPN 서버 운영
	- DNS 서버 운영
     
- **개발 및 배포 플랫폼 (Development & Deployment Platform)**
	- Gitlab/Github과 같은 VCS 플랫폼 운영
	- Ci/CD 파이프라인 시스템 구축 및 운영
	- QA 테스트 및 성능 테스트를 위한 환경 제공
	- 패키지 저장소 운영 및 배포 산출물 관리
     
- **오케스트레이션 플랫폼 (Orchestration Platform)**
	- k8s / ECS / Nomad 같은 오케스트레이션 시스템 구축 및 운영
	- Airflow / Argo Workflows 같은 워크플로우 엔진 구축 및 운영
    
- **관측 플랫폼 (Ovservability Platform)**
	- 로그 / 메트릭 / 업타임 / APM 정보를 관측할 수 있는 중앙화된 시스템 구축 및 운영
	- 주요 이벤트에 대한 알림 시스템 구축
     
- **클라우드 플랫폼 (Cloud Platform)**
	- 개발자들이 활용할 수 잇는 클라우드 환경운영 (AWS나 자체 클라우드 등)

- **보안 플랫폼 (Security Platform)**
	- LDAP / AD / SAML 등을 활용하여 통합된 임직원 계정계 운영(규모가 큰 조직일 경우 입/퇴사의 빈도가 높아 임직원 계정 관리가 필요)
	- 서버 및 데이터베이스 접근제어 시스템 구축 및 운영 (ssh 접근 시 개별사용자 인증 및 워딩 로그를 통해 누가 언제 어떤 일을 했는지 추적, 데이터베이스에서도 개별사용자에 대해 쿼리에 대한 권한 관리 및 감사로그 사용)
	- 네트워크 방화벽 정책 관리
      
- **데이터 플랫폼 (Data Platform)**
	- MySQL / DynamoDB / Redis와 같은 데이터베이스 구축 및 운영
	- RabbitMQ / Kafka / SQS 등과 같은 메시징 서비스 구축 및 운영
	- 데이터 웨어하우스 / BI 대시보드 구축 및 운영
     
- **서비스 운영 (Service Operations)**
	- 개발자들과 협업하여 서비스 공동 운영
      
### 데브옵스 팀의 핵심 지표 
- **장애복구 시간, MTTR (Mean Time To Recovery)**
	- 장애복구 시간이 오래걸린다면 인프라 자동화가 덜 되어있거나 배포 최적화가 필요함
- **변경으로 인한 결함률 (Change Failure Rate)**
	- 결함률이 높다면 CI단계에서 테스트 과정에 잘못된 부분이 있다는걸 나타냄
- **배포 빈도 (Deployment Frequency)**
	- 배포 빈도가 낮다면 조직내 문화적/시스템적 문제가 있을 수 있음
- **변경 적용 소요 시간 (Lead Time for Changes)**
	- 변경 적용 소요 시간이 길다면 배포 최적화를 시도해보고 서비스 자체가 큰게 문제라면 서비스를 쪼개서 마이크로 서비스 형태로 변경하는것을 고려해볼 수 있음
