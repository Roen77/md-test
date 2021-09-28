# 메모앱

# 1. Client

## 버전

|vue|
|:---:|
|v2.6.11|

** vuetify 사용으로 vue 버전은 3 버전이 아닌 2 버전을 사용하였습니다.

<br>


이 부분은 [Google] [1]을 참조하시면 됩니다.

이 부분도 [Google] [1]을 참조하시고 저 부분은 [Facebook] [2]을 참조하세요.

[1]: http://www.google.com
[2]: http://www.facebook.com

<br>



이 부분은 [Google]를 참조하시면 됩니다.

이 부분도 [Google]를 참조하시고 저 부분은 [Facebook]을 참조하세요.

[Google]: http://www.google.com

**testesttse!!**
## 사용한 라이브러리
[axios](https://axios-http.com/docs/intro)
블ㄹ랭크
||<a href="https://github.com/axios/axios"  target="_blank">axios</a>|<a href="https://router.vuejs.org/">vue-router</a>|<a href="https://dev.vuetifyjs.com/en/getting-started/installation/">vuetify</a>|<a href="https://vuex.vuejs.org/">vuex</a>|
|---|---|:---|:---|:---|
|버전|v0.21.4|v3.5.2|2.4.0|3.6.2|
|이유|HTTP 클라이언트 라이브러리로 서버와의 통신을 위해 사용|vue 라우터|vue 전용 디자인 프레임워크|상태 관리를 위한 vuex store 사용|

## 구현 목표
1. <a>회원가입/로그인</a>
2. <a>보드 추가 및 수정 및 삭제</a>
3. <a>보드 가져오기</a>
4. <a>카드 추가 및 수정 및 삭제</a>
5. <a>카드 가져오기</a>
6. <a>카테고리 추가 및 삭제</a>
7. <a>카드 검색</a>

<br>

## 구현 내용
### 라우터
```
/ ---
    |
    |--login
    |
    |--board/_id
    |
    |--card/_id
    |
    |--projects
```
### 구현 공통 요소
- **기본적으로 vuetify 디자인 프레임워크를 사용하여 구현하였습니다.
> <a href="https://vuetifyjs.com/en/introduction/why-vuetify/">vuetify 바로가기</a>**


- <b>기본적으로 vuetify 디자인 프레임워크</b>

<br>

- 모든 라우터는 `로그인` 시, 이용 가능하도록 구현하였습니다.

```js
//~/router/index.js
// 인증 필요
const isAuth=(to,from,next)=>{
    if(to.meta.auth && !store.getters.getUser){
        // 로그인이 필요하기 때문에 login할 수 있는 라우터로 리다이렉트 해준다.
        const loginPath = `/login?rPath=${encodeURIComponent(to.path)}`
        next(loginPath)
        return
      }
      next();
}
// 인증 불필요
const isnotAuth=(to,from,next)=>{
    if(!to.meta.auth && store.getters.getUser){
        // 이미 인증되었거나 인증이 필요없다면 라우터를 진행시킨다.
        next('/')
        return
      }
      next();
}

const router = new VueRouter({
    ...
    routes:[{
        path:'/',
        component:()=>import('../view/Boards.vue'),
        name:'boards',
        meta:{auth:true},
        beforeEnter:isAuth
    },
    ...
  ]
```

> <a href="https://router.vuejs.org/guide/advanced/navigation-guards.html">vue-router 가드</a>로 라우터 진입 전 로그인 인증 필요 여부 확인

<br>

- API 호출은 `axios`를 사용하였습니다.

> 모든 API 호출은 인증이 필요하도록 구현하였으므로, `axios`제공하는 <a href="https://axios-http.com/docs/interceptors">`Interceptor`</a>를 이욯하여
데이터 요청시, headers의 authorization에 토큰값 부여하도록 하였습니다.

<br>

`api/interceptors.js`
```js
// ~api/interceptors.js
import store from '../../store'
// axios interceptor를 이용해 headers의 authorization에 store에 저장한 토큰값을 부여
export const setInterceptors=(instance)=>{
instance.interceptors.request.use(function (config) {
    config.headers.authorization = store.state.token;
    return config;
  }, function (error) {
    return Promise.reject(error);
  });

  return instance
}
```
<br>

`api/index.js`
```js
// ~api/index.js

// axios 사용
import axios from 'axios';
import {setInterceptors} from './common/interceptors'

const instance = axios.create({
    baseURL:process.env.VUE_APP_API_URL
})

export const request=setInterceptors(instance)
```
<br>

|API 요청시 토큰값을 부여하는 이유|
|---|
|<a href="https://jwt.io/">jsonwebtoken</a>는  정보를 JSON 객체로 안전하게 전송하기위한 개방 표준으로, `headers`의 `Authorization`에 저장된 `token`으로 정보에 접근하도록 하기 위해 `axios Interceptors`를 이용해 요청을 보내기 전에 토큰값을 확인 및 저장해줍니다. |

<br>

- 이미지는 완전히 로드된 후, 보여질 수 있도록
이미지 로드 여부를 확인합니다.

`loadImage.js`
```js
// ~utils/loadImage.js
function loadImage (src) {
  return new Promise((resolve) => {
    const img = document.createElement('img')
    img.src = src
    img.addEventListener('load', () => {
      resolve()
    })
  })
}

export { loadImage }

```

### 공통 컴포넌트

#### PageNotFound 컴포넌트

```html
<!-- 라우터가 존재하지 않는다면 해당 컴포넌트를 보여줍니다. -->
<!-- ~/view/PageNotFound.vue -->
<template>
    <v-container>
        <v-row no-gutters>
            <v-col cols="12">
                <v-card elevation="2" shaped>
                    <v-card-title>해당 페이지는 존재 하지 않습니다.</v-card-title>
                    <v-card-subtitle class="text-overline mb-4"><router-link to="/">메인으로 돌아가기</router-link></v-card-subtitle>
                </v-card>
            </v-col>
        </v-row>
    </v-container>
</template>

```

> 라우터가 존재하지 않을 시, 보여줄 컴포넌트를 구현하였습니다.
```js
// ~/router/index.js
const router = new VueRouter({
  mode: 'history',
  routes: [
    ...{
      path: '*',
      component: () => import('../view/PageNotFound.vue')
    },
  ]

})
```


<br>

#### ErrorPage 컴포넌트

> 오류 발생시,보여줄 컴포넌트를 구현하였습니다.
```html
<!-- ~component/common/ErrorPage.vue -->
<template>
    <v-container v-if="hasError">
        <v-row no-gutters>
            <v-col cols="12">
                <template v-if="errMsg.status">
                    <v-card elevation="2" shaped>
                        <v-card-title>{{errMsg.status}}에러</v-card-title>
                        <v-card-subtitle v-if="errMsg.data">{{errMsg.data.msg}}</v-card-subtitle>
                    </v-card>
                </template>
            </v-col>
        </v-row>
    </v-container>
</template>
```

<br>

`state`

```js
export default {
    computed:{
        ...mapState(['hasError','errMsg'])
    }
}
```
|state|타입|설명|
|:---|:---|:---|
|hasError|Boolean|에러 여부|
|errMsg|String|에러 발생시, 보여줄 메세지|
<br>

#### Spinner 컴포넌트

> 스피너를 구현하여 데이터를 가져오기전에 로딩화면이 보여지도록 구현하였습니다.
 <br>
 ```html
 <!-- ~/components/common/Spinner.vue -->
<template>
  <div class="lds-facebook" v-if="loading">
    <div>
    </div>
    <div>
    </div>
    <div>
    </div>
  </div>
</template>
 ```
<br>

`state`
 ```js
export default {
  computed:{
    ...mapState(['loading'])
  }
}
```
|state|타입|설명|
|:---|:---|:---|
|loading|Boolean|스피너(로딩) 진행 유무|

<br>


#### AlerConFirm 컴포넌트
> 데이터 삭제 시, 한번 더 사용자가 확인할 수 있도록 구현하였습니다.
```html
<!-- ~/components/common/AlerConFirm.vue -->
<template>
  <v-row justify="center">
    <v-dialog
        :value="value"
      max-width="500"
    >
      <v-card>
        <v-card-title class="headline">
          {{data}} 을/를 삭제하시겠습니까??
        </v-card-title>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn
            color="green darken-1"
            text
            @click="$emit('disagree')"
          >
            닫기
          </v-btn>
          <v-btn
            color="green darken-1"
            text
            @click="$emit('agree')"
          >
            삭제
          </v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
  </v-row>
</template>
```
`props`
```js
export default {
    props:{
        value:{
            type:Boolean,
            required:true
        },
        data:{
            type:String || Number,
            required:true
        }
    }
}
```
|props|타입|설명|
|:---|:---|:---|
|value|Boolean|알림창을 보여줄지 확인|
|data|String,Number|삭제할 데이터의 제목|

> `vuetify`에서 제공하는 <a href="https://vuetifyjs.com/en/components/dialogs/#usage">v-dialog</a>로 팝업창을 구현

<br>

#### AlertBar 컴포넌트
> 사용자에게 빠르게 알림 메세지를 보여줍니다.

```html
<!-- ~/components/common/AlertBar.vue -->
<template>
    <v-snackbar v-model="alert.success" :timeout="alert.timeout" color="teal" transition="scale-transition">
        {{ alert.text }}
    </v-snackbar>
</template>
```
<br>

`state`

```js
export default {
    computed: {
        ...mapState(['alert'])
    }
}
```
```js
// ~/store/state.js

    alert:{
        success:false,
        text:'',
        timeout:3000
    }
```
|state|타입|설명|
|:---|:---|:---|
|top|Boolean|.|
|bottom|Boolean|.|
|right|Boolean|.|
|left|Boolean|.|
|transition|String|.|
|menus|Array|.|


> `vuetify`에서 제공하는 <a href="https://vuetifyjs.com/en/components/snackbars/#props">v-snackbar</a>로 알림창을 구현하였습니다.

<br>

#### ActionMenu 컴포넌트
> 플로팅 메뉴

```html
<template>
    <v-speed-dial class="action_menu" v-model="fab" :top="top" :bottom="bottom" :right="right" :left="left" :direction="direction"
        :open-on-hover="hover" :transition="transition">
        <template v-slot:activator>
            <v-btn v-model="fab" color="blue darken-2" dark fab>
                <v-icon v-if="fab">
                    mdi-close
                </v-icon>
                <v-icon v-else>
                    mdi-menu
                </v-icon>
            </v-btn>
        </template>
        <v-tooltip bottom v-for="(menu,index) in menus" :key="index">
      <template v-slot:activator="{ on, attrs }">
      <v-btn  @click="$emit('onClickBtn',index)" v-on="on" v-bind="attrs" fab dark small :color="menu.color">
            <v-icon>{{menu.icon}}</v-icon>
        </v-btn>
      </template>
      <span>{{menu.btnTxt}}</span>
    </v-tooltip>
    </v-speed-dial>
</template>
```

<br>

`props`
```js
export default {
   props:{
          top:{
              type:Boolean,
              required:true
          },
          bottom:{
              type:Boolean,
              required:true
          },
          right:{
              type:Boolean,
              required:true
          },
          left:{
              type:Boolean,
              required:true
          },
          direction:{
              type:String,
              required:true
          },
         hover:{
              type:Boolean,
              required:true
          },
         transition:{
              type:String,
              required:true
          },
          menus:{
              type:Array,
              required:true
          }
      },
}
```
|props|타입|설명|
|:---|:---|:---|
|top|Boolean|css `position`속성의 `top`위치|
|bottom|Boolean|css `position`속성의 `bottom`위치|
|right|Boolean|css `position`속성의 `right`위치|
|left|Boolean|css `position`속성의 `left`위치|
|direction|String|메뉴가 보여줄 위치|
|transition|String|메뉴를 보여줄 때 `transition` 애니메이션|
|menus|String,Number|메뉴 내용|

<br>


> `vuetify` 에서 제공하는<a href="https://vuetifyjs.com/en/components/floating-action-buttons/#speed-dial">v-speed-dial</a>를 이용해 버튼을 구현하였습니다.

<br>

### Store
> `vuex`의 `store`를 통해 데이터 관리하도록 구현
```js
// ~/store/index.js
import Vue from 'vue';
import Vuex from 'vuex';
import state from './state'
import mutations from './mutations'
import actions from './actions'
import getters from './getters'

Vue.use(Vuex);

const store = new Vuex.Store({
  state,
  getters,
  mutations,
  actions
})

export default store;
```

> `store`를 모듈화하여 정리하였스니다.

<br>


### 믹스인
`FomrMixin.js`
<br>

 유효성 검사 관련하여 공통된 요소들 정리하였습니다.

> 로그인/회원가입 시 유효성 검사와 보드/카드 데이터 추가시 유효성 검사시 사용


```js
// ~mixin/FomrMixin.js
export default {
  data(){
    return {
      // 유효성 검사시, 유효하지 않을 때 보여줄 메세지
      errmsg:'',
      // 유효성 검사시, 해당 데이터가 유효한지 여부
      valid: true,
    }
  },
    mounted(){
     // 내가 지정한 인풋 태그 요소에 포커스되도록 구현
      if(!this.$refs.input) return
      setTimeout(()=>{
            this.$refs.input.focus()
       },200)
      },
    methods: {
    // 유효성 검사 확인
       validate () {
        this.$refs.form.validate()
      },
      // 입력폼 초기화
      reset () {
        this.$refs.form.reset()
      },
      // 입력폼 유효성 검사 초기화
      resetValidation () {
        this.$refs.form.resetValidation()
      },
    },
}

```
<br>

`vuetify`에서 제공하는 <a href="https://vuetifyjs.com/en/components/forms/#rules">v-form</a>의 유효성 검사를 사용하였습니다.

> <a href="https://www.w3schools.com/howto/howto_js_password_validation.asp">유효성 검사 정규식 참고 문서</a>

<br>

`LoginMixin.js`


로그인/회원가입 시 사용되는 `email`,`password` 대해 유효성을 확인합니다.

> 회원가입/로그인시, 공통된 요소를 정리하였습니다.
```js
// ~/mixin/LoginMixin.js

import FormMixin from '../mixin/FormMixin'
export default {
    // 폼요소에 공통적으로 사용되는 믹스인
    mixins: [FormMixin],
    data() {
        return {
            email: '',
            password: '',
           emailRules: [
                v => !!v || '이메일은 필수입니다.',
                v => /.+@.+/.test(v) || '이메일 양식으로 입력해주세요',
              v => v && v.length<=20 || '이메일은 20자리 이하로 입력해주세요',
            ],
              // 비밀번호 필수 입력 및 최소8자리 이상 작성되고 숫자와 특수문자를 포함하도록 규칙을 정해줍니다.
            passwordRules: [
                v => !!v || '비밀번호는 필수입니다.',
              v => v && v.length >= 8 && v.length<=20 || '비밀번호는 최소 8자리 이상 20자리 이하로 입력해주세요',
                v=> /^(?=.*[0-9])(?=.*[!@#$%^&*])[a-zA-Z0-9!@#$%^&*]{8,20}$/.test(v) ||'비밀번호는 최소 숫자와 특수문자가 포함되어야 합니다.'
            ],
        }
    },

}
```
<br>


> `vutify`에서 제공하는 `rules`를 사용해 `input`태그에 입력되는 데이터의 규칙을 정할 수 있습니다.(<a hreef="https://vuetifyjs.com/en/components/inputs/#rules">input rules</a> 참고)

> DB에서 각각 `email`은 문자열 50자까지, `password`는 문자열 100자까지 가능하도록 제한해주었기 때문에 오류 방지를 위해 길이값을 확인합니다.

<br>

`FetchMixin.js`
> 데이터를 가져오는 API를 공통요소로 구현하였습니다.
```js
export default {
    created() {
        this.fetchData()
      },
      methods: {
    ...mapActions(['FETCHLISTS']),
    fetchData(){
        // 데이터 가져오기
        this.FETCHLISTS({routeName:this.$route.name,id:this.$route.params?this.$route.params.id:''})
         }
    }
}
```
`created`훅으로 인스턴스가 생성된 후 데이터를 가져오는 API를 호출합니다.

<br>


## 상세 구현 내용
## 1. 회원가입/로그인
### 1-1. 회원가입/로그인 공통 요소
<b>`vuetify`의  <a href="https://vuetifyjs.com/en/components/windows/#account-creation">v-window</a> 사용으로 로그인폼과
회원가입폼을 보여주도록 구현하였습니다.</b>

> `v-window`는 한 창에서 다른 창으로 콘텐츠를 전환하기위한 기본 기능을 제공합니다.
```html
<!-- view/Login.vue -->
<template>
    <v-container class="fill-height" fluid>
        <v-row align="center" justify="center">
            <v-col cols="12" sm="8" md="8">
                <v-card class="elevation-12">
                    <v-window v-model="step">
                        <v-window-item :value="1">
                            <login-form @increaseStep="onIncreseStep"></login-form>
                        </v-window-item>
                        <v-window-item :value="2">
                            <register-form @decreaseStep="onDecreaseStep"></register-form>
                        </v-window-item>
                    </v-window>
                </v-card>
            </v-col>
        </v-row>
    </v-container>
</template>

```
<br>

`data`
```js
    data() {
        return {
            step: 1
        }
    }
```
> `v-window`에 `v-model`로 `step`을 바운딩 시켜 창을 전환시킵니다.

<br>

`LoginForm 컴포넌트`

```js
// ~components/form/LoginForm.vue

 // 기존데이터를 초기화 시키고,로그인폼과 회원가입폼 교체합니다. (버튼 클릭시, 로그인폼으로 이동)
  methods: {
   onChangeForm(){
          this.reset()
          this.$emit('increaseStep')
      }
  }
```

<br>

`RegisterForm 컴포넌트`
```js
// ~components/form/RegisterForm.vue

 // 기존데이터를 초기화 시키고,로그인폼과 회원가입폼 교체합니다. (버튼 클릭시, 로그인폼으로 이동)
    methods: {
      onChangeForm(){
          this.reset()
          this.$emit('decreaseStep')
      }
    }
```

<br>

<b>사용자 정보 저장</b>
> 로그인/회원가입 시, 브라우저의 쿠키에 사용자 정보를 저장합니다.

```js
// ~/utils/cookie.js
// 쿠키에 토큰 저장
function saveAuthToCookie(value) {
  document.cookie = `memo_auth=${value}`;
}
// 쿠키에 사용자 정보 저장
function saveUserToCookie(value) {
  document.cookie = `memo_user=${value}`;
}
// 쿠키에 저장된 토근 정보 가져오기
function getAuthFromCookie() {
  return document.cookie.replace(
    /(?:(?:^|.*;\s*)memo_auth\s*=\s*([^;]*).*$)|^.*$/,
    '$1',
  );
}
// 쿠키에 저장된 사용자 정보 가져오기
function getUserFromCookie() {
  return document.cookie.replace(
    /(?:(?:^|.*;\s*)memo_user\s*=\s*([^;]*).*$)|^.*$/,
    '$1',
  );
}
// 로그아웃 시, 쿠키에 저장된 정보 삭제
function deleteCookie(value) {
  document.cookie = `${value}=; expires=Thu, 01 Jan 1970 00:00:01 GMT;`;
}

export {
  saveAuthToCookie,
  saveUserToCookie,
  getAuthFromCookie,
  getUserFromCookie,
  deleteCookie
}
```
<br>

### 1-2. 로그인
|컴포넌트|라우터|
|---|---|
|LoginForm|/login|

<br>

<b>로그인 버튼 클릭</b>

> `store`의  `actions`함수 `LOGIN` 호출
```js
// ~/components/form/LoginForm.vue
 methods: {
     ...mapActions(['LOGIN']),
     async login() {
         try {
             // 성공적으로 로그인 시, 메인페이지로 라우터를 이동시킵니다.
             await this.LOGIN({
                 email: this.email,
                 password: this.password
             })
             this.$router.push('/')
         } catch (error) {
             // 오류 발생시, 메세지를 사용자에게 보여줍니다.
             this.errmsg = error.response.data.msg
         }
     }
 }
```
<br>

<b> `API` </b>
> `axios`를 이용해 로그인 API 호출

```js
// ~/api/auth.js
import {request} from './index'
export const auth={
    loginUser(userInfo){
        return request.post('user/login',userInfo)
    },
}
```
> <a href="#">`axios`에 관한 내용은 위의 공통 구현 요소에서 정리하였습니다.</a>

<br>

<b> `store` </b>

|actions|
|---|
|LOGIN|

```js
// ~/store/actions.js
import {auth} from '../api/auth'
 // 로그인
   // 로그인
    async LOGIN({commit},userInfo){
      const {data}= await auth.loginUser(userInfo)
      commit('SET_USER',data)
      return data
    }
```
<br>

|<div id="token">mutations</div>|
|---|
|SET_USER|

```js
// ~/store/actions.js
import {saveAuthToCookie,saveUserToCookie,deleteCookie} from '../../utils/cookie'

 SET_USER(state,data){
        state.user=data.user.nickname
        state.token=data.token
        // 쿠키에 토큰과 사용자의 닉네임 정보 저장
        saveAuthToCookie(data.token)
        saveUserToCookie(data.user.nickname)
    },
```
> 쿠키에 사용자의 닉네임과 토큰값을 추가적으로 저장합니다.

<br>

|쿠키에도 따로 저장하는 이유|
|---|
|새로고침시, `store`에 저장된 정보는 유지되지 않으므로, 브라우저의 쿠키에 필요한 데이터를 사용하기 위해 저장하도록 구현하였습니다.|
|`nickname`정보는 사용자의 닉네임이 새로고침시에도 유지되도록 하기 위해서 저장합니다.|
|`token`정보로 사용자의 로그인 유무를 구분하기 때문에,로그인 시 새로고침할 때 로그인 정보가 유지되도록 하기 위해서 저장합니다.|

> <a href="#">쿠키에 관련된 요소들은 함수로 따로 만들어 정리하였습니다.</a>

<br>

|getters|
|---|
|getToken|
| getUser|
```js
    // 토큰 정보 가져오기
    getToken(state){
        return state.token
    },
    // 사용자 정보 가져오기
    getUser(state) {
        return state.user
      },
```
<br>

|state|
|---|
|user|
|token|
```js
// ~/store/state.js
   import {getAuthFromCookie,getUserFromCookie} from '../../utils/cookie'
export default {
    // 쿠키에 저장된 정보를 가져오고,없으면 빈문자열을 저장합니다.
    user:getUserFromCookie() ||'',
    token:getAuthFromCookie() || '',

}
```
> <a href="#">쿠키에 관련된 요소들은 함수로 따로 만들어 정리하였습니다.</a>

<br>

### 1-3. 회원가입
|컴포넌트|라우터|
|---|---|
|RegisterForm|/login|


<b>회원가입 버튼 클릭</b>

> `store`의 `actions`함수 `REGISTER` 호출
```js
// ~/components/form/RegisterForm.vue
 methods: {
   ...mapActions(['REGISTER']),
        async userRegister(){
         try {
          // 성공적으로 회원가입 시, 메인페이지로 라우터를 이동시킵니다.
             await this.REGISTER({email:this.email,password:this.password,nickname:this.nickname})
            this.$router.push('/')
         } catch (error) {
              // 오류 발생시, 메세지를 사용자에게 보여줍니다.
                console.log(error)
               this.errmsg = error.response.data.msg
         }
      }
 }
```

<br>

<b>`API`</b>

```js
// ~/api/auth.js
import {request} from './index'
export const auth={
    registerUser(userInfo){
        return request.post('user/register',userInfo)
    },
}
```
> <a href="#">`axios`에 관한 내용은 위의 공통 구현 요소에서 정리하였습니다.</a>

<br>

<b>`store`</b>

|actions|
|---|
|REGISTER|

```js
// ~/store/actions.js
import {auth} from '../api/auth'
  // 회원가입
    async REGISTER({commit},userInfo){
     const {data}= await auth.registerUser(userInfo)
     console.log(data)
      commit('SET_USER',data)
      return data
    },
```
<br>

|mutations|
|---|
|SET_USER|

```js
// ~/store/actions.js
import {saveAuthToCookie,saveUserToCookie,deleteCookie} from '../../utils/cookie'

 SET_USER(state,data){
        state.user=data.user.nickname
        state.token=data.token
        // 쿠키에 토큰과 사용자의 닉네임 정보 저장
        saveAuthToCookie(data.token)
        saveUserToCookie(data.user.nickname)
    },
```

> 로그인 뿐아니라 회원가입 진행시에도 회원정보를 저장시킵니다.

> <a href="#">쿠키에 관련된 요소들은 함수로 따로 만들어 정리하였습니다.</a>

<br>

|회원가입시에도 사용자 정보를 저장하는 이유|
|---|
|회원가입이 성공적으로 진행되면, 로그인을 생략하고 바로 회원가입한 정보로 로그인이 진행하도록 하기위해, `mutations`을 이용해 `state`의 `user`객체에 사용자 정보를 저장합니다.|
<br>



|getters|
|---|
|getToken|
| getUser|
```js
    // 토큰 정보 가져오기
    getToken(state){
        return state.token
    },
    // 사용자 정보 가져오기
    getUser(state) {
        return state.user
      },
```
<br>

|state|
|---|
|user|
|token|
```js
   import {getAuthFromCookie,getUserFromCookie} from '../../utils/cookie'
export default {
        // 쿠키에 저장된 정보를 가져오고,없으면 빈문자열을 저장합니다.
    user:getUserFromCookie() ||'',
    token:getAuthFromCookie() || '',

}
```
> <a href="#">쿠키에 관련된 요소들은 함수로 따로 만들어 정리하였습니다.</a>

<br>

## 2. 데이터(보드,카드) 가져오기

### 2-1. 데이터 가져오기 공통 구현 요소

<b> `믹스인`을 이용해 데이터를 가져오는 API를 호출합니다.</b>

```js
// ~/view/Board.vue
export default {
   mixins: [FetchMixin]
}
```
<br>

`FetchMixin`
```js
import {mapActions} from 'vuex';
export default {
    created() {
        this.fetchData()
      },
      methods: {
    ...mapActions(['FETCHLISTS']),
    fetchData(){
        // 데이터 가져오기
        this.FETCHLISTS({routeName:this.$route.name,id:this.$route.params?this.$route.params.id:''})
         }
    }
}
```
> `Boards.vue`, `Board.vue`, `BCard.vue`에서 `created`훅으로 데이터를 가져오는 API를 호출합니다.

|_|Boards.vue|Board.vue|BCard.vue|
|---|---|---|---|
|라우터|/|/board/_id|card/_id|
|데이터|보드 데이터를 불러옵니다.|보드 안에 있는 카드 데이터들을 불러옵니다.|카드 데이터(단일)를 불러옵니다.|

<br>


<b>`API`</b>

> `axios`를 이용해 데이터를  가져오는  API 호출

```js
// ~/api/list.js
import {request} from './index'
//보드,카드
export const list={
    // 보드,카드 가져오기
    fetchs(payload){
        return payload.id?request.get(`${payload.routeName}/${payload.id}`): request.get(`${payload.routeName}`)
    }
}
```

> `routeName`으로 `보드` 데이터와 `카드`데이터를 불러올 수 있도록 구현하였습니다.

<br>

`id`속성에 따라 데이터 가져오기

`id`가 있을 때|`id`가 없을 때|
|---|---|
|`/boards/_id`를 호출하여 해당 보드의 `id`를 가지고 있는 `카드 데이터`를 호출합니다.|`/boards`를 호출하여 해당 사용자의 `보드 데이터`를 호출합니다.|

<br>


<b> `store` </b>

|actions|
|---|
|FETCHLISTS|
```js
// ~/store/actions.js

import {list,category,search} from '../api/list'
  // 데이터 가져오기(보드,카드)
       async FETCHLISTS({commit},payload){
        try {
            // 기존 데이터 초기화 및  로딩 시작
            commit('UPDATE_STATE',{
                dataList:[],
                showCard:false,
                loading:true,
                hasError:false
            })
            let List;
            const {data}=await list.fetchs(payload)
            // 보드 데이터와 카드 데이터를 구분하여 데이터 저장
            data.lists?List={dataList:data.lists}:List={unitCard:data.list}
            // 데이터 저장
            commit('UPDATE_STATE',{
                    ...List,
                    showCard:true
            })
            return data
        } catch (error) {)
        // 오류 시, 에러 페이지 보여주기
            commit('UPDATE_STATE',{
                hasError:true,
                errMsg:error.response
            })
        }finally{
          // 로딩 종료
            commit('UPDATE_STATE',{
                loading:false
            })
        }
    },
```
> `commit`으로 `mutations`의 `UPDATE_STATE`를 실행시킵니다.

<br>

|mutations|
|---|
|UPDATE_STATE|
```js
// ~/store/mutations.js
       UPDATE_STATE(state,payload){
        Object.keys(payload).forEach(key => {
            state[key]=payload[key]
        })
    }
```
> `state`값을 저장시킵니다.

<br>

|state|설명|
|---|---|
|loading|로딩 여부|
|hasError|에러 여부|
| dataList|보드/카드 리스트|
|unitCard|카드(달인 데이터)|


```js
//  ~/store/state.js
    // 로딩
    loading:false,
    // 에러 여부
    hasError:false,
    // 보드,카드 리스트
    dataList:[],
  // 카드(단일 데이터)
    unitCard:{},
```
<br>

** 성공적으로 데이터를 가져오는 API 호출시, 저장되는 데이터의 예시

- 보드 데이터
```js
dataList = [
  {
    // 사용자의 id
    UserId:1
    // 배경 색
    bgcolor:"#FFF8E1"
    // 보드 생성 날짜
    createdAt:"2021-08-20T16:03:12.100Z"
    // 보드 설명
    description:"ss"
    // 보드 id
    id:28
    // 보드 제목
    title:"ss"
    // 보드 업데이트 날짜
    updatedAt:"2021-08-20T16:03:12.100Z"
  },
  ...
]
```

<br>

- 카드 데이터(보드가 가지고 있는 카드 데이터)
```js
dataList = [
  {
    // 보드 id
    BoardId:28
    // 카드가 가지고 있는 카테고리 리스트
    CardTypes:Array[2]
    // 카드가 가지고 있는 카테고리 리스트 중 대표 카테고리 정보
    Category:Object
    // 카드가 가지고 있는 카테고리 리스트 중 대표 카테고리 id
    CategoryId:21
    // 사용자의 id
    UserId:1
    // 배경 색
    bgcolor:"#008EFFFF"
    // 카드 진행 상황 여부
    complete:false
    // 카드 생성 날짜
    createdAt:"2021-09-06T14:35:12.907Z"
    // 카드 내용
    description:"샘플 카드 내용 입니다"
    // 카드 id
    id:47
    // 카드 제목
    title:"샘플카드.."
    // 카드 업데이트 날짜
    updatedAt:"2021-09-06T19:08:04.239Z"

  }
]
```
<br>

- 카드 데이터(단일)
```js
unitCard = {
   // 보드 id
    BoardId:28
    // 카드가 가지고 있는 카테고리 리스트
    CardTypes:Array[2]
    // 카드가 가지고 있는 카테고리 리스트 중 대표 카테고리 정보
    Category:Object
    // 카드가 가지고 있는 카테고리 리스트 중 대표 카테고리 id
    CategoryId:21
    // 사용자의 id
    UserId:1
    // 배경 색
    bgcolor:"#008EFFFF"
    // 카드 진행 상황 여부
    complete:false
    // 카드 생성 날짜
    createdAt:"2021-09-06T14:35:12.907Z"
    // 카드 내용
    description:"샘플 카드 내용 입니다"
    // 카드 id
    id:47
    // 카드 제목
    title:"샘플카드.."
    // 카드 업데이트 날짜
    updatedAt:"2021-09-06T19:08:04.239Z"
}
```



<br>

## 3. 데이터(보드,카드) 보여주기
### 3-1. 데이터 보여주기 공통 구현 요소

<b>제대로 데이터를 불러왔다면,해당 데이터를 보여줍니다.</b>
```js
<!-- ~/view/Board.vue -->
<template>
  <div>
    <div v-if="!hasError">
    <!-- 에러 없이 성공적으로 데이터를 가져올 경우 해당 데이터를 보여줍니다. -->
    </div>
  </div>
</template
```
> 제대로 데이터를 불러오지 못하고 오류가 발생한 경우에는 `ErrorPage` 컴포넌트를 보여줍니다. <br><a href="#">해당 내용은 위의 공통 요소에서 정리하였습니다.</a>


** ex) route의 params의 `id`값으로 해당 `id`값을 가지는 보드와 카드 데이터 정보를 호출할 때, 사용자가 잘못된 `id`값으로 호출했을 경우,에러 페이지를 보여줍니다.
(예시) 사용자가 `/board/fd44`이나 `/board/ddsss` 같은 존재하지 않는 라우터에 진입하려고 할 경우)

