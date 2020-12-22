# Body Parser & Postman을 이용한 회원가입 기능 만들기

### Client - Server
브라우저(ex 크롬..)를 client라고 보면되고 예를 들어 네이버 회원가입을 하려고 하면 이름,비밀번호등 각종 데이터를 입력한 후
그 데이터를 서버로 보내야한다. MongoDB등을 통하여 서버를 만든다.
데이터를 body에 담아 POST request를 보내고자 할때 undefined error를 마주하게 되는데 해결방안으로 body-parser를 사용한다.</br>
``
npm install body-parser --save
``
</br>

현재 만들어진 client가 없기 때문에 postman을 이용해서 데이터를 전송할것이다.</br>
https://www.postman.com/ 해당 사이트에서 회원가입을 하고 다운로드

</br>

### Register Route 만들기

```js
const mongoose = require('mongoose');

const userSchema = mongoose.Schema({
    name:{
        type:String,
        maxlength:50
    },
    email:{
        type:String,
        trim:true,
        unique: 1
    },
    password:{
        type:String,
        minlength:5
    },
    lastname:{
        type:String,
        maxlength:50
    },
    role:{
        type:Number,
        default:0
    },
    image:String,
    token:{
        type:String
    },
    tokenExp:{
        type:Number
    }
})

const User = mongoose.model('User', userSchema)
module.exports = { User }
```
위의 준비된 Schema 데이터와 body Parser를 index.js에 import 해온다.
```
const { user } = require("./models/User")
const bodyParser = require('body-parser')
```  
이제 BodyParser에 옵션을 준다.
```js
//application/x-www-form-urlencoded 이렇게 된 데이터를 분석해서 가지고 온다.
app.use(bodyParser.urlencoded({extended:true}));
//application/json 이렇게 된 데이터를 분석해서 가지고 온다.
app.use(bodyParser.json());
```



```js
app.post('/register',(req,res)=>{
    //회원가입 할 때 필요한 정보들을 client에서 가져오면 그 데이터를 데이터베이스에 넣어준다
    
    Const user = new User(req.body) //인스턴스를 만든다 req.body에는 {name:"hello",password:"1234"}이런식으로 데이터가 들어있는것. 이것은 body-parser를 이용해서 받는것이다.
    
    //user.save는 mongoDB의 메소드
    user.save((err,userInfo)=>{
      if(err) return res.json({success:false,err}) // 만약 저장을 할때 에러가 있으면 클라이언트에 에러가 있다고 전달해주는데, json형식으로 전달해주는것
      return res.status(200).json({success:true 
      })
    })
    
})
```
status(200)은 성공했다는 http 상태코드이다 [여기](https://developer.mozilla.org/ko/docs/Web/HTTP/Status)에서 다른 코드들을 확인 해 볼수 있다.
 </br>

Postman에서 post requset로 설정하고 URL은 http://localhost:(설정한포트)/설정한주소
위의 경우에는 http://localhost:5000/register 이다</br>

Body를 클릭하고 raw를 체크하고 json설정 후 그 안에 데이터를 입력하면된다.
```
{
  "name":"lee",
  "email":"lee@naver.com",
  "password":"1234"
}
```
**계속해서 에러가 떠서 찾아본 결과 몽고DB 홈페이지에서 network access -> ip access list에 내 아이피를 추가했어야했다.
추가하고 실행 해본 결과 정상작동 ** 

설정한 schema에서 필수값으로 지정하지 않았기 때문에 다른 값을 다 넣어주지 않아도 된다.
값을 입력 한후 send를 하면 정상적으로 success:true를 확인 할 수 있다.
