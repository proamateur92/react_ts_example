# 타입스크립트
1. 함수의 매개변수 타입, 객체의 속성 등을 보장받고 개발자의 실수를 줄일 수 있다.
2. 타입스크립트 코드의 이상이 없으면 자바스크립트로 변환할 수 있다.
3. 브라우저는 오로지 자바스크립트만 읽어들이므로 변환 과정을 거쳐야 한다.

<br>

## 타입스크립트 시작하기
```
https://create-react-app.dev/docs/adding-typescript/
```

1. 타입스크립트와 함께 리액트 앱 설치하기
2. 기존 리액트 앱에 타입스크립트 설치하기
-> 확장자 js를 tsx로 변경해주고 tsconfig.json 파일을 직접 생성해주어야 한다.

<br>

### 기존 React App에 Typescript 적용하기
- Typescript 의존성 추가
- 타입 스크립트를 적용하기 위해 필요한 라이브러리들을 package.json에 의존성을 추가한다. (아래 명령어 참고)

```
npm install --save typescript @types/node @types/react @types/react-dom @types/jest
```

<br>

> 위 명령어를 수행하면 아래와 같이 package.json > devDependencies 에 의존성 라이브러리들이 추가된다.

```
{
  ...
  "devDependencies": {
    "@types/jest": "^27.4.1",
    "@types/node": "^17.0.21",
    "@types/react": "^17.0.39",
    "@types/react-dom": "^17.0.12",
    "typescript": "^4.6.2"
    ...
  },
  ...
}
```

<br>

### Typescript 설정 (tsconfig.json)

<br>

> tsc 명령어를 사용하여 tsconfig.json을 생성한다.

```
tsc --init

{
  "compileOnSave": true,
  "compilerOptions": {
    "target": "es2016",
    "jsx": "react-jsx",
    "module": "esnext",
    "sourceMap": true,
    "removeComments": true,
    "esModuleInterop": true,
    "forceConsistentCasingInFileNames": true,
    "strict": true,
    "skipLibCheck": true,
    "lib": [
      "dom",
      "dom.iterable",
      "esnext"
    ],
    "allowJs": true,
    "allowSyntheticDefaultImports": true,
    "noFallthroughCasesInSwitch": true,
    "moduleResolution": "node",
    "resolveJsonModule": true,
    "isolatedModules": true,
    "noEmit": true
  },
  "include": [
    "./src/"
  ],
  "exclude": [
    "node_modules/*"
  ]
}
```

<br>

> tsconfig.json은 더욱 상세하게 설정이 가능하므로 필요한 경우 아래 사이트를 참고하도록 하자.

```
https://www.typescriptlang.org/tsconfig
```

<br>

### 기존 js 파일 확장자 수정

<br>

#### index.js -> index.tsx

```
import React from 'react';
import ReactDOM from 'react-dom';
import './index.css';
import App from './App';

ReactDOM.render(
  <React.StrictMode>
    <App />
  </React.StrictMode>,
  document.getElementById('root')
);
```

<br>

#### App.js -> App.tsx

```
import React from 'react';
import './App.css';

const App: React.FC = () => {
  return (
      <div className="App">
        Hello Typescript
      </div>
  );
}

export default App;
```

<br>

## 타입스크립트 알아보기

<br>

### 함수 등 특정 변수의 타입을 지정할 때
- 파라미터 등 변수의 타입을 명시해주어야 한다.

```
const add = (a: number, b: number) => a + b;
```

<br>

## 본 실습을 통해 styled-components와 함께 ts를 적용해보자.

>styled-component 설치
- 타입스크립트가 알아들을 수 있는 type definition 파일 설치하여야 한다.

```
npm i @types/styled-components
```

<br>

※ styled-components 라이브러리를 추가적으로 설치해주어야 한다.
```
npm -i styled-components
```

<br>

> package.json > devDependencies에 따로 개발 시에만 필요한 라이브러리를 정리하기도 한다.
나의 경우 타입스크립트 예제 프로젝트이므로 추후에 더 깊이 알아볼 계획이다.

<br>

### interface를 통해 props 타입 보장해주기

<br>

#### App.tsx
```
import Circle from "./Circle";

function App() {
  return (
    <div>
      <Circle borderRadius="50%" />
      <Circle borderRadius="25px" />
    </div>
  );
}

export default App;
```