### 3-2. 보드 보여주기
> 데이터를 가져오는 API를 통해 보드 데이터를 보여줍니다.
<br>

`Boards.vue`
```html
<!-- ~/view/Boards.vue -->
<template>
    <div>
        <div v-if="!hasError">
            <h1 class="subheading grey--text ">보드</h1>
            <v-container>
                <v-row no-gutters>
                    <v-col class="py-1" cols="12" md="4" lg="3" v-for="board in boards" :key="board.id">
                        <board-card :board="board"></board-card>
                    </v-col>
                </v-row>
            </v-container>
        </div>
    </div>
</template>
```
```js
    computed:{
        ...mapState({boards:'dataList'})
    }
```
> 불러온 데이터인 `dataList`배열을 `BoardCard.vue` 컴포넌트에 `props`로 내려줍니다.

<br>

`BoardCard.vue`
```js
    props: {
        board: {
            type: Object,
            required: true
        }
    }
```
|props|타입|설명|
|---|---|---|
|board|객체|데이터를 가져오는 API를 통해 가져온 보드 데이터|

<br>

<b>보드는 `제목`,`내용`,`보드 색상`을 보여줍니다.</b>

> 보드 데이터에 `description`속성이 있다면, 메모 표시를 보여주어 보드 내용이 존재하는지 보여줍니다.

