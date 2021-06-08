# ECS Port Mapping
    
## ECS Task Networking
ECS Task Definition에서 네트워크 모드는 4가지가 있다
     
1. awsvpc
	- Elastic Network Interface(ENI)와 private IPv4를 할당받음
	- port는 컨테이너 포트만 지정 가능

2. bridge
	- Docker의 기본 네트워킹 모드
	- Windows의 LInux Bridge 및 NAT Bridge와 같음

3. host
	- Docker의 기본 네트워키를 사용하면서 EC2 인스턴스의 ENI를 할당받음
	- port는 컨테이너 포트만 지정 가능

4. none
	- 네트워크를 사용하지 않음
      
## Port Mapping
위의 4가지 네트워크 모드 중 bridge를 사용할 경우 컨테이너 포트와 호스트 포트(필수는 아님)를 지정해줘야 한다
     
- Container Port
	- container(Docker container)에 할당된 포트번호
	- 지정하거나 자동할당됨 
      
- Host Port
	- container instance에 할당한 포트번호
	- awsvpc/host : 빈칸으로 두거나 Container Port와 같은 값으로 세팅됨
	- bridge : 빈칸으로 두거나 0으로 세팅할 경우 dynamic port mapping되어 Docker container 생성 시마다 포트는 자동으로 할당됨
		- dynamic port mapping 설정 후 elb를 사용할 경우 host port 때문에 health check에 실패해 인스턴스가 제대로 띄워지지 않을 수 있으므로 유의해야 됨