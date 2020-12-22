# NODEMON

### Nodemon이 무엇인가?

Node 서버를 켠 후 수정 사항이 있으면 서버를 내리고 다시 서버를 올려야 수정사항이 적용되는 번거로움이 있지만,
Nodemon은 소스를 변경 할 때 그것을 감지해서 자동으로 서버를 재시작해주는 툴입니다.  

#### Nodemon 설치방법
```
npm install nodemon --save-dev
```
보통 어떠한 라이브러리나 툴을 받을때 --save까지만 붙여주는데 -dev까지 붙이는 이유는 , develoment mode 즉 로컬 개발 환경에서만 사용하겠다는 것이다. -dev 붙이지 않아도 상관은 없다.

설치 후 package.json으로 가보면 -dev를 붙이지 않고 설치를 하면 dependencies에 설치한 항목이 있지만, dev를 붙이고 설치를 했을 경우에는 devDependencies에 항목에 추가된 것을 확인 할 수 있다.

기존에는 npm start를 입력해 실행을 했는데 nodemon으로 실행 하기 위해 명령어를 추가해야 한다.
```js
"scripts": {
    "start": "node index.js",
    "backend":"nodemon index.js",
    "test": "echo \"Error: no test specified\" && exit 1"
  },

```
`"start": "node index.js"` 이 부분이 기존에 사용하던 명령어이며 ` "backend":"nodemon index.js"` 이 부분이 nodemon으로 실행을 하겠다라는 명령어이다. backend라는 이름은 아무거나 본인이 원하는대로 작명이 가능하다.
터미널에 `npm run backend`를 입력해서 서버를 작동하고 소스를 수정하고 저장후 새로고침을 하면 서버를 수동으로 내리고올리지 않아도 자동으로 적용되는것을 확인 할 수 있다!
