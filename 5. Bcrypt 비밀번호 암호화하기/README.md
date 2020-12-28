# 비밀번호 암호화 하기

그냥 데이터를 전송하게 되면 비밀번호와 같이 암호화가 필요한 중요 정보가 가공 없이 그대로 데이터베이스에 들어오게 됩니다.</br>
데이터를 받는 사람 또한 중요 정보를 볼 수 없게 암호화가 필요 합니다.</br>
데이터를 암호화하기 위해 Bcrypt 라이브러리를 이용할 수 있습니다.</br>

>bcrpyt 설치</br>
`npm install bcrypt --save`</br>
[홈페이지](https://github.com/kelektiv/node.bcrypt.js#readme)


> 순서</br>

1. Register Router이동
 ```js
 app.post('/register',(req,res)=>{
    // 회원 가입 할 때 필요한 정보들을 클라이언트에서 가지고 오면 
    // 그 데이터를 데이터 베이스에 넣어준다.
    
    const user = new User(req.body)
    user.save((err,userInfo)=>{
      if(err) return res.json({success:false, err})
      return res.status(200).json({
        success:true
      })
    } 

)})

 ```
 - user.save에서 값이 저장 되기전에 암호화 과정을 거쳐야 한다.
 2. User Model(UserSchema)로 이동
 ```js
 userSchema.pre('save',function(next){
    var user = this;

    if(user.isModified('password')){
  //비밀번호를 암호화 한다
        bcrypt.genSalt(saltRounds, function(err, salt) { 
        // 만약 에러가 나면 에러를 가지고 바로 user.save로 이동
            if(err) return next(err);
        // salt를 제대로 생성한 후에 행위
                bcrypt.hash(user.password, salt, function(err,hash){
                    if(err) return next(err)
                    user.password = hash
                    next()
                })
            });
           }
           else{
              next()
            }
})

 ```
 - `userSchema.pre()`는 mongoose에서 가지고 온 메소드이다. 안에 'save'를 입력하면 유저정보를 저장하기전에 어떠한 행위를 하게 하는것이다.
   두번째 파라미터로는 어떤 행위를 할것인지에 대한 함수가 들어온다.
   두번째 파라미터 함수에는 next를 파라미터로 줘서 행위가 끝나면 Regiser router로 이동시킨다.
   
 - 설치한 bcrpyt를 가지고 온다 `const bcrypt = require('bcrypt'); `
 
 3. Salt 생성
 - salt를 생성하고 그 Salt로 암호화를 한다
 - saltRounds를 성정한다(salt가 몇 글자인지 설정)
 - salt를 이용해서 비밀번호를 암호화 한다.
 - `const saltRounds = 10;` 
 - bcrypt.hash()의 첫번째 인자는 myPlaintextPassword를 받는다. myPlaintextPassword란 암호화되지 않은 비밀번호이다.</br>
 userSchema의 password를 받아오면 된다. ` var user = this; ` this는 userSchema를 가르키고있다. 
 
 >**주의** arrow function을 이용하면 this가 제대로 작동하지 않는다
 
 -  `bcrypt.hash(user.password, salt, function(err,hash) )` userSchema의 password를 가지고온다.
 - 두번째 인자로는 salt를 받아오는데 위에서 받아오기 때문에 salt를 입력하고 세번째 인자로는 함수를 받는다.
    (err,hash) 에러와 hash를 받는데 암호화에 성공 했다면  `user.password = hash`에서 암호화된 비밀번호를 password에 넣고 next()함수를 통해 user.save로 이동한다.
   
 
 여기서 한가지 더 해줘야 할 것이 있다.
 비밀번호를 변경 할 경우도 있지만 이름,이메일등 다른것을 변경 할때도 있는데, 이렇게 하게되면 다른것을 변경 할 때 마다 암호화를 다시 하게 된다.
 비밀번호를 바꿀때만 실행되도록 조건을 걸어줘야 한다.
 ` if(user.isModified('password'))`  model filed중에 password가 변경 될때만 비밀번호를 암호화 해준다.
 
 
### POST 결과
![image](https://user-images.githubusercontent.com/61371367/103218181-b86efe00-495d-11eb-99cd-f7db1c2c5dc7.png)
암호화 이전값과 암호화 이후값이 정상적으로 작동하였다. 