> 보드 색상은 `style`을 바운딩하여 보드 배경색으로 보여줍니다.
```html
<!-- ~/components/BoardCard.vue -->
<template>
    <div>
      <!-- 보드 배경색 -->
      <v-card class="text-center ma-3"
      :style="`background-color:${board.bgcolor}`">
          <v-card-title>
            <!-- 보드 제목 -->
              <div class="subheading">{{board.title}}</div>
              <v-spacer></v-spacer>
              <!-- 보드 설명 -->
              <span v-if="board.description">
                  <v-tooltip bottom>
                      <template v-slot:activator="{ on, attrs }">
                          <v-icon color="black" dark small v-bind="attrs" v-on="on">
                              mdi-note
                          </v-icon>
                      </template>
                      <span>메모가 있습니다.</span>
                  </v-tooltip>
              </span>
          </v-card-title>
        ...
      </v-card>
    </div>
</template>
```
`vuetify`에서 제공하는<a href="https://vuetifyjs.com/en/components/menus/#activator-and-tooltip"> `v-tooltip`</a>를 이용해 `메모그림`에 마우스를 올렸을 때, 툴팁이 보이도록 구현하였습니다.

<br>

### 3-3. 보드가 가지고 있는 카드 보여주기
> 데이터를 가져오는 API를 통해 보드가 가지고 있는 카드 데이터를 보여줍니다.

<br>

`Board.vue`
```html
<!-- ~/view/Board.vue -->
<template>
  <div>
    <div v-if="!hasError">
      <div>
        <h1 class="subheading grey--text">카드</h1>
        <v-container>
          <v-row no-gutters>
            <card-list v-for="card in cards" :key="card.id" :card="card"></card-list>
          </v-row>
        </v-container>
        ...
      </div>
    </div>
  </div>
</template>
```
```js
     computed:{
        ...mapState({cards:'dataList',hasError:'hasError'})
    },
```
> 불러온 데이터인 `dataList`배열을 `CardList.vue` 컴포넌트에 `props`로 내려줍니다.

<br>

`CardList.vue`
```js
    props:{
        card:{
            type:Object,
            required:true
        }
    },
```
|props|타입|설명|
|---|---|---|
|board|객체|데이터를 가져오는 API를 통해 가져온 카드 데이터|

<br>

<b>카드는 `제목`,`대표 카테고리`,`카드 진행 상황`을 보여줍니다.</b>

> 카드가 가지고 있는 대표 카테고리 이미지가 있다면,해당 이미지를 보여주도록 구현하였습니다.
```html
...
<!-- ~/components/card/CardList.vue -->
<template>
    <v-col v-if="card" cols="12" md="4" lg="3">
      <v-card class="ma-3">
        <v-list-item >
          <!-- 카드 카테고리 -->
          <v-list-item-avatar v-if="card.Category" :color="card.bgcolor" class="mt-n7 elevation-5" width="60" height="60">
          <!-- 이미지가 아니라면 아이콘 보여주기 -->
            <v-icon v-if="!card.Category.imagetype" elevation="10" dark large>
              {{card.Category.icon}}</v-icon>
              <!-- 이미지라면 이미지 보여주기 -->
            <v-img v-else :src="card.Category.icon" alt="카테고리 아이콘">
              </v-img>
          </v-list-item-avatar>
          ...
      </v-card>
    </v-col>
</template>
```

<br>

> `card`의 `Category.imagetype`으로 이미지 여부를 확인하여 이미지가 맞다면 `img 태그`의 `src`속성에 바운딩하여 이미지 정보를 보여줍니다.
```js
// 카드 데이터에서 카테고리 속성 예시
{
  // 대표 카테고리 데이터
  Category:{
    // 카테고리 생성 날짜
    createdAt:"2021-06-25T13:51:10.904Z"
    // 카테고리 아이콘
    icon:"mdi-battery-heart"
    // 카테고리 id
    id:3
    // 카테고리가 이미지인지 확인
    imagetype:false
    // 카테고리 이름
    type:"힐링"
    // 카테고리 업데이트 날짜
    updatedAt:"2021-06-25T13:51:10.904Z"

  }
}
```
`vuetify`에서 제공하는 <a href="https://vuetifyjs.com/en/api/v-list-item-avatar/">v-avatar</a>를 통해 심볼을 보여줍니다.

<br>

> 카드의 진행중/완료 상태를 확인하여, 완료 표시 여부를 보여줍니다.

```html
<!-- ~/components/card/CardList.vue -->
...
<template>
    <v-col v-if="card" cols="12" md="4" lg="3">
      <v-card class="ma-3">
        ....
        <!-- 카드 버튼 -->
        <v-card-actions>
          <router-link :to="`/card/${card.id}`">
            <v-btn color="secondary" @click="dialog=true">카드 보기</v-btn>
          </router-link>
          <v-spacer></v-spacer>
          <!-- 카드 상태여부를 확인하여 완료 표시를 보여줍니다. -->
          <v-icon v-if="card.complete" color="primary">mdi-check-circle</v-icon>
        </v-card-actions>
      </v-card>
    </v-col>
</template>
```

`vuetify`에서 제공하는 <a href="https://vuetifyjs.com/en/components/icons/#api">v-icon</a>를 통해  카드 진행 상황 여부 표시를 보여줍니다.

<br>


### 3-4.  카드 보여주기(카드 세부 내용 보여주기)
> `카드 보기`버튼 클릭시 라우터를 변경하여 카드 데이터를 보여줍니다.
<br>

`CardList.vue`
```html
<!-- ~/components/card/CardList.vue -->
<template>
    <v-col  cols="12" md="4" lg="3">
      <v-card class="ma-3">
        ....
        <!-- 카드 버튼 -->
        <v-card-actions>
        <!-- 라우터 변경 -->
          <router-link :to="`/card/${card.id}`">
            <v-btn color="secondary" @click="dialog=true">카드 보기</v-btn>
          </router-link>
          <v-spacer></v-spacer>
          ...
      </v-card>
    </v-col>
</template>
```
<br>

<br>

<b>카드 상세보기는 `제목`,`내용`,`카드 카테고리`,`카드 상태(완료 여부)`을 보여줍니다.</b>

> `카테고리`와 `카드 상태`는 카테고리 보여주기와 카드 상태 보여주기에서 따로 정리하였습니다.

<br>


`CardEditForm.vue`
> `input`태그의 `valule` 속성에 바운딩하여 기존 카드의 정보를 보여줍니다.

** input태그 대신 `vuetify` 에서 제공하는 <a href="https://vuetifyjs.com/en/components/text-fields/">`v-text-field`</a>를 사용하였습니다.

```html
<!-- ~/components/form/CardEditForm.vue -->
<template>
  <div>
    ...
    <!-- 카드 제목 -->
    <v-card class="my-6" elevation="7" shaped>
      <v-form v-model="valid">
        <v-card-text>
          <!-- value를 바운딩시켜 카드 제목을 보여준다 -->
          <v-text-field ref="input" label="카드 제목" :clearable="editState" :readonly="!editState" :value="unitCard.title"
          @input=" chageInput('title',$event)"
            prepend-icon="mdi-format-title" :rules="titleRules">
          </v-text-field>
          <!-- 카드 내용 -->
            <!-- value를 바운딩시켜 카드 내용을 보여준다 -->
          <v-textarea ref="desInput" label="메모"
          :value="unitCard.description"
          @input=" chageInput('description',$event)"
          :rules="descriptionRules" prepend-icon="mdi-content-paste"
            :clearable="editState" :readonly="!editState">
          </v-textarea>
        </v-card-text>
            ...
          </v-card>
        </v-row>
      </v-form>
    </v-card>
  </div>
</template>
```

<br>


## 3. 데이터(보드,카드) 추가
### 3-1. 공통 구현 요소

<b>`API`</b>

> `axios`를 이용해 데이터 추가  API 호출

```js
// ~/api/list.js
import {request} from './index'
  // 데이터 추가(보드 추가, 카드 추가)
  export const list={
       create({routeName,info}){
        return request.post(`${routeName}`,info)
    }
  }

```
> `routeName`으로 `보드 추가`와 `카드 추가`시, 호출되는 API를 구분하도록 구현하였습니다.


<br>

<b> `store` </b>

