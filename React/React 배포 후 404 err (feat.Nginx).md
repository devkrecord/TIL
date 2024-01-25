## 리액트 앱을 엔진엑스 서버에 올리고 새로고침했을 때 404 not found 에러 발생 😶‍🌫️

<br>

### 원인 👿

빌드된 React 앱을 Nginx의 웹 루트로 배포했을 때 react-router를 쓰지 않는 React 앱일 경우에는 크게 문제가 없이 페이지전환도 잘 된다. 하지만 react-router-dom 라이브러리를 사용하여 react-router를 사용할 때 처음에는 잘 동작하는 듯 보이나 페이지 이동 후, Refresh를 하게 되면 404 not found가 발생하게 된다.

그 이유는 Nginx가 클라이언트 측에 라우팅을 인식하지 못해서 발생하게 되는데, React-Router를 통해 클라이언트 측의 라우팅을 처리하는 경우, 서버는 해당 경로에 대한 파일을 찾을 수 없기 때문에 404 not found 오류가 발생한다.

<br/><br/>

### 해결책 🥸

nginx.conf 파일 수정 -> nginx 재실행

```
    location / {
        try_files $uri /index.html;
    }
```

Nginx에서 발생하는 404 not found 에러는 위와 같이 nginx.conf 파일을 수정하여 해결할 수 있다.

Nginx의 try_files 지시어는 정적 파일 처리 및 요청에 관련된 Redirection을 처리하는 지시어로

try_files $uri /index.html 코드를 작성해주면 Nginx가 해당 경로의 정적 파일을 먼저 찾아보고 없으면 React의 index.html를 반환하도록 구성할 수 있다. 이후 React Router가 해당 경로를 처리하고 해당 페이지를 표시할 수 있다.

예를 들어, /about 페이지에 대한 요청이 있을 경우, nginx는 먼저 /about 파일을 찾고 없으면 /about/ 으로 redirect한뒤 그래도 없으면 index.html을 반환하게 된다.

<br/>
<br/>
<br/>
Reference<br>
https://jw910911.tistory.com/151
