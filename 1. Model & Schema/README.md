# MongoDB Model & Schema

Model은 Schema를 감싸주는 역할을 하는것이다.
그렇다면 Schema는 무엇일까?
MongoDB는 테이블이 없는 nosql입니다.
다큐먼트에 아무거나 넣어도 에러가 생기지 않습니다.
실제로 사용하다보면 아무거나 다 들어가서 문제가 생기는데 이러한 문제를 해결하기 위해 Schema라는것이 생겼습니다.
사용자가 작성한 Schema를 기준으로 데이터를 DB에 넣기전에 먼저 검사합니다.
작성한 Schema에 어긋나는 데이터가 있으면 에러를 발생시킵니다.

```
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
이것이 바로 Schema라고하는것인데 보통 한 파일에 하나의 스키마를 작성하고 스키마가 커지면 여러 파일로 분산하여 만듭니다.</br>
name password lastname 등이 속성명으로 필드의 이름이 됩니다.</br>
email은 타입명이 문자열이며 공백(trim)을 제거하고 유일(unique)해야 한다고 정해주었습니다.</br>
이런식으로 기준을 작성하고 Model으로 감싸줍니다. </br>
('모델이름',스키마명)으로 작성합니다.</br>
</br>
``
const User = mongoose.model('User', userSchema)
``
</br>
이런식으로 작성합니다.</br>
이 스키마를 다른 파일에서도 사용 할 수 있도록 내보내기를 해줍니다.
</br></br>
``
module.exports = { User }
``

