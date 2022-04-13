# Localhost vs 127.0.0.1 vs 0.0.0.0

데스크탑에서 리액트앱을 실행하고(Localhost:3000)
데스크탑 ip 주소를 이용하여 노트북에서 접근을 시도했다. (192.168.xxx.x)
하지만 접근이 되지 않았다.

네트워크 상태를 검색해보았다.

```


리눅스
# netstat -tnlp

macOS
$ lsof -iTCP -sTCP:LISTEN -n -P

```

node의 ip 주소가 127.0.0.1로 되어있었고
webpack devServer에 host: '0.0.0.0' 를 추가하니
데스크탑 ip와 port를 입력하여 노트북에서 접근할 수 있었다.

### Localhost vs 127.0.0.1

localhost:3000 과 이것의 ip주소인 127.0.0.1:3000 을 브라우져 창에 입력하면 같은 결과가 나온다.

이것을 루프백이라고 하는데, 자신의 컴퓨터를 스스로 서버로 만들고 그 컴퓨터에서 콜을 보내 응답을 받을 수 있는 것을 말한다. 즉 네트워크를 구축하지 않고도 네트워크 처럼 요청을 보내고 응답을 받는 것이 가능한 기능을 뜻한다.

**그렇다면 localhost:3000 과 127.0.0.1:3000 은 어떤 차이가 있을까?**

> <br>비유적으로 localhost:3000 은 www.naver.com 이고
> 127.0.0.1:3000 은 125.209.222.141(네이버 ip주소) 라고 생각하면 될 듯 하다.
> <br>

우리가 브라우저에 입력하는 사이트 url은 DNS(domain name server)를 통해 ip 주소로 변환되게 되고 이를 통해 통신이 가능해진다.

루프백은 내 컴퓨터가 내 컴퓨터에 요청을 보내고 응답을 받는 기능이기 때문에, DNS 서버를 통해 ip주소로 변환할 수 있는 기능이 없다.

때문에 OS가 이를 조정을 해주어야 되는데, 그것을 담당하는 부분이 OS hosts 파일의 redirect rules이다.

여기에서 localhost 를 어떤 ip로 redirecting 시켜줄지를 지정해줄 수 있는데, 만약 이 부분에서 localhost의 redirecting ip를 다르게 지정해 놓았다면 localhost:3000을 브라우져에 입력하더라도 원하는 결과를 얻지 못할 수 있다.

<br>

### 127.0.0.1 vs 0.0.0.0

> <br>**127.0.0.1**<br>
> 127.0.0.1은 Localhost 즉 자기 스스로를 가리키는 IP
> <br>

- 동일한 기계에서만 액세스할 수 있음
- 포트가 인터넷이나 네트워크가 아닌 PC 자체의 연결만을 수신

<br>

> <br>**0.0.0.0**<br>
> 0.0.0.0은 보통 서브넷 마스크 0.0.0.0과 같이 사용되며 모든 IP를 의미
> <br>

- 모든 IPv4 주소 지정 방법
- 라우팅 테이블에서 사용되는 경우 기본 게이트웨이를 식별하는데 0.0.0.0에 대한 경로가 기본 경로
- 컴퓨터, 모뎀 및 네트워크 카드에서 수신 대기 중임을 의미
- 네트워크에 액세스할 수 있는 곳이면 어디에서나 접근 가능
- `::` 0.0.0.0의 IPv6 버전

<br>

> <br> 루프백(loopback)
> <br>

- loopback을 호스트 네임으로는 localhost 라고 명한다.
- 127.0.0.X에 해당하는 모든 주소가 loopback이라고 한다. 하지만 일부 운영체제가 정확히 127.0.0.1을 입력해야 loopback으로 인식
- loopback은 테스팅, 디버깅 등 해당 용도로 사용될 수 있다.
- loopback은 네트워크를 통해 데이터 패킷이 전송되어 다시 같은 디바이스로 돌아온다.
  <br>

Reference<br>

https://velog.io/@lky9303/127.0.0.1-%EA%B3%BC-localhost%EC%9D%98-%EC%B0%A8%EC%9D%B4

https://m.blog.naver.com/PostView.naver?isHttpsRedirect=true&blogId=aou12&logNo=221371564012

https://woo-dev.tistory.com/190
