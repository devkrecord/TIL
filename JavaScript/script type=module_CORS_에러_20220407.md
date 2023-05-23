### 💥 `<script type=module>` 은 로컬에서 실행 시 자바스크립트 모듈 보안 요구로 인해 CORS 에러가 발생한다.

<br>

**1. `<script type=module>` 의 특성**

- 일반 스크립트와 달리 ES6 Module은 Same-Origin Policy(동일 출처 정책)가 적용된다.
  - CORS Header가 없다면 Cross-Origin 또는 File System에 접근할 수 없다.
  - 로컬 파일의 경우 CORS Header를 설정할 수 없다.
    <br>
- MDN의 javascript modules에 관한 설명에 따르면 type을 module로 설정한 `<script>` 태그가 포함된 HTML 파일을 로컬에서 로드할 경우 자바스크립트 모듈 보안 요구사항으로 인해 CORS 오류가 발생한다고 한다.
  - API 호출(axios, ajax)이 없음에도 불구하고 CORS 오류가 발생

<br>
<br>

**2. 로컬시스템에서 로컬 파일 리소스를 요청할 때 origin(출처)이 null로 넘어가기 때문에 CORS에러가 발생한다.**
<br>

```
origin 'null' has been blocked by CORS policy
```

- Requests Headers에서 origin(출처)값이 null인 경우 동일 출처 정책 때문에 CORS 에러 발생.
- 요청한 데이터를 브라우저에서 보안목적으로 차단
  (브라우저가 웹에서 로컬 파일에 접근하지 못하도록 하기 위하여 로컬 파일을 실행시킬 경우 Origin을 null처리 한다.)

#### ✨ 해결방법은?

- Local Server를 실행시키고, Local Server에서 html 파일을 실행시킨다면 위의 이슈에서와 같이 누락된 Origin이 추가되기 때문에 정상적인 동작이 가능.

- `<script type=module>` -> `<script>`로 수정

---

##### CORS란?

- Cross-Origin Resource Sharing의 약자입니다. 교차 출처 리소스 공유로 번역될 수 있는데, 브라우저에서 다른 출처의 리소스를 공유하는 방법

##### 출처(Origin)란?

- 출처(Origin)란 URL 구조에서 Protocal, Host, Port를 합친 것을 말한다. 브라우저 개발자 도구의 콘솔 창에 location.origin를 실행하면 출처를 확인할 수 있다.

<br>
Reference

<https://jaenjoy.tistory.com/25>
