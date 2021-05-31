# k8s Pod 디버깅

k8s에서 daemonset 생성 및 수정 후 하위 pod이 잘 안뜨는 경우 해결방법

​     

## 인터넷 확인

가장 기본적인 확인사항     

호오오옥시나 인터넷이 안되어서(혹은 너무 느려서) daemonset의 변경사항이 곧바로 적용이 되지 못하는 경우가 있으니 가장 우선적으로 확인

​     

## docker 동작 확인

아래 명령어로 pod 인스턴스에서 필요한 컨테이너들이 잘 동작하고 있는지 확인

`docker ps`

​     

## Kubelet 동작 확인

아래 명령어로 kubelet 서비스가 잘 동작하고 있는지 확인

`sudo systemctl status kubelet`

​    

만약 running 상태가 아니라면 아래 명령어로 kubelet 서비스 활성화

`sudo systemctl start kubelet`

​     

그럼에도 불구하고 잘 동작하지 않는다면 아래 명령어로 로그 파악

`sudo journalctl -u kubelet`

​     

확인 후 적절히 구글링하여 오류에 대한 조치

​      

### swap으로 인한 문제 시

```
server.go: ] failed to run Kubelet: running with swap on is not supported, please disable swap! or set --fail-swap-on flag to false.
...
```

`sudo swapoff -a`