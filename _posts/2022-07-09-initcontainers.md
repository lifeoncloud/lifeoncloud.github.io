---
title:  "초기화 컨테이너(initContainers) 간단 설명"
excerpt: "간단하게 초기화 컨테이너(initContainers)에 대해 적어보았습니다. (feat. Nujabes)"

categories: k8s
tags: [Kubernetes]

toc: true
toc_sticky: true
comments: true

date: 2022-07-09
last_modified_at: 2022-07-10
---

## initContainers(초기화 컨테이너)란?
![초기화 컨테이너를 흙에 비유한 그림](/assets/images/k8s/220709-initcontainer-pic.jpg)
시작할 때만 Pod에 도움 주고 빠지는 컨테이너다.   
식물로 치면 식물이 잘 자랄 수 있도록 도와주는 흙 역할을 한다.



### initContainers장점
* 최소한의 리소스로 잘 돌아가는 애플리케이션을 설계할 수 있다.
* 예를 들어 initContainers에서 미리 git 레파지토리를 git pull 로 받아두면, 메인 컨테이너에서는 git 레파지토리가 이미 있다는 전제 하에 로직을 진행할 수 있다.


### initContainers특징
* initContainers는 항상 완료(Completed)를 목표로 실행된다.
* 하나의 파드에 initContainers가 여러개 있을 경우
  * kubelet은 각 initContainers를 한번에 하나씩 실행한다.
  * 하나의 initContainers가 성공적으로 완료(Completed)된 후, 다음 initContainers가 시작된다.
* (파드의 restartPolicy의 기본값은 Always이기 때문에)만약 파드의 initContainers가 실패하면, kubelet은 초기화 컨테이너가 성공할 때까지 반복적으로 재시작한다. 
* 파드의 restartPolicy를 Never로 설정하면, 해당 파드를 시작하는 동안 initContainers가 실패하면, 쿠버네티스는 전체 파드를 실패한 것으로 처리한다.
* 컨테이너를 initContainers로 지정하기 위해서는, PodSpec에 initContainers항목을 containers항목과 동일한 레벨에 지정한다 (containers 항목 하위에 명세 지정x)
* initContainers는 파드가 준비 상태가 되기 전에 완료되기 때문에 livenessProbe, readinessProbe, startupProbe를 지원하지 않는다.


### initContainers 예시 1 : 미리 index.html 파일 만들어두기

초기화 컨테이너가 들어있는 yaml파일은 다음과 같다.
```yaml
apiVersion: v1
kind: Pod
metadata:
 name: initcontainer-pod
 labels:
   app: init
spec:
 containers:
 - name: web
   image: nginx
   volumeMounts:
   - name: empty-box
     mountPath: /usr/share/nginx/html
 initContainers:
 - name: making-index
   image: alpine/git
   command: ["/bin/sh", "-c"]
   args:
   - echo "Nujabes - Luv(sic.) pt3" > /html-dir/index.html;
   volumeMounts:
   - name: empty-box
     mountPath: "/html-dir"
 volumes:
 - name: empty-box
   emptyDir: {}
```

이를 적용해보자.
![초기화 컨테이너 yaml 파일](/assets/images/k8s/220709-initcontainer-nujabes-yaml.png)

curl로 잘 확인되는걸 볼 수 있다.
![curl이 잘 작동하는 그림](/assets/images/k8s/220709-initcontainer-nujabes.png)

노드포트로도 노출이 잘 되는걸 확인할 수 있다.
![노드포트로 노출해서 브라우저에서 접근한 그림](/assets/images/k8s/220709-initcontainer-nujabes-nodeport-exposed.png)

(헬로월드 말고 뭐 넣을까 고민하다가 그냥 좋아하는 노래를 넣었다. 모두 Nujabes를 들어주세요😌)




## 참고링크
* 강의 [<그림으로 배우는 쿠버네티스>](https://www.inflearn.com/course/%EA%B7%B8%EB%A6%BC%EC%9C%BC%EB%A1%9C-%EB%B0%B0%EC%9A%B0%EB%8A%94-%EC%BF%A0%EB%B2%84%EB%84%A4%ED%8B%B0%EC%8A%A4/dashboard)
* 강의 [<그림으로 배우는 쿠버네티스 깃헙>](https://github.com/sysnet4admin/_Lecture_k8s_learning.kit/blob/main/ch7/7.6/pod-initContainers.yaml)
* 책 [<핵심만 콕! 쿠버네티스>](https://www.kyobobook.co.kr/product/detailViewKor.laf?mallGb=KOR&ejkGb=KOR&barcode=9791165920180)
* 책 [<핵심만 콕! 쿠버네티스> 깃헙](https://github.com/bjpublic/core_kubernetes/blob/master/chapters/05/init-container.yaml)
* 공식 문서 [초기화 컨테이너](https://kubernetes.io/ko/docs/concepts/workloads/pods/init-containers/)
* 관련 포스팅 [노드포트(NodePort) 간단 설명](https://lifeoncloud.github.io/k8s/nodeport/) 