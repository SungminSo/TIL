# ALB vs NLB

## ALB (Application Load Balancer)
- layer: L7
- protocol: HTTP, HTTPS, HTTP/2
- 특징:
	- path-based (경로 기반) 라우팅 지원됨
		- 특정 path로 들어올 때 다른 서버로 라우팅 시켜주어야 한다면 유용
		- 마이크로서비스 구조에서 path별 각 마이크로서비스로 라우팅이 필요한 api gw는 alb를 사용하는게 유리
	- ip + port를 보는 NLB와 다르게 ip + port + packet content까지 보기 때문에 더 많은 기능 제공 가능 

## NLB (Networke Load Balancer)
- layer: L4
- protocol: TCP, TLS
- 특징: 
	- network 계층까지만 확인하므로 alb보다 빠름
		- 단순한 라우팅이 필요하고, 트래픽이 매우 많은 경우 유리
	- privateLink 및 영역 고정 IP 주소 지원
		- privateLink는 퍼블릭 인터넷에 트래픽을 노출시키지 않음
		- 고정 IP는 클라이언트의 네트워크 및 보안 구성을 단순화할 수 있음

## Notes
- https://haloworld.tistory.com/137
- https://no-easy-dev.tistory.com/entry/AWS-ALB%EC%99%80-NLB-%EC%B0%A8%EC%9D%B4%EC%A0%90
- https://pearlluck.tistory.com/114

## AWS ALB
- NLB에서 ALB로 직접 트래픽 전달 지원 (2021.09.27 게시)
	- NLB의 privateLink 및 영역 고정 IP 주소에 대한 지원은 ALB에서 사용 가능
	- 사용하려면 새 ALB 유형 대상 그룹 생성 및 ALB 등록 필요 
- notes: https://aws.amazon.com/ko/about-aws/whats-new/2021/09/application-load-balancer-aws-privatelink-static-ip-addresses-network-load-balancer/