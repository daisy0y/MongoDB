웹사이트는 여러 페이지중 모든 유저가,로그인 된 경우에만, 관리자 계정만 사용 가능한 페이지로 나뉘어져있다.

현재 사용중인 유저의 상태를 체크하기 위해 Auth 기능을 추가한다.

## Route 생성

> index.js

```js
const { auth } = require('./middleware/auth')
app.get('api/users/auth', auth , (req, res)=>{})
```

두번째 인자에 auth는 미들웨어이다. 

auth 미들웨어는 엔드포인트 'api/users/auth' 에서 request를 받고 콜백 함수 실행전에 무언 행위를 해준다.

먼저 import 해주었지만 생성하지 않았기 때문에 생성해준다.

root 디렉토리에 별도의 middleware 폴더를 생성하고 auth.js 파일을 생성한다.

> auth.js

```js
const { User } = require("../models/User");
let auth = (req,res,next) => {}
    // 인증처리

// 클라이언트 쿠키에서 토큰을 가져온다. index.js에서 cookie parser 사용
        let token = req.cookies.x_auth;
    // 토큰을 복호화 한후 유저를 찾는다. findByToken은 User.js에 생성
        User.findByToken(token, (err,user)=>{
            if(err) throw err;
            if(!user) return res.json({isAuth:false,error:true})

            req.token = token;
            req.user = user;
            next()
        })
    //  유저가 있으면 인증 완료

    // 유저가 없으면 인증 실패
module.exports = { auth };

```

> index.js

```js

userSchema.statics.findByToken = function (token,cb ){
    var user = this;
    //  토큰을 descode한다.
    jwt.verify(token, secretToken,function(err,decoded){
        // 유저 id를 이용해서 유저를 찾은 다음 
        //클라이언트에서 가져온 token과 db에 보관된 토큰이 일치하는지 확인

        user.findOne({"_id":decoded,"token": token}, function(err,user){

            if(err) return cb(err);
            cb(null, user)
        })
    })

    jwt.verify(token, 'shhhhh', function(err, decoded) {
        console.log(decoded.foo) // bar
      });
}

```

> index.js
```js

app.get('api/users/auth', auth , (req, res)=>{
      // 여기까지 미들웨어 통과시 Auth이 true
      res.status(200).json({
        _id: req.user._id,
        isAdmin: req.user.role === 0 ? false : true,
        isAuth: true,
        email: req.user.email,
        name: req.user.name,
        lastname: req.user.lastname,
        role: req.user.role,
        image: req.user.image
      })
})

```

Auth가 true이면 id,admin여부, 이메일등의 정보를 알려준다.
