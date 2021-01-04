# Bcrypt를 이용한 로그인 기능 만들기

### login flow
> 데이터 입력(이메일,패스워드) -> 입력 받은 이메일을 데이터 베이스에서 찾기 -> 이메일이 있다면 비밀번호가 동일한지 확인-> 동일하면 Token 생성

1. Login Route 만들기

index.js에서 post 메소드를 이용해 라우트 생성

post는 인자로 경로와 콜백 함수를 받는다.

`app.post('/login',(req,res)=>{})`

요청된 데이터를 데이터베이스에서 있는지 찾기 위해 User 모델을 가지고 온 뒤 findOne이라는 MongoDB 메소드를 이용한다.

findOne은 두개의 인자를 받는다.

우리가 찾을 email데이터를 첫번째 인자로 준다

`User.findOne({email:req.body.email},콜백함수)`

findOne의 두번째 인자로는 callback fucntion이고 callback function은 2개의 파라미터를 가진다.

```js
  User.findOne({email:req.body.email},(err,user)=>{
    if(!user){
      return res.json({
        loginSuccess: false,
        message: "제공된 이메일에 해당하는 유저가 없습니다."
      })
    }
```

user데이터가 존재하지 않으면 json데이터로 로그인 상태 실패와 메세지를 보내준다.

존재한다면 입력 받은 비밀번호와 데이터베이스 안에 있는 비밀번호와 동일한지 확인을 한다.

먼저 메소드를 만들어야 하는데 메소는 유저 모델에서 생성한다.

> 'User.js'

```js

userSchema.methods.comparePassword = function(plainPassword, cb){
    // 입력된 비밀번호와 암호화된 비밀번호를 비교
    bcrypt.compare(plainPassword, this.password, function(err, isMatch){
        if(err) return cb(err),
        cb(null, isMatch)
    })
}
```

comparePassword는 원하는대로 작명이 가능하다. 

첫번째 파라미터인 plainPassword는 입력 받은 비밀번호이다.

두번째 파라미터로는 콜백함수를 받는다.

비밀번호는 입력 받은 비밀번호와 암호화 된 데이터베이스의 비밀번호와 비교하기 위해 bcrypt를 이용한다.

compare는 3가지를 인자로 받는다.

첫번째는 입력 받은 비밀번호, 두번째는 암호화된 비밀번호, 세번째는 콜백함수이다.

두개의 비밀번호가 일치하지 않으면 에러를 반환하고, 일치 한다면 에러는 null, isMatch는 true를 반환한다.

> `index.js`

```js
    // 요청된 이메일이 데이터베이스에 있다면 비밀번호가 맞는지 확인
    user.comparePassword(req.body.password ,(err,isMatch)=>{
        if(!isMatch)
        return res.json({loginSuccess: false, message:"비밀번호가 틀렸습니다."})
```

isMatch가 false이면 json 데이터로 상태와 메세지를 보내준다.

비밀번호가 일치하는 경우에는 토큰을 생성 해야하는데 JSONWEBTOEKN 라이브러를 이용하여 토큰을 생성한다.

`npm install jsonwebtoken --save`

사용방법은 [참조사이트](https://www.npmjs.com/package/jsonwebtoken)를 이용 한다.

예제를 살펴보자.

```js
    var jwt = require('jsonwebtoken');
    var token = jwt.sign({ foo: 'bar' }, 'shhhhh');
```

json webtoken을 import하고 sign이라는 메소드를 이용해 합쳐주면 토큰이 생성이 된다.

> User.js

```js
    var jwt = require('jsonwebtoken');
    userSchema.methods.generateToken = function(cb){
    var user = this;
    // jsonwebtoken을 이용해 toekn 생성
    jwt.sign(user._id.toHexString(), 'secretToken')

}

```

generateToken는 임의로 지정할 수 있다.

user id를 넣어줄것인데 es5 문법을 쓰고 있으니 `var user = this;` 를 이용해 지정 해주고 sign의 두번째 인자로는 임의로 지정해 넣으면된다.

user._id + 'secretToken' = token 이며  secretToken를 넣으면 user id가 나오는데 token을 이용해 이 사람이 누구인지를 판별 할 수 있다.

secretToken를 넣었을 때 user.id가 나오기 때문에 secretToken도 기억을 해야한다. 

token이라는 변수로 지정해주었다.

` var token = jwt.sign(user._id, 'secretToken') ` 

User Schema에 token field에 넣어 주고 저장 한다.


```js
    user.token = token
    user.save(function(err,user){
        if(err) return cb(err)
        cb(null,user)
    })
```

쿠키 혹은 로컬스토리지에 토큰을 저장해줘야 하는데 여기에선 쿠키를 이용한다.

express에서 제공하는 cookieparser라는 라이브러리가 추가로 필요하다. 

`npm install cookie-parser --save`

>index.js

```js
  const cookieParser = require('cookie-parser')
  app.use(cookieParser())
```


```js
 user.generateToken((err,user)=>{
          if(err) return res.status(400).send(err);

             // 쿠키, 로컬스토리지 등에 토큰을 저장
            res.cookie('x_auth',user.token)
            .status(200)
            .json({loginSuccess: true, userId: user._id})


```

cookie는 두개의 인자를 받는데 첫번째 인자는 쿠키의 네임, 두번째 인자는 벨류값이 된다.


