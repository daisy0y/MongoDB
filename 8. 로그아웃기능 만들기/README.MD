로그아웃 라우트 생성
로그아웃하려는 유저를 데이터베이스에서 찾아서
그유저의 토큰을 삭제한다.


> 로그아웃 라우트 생성 index.js
```js
app.get('/api/users/logout', auth, (req,res)=>{

    User.findOneAndUpdate({_id: req.user._id},
      {token: ""},(err,user)=>{
        if(err) return res.json({succes:false , err});
        return res.status(200).send({
          success:true
        })
      })
})
```

인자로 앤드포인트와 로그인된 상태이기때문에 auth 미들웨어와 콜백함수를 넣어준다.

콜백함수에는 로그아웃하려는 유저를 findOneAndUpdate 메소드를 통해 데이터베이스에서 찾는다.

두번째 오브젝트에서는 토큰을 지워주고 콜백함수로 boolean 결과에 따른 행위를 정의해준다.

POSTMAN에서 로그인된 계정을 /api/users/logout 해당 앤드포인트로 send보내면 정상적으로 로그아웃 되는것을 확인 할 수 있다.
