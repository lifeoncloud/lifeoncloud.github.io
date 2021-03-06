---
title:  "GCP에서 과도한 요금이 청구되었을 때 채팅/이메일 지원 요청 절차"
excerpt: "살려주세요..진짜"

categories: [gcp]
tags: [GCP,billing]

header:
  teaser: "/assets/images/gcp/201203-billing-support-1.png" 

toc: true
toc_sticky: true
comments: true

date: 2020-12-03
last_modified_at: 2022-07-23
---

가끔 클라우드 쓰다보면 해킹 당해서, 혹은 그렇게 비싼 제품인 줄 상상도 못해서 등등의 이유로 예상보다 높은 금액이 청구된다.

!["MFA! MFA 설정해놔!" 라고 외치는 인터스텔라 패러디 짤](/assets/images/gcp/201203-billing-support-mfa-400.gif)
<br>지금도 지구촌 여러곳에서 일어나고 있을 장면.gif

AWS는 이런 문제에 대하여 대응 및 지원 프로세스가 잘되어있는 것으로 알고있다.
나 역시 취준생 때 같이 공부하던 팀원들과 프로젝트 계정을 해킹 당해서 300만원이 청구되었었는데, AWS느님이 자비를 베풀어주어 구제받은 경험이 있다.

정말이지 지금 생각해도 아찔하고 감사한 경험이다.

![빌링그래프로 63빌딩 세운 현장](/assets/images/gcp/201203-billing-support-0-63building.png)
<br>
~~63빌딩~~


+) 추가로 리눅서님이 작성하신 AWS 보안 기초 포스팅을 소개한다.<br>
[클라우드를 시작하는 사람을 위한 안내서](https://linuxer.name/2022/05/%ed%81%b4%eb%9d%bc%ec%9a%b0%eb%93%9c%eb%a5%bc-%ec%8b%9c%ec%9e%91%ed%95%98%eb%8a%94-%ec%82%ac%eb%9e%8c%ec%9d%84-%ec%9c%84%ed%95%9c-%ec%95%88%eb%82%b4%ec%84%9c/){:target="_blank"}






이렇듯, AWS는 주변이나 인터넷에 물어보면 면책 및 구제를 해준 케이스를 꽤 자주 들을 수 있다.

GCP는 어떻게 대처하는지 궁금하던 참에, 이에 관한 질문을 받았다.

나도 잘 모르는 부분이라 잘 아실 것 같은 분(!)에게 여쭤보았다.

들은 답변을 바탕으로 지원 메뉴 위치와 순서를 정리했다.

---
## 순서
1.GCP 콘솔  > 지원 > 채팅 지원  메뉴로 이동합니다.

[https://console.cloud.google.com/support/chat](https://console.cloud.google.com/support/chat){:target="_blank"}


![GCP 콘솔에서 지원 > 채팅 지원 메뉴 클릭](/assets/images/gcp/201203-billing-support-1.png)


<br>
<br>
<br>

2.[GCP 결제 지원 받기] 버튼을 클릭합니다.
![GCP 결제 지원 받기 클릭](/assets/images/gcp/201203-billing-support-2.png)


<br>
<br>
<br>

3. ‘결제 도우미’창이 나타납니다. [결제 지원팀 문의]를 클릭합니다.
![결제 지원팀 문의 클릭](/assets/images/gcp/201203-billing-support-3.png)

<br>
<br>
<br>

4. 다음과 같이 선택지를 클릭합니다.

```
Q. 어떤 결제 계정에 관해 지원팀에 문의 하려고 하시나요?
A. 결제 계정 : {과도한 요금이 청구된 빌링 계정}

Q. 다음 중 문제에 대해 가장 잘 설명한 항목은 무엇입니까?
A. [기타]

Q. 알려주신 내용을 살펴보니 다음 내용이 도움이 될 수 있을 것 같습니다.
A. [도움이 되지 않았으며 지원팀에 문의]

Q. 좋습니다. 지원팀에는 어떻게 연락하시겠어요? 질문이 복잡한 경우 이메일을 권장해 드리며, 그렇지 않으면 채팅을 이용해 주세요.
A. [채팅] / [전화] / [이메일] 중 택 1
```
![위 선택지대로 채팅 진행](/assets/images/gcp/201203-billing-support-4.png)