<br>

#### Circle.tsx

```
const Container = styled.div<CircleProps>`
  width: 200px;
  height: 200px;
  border-radius: ${(props) => (props.borderRadius ? props.borderRadius : "0")};
  background-color: salmon;
`;

interface CircleProps {
  borderRadius: string;
}

const Circle = ({ borderRadius }: CircleProps) => {
  return <Container borderRadius={borderRadius} />;
};
```
<br>

## optional props, default props 
- 위의 코드에서 선언해준 interface CircleProps의 props를 내려주지 않으면 에러가 발생한다.

<br>

#### App.tsx

```
function App() {
  return (
    <div>
      <Circle borderRadius="50%" />
      <Circle />	// 에러 발생. borderRadius는 필수 props이다.	
    </div>
  );
}
```

<br>

> Circle 컴포넌트에 borderColor props를 추가로 내려보자.

#### App.tsx

```
<div>
  <Circle borderRadius="50%" borderColor="mediumPurple" />
  <Circle borderRadius="25px" />
</div>

// CircleProps에 props를 require props가 아닌 option props를 추가해보자.

interface CircleProps {
  borderRadius: string;
  borderColor?: string;
}
```

<br>

> 추가 interface로 ContainerProps를 만들어주자.
> CircleProps와 props는 같지만 optional props 처리는 해주지 않는다.

<br>

#### Circle.tsx
```
interface ContainerProps {
  borderRadius: string;
  borderColor: string;
}
```

> CircleProps는 상위 컴포넌트에서 호출될 때 필요한 props 타입을 지정하기 위해 선언해주었다. <br>
> ContainerProps는 styled-components에 사용될 props 타입을 지정하기 위해 선언해주었다.  <br>
> CircleProps에서 borderColor는 optional 하지만 ContainerProps에서는 필수적으로 추가해주어야 한다.  <br>
> 따라서, borderColor props를 내려받지 못할 경우를 대비해 ?? 연산자를 활용해 props로 내려준다. <br>
> a ?? b => a가 true면 a, a가 false면 b

<br>

#### Circle.tsx
```
<Container
  borderRadius={borderRadius}
  borderColor={borderColor ?? "salmon"}
/>
```

<br>

### Circle.tsx

```
interface ContainerProps {
  borderRadius: string;
  borderColor: string;
}

const Container = styled.div<ContainerProps>`
  width: 200px;
  height: 200px;

  border-radius: ${(props) => (props.borderRadius ? props.borderRadius : "0")};
  border: 3px solid ${(props) => props.borderColor};

  background-color: salmon;
`;

interface CircleProps {
  borderRadius: string;
  borderColor?: string;
}

const Circle = ({ borderRadius, borderColor }: CircleProps) => {
  return (
    <Container
      borderRadius={borderRadius}
      borderColor={borderColor ?? "salmon"}
    />
  );
};
```

<br>

> default props는 ts가 아닌 ES6문법이다.
> 아래와 같이 props.속성 = default value식으로 넘겨주면 된다.
```
const AnyComponent = ({whichProps = 'default'}) => {
	...
}
```

<br>

### 정리
> interface 내부의 optional props는 ?:로 작성.

<br>

## useState
- ts는 useState의 초기값으로 타입을 암묵적으로 지정해준다.

```
// str을 초기화해주었으므로 str은 string 타입을 가진다.
const [str, setStr] = useState("");

// value는 number 또는 string 타입을 가진다.
const [value, setValue] = useState<number | string>("");
```

> 해당 타입이 아닌 값을 set해주면 당연하게도 에러가 발생한다.

<br>

## form을 통해 ts 더 알아보기

- input 태그와 form태그의 타입은 아래 코드와 같다.

```
const onChange = (e: React.FormEvent<HTMLInputElement>) => {
const newValue = e.currentTarget.value;
setUsername(newValue);
};

const onSubmit = (e: React.FormEvent<HTMLFormElement>) => {
e.preventDefault();
alert(`HELL'O ${username}!`);
};

return (
<div>
  <form onSubmit={onSubmit}>
	<input type="text" value={username} onChange={onChange} />
	<button>lon in</button>
  </form>
</div>
);
```
