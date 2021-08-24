# k8s monitoring with grafana/prometheus

## k8s 기본 구조
- Cluster
	- Namespace : 가상의 클러스터. 클러스터 안에서 노드 그룹핑. 하나의 클러스터 안에서 여러개의 namesapce 가질 수 있음
		- Node : `control plane`을 제외한 모든 node는 worker. 하나의 node는 kubelet을 가지는데, 이 kubelet이 API server로부터 지시를 받고 pod과 container 상태 확인 및 관리
			- Pod : 가장 작은 배포 단위. 1개 이상의 container를 가짐.
				- Container : docker container 등 
      
## 모니터링 방법
- Metrics Server
	- k8s node 중 `control plane`는 중앙화된 API를 가지고 클러스터 내부 서비스들을 관리.
		- 클러스터 상태에 대한 기록도 남기는데, `etcd key-value store`에 남김.
		- 각 node에 있는 `kubelet`에 request를 보내 해당 노드의 런타임 환경을 기록하도록 함
		- 이때 사용하는 API가 Metrics API이고, CPU 또는 memory 사용량 등의 정보를 메모리에 수집
		- https://github.com/kubernetes/metrics
- kube-state-metrics
	- Metrics Server가 pod과 node의 리소스 샤용량을 나타냈다면, kube-state-metrics는 kubernetes의 전반적인 데이터를 `control plane API server`를 통해서 받는다.
	- 사용하고 있는 마스터 노드에서 아래 github를 클론받아서 실행시켜 `/metrics` 엔드포인트를 활성화하고, 해당 엔드포인트를 `Prometheus`의 targets에 연결한다.
	- https://github.com/kubernetes/kube-state-metrics
      
## 모니터링 예시
- Node Status
	- `kube_node_status_condition` metric 활용
	-  ex. `sum by (node) (kube_node_status_condition{condition="Ready", status="true", node=~"*."})`
	-  condition은 `OutOfDisk | MemoryPressure | PIDPressure | DiskPressure | NetworkUnavailable` 중 하나
	-  status는 `true | false | unknown` 중 하나
	-  node 부분에서 `=~` 연산자는 `PromQL` 정규표현식 연산자
		-  https://prometheus.io/docs/prometheus/latest/querying/basics/

## Notes
- https://www.datadoghq.com/blog/monitoring-kubernetes-performance-metrics/
- https://www.circonus.com/2021/01/guide-to-monitoring-kubernetes-part-2-which-metrics-and-health-conditions-you-should-be-monitoring/
	