|actions|
|---|
|CREATLIST|
```js
// ~/store/actions.js
import {list,category,search} from '../api/list'
 // 데이터 추가(보드 추가, 카드 추가)
    async CREATLIST({commit},{routeName,info}){
        try {
        const {data}=await list.create({routeName,info})
        commit(`ADD_LIST`,data.list)
        return data
        } catch (error) {
          // 오류 시, 처리
            commit('UPDATE_STATE',{
                hasError:true,
                errMsg:error.response
            })
        }
    },
```

> `commit`으로 `mutations`의 `ADD_LIST`를 호출합니다.

<br>

|mutations|
|---|
|ADD_LIST|
```js
// ~/store/mutations.js
    //보드/카드 추가
    ADD_LIST(state,list){
        state.dataList=[list,...state.dataList]
    },
```
> 추가한 데이터는 가장 앞에서 보일 수 있도록 맨 앞에 데이터를 저장시킵니다.

<br>

### 3-2. 보드 추가
|컴포넌트|
|---|
|AddBoardPopup.vue|
|BoardForm.vue|

<b>모달 형식으로 보드 추가 컴포넌트를 보여줍니다.</b>

`Navbar.vue`
```html
 <!-- ~/components/common/Navbar.vue -->
<template>
  <nav>
    ...
    <!-- 사이드 메뉴 -->
    <v-navigation-drawer v-model="drawer" dark app class="indigo">
      ...
      <div class="my-6">
        <!-- 보드 추가 모달창 보여주기 -->
        <v-row justify="center">
          <add-board-popup :title="`새 보드`" :btnTxt="`보드 추가`"></add-board-popup>
        </v-row>
      </div>
    ...
    </v-navigation-drawer>
  </nav>
</template>
```
<br>

`AddBoardPopup.vue`
> `AddBoardPopup`컴포넌트는 보드 추가와 보드 수정시, 공통적으로 사용하였습니다.

```html
<template>
        <!-- 팝업 -->
      <v-dialog v-model="dialog" max-width="800px">
              <template v-slot:activator="{ on, attrs }">
                    <!-- 보드 추가 및 수정 버튼 -->
                      <v-btn v-on="on" v-bind="attrs" :text="board?true:false" >{{btnTxt}}</v-btn>
              </template>
          <v-card>
              <v-card-title>
                  <span class="headline">{{title}}</span>
              </v-card-title>
                <!-- 보드 추가 및 수정 컴포넌트 -->
              <board-form @close="onClose" :board="board"></board-form>
          </v-card>
      </v-dialog>
</template>
```
<br>

`props`
```js
  props:{
    title:{
      type:String,
      required:true
    },
   btnTxt:{
      type:String,
      required:true
    },
    board:{
      type:Object,
      required:false
    }
  },
```
|props|타입|설명|
|---|---|---|
|title|String|모달창 제목|
|btnTxt|String|모달창 버튼의 내용|
|board|Object|보드 데이터(보드 수정시에만,기존 데이터를 보여주기위해 보드 데이터를 porps로 내려줌)|

<br>


<b>보드 추가시, `제목`,`설명`,`보드 색` 세가지 정보로 보드를 추가할 수 있도록 구현하였습니다.</b>

`BoardForm.vue`
```html
<template>
  <v-form class="px-3" ref="form" v-model="valid">
    <v-card-text>
      <!-- 보드 제목 -->
      <v-text-field ref="input" clearable label="보드 제목" v-model="title" :counter="10" prepend-icon="mdi-format-title"
        :rules="titleRules">
        <!-- 보드 설명(메모) -->
      </v-text-field>
      <v-textarea label="메모" clearable v-model="description" :counter="50" prepend-icon="mdi-content-paste"
        :rules="descriptionRules">
      </v-textarea>
    </v-card-text>
    <!-- 보드 색 변경 -->
    <v-card-subtitle>
      <v-btn outlined color="indigo darken-1" dark @click="showColorpicker=!showColorpicker">보드 색 변경</v-btn>
    </v-card-subtitle>
    <v-row v-if="showColorpicker" justify="center" align="center" class="ma-1">
      <v-card>
        <v-color-picker style="max-width: 500px" v-model="color" hide-canvas></v-color-picker>
      </v-card>
    </v-row>
    <v-card-actions>
      <v-spacer></v-spacer>
      <v-btn color="blue daren-1" text @click="reset">닫기</v-btn>
      <!-- 보드 추가와 수정시 보여지는 버튼 구분하기 -->
      <template v-if="!board">
        <v-btn clor="green" text outlined @click="onAddBoard" :disabled="!valid">생성</v-btn>
      </template>
      <template v-else>
        <v-btn clor="green" text outlined @click="onEditBoard" :disabled="!valid">수정</v-btn>
      </template>
    </v-card-actions>
  </v-form>
</template>
```

`data`

```js
// ~/components/form/BoardForm.vue
    data() {
        //  props로 받은 데이터가 있다면 그 데이터를 보여주고, 없다면 빈문자열을 보여줍니다.
        return {
            title: this.board.title || '',
            description: this.board.description || '',
            color: this.board.color || '',
            showColorpicker: false
        }
    }
```
|data|설명|
|---|---|
|title|보드 제목|
|description|보드 내용|
|color|보드 색|
|showColorpicker|컬러 피커 보여줄지 유무(보드 색 변경 버튼 클릭 시에만 컬러 피커를 보여줍니다.)|

`v-model`을 사용하여 `data`인 `title`,`description`,`color`를 바운딩해줍니다.

** `BoardForm.vue`컴포넌트로 `보드 추가`/ `보드 수정`을 구현하기 위해, `props`로 내려준 `board`데이터가 있다면, 이미 추가된 데이터가 있으므로 그 데이터를 보여주고, 없다면 아직 추가된 데이터가 없으므로 빈 데이터를 보여줍니다.

<br>


> 보드 색 변경은 `vuetify`에서 제공해주는 <a href="https://dev.vuetifyjs.com/en/components/color-pickers/">v-color-picker</a> 를 사용하여 구현하였습니다.
```html
<!--  ~/componnets/form/BoardForm.vue -->
<template>
    ...
    <!-- 보드 색 변경 -->
    <v-card-subtitle>
    <!-- 버튼을 클릭시 데이터의 showColorpicker 값을 변화시켜, 컬러 선택 창을 보여줍니다. -->
        <v-btn outlined color="indigo darken-1" dark @click="showColorpicker=!showColorpicker">보드 색 변경</v-btn>
    </v-card-subtitle>
    <v-row v-if="showColorpicker" justify="center" align="center" class="ma-1">
        <v-card>
            <v-color-picker style="max-width: 500px" v-model="color" hide-canvas></v-color-picker>
        </v-card>
    </v-row>
    ...
</template>
```


<br>

<b>보드 생성 버튼 클릭</b>


> `store`의  `actions`함수 `CREATLIST` 호출
```js
// ~/components/BoardForm.vue
  methods: {
      ...mapActions(['CREATLIST']),
      onAddBoard() {
          const info = {
            title: this.title,
            description: this.description,
            bgcolor: this.color
          }
          // 보드 추가
          this.CREATLIST({
              routeName: 'boards',
              info
            })
            .then(() => {
              //기존 데이터 초기화
              this.reset()
              // 메인으로 라우터 이동
              if (this.$route.path !== '/') {
                this.$router.push('/')
              }
            })
        },
        reset() {
          // 보드 수정시 초기화
          if (this.board) {
            this.title = this.board.title
            this.description = this.board.description
          } else {
            // 보드 추가시 초기화
            this.title = ''
            this.description = ''
          }
          this.color = ''
          this.showColorpicker = false
          //  보드 추가 팝업창 닫기
          this.$emit('close')
        }
  }
```
<br>

### 3-3. 카드 추가
|컴포넌트|
|---|
|CardForm.vue|

<b>팝업 형식으로 라우터를 보여주기 위해 중첩된 라우터를 사용하였습니다.</b>

```js
// ~/router/index.js
const router = new VueRouter({
  routes: [{
    path: '/board/:id',
    component: () => import('../view/Board.vue'),
    name: 'boards',
    meta: {
      auth: true
    },
    beforeEnter: isAuth,
    // 중첩 라우터
    children: [{
      name: 'boards',
      path: 'add',
      component: () => import('../view/AddCards.vue'),
    }]
  }]
})
```
```html
<!-- ~/view/Board.vue -->
<template>
  <div>
    <div v-if="!hasError">
      <div>
        <h1 class="subheading grey--text">카드</h1>
       ....
       <!-- 중첩된 라우터를 보여줍니다. -->
      <router-view></router-view>
    </div>
  </div>
</template>
```

> 중첩된 라우터를 사용하여 `/board/:id`가 뒤에 보여지고, 팝업형식 처럼 `/board/:id/add`라우터가 보여지도록 구현하였습니다.
(<a href="https://router.vuejs.org/guide/essentials/nested-routes.html">중첩된 라우터에 관한 문서 바로가기</a>)

<br>

<b>카드 추가시, `카테고리`,`카드 제목`,`카드 내용`,`카드 색` 정보로 카드를 추가할 수 있도록 구현하였습니다.</b>

`CardForm.vue`
```html
<!-- ~/components/form/CardForm.vue -->
<template>
    <v-form v-model="valid" class="px-3" ref="form">
      <!-- 카테고리 리스트 -->
      ...
      <!-- 카드 제목 -->
      <v-card-text>
        <v-text-field ref="input" label="카드 제목" v-model="title" :counter="10" :rules="titleRules" clearable
          prepend-icon="mdi-format-title">
        </v-text-field>
        <!-- 카드 메모 -->
        <v-textarea v-model="description" :counter="50" :rules="descriptionRules" label="메모" clearable
          prepend-icon="mdi-content-paste">
        </v-textarea>
      </v-card-text>
      <!-- 카드 색 변경 -->
      <v-card-subtitle>
        <v-btn outlined color="indigo darken-1" dark @click="showColorpicker=!showColorpicker">카드 색 변경</v-btn>
      </v-card-subtitle>
      <!-- 카드 색 선택창 -->
      <v-row v-if="showColorpicker" justify="center" align="center" class="ma-1">
        <v-card>
          <v-color-picker style="max-width: 500px" v-model="color" hide-canvas></v-color-picker>
        </v-card>
      </v-row>
      ...
    </v-form>
    </template>
```

`data`

```js
// ~/components/form/CardForm.vue
    data() {
        return {
            // 카테고리 리스트
            selectList:[],
            // 대표 카테고리
            representCategory:{},
            title:'',
            description:'',
            color:'',
            showColorpicker:false
        }
    }
```
|data|설명|
|---|---|
|selectList|카드의 카테고리 리스트|
|representCategory|카드의 대표 카테고리|
|title|카드 제목|
|description|카드 내용|
|color|카드 색|
|showColorpicker|컬러 피커 보여줄지 유무(카드 색 변경 버튼 클릭 시에만 컬러 피커를 보여줍니다.)|

`v-model`을 사용하여 `data`인 `title`,`description`,`color`를 바운딩해줍니다.

> 카테고리는 <a href="#">카테고리 구현 내용</a>에 따로 정리하였습니다.

<br>


<b>카드 생성 버튼 클릭</b>

> `store`의 `actions`함수 `CREATLIST` 호출
```js
// ~/components/BoardForm.vue
  methods: {
    ...mapActions(['CREATLIST']),
    onAddCard() {
      const info = {
        title: this.title,
        description: this.description,
        bgcolor: this.color,
        category: this.selectList,
        BoardId: this.$route.params.id,
        CategoryId: this.representCategory.id
      }
      this.CREATLIST({
          routeName: 'cards',
          info
        })
        // 카드 추가 후 라우터 이동
        .then(() => this.$router.push(`/board/${this.$route.params.id}`))
    }
  }
```

## 4. 데이터(보드,카드) 수정
### 4-1. 공통 구현 요소

<b>`API`</b>

> `axios`를 이용해 데이터 수정  API 호출

```js
// ~/api/list.js
import {request} from './index'
    // 보드,카드 수정
   export const list={
      // 보드,카드 수정
    update({routeName,id,info}){
        return request.put(`${routeName}/${id}`,info)
    },
  }
```
> `routeName`으로 `보드 수정`과 `카드 수정`시, 호출되는 API를 구분하도록 구현하였습니다.


<br>

<b> `store` </b>

|actions|
|---|
|UPDATELIST|
```js
// ~/store/actions.js
import {list,category,search} from '../api/list'
   // 데이터 수정(보드 수정,카드 수정)
    async UPDATELIST({commit,state},{routeName,id,info,updateCard}){
        try {
          // 데이터 수정 API
            const {data}=await list.update({routeName,id,info})
            // 보드 수정
            if(routeName === "boards"){
                commit(`UPDATE_LIST`,{list:data.list,id})
            }
            // 카드 수정(카드 상태 수정)
            if(updateCard) {
                commit('UPDATE_STATE',{
                    unitCard:{
                        ...state.unitCard,
                        complete:info.complete
                    },
                    // 수정 알람 메세지 보여주기
                    alert:{
                        success:true,
                        text:data.msg,
                        timeout:1000
                    }
                })
            }
            return data
        } catch (error) {
          // 오류
            commit('UPDATE_STATE',{
                hasError:true,
                errMsg:error.response
            })
        }
    },
```
> `commit`으로 보드 수정시 `mutations`의 `UPDATE_LIST`를 호출하고, 카드 수정시 `mutations`의 `UPDATE_STATE`를 호출합니다.

<br>

|mutations|
|---|
|UPDATE_LIST|
|UPDATE_STATE|
```js
// ~/store/mutations.js
    UPDATE_STATE(state,payload){
            Object.keys(payload).forEach(key => {
                state[key]=payload[key]
            })
        },
     // 수정
    UPDATE_LIST(state, {list,id}) {
        const index = state.dataList.findIndex(board => board.id === id)
        state.dataList.splice(index, 1, list)
    },
```
|보드 수정|카드 수정(카드 상태 수정)|
|---|---|
|보드의 `id`로 수정할 데이터(보드)를 찾아 <a href="https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/splice">splice</a>를 이용해 기존 데이터(기존 보드)를 삭제하고 변경된 데이터(수정한 보드)를 추가로 넣어줍니다.|`state`의 `unitCard`의 `complete`속성을 수정하여 카드 상태를 변경합니다.|



