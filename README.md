# 컨테이너 인프라 환경 구축을 위한 쿠버네티스/도커

* Book Resources 
*## 허대영 Google books  

## K8S Note
* kubectl get nodes // node 확인
* kubectl get pods [-o wide] // pod 확인
* kubectl run ngnix-pod --image=nginx // 단일파드 배포
* kubectl create -f ~/_Book_k8sInfra/ch3/3.1.6/nginx-pod.yaml // 파일로 파드생성
* kubectl create deployment dpy-nginx --image=nginx // deployment 그룹에 파드생성
* kubectl delete deployment dpy-nginx // deployment 삭제
* kubectl delete -f xxx.yaml // 배포한 deployment 를 yaml 파일로 삭제한다.
* kubectl delete pod nginx-pod // 파드삭제, 오래걸림
* kubectl scale deployment dpy-nginx --replicas=3 // deployment replica 확장, create 생성만 되고 scale 에서는 확장만 가능함. => 파일(오브젝트스펙,야물)로 됨.
* kubectl scale pod dpy-nginx --replicas=3 // 에러, 처음에 pod로 만들면 에러남.
* kubectl api-versions  // 오브젝트스펙 야물에 api version 종류 리스트 확인
* kubectl create -f ./echo-hname.yaml  // yaml 로 create 시 replica 동시생성
* kubectl apply -f ./echo-hname.yaml  // apply를 할 경우에는 기존 설정에 대한 정보가 주석처리되어 확인이 가능하다는 점이 가장 큰 차이점.
* kubectl exec -it nginx-pod -- /bin/bash // nginx pod container 에 접속
* i=1; while true; do sleep 1; echo $((i++)) `curl --silent 172.16.221.131 | grep title`; done // nginx 를 silent 로 1초마다 확인요청, 파드를 kill 로 죽여도 되살아난다.
* kubectl get pods \
* -o=custom-columns=NAME:.metadata.name,IP:.status.podIP,STATUS:.status.phase,NODE:.spec.nodeName // 지정된 컬럼이름으로 값을 매핑에서 조회
* kubectl get pod echo-hname-7894b67f-4n876 -o yaml > pod.yaml // 실행중인 pod 의 yaml 을 파일로 받는다.
* kubectl cordon w3-k8s // w3-k8s 노드를 격리시킨다.
* kubectl uncordon w3-k8s // w3-k8s 노드를 격리해제시킨다.
* kubectl drain w3-k8s --ignore-daemonsets // 지정된 노드의 파드를 전부 다른곳으로 옮긴다. DaemonSet 에러가 발생하지만 무시한다.
* kubectl apply -f ch3link/3.2.10/rollout-nginx.yaml --record // --record 옵션으로 배포정보의 history 를 기록한다.
* kubectl rollout history deployment rollout-nginx // rollout history 명령으로 배포기록을 조회한다.
* kubectl set image deployment rollout-nginx nginx=nginx:1.16.0 --record // 이미 배포된 image 들의 버전을 업데이트 한다. 이름과 IP가 갱신된다.
* kubectl rollout status deployment rollout-nginx // rollout Deployment 상태를 확인
* kubectl describe deployment rollout-nginx // describe 명령으로 문제발생시 상태를 확인한다.
* kubectl rollout undo deployment rollout-nginx // undo 로 rollout 을 되돌린다. revision 3을 취소하면 revision 2로 되돌려지고 이것은 revision 4로 된다.
* kubectl rollout undo deployment rollout-nginx --to-revision=1 // revision 1로 돌아간다.
* kubectl get services // 서비스를 확인한다.
* i=1; while true; do sleep 1; echo $((i++)) `curl -s 192.168.1.101:30000`; done // 파드분산 모니터링 shell
* kubectl expose deployment np-pods  --port=80 --type=NodePort --name=np-svc-v2  // np-pods의 포트 80을 expose 한다. 타입은 NodePort 이고 이름은 np-svc-v2 이다.
* kubectl delete services np-svc // 서비스삭제
* k get ingress [-o yaml] // ingress controller 설정 확인
* k get pods -n ingress-nginx // namespace 조회
* k get services -n ingress-nginx // namespace 의 서비스조회
* k get configmap -n metallb-system [-o yaml] // configmap 조회
* k top pods // 파드 부하 조회
* k edit deployment hpa-hname-pods // 파드 배포이후 deployment 수정
* k autoscale deployment hpa-hname-pods --min=1 --max=30 --cpu-percent=50 // resources 의 cpu 가 10m 이고 autoscale cpu-percent 가 50 인데 29m 의 부하를 받는 경우 10m/50% => 5m , 즉 29m/5m => 6 이므로 6개의 파드가 생성된다.
* k delete hpa hpa-hname-pods // hpa(horizontal pods autoscaler)
* k get pv // pv 상태확인
* k delete limitranges storagelimits // 용량제한 해제
* k get resourcequotas // resource 쿼터 조회
* 
* k create deployment failure1 --image=multistage-img // 외부img 다운로드 배포
* k create deployment failure2 --dry-run=client -o yaml --image=multistage-img > failure2.yaml // 현재 실행중인 deployment yaml 을 파일로 추출
* docker tag multistage-img 192.168.1.10:8443/multistage-img // tag 를 이용해서 image 의 tag 를 변경해서 신규생성
* docker push 192.168.1.10:8443/multistage-img // docker 를 registry 에 push
* curl https://192.168.1.10:8443/v2/_catalog -k // 사설 registry 조회
* k describe pods -n metallb-system | grep Image: // pods 정보 grep 조회
* k get nodes m-k8s -o yaml | nl // nl 은 number 
* k get serviceaccounts  // 서비스 account 조회
* k create clusterrolebinding jenkins-cluster-admin --clusterrole=cluster-admin --serviceaccount=default:jenkins // jenkins 서비스account 에 cluster-admin role 부여 하여 클러스터 API서버와 통신권한 부여
* k get deployment,service --selector=app=dashboard // 여러조건 검색을 위해 selector 로 조회
* k get deployment --show-labels // 라벨명 조회
* k config view [--raw] // .kube 디렉토리 config 정보 조회
* kubectl get secret --namespace default grafana -o jsonpath="{.data.admin-password}" | base64 --decode ; echo // grafana password 
* k run net --image=sysnet4admin/net-tools --restart=Never --rm -it -- nslookup 192.168.1.12 // CoreDNS 에서 해당 IP URL 조회하기

