---
title:  "저장 후 전달"
excerpt: "저장 후 전달, 종단간 지연, 큐잉 지연"

categories: study
tags: [Network]

header:
  teaser: "/assets/images/study/210516-store-and-forward-transmission.png"

toc: true
toc_sticky: true
comments: true

date: 2021-05-16
last_modified_at: 2022-07-14
---

## 저장 후 전달(store-and-forward transmission)
라우터를 포함한 대부분의 패킷 스위치는 저장-후-전달 전송방식을 이용한다.

이는 패킷 스위치가 출력 링크로 패킷의 첫 비트를 전송하기 전에 전체 패킷을 받아야 함을 의미한다.


![패킷1의 비트를 받았다고 바로 목적지로 보내지 않고 패킷1의 나머지 비트들도 도착하기를 기다리는 라우터](/assets/images/study/210516-store-and-forward-transmission.png)\
패킷1의 비트를 받았다고 바로 목적지로 보내지 않고 패킷1의 나머지 비트들도 도착하기를 기다리는 라우터.jpg
<br>
라우터가 저장-후-전달 방식을 채택하고 있기 때문에, 라우터는 패킷1의 일부분이 도착했다고 해서, 수신한 비트(패킷1의 일부분)을 전달할 수 없다.

대신, 해당 비트를 먼저 저장(buffer 즉 "store”)한 후, 라우터가 패킷의 모든 비트를 수신한 후에만 출력 링크로 그 패킷을 전송(forward)하기 시작한다.

![모든 비트를 수신한 후에만 출력 링크로 그 패킷을 전송(forward)하기 시작한다](/assets/images/study/210516-store-and-forward-transmission-go.jpeg)


## 종단간 지연(End-to-end delay)

패킷은 링크의 최대 전송 속도와 같은 속도로 각각의 통신 링크상에서 전송된다.

따라서 소스 종단 시스템 혹은 패킷 스위치가 R bits/초의 속도로 링크 상에서 L bits의 패킷을 송신한다면, 그 패킷을 전송하는데 걸리는 시간은 L/R초이다.
(속도 관련 수학 공식을 참고하면 아래와 같다.)
![패킷은 링크의 최대 전송 속도와 같은 속도로 각각의 통신 링크상에서 전송된다. 따라서 소스 종단 시스템 혹은 패킷 스위치가 R bits/초의 속도로 링크 상에서 L bits의 패킷을 송신한다면, 그 패킷을 전송하는데 걸리는 시간은 L/R초이다.](/assets/images/study/210516-store-and-forward-transmission-end-to-end-delay-math.png)


소스 노드로부터 목적지 노드까지 N개의 링크로 구성되고, 

소스와 목적지 사이에 N-1개의 라우터가 있다면(그림에서는 링크 2개, 라우터 1개)

각 링크마다 R전송속도를 가지면서 하나의 패킷을 전송하는 종단간 지연은 다음과 같다.


## 큐잉 지연(Queuing delay)

패킷 스위치는 접속된 여러개의 링크를 갖고 있으며, 각 링크마다 출력 버퍼를 갖고 있다. 

출력 버퍼는 해당 링크로 송신하려는 패킷을 저장한다.

방금 도착한 패킷이 출력 링크로 전송되어야 하는데, 해당 링크가 먼저 온 패킷을 전송중이면, 방금 도착한 패킷은 출력 버퍼에서 대기해야 한다.

예를 들면, 짧은 기간 동안에 라우터로 도착하는 패킷의 전송속도가 라우터에서 목적지로 보내는 전송속도보다 빠르면, 라우터에서 혼잡이 발생하게 되어, 라우터는 출력 링크로 전송하기 전에, 해당 링크에 대한 출력 버퍼에 패킷을 큐잉한다.

![큐잉 지연](/assets/images/study/210516-store-and-forward-transmission-queuing-delay.png)


패킷이 출력 버퍼에서 대기하는 것을 큐잉 지연이라고 한다.

따라서 패킷은 저장-후-전달 지연 뿐 아니라, 큐잉 지연도 겪게 된다.

큐잉 지연은 가변적이고 네트워크의 혼잡 정도에 따라 달라진다.

버퍼 공간의 크기가 유한하기 때문에, 버퍼 공간에 패킷들이 꽉 차있을 때, 새로 도착하는 패킷 혹은 이미 큐잉지연 중인 패킷을 폐기(drop)하는 패킷 손실(packet loss)이 발생한다.

(마치 점심시간에 은행에서 대기표 받고 기다리다가 포기하고 나가버리는 직장인처럼..)


## 참고

본 포스팅은 책 [&lt;컴퓨터 네트워킹 하향식 접근>](https://www.aladin.co.kr/shop/wproduct.aspx?ItemId=117081020){:target="_blank"} 을 공부하면서 정리한 내용입니다.

혹시 틀린 부분이 있으면 댓글로 말씀해주시면 감사하겠습니다.


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