<br>

|state|
|---|
|dataList|
|unitCard|


```js
//  ~/store/state.js
 // 보드,카드 리스트
    dataList:[],
    // 카드(단일 데이터)
    unitCard:{},
```
<br>


### 4-2. 보드 수정
|컴포넌트|
|---|
|BoardForm.vue|
|BoardCard.vue|


<b>모달 형식으로 보드 수정 컴포넌트를 보여줍니다.</b>

`BoardCard.vue`
```html
 <!-- ~/components/board/BoardCard.vue -->
<template>
  <div>
    <v-card class="text-center ma-3" :style="`background-color:${board.bgcolor}`">
      ...
      <v-card-actions>
        ...
        <v-spacer></v-spacer>
        <!-- 보드 수정/보드 삭제 버튼 메뉴 -->
        <v-menu offset-y v-model="showMenu">
          <template v-slot:activator="{ on, attrs }">
            <v-btn dark icon v-bind="attrs" v-on="on">
              <v-icon>mdi-dots-vertical</v-icon>
            </v-btn>
          </template>
          <v-list>
            <!-- 보드 수정 -->
            <v-list-item>
              <v-list-item-title>
                <!-- 보드 수정 모달창 -->
                <add-board-popup :title="`보드 수정`" :btnTxt="`보드 보기`" :board="board" @closemenu="showMenu=false">
                </add-board-popup>
              </v-list-item-title>
            </v-list-item>
            <!-- 보드 삭제-->
            <v-list-item>
              <v-list-item-title>
                <v-btn text @click="confirm=true">
                  보드 삭제
                </v-btn>
              </v-list-item-title>
            </v-list-item>
          </v-list>
        </v-menu>
      </v-card-actions>
    </v-card>
  </div>
</template>
```
> `vuetify`에서 제공하는 <a href="https://vuetifyjs.com/en/components/menus/">v-menu</a>로 보드 수정 버튼 클릭 시, `AddBoardPopup` 컴포넌트를 보여줍니다.

> 보드 수정시, 기존 데이터가 보여질 수 있도록 `AddBoardPopup`컴포넌트에 `board`를 `props`로 내려줍니다.

<br>

`AddBoardPopup.vue`
> `AddBoardPopup`컴포넌트는 보드 추가와 보드 수정시, 공통적으로 사용하였습니다.

```html
<template>
        <!-- 팝업 -->
      <v-dialog v-model="dialog" max-width="800px">
              <template v-slot:activator="{ on, attrs }">
                    <!-- 보드 추가 및 수정 버튼 -->
                      <v-btn v-on="on" v-bind="attrs" :text="board?true:false" >{{btnTxt}}</v-btn>
              </template>
          <v-card>
              <v-card-title>
                  <span class="headline">{{title}}</span>
              </v-card-title>
                <!-- 보드 추가 및 수정 컴포넌트 -->
              <board-form @close="onClose" :board="board"></board-form>
          </v-card>
      </v-dialog>
</template>
```
<br>

`props`
```js
  props:{
    title:{
      type:String,
      required:true
    },
   btnTxt:{
      type:String,
      required:true
    },
    board:{
      type:Object,
      required:false
    }
  },
```
|props|타입|설명|
|---|---|---|
|title|String|모달창 제목|
|btnTxt|String|모달창 버튼의 내용|
|board|Object|보드 데이터(보드 수정시에만,기존 데이터를 보여주기위해 보드 데이터를 porps로 내려줌)|

<br>


<b>보드를 수정하므로, 기존 데이터를 먼저 보여줍니다.</b>

`props`
```js
// ~/components/form/BoardForm.vue
    props: {
        board: {
            type: Object,
            required: true
        }
    }
```
|props|타입|설명|
|---|---|---|
|board|Object|보드 데이터|

```js
// props로 받은 board 객체의 에시
board: {
  UserId: 1
  bgcolor: "#FFF8E1"
  createdAt: "2021-06-25T14:05:05.641Z"
  description: null
  id: 4
  title: "샘플보드.."
  updatedAt: "2021-06-25T14:05:05.641Z"
}
```

<br>

`data`
```js
// ~/components/form/BoardForm.vue
    data() {
        //  props로 받은 데이터가 있다면 그 데이터를 보여주고, 없다면 빈문자열을 보여줍니다.
        return {
            title: this.board && this.board.title || '',
            description: this.board && this.board.description || '',
            color: this.board && this.board.color || '',
            showColorpicker: false
        }
    }
```
|data|설명|
|---|---|
|title|보드 제목|
|description|보드 내용|
|color|보드 색|
|showColorpicker|컬러 피커 보여줄지 유무(보드 색 변경 버튼 클릭 시에만 컬러 피커를 보여줍니다.)|

`v-model`을 사용하여 `data`인 `title`,`description`,`color`를 바운딩해줍니다.

** `BoardForm.vue`컴포넌트로 `보드 추가`/ `보드 수정`을 구현하기 위해, `props`로 내려준 `board`데이터가 있다면, 이미 추가된 데이터가 있으므로 그 데이터를 보여주고, 없다면 아직 추가된 데이터가 없으므로 빈 데이터를 보여줍니다.


<br>

<b>보드 수정 버튼 클릭</b>

> `store`의 `actions`함수 `UPDATELIST` 호출

```js
// ~/components/form/BardForm.vue
  methods: {
      ...mapActions(['UPDATELIST']),
       // 보드 수정
        onEditBoard() {
            const info = {
                title: this.title,
                description: this.description,
                bgcolor: this.color
            }
            this.UPDATELIST({
                routeName: 'boards',
                info,
                id: this.board.id
            })
            .then(()=>this.reset())
        },
        //
         reset() {
          // 보드 수정시 초기화
          if (this.board) {
            this.title = this.board.title
            this.description = this.board.description
          } else {
            // 보드 추가시 초기화
            this.title = ''
            this.description = ''
          }
          this.color = ''
          this.showColorpicker = false
          //  보드 추가 팝업창 닫기
          this.$emit('close')
        }
    }
 }
```
<br>

### 4-3. 카드 내용 수정
|컴포넌트|
|---|
|CardEditForm.vue|
|CardSteper.vue|
|CartegoryForm.vue|

** 카드 상태 수정과 카드 카테고리 수정은 <a href="#">카드 상태</a>와 <a href="#">카드 카테고리 수정</a>에 따로 정리하였습니다.


<b>카드 수정 모드시에만, 카드를 수정하도록 구현하였습니다.</b>
> 플로팅 메뉴로 카드 수정 버튼을 구현하였습니다.(<a href="#">`ActionMenu`는 위의 공통 구현 요소에 정리</a>)

```html
<!-- ~/view/BCard.vue -->
<template>
  <div>
    <v-row v-if="showCard && !hasError" justify="center">
      <v-dialog v-model="unitCard" fullscreen hide-overlay transition="dialog-bottom-transition">
        <v-card class="unit_card">
          <!-- 수정 /삭제 메뉴 -->
          <action-menu @onClickBtn="clickBtn" bottom right
             :direction="menu.direction"  :transition="menu.transition"
            :menus="menu.txts"></action-menu>
        </v-card>
      </v-dialog>
    </v-row>
  </div>
</template>
```
`data`
```js
    data() {
      return {
        // 메뉴
        menu: {
          direction: 'top',
          transition: 'slide-y-reverse-transition',
          txts: [{
            icon: 'mdi-pencil',
            btnTxt: '수정모드',
            color: 'green'
          }, {
            icon: 'mdi-delete',
            btnTxt: '삭제',
            color: 'red'
          }]
        }
      }
    }
```
|data|설명|
|---|---|
|menu|`direction`:메뉴가 보여질 방향<br>`transition`:메뉴가 보여질 때, 애니메이션<br>`txts`:메뉴의 내용|

`events`
|event|설명|
|---|---|
|onClickBtn|메뉴 버튼을 클릭 했을 때,발생하는 이벤트|

`methods`
```js
//~/view/BCard.vue
methods: {
  ...mapMutations(['UPDATE_STATE']),
  clickBtn(index) {
    switch (index) {
      //수정모드
      case 0:
        this.onEditCard()
        break;
        //삭제
      case 1:
        this.onremoveCard()
        break;

      default:
        break;
    }
  },
  // 수정모드
  onEditCard() {
    this.UPDATE_STATE({
      edit: {
        editState: true
      }
    })
  },
}
```
|methods|설명|
|---|---|
|clickBtn|`ActionMenu` 컴포넌트에서 버튼 클릭 시, `index` 와 함께 상위 컴포넌트에 `onClickBtn` 이벤트를 전달하여 `clickBtn` 함수를 실행합니다.|
|onEditCard|수정 모드로 변환합니다.|

> 카드 수정 여부는 `state`로 관리되도록 구현하였습니다.
```js
// ~/store/state.js
 //편집 상태
    edit:{
        // 수정 모드 상태
          editState:false,
          // 카드 수정모드시, 카테고리 추가 상태
          addState:false,
          // 카드 수정모드시, 카테고리 삭제 상태
          removeState:false
      }
```
<br>

<b>카드 수정 모드가 아니라면, 기존 데이터를 초기화 시켜줍니다.</b>
> `state`의 `edit`속성을 초기화시켜주지 않으면, 수정 내역이 지워지지 않아, 라우터가 변경되더라도 해당 데이터는 남아있게 됩니다. 해당 내용을 방지하기 위해 수정 중 `수정모드 취소` 버튼을 클릭하거나, 다른 라우터로 이동했을 때,기존의 편집 상태를 초기화시켜줍니다.

```js
//~/view/BCard.vue
  created() {
      this.resetState()
    },
    methods: {
      resetState() {
        this.UPDATE_STATE({
          edit: {
            editState: false,
            addState: false,
            removeState: false
          }
        })
      }
    }
```
`created`훅으로 인스턴스가 생성된 이후,편집 상태를 초기화시켜줍니다.

<br>

<b>카드 내용 수정 버튼 클릭</b>

> `store`의 `actions`함수 `UPDATELIST` 호출
```js
// ~/components/form/BardForm.vue
  methods: {
      ...mapActions(['UPDATELIST']),
       onUpdateCard() {
        const {title,description}=this.newCard;
        const info = {
          title:title || this.unitCard.title,
          description:description || this.unitCard.description,
          bgcolor: this.color
        }
        this.UPDATELIST({
            routeName: this.$route.name,
            id: this.$route.params.id,
            info
          })
          .then(() => {
            // 라우터 이동
            this.$router.push(`/board/${this.unitCard.BoardId}`)
          })
      }
    }
```

<br>

## 5. 데이터(보드,카드) 삭제

### 5-1. 공통 구현 요소
`API`
> `axios`를 이용해 데이터 삭제 API 호출
```js
// ~/api/list.js
import {request} from './index'
  export const list={
    remove({routeName,id,BoardId}){
        return BoardId?request.delete(`${routeName}/${BoardId}/${id}`) :request.delete(`${routeName}/${id}`)
    },
  }

```
> `routeName`으로 `보드 삭제`와 `카드 삭제`시, 호출되는 API를 구분하도록 구현하였습니다.

<br>


<b> `store` </b>

|actions|
|---|
|DELETELIST|
```js
// ~/store/actions.js
import {list,category,search} from '../api/list'
 // 데이터 삭제(보드 삭제,카드 삭제)
    async DELETELIST({commit},{routeName,id,BoardId}){
        try {
            const {data}=await list.remove({routeName,id,BoardId})
            // 보드 삭제
            if( routeName === "boards"){
                commit(`DELETE_LIST`,{id})
            }
            return data
        } catch (error) {
            commit('UPDATE_STATE',{
                hasError:true,
                errMsg:error.response
            })
        }
    },
```
> 보드를 삭제한 경우, `mutaions`의 `DELETE_LIST`를 호출합니다.

> 카드를 삭제한 경우, 라우터가 변경되도록 구현하였으므로, 별도의 `mutations`으로 `state`를 변경하지 않았습니다.

<br>

|mutations|
|---|
|DELETE_LIST|
```js
// ~/store/mutations.js
    DELETE_LIST(state,{id}){
        const index=state.dataList.findIndex(board=>board.id === id)
        state.dataList.splice(index,1)
    },
```

> 보드의 `id`값으로 삭제할 데이터를 찾아 삭제해줍니다.


<br>

|state|
|---|
|dataList|


```js
//  ~/store/state.js
  // 보드,카드 리스트
  dataList:[],
```
<br>

### 5-2. 보드 삭제

<b>보드 삭제 버튼 클릭 시 확인창을 띄워, 한번 더 확인하도록 구현하였습니다.</b>
> <a href="#">알림창(AlertConFirm 컴포넌트)</a>은 공통 구현 요소에 정리하였습니다.

```html
<!-- ~/components/board/BardCard.vue -->
<template>
  <div>
    <v-card class="text-center ma-3" :style="`background-color:${board.bgcolor}`">
      ...
    </v-card>
    <!--삭제 알림창 -->
    <alert-con-firm v-model="confirm" @agree="onAgree" @disagree="ondisAgree" :data="`보드 ${board.title}`">
    </alert-con-firm>
  </div>
</template>
```
`data`
```js
// ~/components/form/BardForm.vue
    data() {
        return {
            confirm:false
        }
    }
```
|data|설명|
|---|---|
|confirm|알림창 보여줄지 유무(삭제 알림창 보여줄지 확인)|

<br>

`methods`
```js
 methods: {
        ...mapActions(['DELETELIST']),
        // 보드 삭제 동의 후 확인창 닫기
        onAgree() {
          // 보드 삭제
            this.onDeleteBoard()
            this.confirm = false

        },
        //보드 삭제 비동의 후 확인창 닫기
        ondisAgree() {
            this.confirm = false
        },
        // 보드 삭제
        onDeleteBoard() {
            this.DELETELIST({
                routeName: 'boards',
                id: this.board.id
            })
        }
    }
```

|methods|설명|
|---|---|
|onAgree|삭제 알림창에서 `삭제` 버튼 클릭 후, 보드를 삭제한 후 삭제 알림창 닫기|
|ondisAgree|삭제 알림창에서 `취소` 버튼 클릭 후, 삭제 알림창 닫기|
|onDeleteBoard|`store`의 `actions`함수를 호출하여 보드 삭제 API 호출|

