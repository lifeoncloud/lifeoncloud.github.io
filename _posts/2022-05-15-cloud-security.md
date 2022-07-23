---
title:  "클라우드 보안 이모저모"
excerpt: "최소한은 하자."

categories: [study]
tags: [security]

header:
  teaser: "/assets/images/gcp/201203-billing-support-0-63building.png" 

toc: true
toc_sticky: true
comments: true

date: 2022-07-23
last_modified_at: 2022-07-23
---
## 들어가며

클라우드 업계에 있다면 심심찮게 볼 수 있는 사고가 최근에 IT커뮤니티를 강타했다. <br>
이 포스팅을 쓰게 된 계기 : [고민) AWS 해킹 당해서 사용료 3억이 넘게 나왔습니다. ](https://www.clien.net/service/board/park/17225662){:target="_blank"}
<br>
<br>
위의 해킹 사건이 내게 남일이 아닌 이유는, 당장 나도 취준생 시절 국비학원에서 지원받은 AWS계정을 <span style="color: red">해킹</span>당했었고, 빌링그래프로 63빌딩을 세웠었기 때문이다. 

![빌링그래프로 63빌딩 세운 현장](/assets/images/gcp/201203-billing-support-0-63building.png)
~~63빌딩~~ <br>
주말에 접속을 안 했었는데, 월요일에 팀원이 급하게 알려줘서 보니 **생전 처음 보는 사양의 ec2가 20개** 돌고있었다.
<br><br>
나는 부끄럽지만 위의 63빌딩 짤을 커뮤니티 이곳 저곳에 꾸준히 올리고 있는데, 그 이유는 한사람이라도 이 짤을 보고 경각심을 가지길 바래서이다.<br>
그런 의미에서, 사용자가 보안 조치할 수 있는 부분에 대해 정리해본다.
<br>
<br>
<br>

## 1. MFA 설정하자.

!["MFA! MFA 설정해놔!" 라고 외치는 인터스텔라 패러디 짤](/assets/images/gcp/201203-billing-support-mfa-400.gif) <br>
사실 위와 같은 해킹은 Google OTP, Microsoft Authenticator 앱으로 MFA설정만 해도 상당 부분 막을 수 있다.

**MFA는 필수다. 꼭 설정해두자.**

나는 NCP, AWS, GCP 같은 클라우드는 물론 중요 계정은 모두 MFA를 설정해두었다.<br>  
<img src="/assets/images/gcp/220515-cloud-security-mfa-list.jpg" width="300px" title="google otp" alt="google otp">
<br>
위 캡쳐에 있는 것보다 더 많은 계정을 MFA 설정해두었다. <br>
솔직히 난 진짜 어지간한 귀차니스트인데, 위에 63빌딩을 목도한 이후로 내내 무서움이 귀찮음을 이기고 있다😇  ~~금융 치료~~

<br>
<br>
<br>

## 2. 액세스키 올리지 않도록 주의하자.

!["깃헙에 액세스키 올리지마!" 라고 외치는 인터스텔라 패러디 짤](/assets/images/gcp/220515-cloud-security-do-not-upload-access-key-to-github-interstella.gif)


사실 MFA만큼이나 깃헙에 액세스키를 그대로 올려서 해킹당하는 사례가 훨씬 많다고 한다.
지금 생각해보면 내가 해킹당했었던 건도 MFA를 안 해둬서라기보단 액세스키를 깃헙에 올려서가 아닐까 생각한다. ~~물론 비밀번호 난이도도 노답이긴 했다.~~

유사하게, 깃헙에는 GCP의 프로젝트ID와 service account, 키파일을 올리지 않는것을 권한다.

<br>
<br>
<br>

## 3. Root계정 말고 IAM계정으로 쓰자

이 부분은 리눅서님이 깔끔하게 올려주셔서, 링크로 대체한다. <br>
[클라우드를 시작하는 사람을 위한 안내서](https://linuxer.name/2022/05/%ed%81%b4%eb%9d%bc%ec%9a%b0%eb%93%9c%eb%a5%bc-%ec%8b%9c%ec%9e%91%ed%95%98%eb%8a%94-%ec%82%ac%eb%9e%8c%ec%9d%84-%ec%9c%84%ed%95%9c-%ec%95%88%eb%82%b4%ec%84%9c/){:target="_blank"}

AWS에는 MFA, budget alert만 세팅해뒀는데, 리눅서님 덕분에 더 안전한 역할 전환을 알게 되었다. 감사합니다🙇🏻‍♀️   
후반부 역할 전환 부분만 더 자세히 설명하면 다음과 같다.   

<AWS 역할 전환 방법><br>
1.A브라우저에서 root 계정으로 로그인 한다.   
2.B브라우저에서 IAM계정으로 로그인 한다.   
3.A브라우저에서 IAM > 역할 > 해당 역할 클릭 후, [역할 전환] 링크를 복사한다.   
4.IAM계정으로 로그인 되어있는 B브라우저에 [역할 전환] 링크를 붙여넣기 한다.   

<br>
<br>
<br>

## 요약
<br>
위 내용을 세줄요약해서 주위에 공유했다.   

<개발자들을 위한 AWS 3계명>
1.MFA 설정해라. ec2 생성보다 MFA설정이 먼저다.
2.root계정 사용하지 말고 하위에 IAM계정 만들어서 작업해라.
3.깃헙에 AWS 액세스키 포함한 채로 코드 올리지 않았나 확인해라

<br>
그리고 주변 분들 반응..   
<img src="/assets/images/gcp/220515-cloud-security-summary.jpg" width="300px" title="세줄 요약을 본 주변 분들 반응" alt="세줄 요약을 본 주변 분들 반응">


<br><br><br>

## 마치며 

마지막으로 내 경험담이 담긴(..) 인터스텔라 짤을 만들었다.
일부러 자폭개그를 섞은만큼, 많이 알려져서 해킹 피해를 줄이는데 도움이 되었으면 좋겠다.

<br><br><br>

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