## docker note
* docker search <검색어>
* docker pull nginx:stable // 안정화버전 다운로드
* docker history nginx:stable // 이미지 생성과정 확인
* docker run -d --restart always nginx // docker 실행, -d(detach) : 백그라운드구동, --restart always : 죽어도 항상 재기동
* docker ps -f id=84 // docker 검색(--filter)
* docker ps -f ancestor=nginx // nginx 선조 조회
* docker run -d -p 8080:80 --name nginx-exposed --restart always nginx // publish(-p) port
* docker run -d -p 8081:80 -v /root/html:/usr/share/nginx/html --restart always --name nginx-bind-mounts nginx // 호스트 볼륨(-v) 을 컨테이너에 연결한다. 컨테이너 볼륨은 덮어써진다.
* docker volume create nginx-volume // docker volume 생성(바인드 마운트와 혼동주의)
* docker volume inspect nginx-volume // docker volume 확인
* docker run -d -v nginx-volume:/usr/share/nginx/html -p 8082:80 --restart always --name nginx-volume nginx // 도커볼륨과 컨테이너 연결, 덮어쓰지 않는다.
* docker ps -q -f ancestor=nginx // pid 만 출력
* docker stop $(docker ps -q -f ancestor=nginx) // 컨테이너ID를 param 으로 모두 중지
* docker ps -aq -f ancestor=nginx // 중지된 컨테이너 모두 조회 all(-a)
* docker rm $(docker ps -aq -f ancestor=nginx) // 컨테이너 ID를 param 으로 모두 삭제
* docker rmi $(docker images -q nginx) // image id 를 param 으로 image 모두 삭제
* docker build -t basic-img . // docker build, tag(-t) .(점)은 현재경로에 만들기
* docker build -t basic-img:1.0 -t basic-img:2.0 . // 1.0을 이용하여 2.0 tag 만들기
* docker rmi $(docker images -f dangling=true -q) // dangling image 삭제

## CI/CD note
kustomize create --namespace=metallb-system --resources namespace.yaml,metallb.yaml,metallb-l2config.yaml //kustomize 로 여러개 yaml 통합
kustomize edit set image metallb/controller:v0.8.2 // kustomize 에 image tag 생성
kustomize build | k apply -f - // kustomize build 결과를 k 로 전달해서 생성
kustomize build | k delete -f - // kustomize 배포결과 삭제

// helm metallb 설치
```
helm install metallb edu/metallb \
 --namespace=metallb-system \
 --create-namespace \
 --set controller.tag=v0.8.3 \
 --set speaker.tag=v0.8.3 \
 --set configmap.ipRange=192.168.1.11-192.168.1.29
```
* bpytop // 파이썬기반 top 유틸