> 삭제 확인 알림창에서 `삭제` 버튼 클릭 시,`store`의 `actions`함수 `DELETELIST` 호출하여 보드를 삭제합니다.

<br>

### 5-3. 카드 삭제

<b>카드 삭제 버튼 클릭 시 확인창을 띄워, 한번 더 확인하도록 구현하였습니다.</b>
> <a href="#">알림창(AlertConFirm 컴포넌트)</a>은 공통 구현 요소에 정리하였습니다.


```html
<!-- ~/components/view/BCard.vue -->
<template>
  <div>
    <v-row v-if="showCard && !hasError" justify="center">
      <v-dialog v-model="unitCard" fullscreen hide-overlay transition="dialog-bottom-transition">
        ...
      </v-dialog>
      <!--삭제 알림창 -->
      <alert-con-firm v-model="confirm" @agree="onAgree" @disagree="ondisAgree" :data="`카드 ${unitCard.title}`">
      </alert-con-firm>
    </v-row>
  </div>
</template>
```
`data`
```js
// ~/components/form/BardForm.vue
    data() {
        return {
            confirm:false
        }
    }
```
|data|설명|
|---|---|
|confirm|알림창 보여줄지 유무(삭제 알림창 보여줄지 확인)|


<br>

`methods`
```js
// ~/components/form/BardForm.vue
methods: {
  ...mapActions(['DELETELIST']),
  // 카드 삭제
    onremoveCard() {
      this.DELETELIST({routeName:this.$route.name,id:this.unitCard.id,BoardId:this.unitCard.BoardId})
      .then((data)=>{
        // 보드가 가지고 있는 카드가 아예 존재 하지 않는다면 메인페이지로 이동시켜줍니다.
        data.isNotCard?this.$router.push(`/`):this.$router.push(`/board/${this.unitCard.BoardId}`)
      })
    },
  onClickBtn(index) {
    switch (index) {
      //수정모드
      case 0:
        this.onEditCard()
        break;
        //삭제
      case 1:
        this.confirm = true
        break;

      default:
        break;
    }
  },
  // 카드 삭제 동의 후 확인창 닫기
  onAgree() {
      this.onremoveCard()
      this.confirm = false
  },
  //카드 삭제 비동의 후 확인창 닫기
  ondisAgree() {
    this.confirm = false
  }
}
```

|methods|설명|
|---|---|
|onremoveCard|`store`의 `actions`함수를 호출하여 카드 삭제 API 호출 후, 라우터를 이동시켜줍니다.|
|clickBtn|버튼에 따라 카드수정/카드 삭제 구분|
|onAgree|삭제 알림창에서 `삭제` 버튼 클릭하여 카드를 삭제하고 삭제 알림창 닫기|
|ondisAgree|삭제 알림창에서 `취소` 버튼 클릭 후, 삭제 알림창 닫기|
|onDeleteBoard|`store`의 `actions`함수를 호출하여 카드 삭제 API 호출|

> 삭제 확인 알림창에서 `삭제` 버튼 클릭 시,`store`의 `actions`함수 `DELETELIST` 호출하여 카드를 삭제합니다.


<br>

## 5. 카드 상태
### 5-1. 카드 상태 보여주기
<b>카드 상태는 `진행중`,`완료` 로 구분하였습니다.</b>

`CardSteper.vue`
```html
<!-- ~/components/card/CardSteper.vue -->
<template>
    <v-stepper  class="card_step my-2 " :value="currentStep" alt-labels>
        <v-stepper-header height="100px" >
            <template v-for="n in steps">
              <!-- 카드 스탭 진행중, 완료 보여주기 -->
                <v-stepper-step class="pa-2" color="pink accent-2" :key="`step${n.step}`" :editable="edit.editState" :step="n.step" @click="changeStep(n.step)">
                    {{n.state}}
                </v-stepper-step>
                <v-divider v-if="n.step===1" :key="n.step"></v-divider>
            </template>
        </v-stepper-header>
    </v-stepper>
</template>
```
`data`
```js
    data() {
        return {
            steps: [{
                step: 1,
                state: '진행중'
            }, {
                step: 2,
                state: '완료'
            }],
            complete: false
        }
    }
```
|data|설명|
|---|---|
|steps|step의 정보|
|complete|step의 진행중/완료 여부|

`computed`
```js
    computed: {
        ...mapState(['unitCard', 'edit']),
        currentStep(){
            return this.unitCard && this. unitCard.complete?2:1;
        }
    },
```
|computed|설명|
|---|---|
|currentStep|기존에 카드가 가지고 있는 카드 상태(기존 카드가 진행중인지 완료인지 확인)<br> `v-stepper`의 `value`를 바운딩하여 현재 카드 상태 확인|

<br>

> `vuetify`에서 제공하는 <a href="https://vuetifyjs.com/en/components/steppers/#dynamic-steps">v-stepper</a>를 이용하여 카드 상태를 보여줍니다.


### 5-2. 카드 상태 변경
`CardSteper.vue`
```html
<!-- ~/components/card/CardSteper.vue -->
<template>
    <v-stepper  class="card_step my-2 " :value="currentStep" alt-labels>
        <v-stepper-header height="100px" >
            <template v-for="n in steps">
              <!-- 카드 스탭 진행중, 완료 보여주기 -->
                <v-stepper-step class="pa-2" color="pink accent-2" :key="`step${n.step}`" :editable="edit.editState" :step="n.step" @click="changeStep(n.step)">
                    {{n.state}}
                </v-stepper-step>
                <v-divider v-if="n.step===1" :key="n.step"></v-divider>
            </template>
        </v-stepper-header>
    </v-stepper>
</template>
```
<br>

`methods`
```js
    methods: {
        ...mapActions(['UPDATELIST']),
        changeStep(step) {
            // 편집모드 가 아니면 리턴해줍니다.
            if (!this.edit.editState) return
            if (step === 1) {
                this.complete = false
            } else {
                this.complete = true
            }
            const info = {
                complete: this.complete,
                cardState: true
            }
            this.UPDATELIST({
                routeName: this.$route.name,
                updateCard:true,
                id: this.unitCard.id,
                info
            })
        }
    }
```
|methods|설명|
|---|---|
|changeStep|vuetify에서 제공해주는 `v-stepper-step`에서 바운딩 시켜준 `step`의 값으로 진행중/완료여부를 확인하여 카드 수정 API를 호출합니다.|

> `vuetify`에서 제공하는 <a href="https://vuetifyjs.com/en/components/steppers/#dynamic-steps">dynamic-steps</a> 확인



## 6. 카테고리 보여주기
<b>`API`</b>

> `axios`를 이용해 카테고리 조회 API 호출

```js
// ~/api/list.js
import {request} from './index'
//보드,카드
export const category={
   // 카테고리 가져오기
    fetchs(payload){
        return request.get(`categorys/${payload.BoardId}`)
    },
}
```

<br>


<b> `store` </b>

|actions|
|---|
|FETCHCATEGORYS|
```js
// ~/store/actions.js

import {list,category,search} from '../api/list'
     //카테고리 가져오기
    async FETCHCATEGORYS({commit},info){
        try {
            const {data}=await category.fetchs(info)
            commit('UPDATE_STATE',{
                categoryList:data.categorys
            })
            return data
        } catch (error) {
            commit('UPDATE_STATE',{
                hasError:true,
                errMsg:error.response
            })
        }
    }
```
> `commit`으로 `mutations`의 `UPDATE_STATE`를 호출합니다.

<br>

|mutations|
|---|
|UPDATE_STATE|
```js
// ~/store/mutations.js
       UPDATE_STATE(state,payload){
        Object.keys(payload).forEach(key => {
            state[key]=payload[key]
        })
    }
```
> `state` 의 `categoryList`에 카테고리 데이터를 저장합니다.

<br>


```js
//  ~/store/state.js
// 카테고리 리스트
categoryList:[]
```
<br>

** 성공적으로 카테고리 조회 API 호출시, 저장되는 데이터

```js
// 카테고리 리스트
caategoryList = [{
    createdAt: "2021-06-25T13:51:10.864Z"
    icon: "mdi-train-car"
    id: 2
    imagetype: false
    type: "여행"
    updatedAt: "2021-06-25T13:51:10.864Z"
  },
  {
    createdAt: "2021-06-25T13:51:10.904Z"
    icon: "mdi-battery-heart"
    id: 3
    imagetype: false
    type: "힐링"
    updatedAt: "2021-06-25T13:51:10.904Z"
  },
  {
   createdAt:"2021-06-25T13:51:10.914Z"
  icon:"mdi-domain"
  id:4
  imagetype:false
  type:"회사"
  updatedAt:"2021-06-25T13:51:10.914Z"
  }
]
```
> 카드 카테고리는 처음 카드를 추가할 때는 디폴트 값으로 무조건 `여행`,`힐링`,`회사` 카테고리를 가질 수 있도록 구현하였습니다.<br><b>(서버에서 디폴트 카테고리를 만들고, 카테고리 조회 API 호출시, 해당 데이터를 보여줍니다.)</b>

<br>

<b>카테고리 리스트 불러오기</b>

카드 추가 컴포넌트에 진입시, 카테고리 데이터를 불러옵니다.


```js
// ~/components/form/CardAddForm.vue
    created() {
        this.onFetchCategory()
    },
        methods: {
        ...mapActions(['FETCHCATEGORYS']),
         onFetchCategory() {
            this.FETCHCATEGORYS({
                BoardId: this.$route.params.id
            })
        }
    }
```
> `created`훅으로 해당 인스턴스가 생성된 후, 카테고리 조회 API를 호출합니다.



<br>


<b>`select`형식으로 카테고리 리스트 보여주기</b>

> 카테고리 조회 API로 가져온 카테고리로 `select`형식으로 카테고리 리스트를 보여줍니다.

<br>

```html
<!-- ~/components/form/CardAddForm.vue -->
<template>
    <v-form v-model="valid" class="px-3" ref="form">
        <!-- 카테고리 리스트 -->
       <category-list :label="`추가 카테고리`" :noDataTxt="`추가할 카테고리가 없습니다.`" :categoryList="categoryList" v-model="selectList"
       @clearCategory="categoryReset"
       @updateInput="onupdateInput"></category-list>
       ...
    </v-form>
</template>
```

<br>

`data `

```js
// ~/components/form/CardAddForm.vue
    data() {
        return {
            // 카테고리 리스트
            selectList:[]
        }
    }
```
|data|설명|
|---|---|
|selectList| 카테고리 리스트 중, 내가 선택한 데이터|

<br>

`methods`

```js
// ~/components/form/CardAddForm.vue
        methods: {
        onupdateInput(value){
            this.selectList=value
        }
    }
```
|methods|설명|
|---|---|
|onupdateInput|하위 컴포넌트인 `CategoryList.vue`에서 선택 리스트가 변화했을 때, 상위 컴포넌트로 이벤트를 전달해줍니다.(카테고리 리스트 중 내가 선택한 데이터를 `selectList`에 저장합니다.)||

<br>

`CategoryList.vue`

 > vuetify에서 제공하는 <a href="https://vuetifyjs.com/en/components/selects/">v-select</a> 로 카테고리 리스트를 보여줍니다.

```html
<!-- ~/components/Categorys/CategoryList.vue -->
<template>
    <div>
        <v-select class="pt-4" :value="value" :rules="selectRules" :items="categoryList" item-text="type"
            item-value="type" return-object :label="label" :multiple="!isEdit" hide-selected outlined :clearable="!isEdit"
         @change="$emit('updateInput',$event)" :no-data-text=" noDataTxt"></v-select>
    </div>
</template>
```
`props`
```js
    props:{
        label:{
            type:String,
            required:true
        },
        noDataTxt:{
            type:String,
            required:true
        },
        categoryList:{
            type:Array,
            required:true
        },
        value:{
            type:Array,
            required:false
        },
        isEdit:{
            type:Boolean,
            required:false
        }
    }
```
|props|타입|설명|
|---|---|---|
|label|String|제목|
|noDataTxt|String|선택 리스트가 없을 때 보여줄 메세지|
|categoryList|Array|선택 리스트|
|value|Array|상위 컴포넌트에서 `v-model`로 바운딩 시켜준 데이터|
|isEdit|Boolean|수정하는 상태인지 여부(카테고리 수정시에만 `multiple`속성과 `clearble` 속성을 `false`로 하여 다중선택 불가 및 삭제 불가 하도록 구현하였습니다.)|

<br>

<b>카테고리 중, 선택한 카테고리 및 대표 카테고리 보여주기</b>

> 카테고리 조회 API로 가져온 카테고리 중, 내가 클릭하여 선택한 카테고리를 vuetify 에서 제공하는 <a href="https://vuetifyjs.com/en/components/chips/#slots">v-chip</a>를 이용하여 `chip`형태로 보여줍니다.

> 내가 클릭하여 선택한 카테고리 중, 사용자가 선택한 대표 카테고리를 따로 보여줍니다.

```html
<!-- ~/components/form/CardAddForm.vue -->
<template>
  <v-form v-model="valid" class="px-3" ref="form">
    ...
    <!-- 대표 카테고리 -->
    <template v-if="hasSelectList">
      <!-- 대표 카테고리가 없을 경우-->
      <v-alert class="caption py-2" v-if="!hasRepresentCategory" border="left" type="warning" colored-border
        color="orange" elevation="2">
        아래 카테고리 중 대표 카테고리를 선택해주세요
      </v-alert>
      <!-- 대표 카테고리가 있을 경우 -->
      <v-container v-else class="d-flex">
        <v-card-title class="pa-0">대표 카테고리</v-card-title>
        <v-chip class="ml-1 font-weight-bold" style="font-size:18px;" text-color="white" color="cyan">
          {{ representCategory.type}}</v-chip>
      </v-container>
    </template>
    <!-- 내가 선택한 카테고리 리스트 -->
    <category-chip :selectList="selectList" @onRepresent="onRepresent"></category-chip>
    ...
  </v-form>
</template>
```
<br>

`data `

