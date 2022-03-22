# k8s 인증서 갱신
- kubeadm으로 클러스터를 구축한 경우  
   
## 인증서 만료일 확인
```
sudo kubeadm alpha certs check-expiration
```
또는   
```
openssl x509 -in apiserver.crt -noout -dates
openssl x509 -in apiserver-kubelet-client.crt -noout -dates
openssl x509 -in apiserver-etcd-client.crt -noout -dates
```
   
## 인증서 및 config 파일 갱신 전 백업
- `/etc/kubernetes/pki`에 있는 `*.crt` 파일들, `*.key` 파일들 백업
- `/etc/kubernetes`에 있는 `*.config` 파일들 백업
     
## 인증서 및 config 파일 갱신
- (kubeadm 1.17 버전 이상) `sudo kubeadm certs renew all`
	- 해당 명령어 실행 후 인증서 만료일 확인 시 인증서 및 config 파일 모두 갱신된것 확인됨
- 또는 (kubeadm 1.17 버전 이상) `sudo kubeadm init phase certs all & sudo kubeadm init phase kubeconfig all`
- ~/.kube/config 파일 업데이트도 필요
	- `sudo cp /etc/kubernetes/admin.conf ~/.kube/config`
    
## k8s 컴포넌트 재시작
- `sudo kill -s SIGHUP $(pidof kube-apiserver)`
- `sudo kill -s SIGHUP $(pidof kube-controller-manager)`
- `sudo kill -s SIGHUP $(pidof kube-scheduler)`
- `sudo systemctl restart kubelet`
   
## 외부에서 k8s API Server 접근 가능하도록 설정
- k8s 컴포넌트 재시작까지만 해도 인증서 갱신은 완료
- 하지만 Lens 등의 툴을 사용해서 API Server 접근해서 사용하고 있었다면 추가 설정을 해주어야 함
    
### kubeadm config 내용 추출
- `sudo kubectl get configmap kubeadm-config -n kube-system -o jsonpath='{.data.ClusterConfiguration}' > kubeadm-conf.yaml`
    
### kubeadm-conf.yaml 파일에 certSANs 추가
- `certSANs`에는 마스터 노드의 ip를 적게되는데, private ip, public ip 순으로 작성해야함
```
apiServer:
    certSANs:
        - { private ip }
        - { public ip }
    ...
```
     
### kubeadm-conf.yaml 반영한 인증서 재생성
- `sudo kubeadm init phase certs apiserver --config ./kubeadm-conf.yaml`
    
### configmap kubeadm-cong에 변경사항 반영
- `sudo kubeadm init phase upload-config kubelet --config ./kubeadm-conf.yaml`
    
### Lens 등 툴에 연결하기 위한 config 출력
- `sudo kubectl config view --raw --minify`
- 출력된 내용 중 서버의 ip가 public ip로 잘 들어가있는지 확인하고, 만약 private ip로 들어갔다면 public ip로 변경
    
## Notes
- https://wookiist.dev/135
- https://kangwoo.github.io/devops/kubernetes/apiserver-kubelet-client-certs-expired/
- https://kubernetes.io/ko/docs/tasks/administer-cluster/kubeadm/kubeadm-certs/
- https://happycloud-lee.tistory.com/235	