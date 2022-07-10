---
title:  "kubeconfig Permission denied 관련"
excerpt: "권한 췤"

categories: k8s
tags: [Kubernetes]

header:
  teaser: "/assets/images/k8s/220311-kubeconfig-troubleshoot-blurred-02.jpg" 

toc: true
toc_sticky: true
comments: true

date: 2022-03-11
last_modified_at: 2022-07-10
---

# GCP kubeconfig 무간지옥 트러블슈팅

## 환경 및 문제된 로그
GKE에서 생성한 클러스터를 Cloud Shell에서 접속하면 다음과 같은 에러가 나타났다.


![GCP Cloud Shell에서 permision denied 에러가 난 상황](/assets/images/k8s/220311-kubeconfig-troubleshoot-blurred-01.jpg)
(내가 뭘 했다고)

```shell
Welcome to Cloud Shell! Type "help" to get started.

Your Cloud Platform project in this session is set to 프로젝트 이름.

Use “gcloud config set project [PROJECT_ID]” to change to a different project.

gcloud container clusters get-credentials test --zone us-central1-c --project 프로젝트 이름soojin@cloudshell:~ (프로젝트 이름)$ gcloud container clusters get-credentials test --zone us-central1-c --project 프로젝트 이름

Fetching cluster endpoint and auth data.

ERROR: (gcloud.container.clusters.get-credentials) Unable to create private file [/home/사용자이름/.kube/config]: [Errno 13] Permission denied: '/home/사용자이름/.kube/config'

```



---




## 해결 과정

구글에 검색해보니, 예전에 kubeconfig 관련해서 삽질(수정하고 날리고 다시 만들고 이하생략)하다가 권한을 건드렸나보다.

그래서 권한을 조정해주었다.

config파일만 조정하니까 안되어서 .kube 디렉토리도 권한을 조정했다.

![GCP Cloud Shell에서 permision denied 에러가 난 상황](/assets/images/k8s/220311-kubeconfig-troubleshoot-blurred-02.jpg)

```shell
soojin@cloudshell:~ (프로젝트 이름)$ ls -al /home/soojin/.kube/config

ls: cannot access '/home/soojin/.kube/config': Permission denied

soojin@cloudshell:~ (프로젝트 이름)$ sudo ls -al /home/soojin/.kube/config

-rwxrwxrwx 1 soojin 1001 0 Mar 11 06:06 /home/soojin/.kube/config

soojin@cloudshell:~ (프로젝트 이름)$ sudo ls -al /home/soojin/.kube

total 28

drw-------  3 soojin soojin 4096 Mar 11 06:06 .

drwx--x--x 11 soojin   1001 4096 Mar 11 06:29 ..

drwxr-x---  4 soojin   1001 4096 May  6  2021 cache

-rwxrwxrwx  1 soojin   1001    0 Mar 11 06:06 config

-rw-r--r--  1 root   root    452 Oct 22 01:21 config-demo

-rw-r--r--  1 soojin   1001 1298 Oct 22 00:58 soojinuser.crt

-rw-r--r--  1 soojin   1001 1119 Oct 22 00:56 soojinuser.csr

-rw-------  1 soojin   1001 1679 Oct 22 00:55 soojinuser.key

soojin@cloudshell:~ (프로젝트 이름)$ sudo chmod 777 /home/soojin/.kube

soojin@cloudshell:~ (프로젝트 이름)$ gcloud container clusters get-credentials test --zone us-central1-c --project 프로젝트 이름

Fetching cluster endpoint and auth data.

kubeconfig entry generated for test.

soojin@cloudshell:~ (프로젝트 이름)$

```

접속 성공..

사랑해요 구글신


## 참고한 링크
* [https://forum.linuxfoundation.org/discussion/858429/kubectl-command-throwing-error](https://forum.linuxfoundation.org/discussion/858429/kubectl-command-throwing-error){:target="_blank"}
* [http://mobilelab.khu.ac.kr/wordpress/dc/?vid=186](http://mobilelab.khu.ac.kr/wordpress/dc/?vid=186) [https://github.com/kubernetes/minikube/issues/5417](https://github.com/kubernetes/minikube/issues/5417){:target="_blank"}
* [https://araikuma.tistory.com/117](https://araikuma.tistory.com/117){:target="_blank"}
* [https://recipes4dev.tistory.com/175](https://recipes4dev.tistory.com/175){:target="_blank"}
