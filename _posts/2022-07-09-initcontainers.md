---
title:  "초기화 컨테이너(initContainers) 간단 설명"
excerpt: "밑작업"

categories: k8s
tags: [Kubernetes, Featured]

toc: false
toc_sticky: false
 
date: 2022-07-09
last_modified_at: 2022-07-09
---


## 들어가며
초기화 컨테이너에 대해 간단히 정리해보았다.

## initContainers(초기화 컨테이너)란?
![초기화 컨테이너를 흙에 비유한 그림](/assets/images/k8s/220709-initcontainer-pic.jpg)


### initContainers장점
* 최소한의 리소스로 잘 돌아가는 애플리케이션을 설계할 수 있다.
* 예를 들어, initContainers에서 미리 git 레파지토리를 git pull 로 받아두면, 메인 컨테이너에서는 git 레파지토리가 이미 있다는 전제 하에 로직을 진행할 수 있다.


### initContainers특징
=* initContainers는 항상 "Completed"를 목표로 실행된다.
* 하나의 파드에 initContainers 가 여러개 있을 경우
    * kubelet은 각 initContainers를 한번에 하나씩 실행한다.
    * 각 initContainers 가 성공적으로 완료(Completed)해야 다음  initContainers 가 시작한다.
* 파드의 restartPolicy의 기본값은 Always이기 때문에, 만약 파드의 initContainers가 실패하면, kubelet은 초기화 컨테이너가 성공할 때까지 반복적으로 재시작한다. 
* 파드의 restartPolicy 를 Never로 설정하면, 해당 파드를 시작하는 동안 initContainers가 실패하면, 쿠버네티스는 전체 파드를 실패한 것으로 처리한다.
* 컨테이너를 initContainers로 지정하기 위해서는, PodSpec에 initContainers 필드를 containers 필드와 동일한 레벨에 지정한다 (containers  하위에 명세 지정x)
* initContainers는 앱 컨테이너의 리소스 상한(limit), 볼륨, 보안 세팅을 포함한 모든 필드와 기능을 지원한다. 그러나, 초기화 컨테이너를 위한 리소스 요청량과 상한은 리소스에 문서화된 것처럼 다르게 처리된다.
* 또한, initContainers 는 파드가 준비 상태가 되기 전에 완료되기 때문에, lifecycle, livenessProbe, readinessProbe 또는 startupProbe를 지원하지 않는다.


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
![초기화 컨테이너 yaml 파일](assets/images/k8s/220709-initcontainer-nujabes-yaml.png)

curl로 잘 확인되는걸 볼 수 있다.
![curl이 잘 작동하는 그림](assets/images/k8s/220709-initcontainer-nujabes.png)

노드포트로도 노출해보자.
![노드포트로 노출해서 브라우저에서 접근한 그림](assets/images/k8s/220709-initcontainer-nujabes-nodeport-exposed.png)


### initContainers 예시 2 : 미리 git clone 해두기
요 코드는 링크로 대신한다. 

책 핵심만 콕! 쿠버네티스 의 공식 깃헙에 가면 볼 수 있다. [https://github.com/bjpublic/core_kubernetes/blob/master/chapters/05/init-container.yaml](https://github.com/bjpublic/core_kubernetes/blob/master/chapters/05/init-container.yaml) 


## 참고링크
* 강의 &lt;[그림으로 배우는 쿠버네티스(v1.22-v1.24)](https://www.inflearn.com/course/%EA%B7%B8%EB%A6%BC%EC%9C%BC%EB%A1%9C-%EB%B0%B0%EC%9A%B0%EB%8A%94-%EC%BF%A0%EB%B2%84%EB%84%A4%ED%8B%B0%EC%8A%A4/dashboard)>
* 강의 그림으로 배우는 쿠버네티스 깃헙: [https://github.com/sysnet4admin/_Lecture_k8s_learning.kit/blob/main/ch7/7.6/pod-initContainers.yaml](https://github.com/sysnet4admin/_Lecture_k8s_learning.kit/blob/main/ch7/7.6/pod-initContainers.yaml) 
* 책 [핵심만 콕!쿠버네티스](https://www.kyobobook.co.kr/product/detailViewKor.laf?mallGb=KOR&ejkGb=KOR&barcode=9791165920180)
* 문서
    * [https://kubernetes.io/ko/docs/concepts/workloads/pods/pod-lifecycle/](https://kubernetes.io/ko/docs/concepts/workloads/pods/pod-lifecycle/)
    * [https://kubernetes.io/ko/docs/tasks/configure-pod-container/configure-pod-initialization/](https://kubernetes.io/ko/docs/tasks/configure-pod-container/configure-pod-initialization/) 
    * [https://github.com/bjpublic/core_kubernetes/blob/master/chapters/05/init-container.yaml](https://github.com/bjpublic/core_kubernetes/blob/master/chapters/05/init-container.yaml) 
    * [https://kubernetes.io/ko/docs/concepts/workloads/pods/init-containers/](https://kubernetes.io/ko/docs/concepts/workloads/pods/init-containers/) 
    * [https://lifeoncloud.github.io/k8s/nodeport/](https://lifeoncloud.github.io/k8s/nodeport/) 