```js
// ~/components/form/CardAddForm.vue
    data() {
        return {
            // 카테고리 리스트
            selectList:[],
            // 대표 카테고리
            representCategory:{},
        }
    }
```
|data|설명|
|---|---|
|selectList|카테고리 리스트 중, 내가 선택한 데이터(<a href="#">위의 CategoryList 컴포넌트 참고</a>)|
|representCategory|내가 선택한 카테고리 중, 대표 카테고리 데이터|

<br>

`computed`
```js
// ~/components/form/CardAddForm.vue
  computed:{
        hasRepresentCategory (){
            return this.representCategory && this.representCategory.type
        },
        hasSelectList () {
            return this.selectList &&this.selectList.length
        }
    },
```
|computed|설명|
|---|---|
|hasRepresentCategory|대표 카테고리를 가지고 있는지 확인|
|hasSelectList|카테고리 리스트 중 내가 선택한 카테고리가 있는지 확인|

<br>

`methods`

```js
// ~/components/form/CardAddForm.vue
        methods: {
         // 대표 카테고리 설정
          onRepresent(choice) {
            this.representCategory = choice
          }
        }
```
|methods|설명|
|---|---|
|onRepresent|하위 컴포넌트인 `CategoryChip.vue`에서 카테고리 칩을 클릭했을 때,상위 컴포넌트로 이벤트를 전달해줍니다.(카테고리 리스트 중 내가 선택한 데이터를 `representCategory`에 저장합니다.)||

<br>

`CategoryChip.vue`

 > vuetify에서 제공하는 <a href="https://vuetifyjs.com/en/components/chips/">v-chip</a>으로 카테고리 리스트를 보여줍니다.

```html
<!-- ~/components/Categorys/CategoryList.vue -->
<template>
    <div>
        <v-chip v-for="(choice,index) in selectList" :key="index" @click="$emit('onRepresent',choice)"
            text-color="white" color="pink" :close="edit.editState && edit.removeState"
            @click:close="onChipClose(choice,index)" class="ma-1 c_chip">
            <!-- 카테고리 이미지가 아니라면 아이콘 보여주기 -->
            <v-icon v-if="!choice.imagetype" left>{{choice.icon}}</v-icon>
            <!-- 카테고리 이미지라면 이미지 보여주기 -->
            <v-avatar class="mr-1" v-else><img :src="choice.icon" alt=""></v-avatar>
            {{choice.type}}
        </v-chip>
        <!-- 오류 메세지 -->
        <v-alert class="mt-1 alert_msg card" v-if="errmsg && edit.editState" outlined border="right" color="red" dense
            elevation="2" type="warning">{{errmsg}}</v-alert>
    </div>
</template>
```
`props`
```js
    props:{
        selectList:{
            type:Array,
            required:true
        }
    }
```
|props|타입|설명|
|---|---|---|
|selectList|Array|카테고리 리스트 중 내가 선택한 카테고리 리스트|

<br>

## 7. 카테고리 편집
### 7-1. 카테고리 편집 공통 요소
<b>카테고리 편집은 카드 수정 모드시에만 편집할 수 있도록 구현하였습니다.</b>

`CategoryForm.vue`
```js
// ~/components/CategoryForm.vue
 computed: {
      // 편집 상태
    ...mapState(['edit']),
  },
  methods: {
    ...mapMutations(['UPDATE_STATE']),
    // 카테고리 추가 편집 모드 상태
      addchangeState() {
         // 입력값 초기화
         this.category.input = ''
          this.UPDATE_STATE({
              edit:{
                  editState:true,
                  addState:this.edit.addState?false:true
              }
          })
      },
      // 카테고리 삭제 편집 모드 상태
      removechangeState() {
           this.UPDATE_STATE({
              edit:{
                  editState:true,
                  removeState:this.edit.removeState?false:true
              }
          })
      }
  }
```
|methods|설명|
|---|---|
|addchangeState|`mutations`의 `UPDATE_STATE`를 호출하여 카드 편집상태 수정|
|removechangeState|`mutations`의 `UPDATE_STATE`를 호출하여 카드 편집상태 수정|

> `state`의 `edit`속성으로 편집 상태를 관리합니다.
```js
// ~/store/state.js
    //편집 상태
    edit:{
        // 수정 모드 상태
          editState:false,
          // 카드 수정모드시, 카테고리 추가 상태
          addState:false,
          // 카드 수정모드시, 카테고리 삭제 상태
          removeState:false
      }
```

### 7-2. 카테고리 추가
> 카테고리 추가는 `이미지`와 `카테고리 이름`으로 추가할 수 있도록 구현하였습니다.

```html
<!-- ~/components/form/CategoryForm.vue -->
<template>
    <v-card class="pa-2">
         ...
        <!--카테고리 리스트 -->
        <div class="category_list">
          <h3 class="overline">카테고리 리스트</h3>
           <!--카테고리 리스트 이미지 추가 로딩 -->
          <div class="imageloading" v-if="imageLoading">
            카테고리 추가중...
            <v-progress-circular indeterminate color="primary" :width="2" :size="20"></v-progress-circular>
          </div>
          <!-- 현재 가지고 있는 카테고리 리스트 -->
          <category-chip :selectList="unitCard.CardTypes || []"></category-chip>
        </div>
        <div v-if="edit.editState">
            <!-- 카테고리 추가 폼 보여주기 버튼 -->
            <v-btn type="button" class="my-1" text small @click="addchangeState" rounded outlined color="info">원하시는
                카테고리가 없나요??
            </v-btn>
            <!-- 카테고리 삭제 폼 보여주기 버튼 -->
            <v-btn type="button" class="my-1" @click="removechangeState" text small rounded outlined color="info">카테고리를
                삭제하고
                싶으신가요??</v-btn>
            <v-card v-if="edit.addState && edit.editState">
                <!-- 카테고리 추가 폼 -->
                <v-card-text>
                    <v-form @submit.prevent="onAddCategory" v-model="category.validcategory">
                      <!-- 카테고리 이름 -->
                        <v-text-field label="카테고리 이름" v-model="category.input"
                            prepend-icon="mdi-emoticon-outline" :rules="category.categoryRules" :counter="10" clearable>
                        </v-text-field>
                        <!-- 카테고리 이미지 -->
                        <v-file-input v-model="files"
                         :rules="imgRules"
                         accept="image/png, image/jpeg"
                        show-size counter label="아이콘 이미지"></v-file-input>
                        <v-btn type="submit" name="submit" :disabled="fileInvalid || !category.validcategory"
                            color="green" text outlined>추가</v-btn>
                        <v-alert class="mt-3" v-if="category.errmsg" border="left" color="red" dense text type="error">
                            {{category.errmsg}}
                        </v-alert>
                    </v-form>
                </v-card-text>
            </v-card>
        </div>
    </v-card>
</template>
```
`data`
```js
 data() {
      return {
        files: [],
        imageLoading: false,
        category: {
            isEdit:true,
            validcategory: true,
            input: "",
          // 카테고리 유효성 검사
            categoryRules: [
                v => v && v.length<11 || '카테고리는 10자이하로 입력해주세요.'
            ],
            errmsg:''
        }
      }
  },

```
|data|설명|
|---|---|
|files|카테고리 추가시, 이미지 저장(`v-file-input`에 바운딩한 데이터로 내가 선택한 이미지를 저장)|
|imageLoading|카테고리 추가시, 이미지가 완전히 로드된 후 보여지도록 하기 위해  변경할 로딩 스피너 |
|category|`isEdit`:수정 상태인지 확인<br>`validcategory`:유효성 검사<br>`input`:카테고리 이름(`input`에 작성한 값)<br>`categoryRules`:카테고리 유효성 검사|

> 이미지 선택은 vuetify에서 제공하는 <a href="https://vuetifyjs.com/en/components/file-inputs/#misc">v-file-input</a>을 사용하였습니다.



<br>


<b>카테고리 추가 버튼 클릭</b>

> `store`의 `actions`함수 `CREATCATEGORY` 호출
```js
// ~/components/form/CategoryForm.vue
 methods: {
     ...mapActions(['CREATCATEGORY']),
    //   카테고리 추가
    async onAddCategory () {
      if (this.category.input.trim().length < 0) {
        this.category.errmsg = '카테고리 이름을 입력해주세요'
        return
      }
      try {
        // 로딩
        this.imageLoading = true
        // FormDta로 이미지와 함께 저장
        const data = new FormData()
        // 카테고리 이름
        data.append('type', this.category.input)
        // 카테고리 이미지
        data.append('image', this.files)
        await this.CREATCATEGORY({
          BoardId: this.unitCard.BoardId,
          CardId: this.unitCard.id,
          info: data
        })
          .then((data) => {
            // 추가한 카테고리 이미지를 불러오고 이미지가 로드된 후 로딩 종료
            loadImage(data.category[0] || '')
            this.category.errmsg = ''
            this.categoryReset()
            this.imageLoading = false
          })
      } catch (error) {
        console.log(error)
        this.category.errmsg = error.response ? error.response.data.msg : error
        this.imageLoading = false
      }
    },
 }
```
> `loadImage` 함수는 위에서 정리하였습니다.

> 카테고리 추가 API를 호출한 후, 카테고리 이미지를 가져오고, 해당 이미지가 완전히 로드된 후, 보여질 수 있도록 `imageLoading`으로 로딩 스피너를 보여줄지 구분합니다.
<br>

<b>`API`</b>

```js
// ~/api/list.js
import {request} from './index'
export const category={
   // 카테고리 추가
    create({info,BoardId,CardId}){
        return request.post(`categorys/${BoardId}/${CardId}`,info)
    }
}
```

<br>

<b>`store`</b>

|actions|
|---|
|CREATCATEGORY|

```js
// ~/store/actions.js
import {category} from '../api/list'
// 카테고리 추가
  async CREATCATEGORY ({ commit }, { BoardId, CardId, info }) {
    const { data } = await category.create({ BoardId, CardId, info })
    commit('ADD_CATEGORY', data.category[0])
    return data
  }
```
> `commit`으로 `mutations`의 `ADD_CATEGORY`를 호출합니다.
<br>

|mutations|
|---|
|ADD_CATEGORY|

```js
// ~/store/mutations.js
    // 카테고리 추가
  ADD_CATEGORY (state, category) {
    state.unitCard.CardTypes=[...state.unitCard.CardTypes,category]
  },
```

> `state` `unitCard`의 카테고리리스트에 추가한 카테고리 정보를 추가합니다.


<br>

|state|
|---|
|unitCard|

```js
  // 카드(단일 데이터)
  unitCard:{}
}
```

`unitCard`에 저장되는 카드 데이터의 예시
```js
unitCard = {
   // 보드 id
    BoardId:28
    // 카드가 가지고 있는 카테고리 리스트
    CardTypes:Array[2]
    // 카드가 가지고 있는 카테고리 리스트 중 대표 카테고리 정보
    Category:Object
    // 카드가 가지고 있는 카테고리 리스트 중 대표 카테고리 id
    CategoryId:21
    // 사용자의 id
    UserId:1
    // 배경 색
    bgcolor:"#008EFFFF"
    // 카드 진행 상황 여부
    complete:false
    // 카드 생성 날짜
    createdAt:"2021-09-06T14:35:12.907Z"
    // 카드 내용
    description:"샘플 카드 내용 입니다"
    // 카드 id
    id:47
    // 카드 제목
    title:"샘플카드.."
    // 카드 업데이트 날짜
    updatedAt:"2021-09-06T19:08:04.239Z"
}
```

> `unitCard`의 `CardTypes`을 수정하여 카테고리 리스트를 추가합니다.

<br>

## 7-3. 대표 카테고리 수정
<b>현재 대표 카테고리로 설정된 카테고리를 제외하고, 대표 카테고리로 추가할 수 있는 카테고리를 보여줍니다.</b>
```html
<!-- ~/components/form/CategoryForm.cue -->
<template>
    <v-card class="pa-2">
        <h3>대표 카테고리</h3>
        <!-- 대표 카테고리 -->
        <v-chip class="my-2 subtitle-1 white--text font-weight-bold" :color="unitCard.bgcolor">
            {{unitCard.Category && unitCard.Category.type}}</v-chip>
        <!-- 변경할 대표 카테고리 리스트 -->
        <div v-if="edit.editState">
            <category-list :label="`변경할 대표 카테고리`" :noDataTxt="`더이상 카테고리가 없습니다.`" :categoryList="Categorys"
                @updateInput="onupdateInput" :isEdit="category.isEdit"></category-list>
            <!-- 변경할 대표 카테고리가 있을 경우에만 -->
            <div class="py-1" v-if="mainCategory.id">
                <!--대표 카테고리 수정 버튼 -->
               <v-btn color="primary" class="mb-2" dark elevation="2" small @click.prevent="onupdateCategory">대표 카테고리 수정</v-btn>
            </div>
        </div>
        ...
    </v-card>
</template>
```

`computed`

```js
  computed: {
      ...mapState(['unitCard', 'edit', 'mainCategory']),
      //대표카테고리 제외하고 보여줄 카테고리
      Categorys() {
          return this.unitCard.CardTypes && this.unitCard.CardTypes.filter(category => category.id !== this.unitCard.Category.id) || []
      }
  }
```
|computed|설명|
|---|---|
|Categorys|`state`에 저장한 카드의 대표 카테고리를 비교하여, 현재 가지고 있는 대표 카테고리를 제외한 카테고리 리스트를 보여줍니다.|

<br>


<b>대표 카테고리  수정 버튼 클릭</b>

> `store`의 `actions`함수 `UPDATECATEGORY` 호출
```js
// ~/components/form/CategoryForm.vue
 methods: {
   ...mapActions(['UPDATECATEGORY']),
   ...mapMutations(['UPDATE_STATE']),
    //  대표 카테고리 수정
      onupdateCategory() {
        this.UPDATECATEGORY({
            BoardId: this.unitCard.BoardId,
            CardId: this.unitCard.id,
            choice: this.mainCategory
        })
        .then(()=>{
          // 대표 카테고리 수정 후 초기화
            this.UPDATE_STATE({
                mainCategory:{}
            })
        })
      }
 }
```

<br>


<b>`API`</b>

```js
// ~/api/list.js
import {request} from './index'
export const category={
    // 대표 카테고리 수정
    update({CategoryId,BoardId,CardId}){
        return request.put(`categorys/${BoardId}/${CardId}`,{CategoryId})
    }
}
```

<br>

<b>`store`</b>

