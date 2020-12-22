# 키 값 숨기기

소스코드중에 API Key나 개인정보가 들어있는 코드가 존재 할때 그 값을 보호하지 않고 그대로 올리게되면 개인정보가 노출되는 불상사가 일어납니다.

이러한 일을 방지하기 위해 키 값들을 따로 파일로 보관하고 gitignore에 추가하여 업로드시 제외되도록 설정합니다.
gitignore 파일에 폴더명 혹은 파일명을 입력해놓으면 해당 파일은 업로드가 되지 않습니다.

개발 할 때는 두가지 환경이 있습니다.
1. local development
2. Deploy 한 후 (production)

두가지 환경에 따로 생각을 해야합니다.
로컬 환경에서는 변수를 내부 파일에서 가져올 수 있습니다.  
heroku를 사용한다고 가정했을 때에는 따로 value와 key값을 사이트에서 직접 넣어야합니다.  
두가지 환경에 사용 할 수 있도록 분기처리를 해보겠습니다.  

하나의 폴더에 3개의 파일을 만들었습니다.</br>
![config](https://user-images.githubusercontent.com/61371367/102901007-71da5900-44b0-11eb-8690-8a856144e80e.jpg)</br>

`dev.js`
```js
module.exports = {
    mongoURI:'mongoDB키값'
}

```
- dev.js 파일에는 몽고DB 키값을 넣어주었습니다.</br>

`key.js`
```js
if(process.env.NODE_ENV === 'production'){
    module.exports = require('./prod')
}else{
    module.exports = require('./dev')
}
```
- key.js에는 환경변수를 지정해주었습니다. 만약 production mode에 있을때에는 prod.js 파일에서 가지고오고   
  development mode이면 dev.js 파일에서 가지고 오도록 설정했습니다.
  
`prod.js`
```js
  module.exports = {
    mongoURI:process.env.MONGO_URI
}
```
- production mode 일 경우에 키 값입니다. 뒤에 MONGO_URI는 heroku의 config 값과 동일 해야합니다.


이제 필요한 키 값을 가지고 와서 써야합니다.
index.js 파일에서 먼저 import 해옵니다.
```js
const config = require('./config/key')

```
```js
mongoose.connect(config.mongoURI,{
  useNewUrlParser:true, useUnifiedTopology:true,useCreateIndex:true,useFindAndModify:false
}).then(()=>console.log('연결됨'))
.catch(err=>console.log(err,'왜에러요'))
```

import를 해온뒤에 주소를 넣는곳에 불러온 값을 넣어줍니다.
저장 후 실행해보면 정상적으로 작동하는것을 확인 할 수 있습니다.

이제 업로드를 하기 전에 key값이 적혀 있는 dev.js를 gitignore 파일에 넣어줍니다.
`gitignore`
```
node_modules
dev.js
```
위와 같이 dev.js 파일을 추가해줍니다!
의식의 흐름대로
```
node_modules,
dev.js
```
이렇게 콤마를 넣었다가 node_modules 폴더가 함께 업로드 되는 불상사를 겪었습니다.</br>
**콤마** 넣지마세요!

항상 보호 해야 할 정보는 따로 모아서 관리하고 gitignore에 추가해주세요!
