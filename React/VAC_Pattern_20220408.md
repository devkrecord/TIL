# React VAC Pattern

#### React VAC Pattern - View 로직과 JSX의 의존성을 최소화 하자!

<br>

> <br>1. VAC는 View Asset Component의 약자로 렌더링을 관리하는 JSX와 스타일을 관리하는 컴포넌트를 의미합니다. <br> <br>2.VAC 패턴은 View 컴포넌트에서 JSX 영역을 Props Object로 추상화하고, JSX를 VAC로 분리해서 개발하는 설계 방법입니다.<br>이런 설계는 비즈니스 로직 뿐만 아니라 UI 기능 같은 View 로직에서도 렌더링 관심사를 분리하는데 목적이 있습니다.
> <br>

<br>

1. React 협업 - React 환경에서 Front-End 업무는 어떻게 분업화 되어 있나?
   - 데이터나 상호작용 결과를 시각화하는 렌더링 처리 (마크업,CSS, ...)
   - 사용자의 상호작용을 처리하는 UI 기능 개발 (JS,JSX)
   - 비즈니스 로직, UI 기능, 렌더링 처리의 통합(React, Redux, ...)
   - FE 서빙 환경 구출 (Next, graphql, ...)

<br>

2. React 협업이 어려운 점 - React 협업에서 무엇이 우리를 괴롭히는가?
   - FE와 UI 개발 영역이 겹침으로 인한 코드 충돌 발생
   - FE 개발이 적용된 JSX는 UI개발자가 수정하기 어려움

<br>

3. VAC Pattern - View 로직과 JSX를 격리해서 관리하자!

![image](https://wit.nts-corp.com/wp-content/uploads/2021/08/vac_pattern_s1.png)
<br>

- 반복, 조건부 노출 등 스타일 제어 렌더링에 관련된 처리만을 수행
- props를 통해서만 제어되며, 스스로의 상태를 관리하지 않는 stateless 컴포넌트
- 이벤트 함수에 바인딩할 때 추가 처리 없이 적용
- VAC는 state를 가질 수 없지만 state를 가진 컴포넌트를 자식으로 가지는 것은 가능하다. 이 경우 VAC는 부모 컴포넌트와 자식 컴포넌트 중간에서 개입하지 않고 단순히 props를 전달하는 역할만 한다.
- View 컴포넌트에서는 JSX에 어떻게 상태가 적용되는지 신경 쓸 필요가 없으며, VAC(JSX) 관점에서는 어떤 조건에서 버튼이 활성/비활성 되는지를 파악할 필요가 없다.

> <br> VAC의 props 네이밍은 데이터 친화적인 형태 보다는 렌더링에 직관적인 형태로 사용하는 것이 좋습니다. isMax, isMin 보다는 disabledDescrease, disabledIncrease가 렌더링에서 어떤 역할을 하는지 유추하기 더 쉽습니다. <br> <br> 또 여러 정보를 사용하는 경우 개별로 전달하는 것 보다는 조합된 결과만 전달하는 것이 좋습니다. 로그인 한 상태에서 작상자 본인이면 수정 버튼을 노츨하는 조건을 처리한다고 했을 때, isLogin, isOwner을 각각 전달 받아 VAC 내에서 isLogin && isOwner 형태로 사용하는 것 보다는 showEditButton: isLogin && isOwner 형태로 처음부터 조합해서 전달하는 것이 좋습니다.
> <br>

<br>

4. 구현 예제(with VAC Debugger) - VAC Debugger를 활용해 VAC Pattern을 구현해보자.

- 간단한 SpinBox UI 컴포넌트를 통해 VAC를 만드는 예제.
  - JSX영역을 분리하여 VAC로 만든다. 이때 View 컴포넌트에 생성한 Props Object 속성을 참고하여 VAC의 Props를 정의한다.
  - 반대로 이미 만들어진 VAC를 View 컴포넌트에 적용할 때는 VAC의 Props를 참고하여 View 컴포넌트의 Props Object 속성을 정의한다.

```javascript
// VAC
const SpinBoxView = ({ value, onIncrease, onDecrease }) => (
  <div>
    <button onClick={onDecrease}>-</button>
    <span>{value}</span>
    <button onClick={onIncrease}>+</button>
  </div>
);
```

```javascript
// View Component
const SpinBox = () => {
  const [value, setValue] = useState(0);

  const props = {
    value,
    onDecrease: () => setValue(value - 1),
    onIncrease: () => setValue(value + 1),
  };

  // JSX를 VAC로 교체
  return <SpinBoxView {...props} />;
};
```

<br>

5. 서비스 적용 후기(네이버 게임) - 네이버 게임 서비스에서 VAC를 활용하여 개발한 회고

- 장점
  - 마크업 소스와 분리되어 있어서 QA 할 때 수정하는게 편하다.
  - 비지니스 로직이 모여있어 다른 개발자들이 봐도 파악하기 편하다.
  - FE, UI개발 관심사가 분리되어 불필요한 코드 파악이 줄어들었다.
  - UI를 재사용하기 좋고 불필요한 마크업 개발이 줄어 유지보수하기 좋다.
- 단점
  - 처음 도입하는 경우 스펙이나 환경 이해도가 많이 필요하다.
  - UI 개발자가 VAC를 잘 설계하기 위해서는 FE배경 지식이 많이 필요하다.
  - Props 드릴링이 생겨 불편한 경우가 종종 있었다.
  - 스펙 변경 및 설계 변경시 커뮤니케이션 비용이 든다.
  - 플리킹 같이 DOM을 직접 핸들링 하는 경우 적용하기 쉽지 않다.
  - 렌더링에 영향을 받는 속성이 많은 경우는 하위 컴포넌트로 계속 값을 내려줘야하는 등 관리가 어렵다. (Props 드릴링)

<br>

Reference<br>
https://d2.naver.com/news/0568192<br>
https://wit.nts-corp.com/2021/08/11/6461