|actions|
|---|
|UPDATECATEGORY|

```js
// ~/store/actions.js
import {category} from '../api/list'
 // 대표 카테고리 수정
  async UPDATECATEGORY ({ commit, state }, { BoardId, CardId, choice }) {
    try {
      const { data } = await category.update({ BoardId, CardId, CategoryId: choice.id })
      // 카테고리 정보를 저장하고, 알림창 보여주기
      commit('UPDATE_STATE', {
        unitCard: {
          ...state.unitCard,
          Category: choice
        },
        alert: {
          success: true,
          text: data.msg
        }
      })
      return data
    } catch (error) {
      commit('UPDATE_STATE', {
        hasError: true,
        errMsg: error.response
      })
    }
  },
```
> `commit`으로 `mutations`의 `UPDATE_STATE`를 호출합니다.

> `unitCard`의 `Category`을 수정하여 대표 카테고리를 수정하고, 알림창을 보여줍니다.

<br>

|mutations|
|---|
|UPDATE_STATE|

```js
// ~/store/mutations.js
   // state 업데이트
    UPDATE_STATE(state,payload){
        Object.keys(payload).forEach(key => {
            state[key]=payload[key]
        })
    }
```


<br>

|state|
|---|
|unitCard|


```js
  // 카드(단일 데이터)
  unitCard:{}
}
```

`unitCard`에 저장되는 카드 데이터의 예시
```js
unitCard = {
   // 보드 id
    BoardId:28
    // 카드가 가지고 있는 카테고리 리스트
    CardTypes:Array[2]
    // 카드가 가지고 있는 카테고리 리스트 중 대표 카테고리 정보
    Category:Object
    // 카드가 가지고 있는 카테고리 리스트 중 대표 카테고리 id
    CategoryId:21
    // 사용자의 id
    UserId:1
    // 배경 색
    bgcolor:"#008EFFFF"
    // 카드 진행 상황 여부
    complete:false
    // 카드 생성 날짜
    createdAt:"2021-09-06T14:35:12.907Z"
    // 카드 내용
    description:"샘플 카드 내용 입니다"
    // 카드 id
    id:47
    // 카드 제목
    title:"샘플카드.."
    // 카드 업데이트 날짜
    updatedAt:"2021-09-06T19:08:04.239Z"
}
```
> `unitCard`의 `Category`을 수정하여 대표 카테고리를 수정합니다.


<br>


## 7-4. 카테고리 삭제

> 카드 수정 모드시에만 `엑스 버튼`을 보여줍니다.

```html
<!-- ~/components/form/CategoryForm.vue -->
<template>
    <v-card class="pa-2">
        ...
        <!-- 현재 가지고 있는 카테고리 리스트 -->
        <category-chip :selectList="unitCard.CardTypes || []"></category-chip>
        <div v-if="edit.editState">
            <!-- 카테고리 추가 폼 보여주기 버튼 -->
            <v-btn type="button" class="my-1" text small @click="addchangeState" rounded outlined color="info">원하시는
                카테고리가 없나요??
            </v-btn>
            <!-- 카테고리 삭제할 엑스 버튼 보여주기 버튼 -->
            <v-btn type="button" class="my-1" @click="removechangeState" text small rounded outlined color="info">카테고리를
                삭제하고
                싶으신가요??</v-btn>
            ...
        </div>
    </v-card>
</template>
```
> 현재 가지고  있는 카테고리 리스트를 `CategoryChip`컴포넌트로 보여주고, 해당 컴포넌트에서 카테고리를 삭제합니다.

<br>

`CategoryChip.vue`
```html
<!-- ~/components/Category/CategoryChip.vue -->
<template>
    <div>
        <v-chip v-for="(choice,index) in selectList" :key="index" @click="$emit('onRepresent',choice)"
            text-color="white" color="pink"
            :close="edit.editState && edit.removeState"
            @click:close="onChipClose(choice,index)" class="ma-1 c_chip">
          ...
        </v-chip>
    </div>
</template>
```
> `close`속성을 바운딩하여 편집상태일때만 `엑스(close) 버튼`이 보이도록 구현하였습니다.(vuetify 에서 제공하는<a href="https://vuetifyjs.com/en/api/v-chip/#props">v-chip API</a> 확인)

<br>

<b>카테고리 삭제 버튼 클릭</b>

> `store`의 `actions`함수 `DELETECATEGORY` 호출
```js
// ~/components/Categorys/CategoryChip.vue
  computed:{
      ...mapState(['unitCard','edit']),
  },
 methods: {
      ...mapActions(['DELETECATEGORY']),
    //   카테고리 삭제
    onChipClose(choice) {
            try {
              // 기존에 가지고 있는 대표카테고리의 id를 확인하여, 현재 대표 카테고리로 설정된 카테고리는 삭제 불가능하게 구현
                if (choice.id === this.unitCard.Category.id) {
                    this.errmsg = `현재 등록된 대표 카테고리 ${this.unitCard.Category.type}은/는 삭제 할 수 없습니다`
                    return
                }
               this.DELETECATEGORY({
                        BoardId: this.unitCard.BoardId,
                        CardId: this.unitCard.id,
                        choice
                    })
                    .then(() => {
                        this.errmsg = ''
                    })
            } catch (error) {
                console.error(error)
                this.errmsg=error.response.data.msg
            }
        }
    }
 }
```

<br>


<b>`API`</b>

```js
// ~/api/list.js
import {request} from './index'
export const category={
    // 카테고리 삭제
    remove({CategoryId,BoardId,CardId}){
        return request.delete(`categorys/${BoardId}/${CardId}/${CategoryId}`)
    }
}
```

<br>

<b>`store`</b>

|actions|
|---|
|DELETECATEGORY|

```js
// ~/store/actions.js
import {category} from '../api/list'
 // 카테고리 삭제
    async DELETECATEGORY({commit},{BoardId,CardId,choice}){
        const {data}=await category.remove({BoardId,CardId,CategoryId:choice.id})
        commit(`DELETE_CATEGORYS`,choice)
        commit('UPDATE_STATE',{
           alert:{
               success:true,
               text:data.msg
           }
        })
        return data
    }
```
> `commit`으로 `mutations`의 `DELETE_CATEGORYS`를 호출합니다.

> 카테고리 삭제 API를 호출 후, 카테고리를 삭제하고 알림창을 띄어 사용자에게 보여줍니다.
<br>

|mutations|
|---|
|DELETE_CATEGORYS|

```js
// ~/store/mutations.js
 // state 업데이트
    UPDATE_STATE(state,payload){
        Object.keys(payload).forEach(key => {
            state[key]=payload[key]
        })
    },
   // 카테고리 삭제
    DELETE_CATEGORYS(state,choiceCategory){
        const index= state.unitCard.CardTypes.findIndex(category=>category.id == choiceCategory.id)
        state.unitCard.CardTypes.splice(index,1)
    },
```

> `category`의 `id`를 비교하여 내가 선택한 카테고리를 삭제합니다.


<br>

|state|
|---|
|unitCard|
|alert|

```js
  // 카드(단일 데이터)
  unitCard:{},
   // 알람상태
  alert:{
      success:false,
      text:'',
      timeout:3000
  },
}
```

`unitCard`에 저장되는 카드 데이터의 예시
```js
unitCard = {
   // 보드 id
    BoardId:28
    // 카드가 가지고 있는 카테고리 리스트
    CardTypes:Array[2]
    // 카드가 가지고 있는 카테고리 리스트 중 대표 카테고리 정보
    Category:Object
    // 카드가 가지고 있는 카테고리 리스트 중 대표 카테고리 id
    CategoryId:21
    // 사용자의 id
    UserId:1
    // 배경 색
    bgcolor:"#008EFFFF"
    // 카드 진행 상황 여부
    complete:false
    // 카드 생성 날짜
    createdAt:"2021-09-06T14:35:12.907Z"
    // 카드 내용
    description:"샘플 카드 내용 입니다"
    // 카드 id
    id:47
    // 카드 제목
    title:"샘플카드.."
    // 카드 업데이트 날짜
    updatedAt:"2021-09-06T19:08:04.239Z"
}
```

> `unitCard`의 `CardTypes`을 수정하여 카테고리를 삭제합니다.

> 알림창은 위의 공통 요소에서 정리하였습니다.

<br>

## 8. 데이터(보드,카드) 검색

### 8-1. 공통 구현 요소
> 진행중인 카드/ 완료중인 카드로 카드를 가져올 수 있도록 구현하였습니다.

> 불러온 카드 리스트는 라이브러리인 <a href="https://github.com/Akryum/vue-virtual-scroller#readme">vue-virtual-scroller</a> 를 사용하였습니다.
<br>
(`vue-virtual-scroller`는 많은 양의 데이터를 스크롤할 시,목록에서 보이는 항목만 렌더링하여 보여줍니다.)


```html
<!-- ~/components/search/InfiniteScroller.vue -->
<template>
    <RecycleScroller class="scroller" :items="searchList" :item-size="160" key-field="id" page-mode v-slot="{ item }">
        <!-- {{item}} -->
        <router-link :to="`/card/${item.id}`">
            <v-card class="mb-1 pa-1 card scroll_card" :style="`border:5px solid ${item.bgcolor}`" height="140">
                <v-row no-gutters class="`pa-3 project" :class="{'complete':item.complete}">
                    <!-- 카드 제목 -->
                    <v-col md="6" sm="4">
                        <div class="caption grey--text txt">
                            카드 제목
                        </div>
                        <div>{{item.title}}</div>
                    </v-col>
                    <!-- 대표 카테고리 -->
                    <v-col class="category_area" md="2" sm="4">
                        <div class="caption grey--text txt">
                            대표 카테고리
                        </div>
                        <div>
                            <v-chip :color="item.bgcolor">{{item.Category && item.Category.type}}</v-chip>
                        </div>
                    </v-col>
                    <!-- 카드 내용 -->
                    <v-col md="4" sm="4">
                        <div class="caption grey--text">
                            카드 내용
                        </div>
                        <div class="overline des">{{item.description}}</div>
                    </v-col>
                </v-row>
            </v-card>
        </router-link>
    </RecycleScroller>
</template>

```

`computed`

```js
    computed:{
        ...mapState({searchList:'dataList'})
    }
```

<br>


#### 8-2. 진행중/완료된 카드 가져오기
<b>진행중인 카드 버튼 클릭</b>

> `store`의 `actions`함수 `FETCHSEARCHCARD` 호출
```js
// ~/view/Projects.vue
  data() {
      return {
          ingBtn: true
      }
  },
 methods: {
   ...mapActions(['FETCHSEARCHCARD']),
   // 진행중인 카드 버튼 클릭시, 진행중인 카드 가져오기
   ondisCompleteFetchCard() {
     // 진행중인 카드 버튼
     this.ingBtn = false
     this.FETCHSEARCHCARD({
       routeName: 'cards',
       complete: "1"
     })
   }
 }
```

<br>

<b>완룐된 카드 버튼 클릭</b>

> `store`의 `actions`함수 `FETCHSEARCHCARD` 호출
```js
// ~/view/Projects.vue
 methods: {
   ...mapActions(['FETCHSEARCHCARD']),
   // 완료된 카드 버튼 클릭시, 완료된 카드 가져오기
   onCompleteFetchCard() {
     this.FETCHSEARCHCARD({
       routeName: 'cards',
       complete: "2"
     })
   }
 }
```


<b>`API`</b>

```js
// ~/api/list.js
import {request} from './index'
// 검색
export const search={
    fetchs({routeName,complete}){
        return request.get(`${routeName}/status/${complete}`)
    }
}
```

<br>

<b>`store`</b>

|actions|
|---|
|FETCHSEARCHCARD|

```js
// ~/store/actions.js
import {search} from '../api/list'
    // 검색
    async FETCHSEARCHCARD({commit},{routeName,complete}){
        try {
            // 로딩 시작
            commit('UPDATE_STATE',{
                loading:true
            })
            const {data}=await search.fetchs({routeName,complete})
            commit('UPDATE_STATE',{
                dataList:data.lists
            })
            return data
        } catch (error) {
            commit('UPDATE_STATE',{
                hasError:true,
                errMsg:error.response
            })
        }finally{
            // 로딩 종료
            commit('UPDATE_STATE',{
                loading:false
            })
        }
    }
```
> `commit`으로 `mutations`의 `UPDATE_STATE`를 호출합니다.


<br>

|mutations|
|---|
|UPDATE_STATE|

```js
// ~/store/mutations.js
 // state 업데이트
    UPDATE_STATE(state,payload){
        Object.keys(payload).forEach(key => {
            state[key]=payload[key]
        })
    }
```


<br>

|state|
|---|
|dataList|


```js
    // 보드,카드 리스트
    dataList:[]
}
```

`dataList`에 저장되는 카드 데이터의 예시
```js
dataList = [{
  // 카드의 카테고리
  Category: Object
  // 카드 색
  bgcolor: "#00FFFFFF"
  // 카드 진행 여부
  complete: false
  // 카드 내용
  description: "샘플 카드 내용 입니다"
  // 카드 id
  id: 68
  // 카드 제목
  title: "샘플카드.."
}, ...]

```

> `complete` 속성에 따라 진행중/완료 카드를 구분하여 데이터를 가져옵니다.

** `compplete` 속성에 따른 데이터

|complete|데이터|
|---|---|
|false|진행중인 카드 데이터|
|true|완료된 카드 데이터|


<br>

<b>해당 라우터에 진입시, 검색 데이터는 초기화 시켜줍니다.</b>

```js
    created(){
        // 검색 결과 초기화
        if(this.searchList)this.UPDATE_STATE({dataList:[]})
    },
    computed:{
    ...mapState({searchList:'dataList',loading:'loading'})
},
```

> `created`훅으로 인스턴스가 생성된 후, 기존 데이터를 초기화해줍니다.

# 2. Server
### node + express

### 1. 사용한 라이브러리




### 2. 사용한 DB
- <a href="https://www.postgresql.org/">postgreSQL</a>
-  <a href="https://sequelize.org/master/">sequlize</a> 사용하여 DB 작성

- 관계도





## 배포
- client : <a href="#">netlify</a>에 배포
- server : <a href="https://devcenter.heroku.com/">heroku</a>에 배포


