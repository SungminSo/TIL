# k8s 워커노드 추가

1. 마스터 노드에서 토큰 확인. 유효한 토큰이 없을 경우 발급
	- 토큰 확인 : `kubeadm token list`	
	- 토큰 발급: `kubeadm token create`
2. 마스터 노드에서 ca.crt 해쉬값 확인
	- `openssl x509 -pubkey -in /etc/kubernetes/pki/ca.crt | openssl rsa -pubin -outform der 2>/dev/null | openssl dgst -sha256 -hex | sed 's/^.* //'`
3. 워커 노드 join 시 접근할 클러스터 정보 확인
	- `kubectl get cm cluster-info -oyaml -n kube-public`
	- 혹시 cluster의 server값이 private ip로 되어있다면, public ip로 변경(`kubectl edit cm cluster-info -oyaml -n kube-public`으로 직접 변경)
4. 연결하고자 하는 워커 노드에서 join
	- `kubectl get cm cluster-info -oyaml -n kube-public`
5. 마스터 노드에서 확인
	- `kubectl get nodes`