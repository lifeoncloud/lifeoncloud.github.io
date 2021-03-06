---
title:  "Operator 패턴 소개"
excerpt: "Custom Resource, Custom Resource Definition, Custom Controller 그리고 오퍼레이터"

categories: k8s
tags: [Kubernetes]

header:
  teaser: "/assets/images/k8s/220612-doik-operation-pattern.jpg" 

toc: true
toc_sticky: true
comments: true

comments: true

date: 2022-06-12
last_modified_at: 2022-07-18
---
## 들어가며

본 포스팅은 [CloudNet@ 팀의 가시다 님](https://gasidaseo.notion.site/gasidaseo/CloudNet-Blog-c9dfa44a27ff431dafdd2edacc8a1863){:target="_blank"} 이 진행하시는 [쿠버네티스 데이터베이스 오퍼레이터 스터디](https://gasidaseo.notion.site/e49b329c833143d4a3b9715d75b5078d){:target="_blank"} 내용을 정리한 내용입니다.


## 오퍼레이터란?

[공식 쿠버네티스 문서](https://kubernetes.io/ko/docs/concepts/extend-kubernetes/operator/){:target="_blank"} 에선 오퍼레이터를 다음과 같이 설명하고 있다.

"오퍼레이터(Operator)는 사용자 정의 리소스를 사용하여 애플리케이션 및 해당 컴포넌트를 관리하는 쿠버네티스의 소프트웨어 익스텐션이다. 오퍼레이터는 쿠버네티스 원칙, 특히 컨트롤 루프를 따른다."

이를 쉽게 풀어서 말하면, 다음과 같다.

"오퍼레이터(Operator)는 쿠버네티스에서 '사용자 정의 리소스(Custom Resource)'를 사용하여 애플리케이션을 관리하는 쿠버네티스의 '확장 프로그램'이다."

특히 오퍼레이터 패턴은 CR과 CC의 조합을 이용하여 애플리케이션이나 서비스를 생성/삭제/관리 하는 패턴을 말하는데, 이를 그림으로 나타내면 다음과 같다.

![오퍼레이터 설명](/assets/images/k8s/220612-doik-operation-pattern.jpg)


## 오퍼레이터의 구성요소 혹은 친구들


### CR(Custom Resource)

사용자 정의 리소스(Custom Resource, 이하 CR)은  사용자가 원하는 대로 쿠버네티스 API를 확장해서, 사용자가 새롭게 정의(!)한 새로운 리소스를 말한다.

지금까지는 Pod, Deployment, Ingress.. 같이 쿠버네티스의 기본 리소스들만 있었다면 이젠 사용자가 리소스를 ~~창조~~ 만들 수 있는 것이다.

예를 들어, 나는 기존의 Pod가 아니라 PodForDoik라는 이름의 리소스를  생성했다.


```yaml
apiVersion: podfordoiks.stable.example.com
kind: PodForDoik
metadata:
  name: podfordoik-test
spec:
  image: nginx
```

### CRD(Custom Resource Definition)

그리고 CR을 만드려면 앞서, CRD로 CR의 ~~이름, 외모, 성격, 체격 ~~명세를 정의해야 한다. 

정리하면, 쿠버네티스가 제공하는 CRD를 사용하여, 사용자는 자기가 원하는 리소스(CR)를 정의할 수 있는 것이다.

CRD는 아래와 같이 만들 수 있다.

![텍스트가 곁들여진 crd 설명](/assets/images/k8s/220612-doik-crd-with-text.png)

그리고 CR을 확인해보면, CRD에서 명시한대로 만들어졌음을 알 수 있다.


![텍스트가 곁들여진 cr 설명](/assets/images/k8s/220612-doik-cr.png)



### CC(Custom Controller)

위와 같이 쿠버네티스 내부적으로 존재하지 않은 사용자 정의 리소스에 대해서는 (쿠버네티스의 기존 컨트롤러가 아닌)Custom Controller를 사용한다. 

Controller는 다음 툴로 생성할 수 있다.



* [Metacontroller](https://metacontroller.github.io/metacontroller/intro.html){:target="_blank"} : CC를 쉽게 작성하고 배포할 수 있게 해주는 Kubernetes 용 애드온이다. 어떤 언어, 프레임워크든지 Operator를 구현할 수 있다.
* [KUDO](https://kudo.dev/){:target="_blank"} : 위의 툴들이 프로그래밍 언어로 작성하는 방법인 것과 대조되게, CC마저도 선언적 명령, 즉 yaml파일(!)로 생성하는 툴이다.


## 오퍼레이터를 만들 수 있는 툴들

오퍼레이터는 쿠버네티스 API를 이용해서 처음부터(맨바닥부터) 개발해도 되지만, 기존에 오퍼레이터를 편하게 만들 수 있는 툴들이 이미 나와있다.



* [kubebuilder](https://book.kubebuilder.io/){:target="_blank"} : CRD를 사용하여 Kubernetes API를 빌드하기 위한 SDK 프레임워크다.
* [Operation Framework](https://operatorframework.io){:target="_blank"} : 효과적이고 자동화되고 확장 가능한 방식으로 Operators를 관리하기 위한 오픈 소스 툴킷이다.


## 참고 링크



* [https://kubernetes.io/docs/concepts/extend-kubernetes/api-extension/custom-resources/](https://kubernetes.io/docs/concepts/extend-kubernetes/api-extension/custom-resources/){:target="_blank"} 
* [https://kubernetes.io/docs/concepts/extend-kubernetes/api-extension/custom-resources/#customresourcedefinitions](https://kubernetes.io/docs/concepts/extend-kubernetes/api-extension/custom-resources/#customresourcedefinitions){:target="_blank"} 
* [https://kubernetes.io/docs/concepts/extend-kubernetes/api-extension/custom-resources/#custom-controllers](https://kubernetes.io/docs/concepts/extend-kubernetes/api-extension/custom-resources/#custom-controllers){:target="_blank"} 
* [https://ccambo.blogspot.com/2021/11/kubernetes-crd-cr.html](https://ccambo.blogspot.com/2021/11/kubernetes-crd-cr.html){:target="_blank"} 
* [https://ccambo.blogspot.com/2020/12/kubernetes-operator-kubernetes-operator.html](https://ccambo.blogspot.com/2020/12/kubernetes-operator-kubernetes-operator.html){:target="_blank"} 


{% if page.comments %}
<div id="disqus_thread"></div>
<script>
    (function() { // DON'T EDIT BELOW THIS LINE
    var d = document, s = d.createElement('script');
    s.src = 'https://lifeoncloud-github-io.disqus.com/embed.js';
    s.setAttribute('data-timestamp', +new Date());
    (d.head || d.body).appendChild(s);
    })();
</script>
<noscript>Please enable JavaScript to view the <a href="https://disqus.com/?ref_noscript">comments powered by Disqus.</a></noscript>
{% endif %}

