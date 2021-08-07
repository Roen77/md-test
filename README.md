# libraryApp
## client

### vue +nuxt
- ssr(서버사이드렌더링)과 seo(검색엔진최적화) 를 위해 nuxt 프레임워크를 사용하였습니다.
- vue 는 안정화버전인 2.x 버전을 사용하였습니다.
- Visual Studio(vscode)를 이용해 작업하였습니다.
### 1. 버전


|node|Nuxt|vue|
|---|:---|:---:|
|v12.13.0|v2.15.4|v2.x|

- 사용한 라이브러리

||axios|chart.js|vue2-datepicker|@nuxtjs/moment|intersection-observer|
|---|---|:---|:---|:---|:---|
|버전|v5.13.1|v2.9.4|3.9.0|1.6.1|0.12.0|
|이유|서버에 api 호출|데이터를 이용해 chart 사용|달력 사용|날짜 포맷 변경|IE 에서 `intersection-observer`를 사용할 수 있도록 해주는 라이브러리|



### 2. 구현 목표

1. <a href="#register">회원가입/로그인 구현</a>
   - 이메일/비밀번호 구현(이메일,이름,비밀번호,닉네임)
   - 소셜로그인(카카오,구글 로그인) 구현
2. <a href="#user_info">사용자 정보 수정</a>
   - 사용자의 비밀번호,닉네임,프로파일 이미지 수정 가능하도록 구현
   - 프로파일 사진 추가 및 수정 구현
3. <a href="#book_search">원하는 책 검색 및 추가</a>
   - 책 검색으로는 카카오 책 검색 api 사용
    - 위의 카카오 검색 api로 원하는 책 검색하여 생성할 수 있도록 구현
4. <a href="#book_create">직접 책 추가 및 추가한 책 수정 및 삭제</a>
  - 내가 추가한 책들 중에서 원하지 않는 책 수정 및 삭제 기능 구현
5. <a href="#books">내가 추가한 책들 보여주기</a>
5. <a href="#book">내가 추가한 책 상세 보기</a>
6. <a href="#bookmark">내 책 북마크 추가 및 삭제</a>

8. <a href="#heart">다른 유저의 책 좋아요 생성 및 삭제</a>
9. <a href="#tag">내가 추가한 책 태그 추가 및 삭제</a>
10. <a href="#tags">태그별로 책 모아보기</a>
11. <a href="#other_books">다른 유저가 추가한 책 보기</a>
12. <a href="#comment">다른 사용자 정보의 책에 댓글 쓰기 구현 및 내가 추가한 책에서도 댓글 쓰기 구현</a>
    - 내가 추가한 댓글만 삭제 가능하도록 구현 
13. <a href="#star">책에 별점 주기 가능</a>
14. <a href="#other_search">다른 사용자의 책 검색 구현</a>
    - 책 제목과 책의 저자를 선택하여 검색할 수 있도록 구현
15. <a href="#sum">통계</a>


### 3. 구현 세부 내용

#### 3-1 라우터 구조

```
/ ---- atuh ---- register
    |    |------- login
    |
    |--user ----profile  
    |    |------info 
    |
    |--books
    |    |----b
    |    |    |----_id
    |    |
    |    |----add
    |    |
    |    |----_page
    |    |
    |    |----others
    |    |      |-----b
    |    |      |     |----_id
    |    |      |   
    |    |      |-----_page
    |    |
    |    |----search
    |           |-----_page
    |
    |--hastags/_tagname
```
#### 3-2. css
|구현 방법|구현 내용|
|---|---|
|CSS|static/css/common.css 만들어 공통 요소로 초기화|
|CSS|static/css/style.css 만들어 css 요소 정리|

#### 3-3. `vuex` `store` 정리
 
 1. store
 2. getter
 3. mutations
 4. actions

 #### 3-4. 구현 공통 요소 
1. `component`(컴포넌트)는 `import`해서 가져오지 않아도, `nuxt` 에서  `component`(컴포넌트)를 자동으로 가져올 수 있습니다.(`nuxt` v2.13 버전 이상)
 - <a href="https://nuxtjs.org/docs/2.x/directory-structure/components">`nuxt` 공식 문서 바로 가기</a>
```js
// nuxt.config.js
export default {
  // 해당 속성을 true 하여, 컴포넌트 경로에서 자동으로 가져올수 있도록 하였습니다.
 components: true
}
```

### 4. 구현 세부 내용 정리
<br>

### <div id="register" style="color:blue;"><b>1. 회원가입/로그인 구현</b></div>

|컴포넌트|라우터|
|---|---|
|components/form/Register.vue|auth/register|
|components/form/Login.vue|auth/login|


#### <div style="color:red;">1-1. 회원가입</div>

|컴포넌트|라우터|
|---|---|
|components/form/Register.vue|auth/register|

#### 1. 이메일,닉네임,비밀번호를 입력해야 가입될 수 있도록 구현하였습니다.
```html
<!-- ~/components/form/Register.vue -->
<template>
  <div class="user signupbox">
    <div class="formbx">
      <form @submit.prevent="UserRegister">
        <h2>회원가입</h2>
        <div :class="{'invalid':!email}">
          <label for="email">email</label>
          <!-- v-model를 이용해 바운딩 -->
          <input id="email" ref="emailinput" v-model="email" type="text" placeholder="이메일">
        </div>
        <div v-if="!isvalidEmail && email" class="err">
          이메일 형식으로 입력해주세요.
        </div>
        <div :class="{'invalid':!username}">
          <label for="username">username</label>
          <input id="username" v-model="username" type="text" placeholder="닉네임">
        </div>
        ....
        </div>
      </form>
    </div>
  </div>
</template>
```
```js
export default{
  data () {
    return {
      email: '',
      username: '',
      password: '',
      confirm_password: '',
      ...
    }
}
```
- `v-model`를 이용해 데이터를 양방향 바운딩해주었습니다.<br>
(`v-model`를 이용해 `email`,`username`,`password`,` confirm_password`를 바운딩 하였습니다.)
<br><br>

#### 2. 유효성 검사는 이메일은 이메일 형식으로,비밀번호는 최소 8자리 이상,비밀번호 확인으로 유효성 검사를 구현하였습니다.
 - ~/util/validate.js 별도의 유효성 검사 파일을 만들어 이메일과 입력값의 길이가 8 이상인 경우의 유효성 검사 함수를 만들었습니다.

```js
// ~/utils/validate.js

// 이메일 유효성 검사
const validEmail = (mail) => {
  if (/^[a-zA-Z0-9.!#$%&'*+/=?^_`{|}~-]+@[a-zA-Z0-9-]+(?:\.[a-zA-Z0-9-]+)*$/.test(mail)) {
    return (true)
  }
  return (false)
}
// 압력값의 길이가 8 이상인지 확인하는 감수
const validLength = (value) => {
  if (value && value.length >= 8) {
    return true
  } else {
    return false
  }
}

export { validLength, validEmail }
```
```js
// ~/components/form/Register.vue

import { validLength, validEmail } from '~/utils/validate'

 computed: {
    isvalidLength () {
      return validLength(this.password)
    },
    isvalidEmail () {
      return validEmail(this.email)
    },
    isconfirmPassword () {
      return this.password === this.confirm_password
    },
    totalConfirm () {
      return this.isvalidLength && this.isvalidEmail && this.isconfirmPassword && this.username
    }
  }
```
### computed
|isvalidLength|
|---|
|`data`의 `password`의 길이가 8이상인지 확인합니다.|

|isvalidEmail|
|---|
|`data`의 `email`의 양식이 이메일인지 확인합니다.|

|isconfirmPassword|
|---|
|`data`의 `password`와 `data`의 `confirm_password`가 완전히 일치하는지 확인합니다.|

|totalConfirm|
|---|
|위의 유효성 검사를 모두 통과하는지 확인합니다.|
<br><br>

#### 3. `computed`를 통해 유효성 검사를 통과하지 못 했을 경우, 에러 메세지를 보여줍니다.

#### 4. 필수 입력값이 없다면, 입력폼의 앞에 `*`표시를 해주어 사용자에게 확인시켜줍니다.

#### 5. `computed` `totalConfirm`를 이용해, 유효성 검사를 모두 통과했을 경우에만 버튼을 클릭할 수 있도록 구현하였습니다.

```html
<template>
  <div class="user signupbox">
    <div class="formbx">
      <form @submit.prevent="UserRegister">
        <h2>회원가입</h2>
        <div :class="{'invalid':!email}">
          <label for="email">email</label>
          <input id="email" ref="emailinput" v-model="email" type="text" placeholder="이메일">
        </div>
        <!-- 이메일이 존재하고, 이메일 형식이 아니라면 아래 메세지를 보여줍니다. -->
        <div v-if="!isvalidEmail && email" class="err">
          이메일 형식으로 입력해주세요.
        </div>
        <!-- 클래스를 바운딩해 username이 없다면 클래스명에 invalid를 추가합니다. -->
        <div :class="{'invalid':!username}">
          <label for="username">username</label>
          <input id="username" v-model="username" type="text" placeholder="닉네임">
        </div>
            <!-- 클래스를 바운딩해 password가 없다면 클래스명에 invalid를 추가합니다. -->
        <div :class="{'invalid':!password}">
          <label for="password">password</label>
          <input id="password" v-model="password" type="password" placeholder="비밀번호">
        </div>
          <!-- 비밀번호가 존재하고, 비밀번호의 길이가 8 이상이 아니라면 아래 메세지를 보여줍니다. -->
        <div v-if="!isvalidLength && password" class="err">
          비밀번호는 8자리 이상이어야 합니다.
        </div>
        ...
        <!-- 
        회원가입 시 에러가 발생했을 때(이메일이 이미 등록되어 있는 경우 등의 오류가 발생했을 때)
        이를 사용자가 확인할 수 있도록 구현했습니다.-->
          <div v-if="errmsg" class="errmsg" :class="{'visible':email}">
            {{ errmsg }}
          </div>
          <!-- disabled를 바운딩시켜, 유효성 검사를 모두 다 통과한다면 버튼을 클릭할 수 있도록 합니다.
          유효성 검사를 모두 다 통과한다면 disabled속성을 사라져 버튼을 클릭할 수 있습니다. -->
          <button class="primary-btn" type="submit" :disabled="!totalConfirm">
            회원가입
          </button>
        </div>
      </form>
    </div>
  </div>
</template>
```

#### <div id="invalid">`클래스명` invalid 추가</div>
```css
/* 클래스명에 invalid가 있다면 *표시를 보여줍니다. */
.invalid::after {
    content: '*';
    position: absolute;
    left: -8px;
    top: -8px;
    color: crimson;
}
```
- 해당 클래스명은 다른 필수 입력폼에 *표시될 수 있도록 공통적으로 사용하였습니다.
<br><br>

#### 5. 편의성을 위해 회원가입페이지나 로그인 페이지에 진입시, 이메일 입력폼에 포커스 될 수 있도록 구현하였습니다.
```html
<template>
  <div class="user signupbox">
    <div class="formbx">
      <form @submit.prevent="UserRegister">
        <h2>회원가입</h2>
        <div :class="{'invalid':!email}">
          <label for="email">email</label>
          <!-- ref를 사용해 태그를 참조합니다. -->
          <input id="email" ref="emailinput" v-model="email" type="text" placeholder="이메일">
        </div>
        ....
      </form>
    </div>
  </div>
</template>
```
```js
// ~/components/form/Register.vue
  mounted () {
    this.inputfocus()
  },
  methods:{
      inputfocus () {
        //ref를 이용해 참조한 태그에 포커스시킵니다.
      this.$refs.emailinput.focus()
    }
  }
```
- `mounted ` 라이프사이클 훅 함수를 이용해 해당 컴포넌트에 진입시 이메일 input 입력폼에 포커스되도록 구현하였습니다.
<br><br>

#### 6. api 호출

#### 6-1. store
|actions|
|---|
|register|

```js
//store/user.js actions
    async register ({ commit }, userinfo) {
    const res = await this.$axios.post('user/register', userinfo)
    commit('setUser', res.data)
  }
```
- `axios`를 이용해 api를 호출합니다.
- `api`를 호출해 데이터를 가져온다면 `commit`으로 `mutations`를 호출하여 `state`의 `user` 객체에 사용자의 정보를 저장합니다. 

|회원가입시에도 사용자의 정보를 저장한 이유|
|---|
|편의성을 위해, 사용자가 회원가입을 한 후, 로그인을 따로 진행하지 않고, 바로 사용자의 정보를 `state`에 저장하여 로그인을 생략할 수 있도록 구현하였습니다.|

|mutations|
|---|
|setUser|
```js
//store/user.js mutations
 setUser (state, user) {
    state.user = user
  }
```
- `state`의 `user`객체에 사용자의 정보를 저장합니다.

|getters|
|---|
|getUser|
```js
//store/user.js getters
  getUser (state) {
    return state.user
```
- `state`의 `user` 객체를 가져옵니다.

|state|
|---|
|user|

```js
//store/user.js state
 user: {}
```
 - <div id="state_user">성공적으로 회원가입 시, 아래 사용자 정보를 저장합니다.</div>
```js
// user 객체에 저장되는 사용자의 정보 예시
  user:{
    // 이메일
    email:"q@q.com" 
    // 아이디
    id:7
    // 카카오로그인인지,구글로그인인지 구분하는 속성
    // 카카오로 로그인할시, 해당 속성은 null 이 아닌 kakao로 저장된다.
    provider:null
    // 썸네일 이미지로, 없다면 빈 문자열이 출력된다.
    thumbnail:"https://am-clone.s3.ap-northeast-2.amazonaws.com/1623252830675"
    // 사용자이름
    username:"1234"
  }
```
<br><br>

#### 6-2. 회원가입 api를 호출합니다.
- `store`의 `actioins` 함수 `register`를 호출합니다. 
```js
// <!--components/form/Register.vue -->

  methods: {
    ...mapActions('user', ['register']),
    async UserRegister () {
      try {
        const userinfo = {
          email: this.email,
          username: this.username,
          password: this.password
        }
        await this.register(userinfo)
        // 성공적으로 회원가입되면 메인페이지로 이동
        this.$router.push('/')
      } catch (error) {
        // 에러 발생시 에러메세지 출력될 수 있도록 구현
        this.errmsg = error.response.data.msg
      } finally {
        // 데이터 전송이 실패했든 성공했든 상관없이 전송후엔 무조건 기존 입력값을 초기화시켜준다.
        this.resetInput()
        this.inputfocus()
      }
    },
    resetInput () {
      this.email = ''
      this.username = ''
      this.password = ''
      this.confirm_password = ''
    },
    inputfocus () {
      this.$refs.emailinput.focus()
    }

  }
```

<br><br>

 #### <div style="color:red;">1-2. 로그인</div>
 1. 로그인 구현은 이메일로 로그인.카카오로 로그인.구글로 로그인 세가지 방법으로 구현하였습니다.
 - 서버에서 passport를 사용하여 구현하였는데 해당내용은 아래의 서버 구현 내용에서 정리하겠습니다.<br>
 (<a href="https://www.passportjs.org/packages/passport-kakao/">`passport`카카오 로그인 참고 문서 바로 가기</a>)<br>
 (<a href="https://www.passportjs.org/packages/passport-google-oauth20/">`passport`구글 로그인 참고 문서 바로 가기</a>)<br>

 ### *사용자 정보 저장시 문제점
 |문제점|
 |---|
 |`store`의 `state`에 저장된 사용자의 정보로 로그인 유무를 확인하는데, 새로고침시, `state`에 저장된 `user`의 정보는 유지 되지 않는 문제가 발생합니다.|

|해결|
 |---|
 |`쿠키`나 `로컬스토리지`에 사용자의 정보를 저장할수 있지만, `nuxt`를 사용하여 서버사이드 랜더링을 사용했으므로, `nuxt`에서 제공하는 `nuxtServerInit` `actions`함수를 사용해 사용자 정보를 가져오도록 했습니다. (<a href="https://nuxtjs.org/docs/2.x/directory-structure/store#the-nuxtserverinit-action">`nuxtServerInit`참고 문서 바로 가기</a>)|
 ```js
// <!-- store/index.js -->
export const actions = {
  // 사용자의 정보를 서버에서 가져옵니다.
  async nuxtServerInit ({ dispatch }) {
    await dispatch('user/fetchUser')
  }
}
```
<br><br>

### <div id="user_info" style="color:blue;"><b>2. 사용자 정보 수정</b></div>
|라우터|
|---|
|user/info|

#### 1. 사용자 정보 수정은 크게 2가지로 나누었습니다.
  - 프로필 정보 수정
  - 비밀번호 수정

#### <div style="color:red;">1-1.  프로필 정보 수정</div>

#### 1. 이름,프로필 이미지를 수정할 수 있도록 구현했습니다.
#### 2. 기존에 있는 정보를 수정하므로, 기존 정보를 보여줍니다.
```html
<!-- ~/page/user/info.vue -->
<template>
  <div class="profile_box">
    <!-- profile -->
    <h2>프로필 정보 수정</h2>
    <form class="form_content" @submit.prevent="onChangeProfile">
      <div>
        <label for="email">이메일</label>
        <!-- getter를 이용해 사용자의 정보를 가져와서 value 를 바운딩시켜 사용자의 이메일을 보여줍니다. -->
        <p><input id="email" class="readonly" readonly :value="getUser.email" type="text"></p>
      </div>
        ....
      <button class="round-btn" type="submit" :disabled="!isconfirmPassword && password">
        프로필 정보 수정
      </button>
    </form>
  </div>
</template>
```
```js
 computed: {
  //  getter를 이용해 store의 state 의 user 정보를 가져옵니다.
    ...mapGetters('user', ['getUser']),
 }
```
<br><br>

#### 3. 사용자의 프로필(썸네일) 이미지를 업 로드할수 있도록 구현하였습니다.(api 호출)

#### 3-1 store
|<div id="uploadImg">actions</div>|
|---|
|uploadImg|

```js
//store/book.js actions
  async uploadImg ({ commit }, userinfo) {
    try {
      let res
      // 사용자의 프로필(썸네일) 이미지를 저장하는 api 호출
      if (userinfo && userinfo.user) {
        res = await this.$axios.post('user/thumbnail', userinfo)
      } else {
        // 책의 썸네일 이미지를 저장하는 api 호출
        res = await this.$axios.post('books/thumbnail', userinfo)
      }
      commit('setThumbnail', res.data)
    } catch (error) {
      console.error(error)
    }
  },
```
- `axios`를 이용해 api를 호출합니다.
-  사용자의 프로필(썸네일) 이미지 뿐만 아니라, 책의 썸네일 이미지를 업로드할때 사용하기 때문에, `{user:true}` 인 경우에는 사용자 정보 썸네일 이미지를, 그렇지 않다면 책의 썸네일 이미지를 업로드하는 api 함수를 호출 할수 있도록 구현하였습니다.
- `commit`를 이용해 `mutatonis`을 호출합니다.


|mutations|
|---|
|setThumbnail|
```js
//store/book.js  mutations
 setThumbnail (state, image) {
    state.imagePath = image
  },
```
- `state`의 `imagePath`에 이미지 정보를 저장합니다.(문자열로 저장)

|getters|
|---|
|getUser|
```js
//store/book.js  getters
  getImagePath (state) {
    return state.imagePath
  }
```
- `state`의 `imagePath`을 가져옵니다.

|state|
|---|
|imagePath|
```js
//store/book.js state
 imagePath: ''
```
 - 성공적으로 이미지 업로드 시, 아래 이밎 정보를 저장합니다.
```js
// imagePath에 저장되는 사용자의 정보 예시
// 아마존 버킷에 저장된 이미지의 주소를 저장합니다.
 imagePath:"https://am-clone.s3.ap-northeast-2.amazonaws.com/1623598375400"
```
<br><br>

#### 3-2 <div id="image_add">이미지 선택</div>
- `store`의 `actioins` 함수 `uploadImg`를 호출합니다. 
- 참고로 사용자의 프로필(썸네일) 이미지 말고도 책의 썸네일 이미지를 추가하거나 수정할 때도 공통적으로 사용하였습니다.<br>
(<a href="#book_image_add">책의 이미지 추가 바로가기</a>)<br>
(<a href="#book_image_">책의 이미지 수정 바로가기</a>)<br>
```html
<!-- ~/page/user/info.vue -->
<template>
  ...
  <div class="file_container add">
    <div class="txt">
      <label for="fileinput"><span class="round-btn yellow"><i class="far fa-file-image"></i>프로필 이미지 수정</span></label>
      <!-- input type=file로 설정하여 파일창으로 파일을 선택할 시  onChangeImage 함수가 호출됩니다.-->
      <input id="fileinput" ref="file" style="display:none" type="file" @change="onChangeImage">
    </div>
    ....
  </div>
  <button class="round-btn" type="submit" :disabled="!isconfirmPassword && password">
    프로필 정보 수정
  </button>
  ...
</template>
```
```js
// ~/page/user/info.vue
 data() {
     return {
       selectedFile: ''
     }
   },
   methods: {
     ...mapActions('books', ['uploadImg']),
     onChangeImage(e) {
       const selectedFile = e.target.files[0]
       const imageFormData = new FormData()
       imageFormData.append('photo', selectedFile)
       this.uploadImg(imageFormData, {
         user: true
       })
     },
   }
```

### methods
|onChangeImage|
|---|
| FormData 형식을 이용해 이미지 정보를 저장합니다.(여기서는 이미지는 1개만 선택할수 있도록 하였습니다.)|
|`{user:true}`속성으로, 책의 썸네일 이미지가 아닌 사용자의 프로필(썸네일) 이미지를 업로드하는 api를 호출합니다. (<a href="#uploadImg">`actions` 함수 `uploadImg` 바로가기</a>)|
<br><br>

#### 4. 사용자가 변경할 프로필(썸네일) 이미지를 저장하기전에 미리 보여줍니다.
(<b>프로필 정보 수정 버튼을 눌러야 완전히 사용자의 프로필이 저장됩니다.</b>)
```html
<template>
  <!-- profile -->
  ...
  <div v-if="getImagePath" class="photos">
    <div class="images">
      <!---사용자의 프로필(썸네일)이미지가 있고 state의 ImagePath에 값이 없다면, 사용자의 프로필(썸네일) 이미지를 보여줍니다. -->
      <img v-if="getImagePath.length === 0 &&getUser.thumbnail" :src="getUser.thumbnail" alt="userprofile">
      <!--  state의 ImagePath에 값이 있다면  ImagePath에 저장된 이미지 주소를 src에 바인딩하여 보여줍니다. -->
      <img v-if="getImagePath.length>0" :src="`${getImagePath}`" alt="">
    </div>
  </div>
  ...
</template>
```

 ### *이미지를 업로드 할 때 문제점
 |문제점|
 |---|
 |`onChangeImage`함수로 이미지를 업로드하는 api 함수를 호출하여 이미지의 주소를 가져와 `state`의 `imagePath`에 저장하도록 구현하였습니다. 다른 라우터로 이동시에도 `imagePath`에 값이 그대로 저장되어 있기 때문에, 프로필 정보 수정하기 위해 해당 라우터로 진입시,`imagePath`의 이미지가 그대로 보여지게 됩니다.  |

|해결|
 |---|
 |라우터 이동시에는 `imagePath`의 값을 초기화하여 보여지지 않게 합니다.|

 ```js
//  ~layout/default.vue
  watch: {
     ...mapMutations('books', ['resetImgagePath']),
    $route () {
      ...
       // 라우터가 변경되면 썸네일 이미지가 저장하지 않은 경우에는 초기화할 수 있도록 하기
      if (this.Thumbnail.length !== 0) {
        return this.resetImgagePath()
      }
    }
  }
 ```
 - `watch`를 이용하여 `route`를 지켜보고, 변경시에는 이미지를 초기화하는 `mutations`함수를 호출하여 `state`의 `imagePath`를 초기화시켜줍니다.
 ```js
//  ~/store/book.js mutations
   removeThumbnail (state) {
    state.imagePath = []
  }
 ```
 <br><br>

#### <div style="color:red;">1-2. 비밀번호 수정</div>

#### 1.카카오 로그인과 구글 로그인시에는 비밀번호 수정이 필요 없기 때문에 로그인 시 `state`의 `user` 객체에서 `provier` 속성을 이용해 값이 존재하지 않을 경우에만 비밀번호 변경할 수 있도록 구현하였습니다.
(<a href="#state_user">`store`의 `state` `user`정보</a>)
```js
// 로그인시 저장된 user 정보 중 provider 속성+
  user:{
    ...
    // 카카오로그인인지,구글로그인인지 구분하는 속성
    // 카카오로 로그인할시, 해당 속성은 null 이 아닌 "kakao"로 저장된다.
    // 구글로 로그인할시, 해당 속성은 null 이 아닌 "google"로 저장된다.
    provider:null
  }

```
```html
     <!-- user/info.vue -->
     <!-- store 의 state user 객체 중 provider 속성이 null 일 때에만 비밀번호 수정란이 보이도록 구현 -->
      <div v-if="getUser && !getUser.provider" class="profile_box">
      <h2>비밀번호 수정</h2>
      <form class="form_content" @submit.prevent="onChangePassword">
        <div>
          <label for="email">이메일</label>
          <p><input id="email" class="readonly" readonly :value="getUser.email" type="text"></p>
        </div>
        ...
        <button class="round-btn" type="submit" :disabled="!isconfirmPassword ||!password">
          비밀번호 수정
        </button>
      </form>
    </div>
 ```
 - 유효성 검사는 위의 <a href="register">`회원가입/로그인 구현`</a> 시 구현했던 방법과 같습니다.
 <br><br>

### <div id="book_search" style="color:blue;"><b>3. 원하는 책 검색 및 추가</b></div>


|컴포넌트|라우터|
|---|---|
|components/form/Search.vue|books/search|
|components/book/CardDetail.vue|books/search|

#### 1. 카카오 api 중 검색기능을 사용했습니다.<br>
그 중 책검색 api를 이용했습니다.
([카카오 개발자 센터](https://developers.kakao.com/docs/latest/ko/daum-search/dev-guide) 참고)

- 해당 api는 title(제목), isbn (ISBN), publisher(출판사), person(인명)로 검색 필드를 제공해주기 때문에 옵션에 따라, 검색 필드를 제한할 수 있도록 구현했습니다.
```html
<!-- ~pages/search/index.vue -->
<template>
  <!-- v-model 를 이용해 data 인 search 를 바운딩시켜줍니다. -->
  <form-search v-model="search" :options="options" @searchBook="onSearchBook" @selectedOption="onSeletedOption"></form-search>
</template>
```
```js
// ~pages/search/index.vue
data(){
  options: ['통합', '제목', '저자', '출판사', 'isbn'],
   search: '',
   ...
}
```
```js
// ~/components/form/search.vue
  props: {
    options: {
      type: Array,
      required: true
    },
    // v-model로 바운딩시켜준 search 의 value 값
    value: {
      type: String || Number,
      required: false
    }
  },
```
- `v-model`로 바운딩시켜준 `data`의 `search`의 `value`를  props로 내려주고 `form-search.vue`에서 `value`로 props를 받았습니다.

#### 2. `select` 태그는 브라우저마다 다르게 보이기 때문에 통일되게 보일 수 있도록 `select` 태그를 사용하지않고 ul태그를 이용해 구현했습니다.
```html
<template>
  <form class="search_form" @submit.prevent="$emit('searchBook')">
    <!-- 해당 태그에 마우스를 올리면 selected 를 true로 하여 옵션이 보이고, 마우스를 올리지 않으면 selected 를 false로 하여 옵션을 보이지 않게 합니다. -->
    <div class="main_select" @mouseenter="selected=!selected" @mouseleave="selected=false">
      <div>
        <!-- props로 받은  options: ['통합', '제목', '저자', '출판사', 'isbn'] 중 선택한 한개의 요소를 보여줍니다. -->
        <p>{{ selectedOption }}<i class="fas fa-chevron-down"></i></p>
      </div>
      <!--  -->
      <ul v-if="selected" class="custom_select">
        <li v-for="(option,index) in options" :key="index" @click="changeSelect(option)">
          {{ option }}
        </li>
      </ul>
    </div>
    <!-- input으로 입력폼의 값이 변할 때마다 이벤트를 상위 컴포넌트에 보내주어 value값을 변경해줍니다. -->
    <input ref="searchInput" :value="value" type="text" placeholder="검색" 
    @input="$emit('input',$event.target.value)">
    <button type="submit">
      <i class="fas fa-search"></i>
    </button>
  </form>
</template>
```

#### 3. 옵션에 따라 카카오 검색 api를 호출합니다.
#### 3-1 store
|<div>actions</div>|
|---|
|SearchBooks|

```js
//store/book.js actions
   async SearchBooks ({ commit }, bookinfo) {
    try {
      const { size, query, reset, page } = bookinfo
      const api = `/books/search/kakao?query=${query}&size=${size}&page=${page}`
      let res
      // target의 내용에 따라 검색하는 api를 호출합니다.
      // target이 "제목"이라면, 제목에서 검색한 api를 호출합니다.
      // target이 "출판사"이라면, 출판사에서 검색한 api를 호출합니다.
      if (bookinfo.target) {
        res = await this.$axios.get(`${api}&target=${bookinfo.target}`)
      } else {
        // 통합검색(target과 상관없이 "제목","출판사","저자" 등 과 관련없이 모든 요소에서 검색한 api를 호출합니다.)
        res = await this.$axios.get(api)
      }
      commit('addBook', { data: res.data, reset })
    } catch (error) {
      console.error(error)
    }
  }
```
- `axios`를 이용해 api를 호출합니다.
- 통합검색/옵션에 따른 검색 두가지 방법으로 구현하기 위해, 호출하는 api를 구분하였습니다.
- `commit`를 이용해 `mutatonis`을 호출합니다.


|mutations|
|---|
|addBook|
```js
//store/book.js  mutations
   addBook (state, bookinfo) {
    const { data, reset } = bookinfo
    if (reset) {
      state.searchbooks = []
      state.meta = {}
    }
    data.documents.forEach((book) => {
      state.searchbooks.push(book)
    })
    state.searchbooks.forEach((book, index) => {
      book.id = index
    })
    state.meta = data.meta
  }
```
- 카카오 api 호출시, documents와 meta 로 값을 받아옵니다.
- 책에 관련된 정보는 state 의 searchbooks에 저장하고, 검색된 문서 수,마지막페이지인지 여부의 정보는 state의 meta에 저장하였습니다.
- api 호출시 한번에 `20`개씩 데이터를 불러오고, `더보기 버튼`을 누를 시, 다음 데이터 `20`개를 가져오도록 구현하였습니다.
<br><br>
|`{reset:true}`객체를 이용해 초기화 여부를 확인하는 이유|
|---|
|문제점|
|검색되는 정보를 `state`의 `searchbooks`배열에 저장하다보니, 옵션에 따라 검색정보를 다르게 검색했을 때, 기존 배열에 누적되는 문제가 발생했습니다.
예시)옵션을 "통합"으로 설정하여 검색한 후,"제목"이나 "출판사" 등과 같은 다른 옵션으로 다시 재검색시, 기존에 존재하는 책의 정보가 없어지지않고 누적이 됩니다.|

|해결|
|---|
|`{reset:true}` 별도의 객체를 주어, `{reset:true}`일 경우, `searchbooks` 배열을 초기화하고 그러지 않다면 기존 배열에 계속 누적시킵니다.
예시)옵션을 "통합"으로 설정하여 검색한 후,"제목"이나 "출판사" 등과 같은 다른 옵션으로 다시 재검색시, 기존에 존재하는 책의 정보를 초기화시켜줍니다.|

|getters|
|---|
|getsearchbooks|
```js
//store/book.js  getters
  getsearchbooks (state) {
    return state.searchbooks
  },
```
- `state`의 ` searchbooks`배열을 가져옵니다.

|state|
|---|
| searchbooks|
```js
//store/book.js state
searchbooks: [],
 meta: {},
```
 - <div >성공적으로 api 호출 시, 아래 데이터 정보를 저장합니다.</div>
  <a href="https://developers.kakao.com/docs/latest/ko/daum-search/dev-guide">카카오 개발자 센터 책 검색 참고</a>
```js
 searchbooks: [{
     authors: Array[1]
     contents: datetime: "2018-06-01T00:00:00.000+09:00"
     id: 0
     isbn: "1158360940 9791158360948"
     price: 12000
     publisher: "책읽는곰"
     sale_price: 10800
     status: "정상판매"
     thumbnail: "https://search1.kakaocdn.net/thumb/R120x174.q85/?fname=http%3A%2F%2Ft1.daumcdn.net%2Flbook%2Fimage%2F1581875%3Ftimestamp%3D20210613134526"
     title: "친구가 미운 날(작은곰자리 36)(양장본 HardCover)"
     translators: Array[1]
     url: "https://search.daum.net/search?w=bookpage&bookId=1581875&q=%EC%B9%9C%EA%B5%AC%EA%B0%80+%EB%AF%B8%EC%9A%B4+%EB%82%A0%28%EC%9E%91%EC%9D%80%EA%B3%B0%EC%9E%90%EB%A6%AC+36%29%28%EC%96%91%EC%9E%A5%EB%B3%B8+HardCover%29"
   }],
   meta: {
    //  마지막 페이지 여부
     is_end: false 
    //  중복된 문서를 제외하고, 처음부터 요청 페이지까지의 노출 가능 문서 수
     pageable_count: 448
    //  검색된 문서 수
     total_count: 450
   }
```

<br><br>

#### 3-2 입력폼에 검색을 원하는 입력값을 작성하여 검색 api를 호출합니다.
(api 한번 호출될 때 `20`개의 데이터를 불러오고, `더보기 버튼`을 클릭할 때 다음 데이터 `20`개를 불러오도록 구현하였습니다.)
- `store`의 `actioins` 함수 `SearchBooks`를 호출합니다. 
```js
// ~/page/search/index.vue
methods:{
      data(){
          reset: false,
      },
      // 입력폼에 원하는 입력값을 작성하여 엔터를 클릭할시 onSearchBook함수를 호출합니다.
      onSearchBook () {
        // 더보기 버튼으로 구현하여 데이터를 가져오기 때문에, 입력폼에 엔터를 누른다면 초기화시켜줍니다.
      this.resetBook(true)
      this.currentpage = 1
      this.currentCount = 0
      this.onFetchbook()
    },
      onFetchbook () {
        // 입력폼에 아무것도 작성하지 않고 엔터를 누른다면 사용자에게 에러메세지로 알려주고,return 해줍니다.
      if (this.search.length <= 0) {
        this.errmsg = true
        return
      }
      let bookinfo = { size: this.size, query: this.search, page: this.currentpage, reset: this.reset }
      // 옵션에따라 target을 변경합니다.
      switch (this.option) {
        case '통합':
          bookinfo = { ...bookinfo }
          break
        case '제목':
          bookinfo = { ...bookinfo, target: 'title' }
          break
        case '저자':
          bookinfo = { ...bookinfo, target: 'person' }
          break
        case '출판사':
          bookinfo = { ...bookinfo, target: 'publisher' }
          break
        case 'isbn':
          bookinfo = { ...bookinfo, target: 'isbn' }
          break
        default:
          break
      }
      this.loading = true
      this.SearchBooks(bookinfo)
        .then(() => {
          this.errmsg = false
          // 데이터를 호출할때마다 현재페이지를 증가시킵니다.
          this.currentCount += this.size
          // 마지막 페이지인지 여부인지 확인하는 데이터를 저장시킵니다.
          this.isend = this.meta.is_end
          this.showbutton()
          this.loading = false
        })
    },
     resetBook (boolean) {
      this.reset = boolean
    },
      showbutton () {
        // 마지막페이지라면 더보기 버튼이 보여지지 않도록 합니다.
      this.isend ? this.showbtn = false : this.showbtn = true
    },
}

```
#### 3-3. 더보기 버튼을 클릭할 시, 다음 데이터 `20`개를 불러오도록 구현하였습니다.
```html
<!-- ~/page/search/index.vue -->
<template>
  ...
  <!-- 더보기 버튼 -->
  <div v-if="showbtn" ref="btn" class="btn">
    <div v-if="loading" class="loading-spin">
      <i class="fas fa-spinner fa-spin"></i>
    </div>
    <div v-else>
      <button class="round-btn fill more-btn" @click=" addFetchBook">
        {{ currentCount }} / {{ meta.pageable_count }}
        <!-- 현재페이지수/총페이지수(중복제거한 총 페이지 수) -->
      </button>
    </div>
  </div>
  ...
</template>
```
```js
methods:{
  ...
  // 더보기 버튼을 누르면 해당 함수를 호출합니다.
   addFetchBook () {
      if (!this.isend) {
        this.currentpage++
        this.resetBook(false)
        this.onFetchbook()
      }
    },
}
```
#### methods
|addFetchBook|
|---|
|더 불러올 데이터가 있다면, 현재페이지를 증가시켜주고, api를 호출합니다.(더보기 버튼이므로 초기화할 필요 없으므로 `resetBook`에 `false`를 전달해줍니다.)|
<br><br>

 ### *검색 할 때 문제점
 |문제점|
 |---|
 |`SearchBooks`함수로 `state`의 `searchbooks` 배열에 데이터를 저장하도록 구현했습니다.
 라우터 이동시, 초기화시켜주지 않으면 다시 검색하기 위해 해당 라우터에 이동했을 때, 전에 검색했던 데이터가 그대로 남아 보여지게 됩니다.  |

|해결|
 |---|
 |라우터 이동시에는 `searchbooks`배열을 초기화하여 보여지지 않게 합니다.|

 ```js
//  ~layout/default.vue
  watch: {
     ...mapMutations('books', ['initsearchBook']),
    $route () {
      ...
       // 라우터가 변경되면 기존 검색기능 초기화할 수 있도록 하기
     if (this.books.length !== 0) {
        console.log(this.books)
        return this.initsearchBook()
      }
    }
  }
 ```
 - `watch`를 이용하여 `route`를 지켜보고, 변경시에는 이미지를 초기화하는 `mutations`함수를 호출하여 `state`의 `searchbooks`배열을 초기화시켜줍니다.
 ```js
//  ~/store/book.js mutations
// 책의 정보와 meta에 저장된 정보를 초기화시켜줍니다.
  initsearchBook (state) {
    state.searchbooks = []
    state.meta = {}
  },
 ```
 <br><br>

#### 4. 검색한 책 중 원하는 책을 골라 책을 추가할 수 있도록 구현하였습니다.

#### 4-1 store
|<div>actions</div>|
|---|
|createBook|

```js
//store/book.js actions
    async createBook (_,bookinfo) {
    const res = await this.$axios.post('/books/add', bookinfo)
    return res
  }
```
- `axios`를 이용해 api를 호출합니다.
- <b>책을 추가한 후, 추가한 책은 다른 라우터 `/books/_page`에 보여주도록 구현하였고, 해당 라우터에 진입시 `nuxt`의`asyncData` 훅을 사용해, 데이터를 불러오도록 구현하였으므로, 여기서는 별도의 `mutations`를 호출하여 `state` 값을 변화시키지 않았습니다. </b>

<br><br>

#### 4-2 `추가하기 버튼`을 클릭하면 데이터를 추가하는 api를 호출합니다.
- `store`의 `actioins` 함수 `createBook`를 호출합니다. 
```html
<!-- ~/page/search/index.vue -->
<template>
  ...
  <div v-for="(book,index) in books" :key="index" class="search_book">
        <!-- book-card-detail -->
        <book-card-detail :book="book"></book-card-detail>
        <!-- 추가하기 버튼 -->
        <button class="round-btn addbtn red" @click="onaddBook(book)">
          추가하기
        </button>
      </div>
  ...
</template>
```
```js
// ~/page/search/index.vue
methods:{
        async onaddBook (book) {
      try {
        const bookinfo = { title: book.title, contents: book.contents, datetime: book.datetime, isbn: book.isbn, publisher: book.publisher, thumbnail: book.thumbnail, url: book.url, authors: this.bookauthors(book.authors) }
        await this.createBook(bookinfo)
          .then((res) => {
            // 데이터가 성공적으로 호출됐을 때 알림메세지로 알려주도록 구현했습니다.
            bus.$emit('on:alert', res.data.msg)
            setTimeout(() => {
              bus.$emit('off:alert')
            }, 3000)
          })
      } catch (error) {
        // 이미 저장된 데이터를 또 추가하려고 할 때, 알림메세지로 알려주도록 구현했습니다.
        console.log(error)
        bus.$emit('on:alert', error.response.data.msg)
        setTimeout(() => {
          bus.$emit('off:alert')
        }, 3000)
      }
    },
     bookauthors (authors) {
      //  book.authors 는 배열이므로, 문자열로 포맷합니다.
      return authors.join(',')
    },
}
```
#### metohds
|onaddBook|
|---|
|`state`의 `actions` 함수 `createBook` 를 호출합니다.|
|데이터는 `title` , `contents` , `datetime` , `isbn`,`publisher` , `thumbnail` , `url` , `authors`를 객체로 보내줍니다.|

#### 4-3 `추가하기 버튼`을 클릭한 후, 알람메세지를 띄워 알려줍니다.

- `eventbus`로 구현하였습니다.
#### eventbus
```html
<!-- ~layouts/default.vue -->
<template>
  ...
  <common-alert-msg :alert-state="alertState" :data="data"></common-alert-msg>
  ...
</template>

```
```js
//  ~layouts/default.vue
import bus from '~/utils/bus'
...
 data () {
    return {
      alertState: false,
      data: ''
    }
  },
    created () {
    bus.$on('on:alert', (data) => {
      this.data = data
      this.onalert()
    })
    bus.$on('off:alert', this.offalert)
  },
  methods:{
      onalert () {
      this.alertState = true
    },
    offalert () {
      this.alertState = false
    }
  }
```
<br>

```html
<!-- ~/components/common/alertMsg.vue -->
<!-- transition으로 애니메이션 효과 -->
<transition name="upSlide">
    <div v-if="alertState" class="alertmsg">
      {{ data }}
    </div>
  </transition>
```
- `transition`으로 애니메이션 효과를 주었습니다.
(<a href="https://vuejs.org/v2/api/#transition">vue transitioin 문서 바로 가기</a>)
```css
/* upSlide animation */
.upSlide-enter-active {
    transition: all .3s ease;
  }
  .upSlide-leave-active {
    transition: all .5s ease-in-out;
  }
  .upSlide-enter, .upSlide-leave-to/ {
    transform:translateX(-50%) translateY(10px);
    opacity: 0;
  }
```
<br>

```js
//  ~/components/common/alertMsg.vue
  props: {
    alertState: {
      type: Boolean,
      required: true
    },
    data: {
      type: String,
      required: true
    }
  }
```
### props
|alertState|data|
|---|---|
|알람메세지 여부를 `Boolean`으로 받도록 구현했습니다.|`데이터 추가`,`데이터 삭제` 등에 상관없이 알람메세지를 공통으로 적용해주기 위해 알림메세지 내용을 `data` `props`로 받도록 구현했습니다.|

```js
 async onaddBook (book) {
      try {
        ....
        await this.createBook(bookinfo)
          .then((res) => {
            // 서버에서 내려준 데이터를 출력하도록 하고, 3초뒤에 알람메세지가 보이지 않도록 구현했습니다.
            bus.$emit('on:alert', res.data.msg)
            setTimeout(() => {
              bus.$emit('off:alert')
            }, 3000)
          })
      } catch (error) {
        console.log(error)
         // 서버에서 내려준 데이터를 출력하도록 하고, 3초뒤에 알람메세지가 보이지 않도록 구현했습니다.
        bus.$emit('on:alert', error.response.data.msg)
        setTimeout(() => {
          bus.$emit('off:alert')
        }, 3000)
      }
    },
```
<br><br>

### <div id="book_create" style="color:blue;"><b>3. 직접 책 추가 및 추가한 책 수정 및 삭제</b></div>


|컴포넌트|라우터|
|---|---|
|components/form/FormBookAdd.vue|books/add|
|components/book/Edit.vue|books/b/_id|

#### <div  style="color:red;"><b>3-1. 직접 책 추가</b></div>
|컴포넌트|라우터|
|---|---|
|components/form/FormBookAdd.vue|books/add|

#### 1. `책제목` , `책 내용` , `저자` 는 필수 값으로 입력되도록 구현하고, 필수 값이 입력되지 않으면 버튼을 클릭 할 수 없도록 구현하였습니다.
```html
<!-- ~/components/form/BookAdd.vue -->
<template>
  <form class="form_content addform" @submit.prevent="onaddBook">
    <div>
      <label for="">책제목</label>
      <p>
        <input ref="titleInput" v-model="newBook.title" :class="{'invalid':!newBook.title}" type="text"><i
          v-if="newBook.title" class="fas fa-plus-circle" @click="resetInput($event,'title')"></i>
      </p>
    </div>
    <div v-if="!newBook.title" class="err">
      책제목은 필수입니다.
    </div>
    <div>
      <label for="">책 내용</label>
      <p class="text">
        <textarea v-model="newBook.contents" cols="30" rows="10" :class="{'invalid':!newBook.contents}"></textarea><i
          v-if="newBook.contents" class="fas fa-plus-circle" @click="resetInput($event,'contents')"></i>
      </p>
    </div>
    <div v-if="!newBook.contents" class="err">
      책내용은 필수입니다.
    </div>
    ....
    <label>저자</label>
    <p>
      <input v-model="newBook.authors" :class="{'invalid':!newBook.authors}" type="text"><i v-if="newBook.authors"
        class="fas fa-plus-circle" @click="resetInput($event,'authors')"></i>
    </p>
    </div>
    <div v-if="!newBook.authors" class="err">
      저자는 필수입니다.
    </div>
    ...
    <button type="submit" class="round-btn red addbtn"
      :disabled="!newBook.title || !newBook.authors || !newBook.contents">
      추가하기
    </button>
  </form>
</template>
```
```js
data () {
    return {
      newBook: {
        title: '',
        contents: '',
        url: '',
        isbn: '',
        authors: '',
        publisher: '',
        selectedFile: '',
        datetime: new Date()
      }
    }
  }
```
- `button`의 `disabled`에 바운딩시켜 필수값이 없으면 버튼을 클릭 하지 못하도록 구현하였습니다.

- `class`를 바운딩시켜 입력값이 없으면 `invalid`가 추가되도록 구현하였습니다.(<a href="invalid">클래스명 `invalid` 참고</a>)

- 참고로 `<input>` 태그에서는 `after` 가상요소 선택자가 적용되지 않아, 별도의 `<p>` 태그로 감싸주어 해당 태그에서 `class`를 바운딩시켜 `invalid::after`요소를 추가하였습니다.
```html
<!-- ~/components/form/BookAdd.vue -->
...
<!-- 입력폼에 입력값이 없다면 클래스명에 invalid 를 추가합니다.  -->
<p :class="{'invalid':!newBook.title}" >
  <input ref="titleInput" v-model="newBook.title"type="text"><i
    v-if="newBook.title" class="fas fa-plus-circle" @click="resetInput($event,'title')"></i>
</p>
...
```
<br><br>

#### 2. 입력폼에 입력된 값이 하나라도 있다면 `엑스 버튼`이 생기고, 해당 버튼을 클릭하면 해당  입력폼을 초기화시켜줍니다.
```html
<!-- ~/components/form/BookAdd.vue -->
...
<p :class="{'invalid':!newBook.title}" >
  <input ref="titleInput" v-model="newBook.title"type="text">
  <!-- 엑스버튼 -->
  <i v-if="newBook.title" class="fas fa-plus-circle" @click="resetInput($event,'title')"></i>
</p>
...
```
```js
 resetInput (e, data) {
  //  target은 input태그가 된다
      const target = e.target.parentNode.firstChild
      // 입력값 초기화
      this.newBook[data] = ''
      // 입력값을 초기화 한후 target 태그 포커스
      target.focus()
    }
```
#### methods
|resetInput|
|---|
|`event`를 인자로 넘겨주어,`target`이 `input`태그가 선택되도록 구현하였습니다.|
|` const target = e.target.parentNode.firstChild` : `i`태그의 부모요소의 첫번째 자식요소 태그인 `input` 태그|
|`data`의 `newBook`에서 해당 되는 속성을 찾아 빈값을 대입 : `v-model`로 `newBook`의 값을 양방향 바운딩시켜주었기 때문에 해당 값을 빈값으로 초기화 할시, 입력폼에 있는 입력값도 초기화됩니다.|
|`엑스 버튼`으로 입력값을 초기화 시켜주고, 다시 해당 입력폼에 `focus`되도록 구현하였습니다.(`target`인 `input`태그에 `focus`)|
<br>

#### <div style="color:blue;">* 참고로 IE(인터넷 익스플로러) 10 이상에서 Input text Box에 포커스 되면 우측에 x 표시가 생기는데, 이는 직접 구현하였으므로, IE(인터넷 익스플로러) 우측에 x 표시가 생기지 않도록 초기화시켜주었습니다.</div>
```css
/* static/css/style.css */
/* ms 인풋요소 엑스박스제거 */
::-ms-clear{display: none;}
```

<br><br>

####  <div id="book_image_add">3. 책의 썸네일 이미지를 추가할 수 있도록 구현하였습니다.</div>
```html
<!-- ~/components/form/BookAdd.vue -->
<template>
  ...
  <!-- 이미지 추가 -->
  <div class="file_container add">
    <div class="txt">
      <label for="fileinput"><span class="round-btn yellow"><i class="far fa-file-image"></i>책 이미지 추가</span></label>
      <input id="fileinput" style="display:none" type="file" @change="onChangeImage">
    </div>
   ....
  </div>
  ...
</template>
```
- 이미지 추가 관련 내용은 위의 사용자 프로필(썸네일) 업로드 내용과 유사합니다.
(<a href="#image_add">이미지 업로드 관련 내용 바로가기</a>)

```js
 data() {
     return {
       selectedFile: ''
     }
   },
   methods: {
     ...mapActions('books', ['uploadImg']),
     onChangeImage(e) {
       const selectedFile = e.target.files[0]
       const imageFormData = new FormData()
       imageFormData.append('photo', selectedFile)
       this.uploadImg(imageFormData, {
         user: true
       })
     },
   }
```

### methods
|onChangeImage|
|---|
| FormData 형식을 이용해 이미지 정보를 저장합니다.(여기서는 이미지는 1개만 선택할수 있도록 하였습니다.)|
<br><br>

#### 4. 책의 썸네일 이미지를 미리볼 수 있도록 구현하였습니다.
- 책의 썸네일 이미지를 선택하면, 이미지를 미리 보여줍니다.
(<b>`추가하기 버튼`을 클릭해야 책의 썸네일 이미지가 저장됩니다.</b>)
- 책의 썸네일 이미지를 미리 보여줄 때, `엑스 버튼`이 보이고, 해당 버튼을 누르면 미리 보여주고 있는 책의 썸네일 이미지를 없애줍니다.
```html
<!-- ~/components/form/BookAdd.vue  -->
<template>
...
    <div class="file_container add">
      <div class="txt">
        <label for="fileinput"><span class="round-btn yellow"><i class="far fa-file-image"></i>책 이미지 추가</span></label>
        <input id="fileinput" style="display:none" type="file" @change="onChangeImage">
      </div>
      <div v-if="getImagePath.length !== 0" class="photos">
        <div class="images">
          <img :src="getImagePath" alt="">
        </div>
        <div>
        <!-- 미리보기 이미지가 있다면 엑스버튼이 보이고, 엑스버튼을 클릭하면 미리보기 이미지를 업애줍니다. -->
          <button class="deletebtn" type="button" @click="onRemoveImage">
            <i class="fas fa-plus-circle"></i>
          </button>
        </div>
      </div>
    </div>    
    ...
    </template>
```
```js
...mapMutations('books', ['removeThumbnail']
 onRemoveImage () {
      this.newBook.selectedFile = null
      this.removeThumbnail()
    }
```

#### metohds
|onRemoveImage |
|---|
|`store`의 `mutations`함수인 `removeThumbnail`를 호출하여 `state`의 `imagePath`를 초기화시켜줍니다.|
#### mutations
|mutations|
|---|
|removeThumbnail|
```js
// store/book.js mutations
// 이미지 초기화
 removeThumbnail (state) {
    state.imagePath = []
  },
```
<br><br>

#### 5. 날짜는 <a href="https://github.com/mengxiong10/vue2-datepicker#readme">vue2-datepicker</a> 라이브러리를 사용하였습니다.

|해당 라이브러리를 사용한 이유|
|---|
|`<input type="date">`는 브라우저마다 다르게 보여지기 때문에 브라우저에 상관없이 동일하게 보여주기 위해 사용하였습니다.|
<br><br>

#### 6. 책 추가 api 호출
#### 6-1. store
|<div>actions</div>|
|---|
|createBook|

```js
//store/book.js actions
    async createBook (_,bookinfo) {
    const res = await this.$axios.post('/books/add', bookinfo)
    return res
  }
```
- `axios`를 이용해 api를 호출합니다.
- <b>책을 추가한 후, 추가한 책은 다른 라우터 `/books/_page`에 보여주도록 구현하였고, 성공적으로 책 추가시, 라우터를 변경하도록 구현하였습니다.<br>
` this.$router.push('/books/1')` <br>해당 라우터에 진입시 `nuxt`의`asyncData` 훅을 사용해, 데이터를 불러오도록 구현하였으므로, 여기서는 별도의 `mutations`를 호출하여 `state` 값을 변화시키지 않았습니다. </b>


<br><br>


#### <div  style="color:red;"><b>3-2. 추가한 책 수정</b></div>
|컴포넌트|라우터|
|---|---|
|components/book/Edit.vue|books/b/_id|

#### 1. 책 추가와 책 수정은 비슷한 구성이므로, 공통적인 요소는 `mixin`으로 처리하였습니다.

#### Mixin
```js
// ~/mixins/BookFetchMixin.js
 mounted () {
  //  마운트될 때, 타이틀입력폼 태그에 포커스되도록 구현
    setTimeout(() => {
      this.$refs.titleInput.focus()
    }, 400)
  },
  computed: {
    ...mapGetters('books', ['getImagePath'])
  },
  methods: {
    async fetchData () {
      try {
        const data = new FormData()
        data.append('title', this.newBook.title)
        data.append('contents', this.newBook.contents)
        data.append('datetime', this.newBook.datetime)
        data.append('isbn', this.newBook.isbn)
        data.append('publisher', this.newBook.publisher)
        data.append('photo', this.selectedFile)
        data.append('url', this.newBook.url)
        data.append('authors', this.newBook.authors)
        if (!this.$route.params.id) {
          // 책을 추가할 때
          await this.createBook(data)
            .then((res) => {
              this.$router.push('/books/1')
              // 이벤트 버스로 알림메세지 구현
              bus.$emit('on:alert', res.data.msg)
              setTimeout(() => {
                bus.$emit('off:alert')
              }, 3000)
            })
        } else {
          // 책을 편집할 때
          await this.updateBook({ id: this.$route.params.id, data })
            .then((res) => {
              this.oneditStateChange()
              this.showimage = false
              this.resetImage = false
               // 이벤트 버스로 알림메세지 구현
              bus.$emit('on:alert', res.data.msg)
              setTimeout(() => {
                bus.$emit('off:alert')
              }, 3000)
            })
        }
      } catch (error) {
        console.log(error)
          // 이벤트 버스로 알림메세지 구현
        bus.$emit('on:alert', error.response.msg)
        setTimeout(() => {
          bus.$emit('off:alert')
        }, 3000)
      }
    },
    // 이미지 업로드시, 실행되는 함수
    onChangeImage (e) {
      this.selectedFile = e.target.files[0]
      const imageFormData = new FormData()
      imageFormData.append('photo', this.selectedFile)
      this.uploadImg(imageFormData)
    },
    // 엑스버튼을 클릭할 시, 초기화 시켜주는 함수
    resetInput (e, data) {
      const target = e.target.parentNode.firstChild
      this.newBook[data] = ''
      target.focus()
    }
  }
```
<br><br>

#### 2. 책 수정 api 호출
#### 2-1. store
|<div>actions</div>|
|---|
|updateBook|

```js
//store/book.js actions
    async updateBook ({ commit }, bookinfo) {
    const res = await this.$axios.put(`/books/${bookinfo.id}`, bookinfo.data)
    commit('loadbook', res.data.book)
    return res
  }
```
- `axios`를 이용해 api를 호출합니다.
- `commit`를 이용해 `mutatonis`을 호출합니다.


|mutations|
|---|
|loadbook|
```js
//store/book.js  mutations
  loadbook (state, bookinfo) {
    state.book = bookinfo
  }
```
-  `state`의 `book` 객체에 데이터를 저장합니다.
<br><br>

|getters|
|---|
|getBook |
```js
//store/book.js  getters
  getBook (state) {
    return state.book
  }
```
- `state`의 `book` 객체를 가져옵니다.

|state|
|---|
|books|
```js
//store/book.js state
  book: {}
```

<br><br>

#### <div  style="color:red;"><b>3-3. 추가한 책 삭제</b></div>
|라우터|
|---|
|books/b/_id|

#### 1. `삭제 버튼`을 클릭하면 한번 더 확인하는 알림창이 뜨고, 확인 버틀을 누를시에만 삭제하도록 구현하였습니다.
- `삭제 버튼`은 다른 사람의 책이 아닌 내 책에서만 `버튼`이 보이도록 구현하였습니다.
```html
<!-- ~/pages/books/b/_id.vue -->
<template>
  <div>
  ...
  <!-- 삭제 / 수정 버튼 -->
    <div class="control_btns">
      <div class="left_btn">
        <button class="primary-btn red" @click="onremoveBook">
          <i class="fas fa-trash-alt"></i>삭제
        </button>
        <button class="primary-btn" @click="onEditBook">
          <i class="fas fa-pen-square"></i>수정
        </button>
      </div>
  ...
  <!-- 삭제 버튼 클릭 시, 알림창을 띄우기 -->
   <form-alert v-if="alert" :data="getBook && getBook.title" :confirm="`삭제`" @onagree=" agree" @ondisagree="disagree"></form-alert>
  </div>
</template>
```
```html
<!-- ~/components/form/Alert.vue -->
<template>
  <common-modal class="alert_main">
    <div slot="header">
      <b>{{ data }}</b> 을(를) {{ confirm }}하시겠어요??
    </div>
    <div slot="body" class="body">
    <!-- "네" 버튼 클릭 시, onagree 이벤트를 상위 컴포넌트에 보내줍니다. -->
      <button class="primary-btn" @click="$emit('onagree')">
        네
      </button>
        <!-- "아니오" 버튼 클릭 시, ondisagree 이벤트를 상위 컴포넌트에 보내줍니다. -->
      <button class="primary-btn red" @click="$emit('ondisagree')">
        아니오
      </button>
    </div>
    <div slot="footer"></div>
  </common-modal>
</template>
```
```js
  props: {
    data: {
      type: String,
      required: true
    },
    confirm: {
      type: String,
      required: true
    }
  }
```
- 상위컴포넌트에서 `data`와 `confirm`을 `props`로 내려주었습니다.
<br><br>

#### 2. 확인 알림창에서 "네" 클릭시 삭제 api를 호출합니다.
#### 2-1. store
|<div>actions</div>|
|---|
|deleteBook|

```js
//store/book.js actions
    async deleteBook ({ commit }, { id }) {
    const res = await this.$axios.delete(`/books/${id}`)
    return res
  }
```
- `axios`를 이용해 api를 호출합니다.
- <b>책을 삭제한 후, 성공적으로 책 삭제시, 라우터를 변경하도록 구현하였습니다.<br>
` this.$router.push('/books/1')` <br>해당 라우터에 진입시 `nuxt`의`asyncData` 훅을 사용해, 데이터를 불러오도록 구현하였으므로, 여기서는 별도의 `mutations`를 호출하여 `state` 값을 변화시키지 않았습니다. </b>
#### 2-2. 확인 알림창에서 "네" 클릭 시, `agree`함수를 호출합니다.
```js
// ~/pages/books/b/_id.vue
    agree () {
      try {
        this.deleteBook({ id: this.$route.params.id })
          .then(() => {
            this.$router.push('/books/1')
          })
      } catch (error) {
        console.error(error)
      }
    }
```
<br><br>

### <div id="books" style="color:blue;"><b>4. 내가 추가한 책 보여주기</b></div>
|컴포넌트|라우터|
|---|---|
|components/books/Card.vue|books/_page|
|components/books/Empty.vue|books/_page|
|components/books/Pagination.vue|books/_page|

#### 1. `페이지네이션` 으로 구현하였고, `1페이지당` 12개의 데이터를 호출하여 가져오도록 구현하였습니다.
- `params`를 `_page`로 설정하여 라우터를 변경할 수 있도록 구현하였습니다.
예시) 1페이지: /books/1, 2페이지: /books/2 ...

|`params`로 라우터를 변경해주는 이유|
|---|
|`페이지네이션 버튼`을 직접 클릭하지 않아도,사용자가 직접 검색창에 `페이지`를 검색 할 수 있게 하기 위해서입니다. |

#### 2. 책 데이터가 존재하지 않거나, 잘못된 라우터로 진입시, 사용자에게 알려줍니다.
- 책 데이터가 존재하지 않거나, 사용자가 검색창에 `/books/333` 나
`/books/aa` 등 존재하지 않는 `params`로 검색했을 경우,
오류가 발생하지 않고, 사용자에게 책이 없다는 것을 알려줍니다.
```html
<template>
  <div class="bookshelf">
    <div class="head">
      <h2>나만의 책장</h2>
    </div>
    <div class="main_menu" @mouseleave="showMenu=false">
      <ul v-if="showMenu" class="sub_menu">
        <li>
          <nuxt-link to="/books/add">
            <i class="fas fa-book-open"></i>
            책 직접 추가
          </nuxt-link>
        </li>
        <li>
          <nuxt-link to="/books/search">
            <i class="fas fa-search"></i>
            책 검색
          </nuxt-link>
        </li>
      </ul>
      <a href="#" class="floating_btn" @mouseenter="showMenu=true"><i class="fas fa-plus"></i></a>
    </div>
    <!-- 책 데이터가 존재할 경우에만 보여줍니다. -->
    <div v-if="hasBook">
      <div class="books">
        <div v-for="book in getBooks" :key="book.id" class="book">
        <!-- 하위컴포넌트에 api를 호출하여 가져온 데이터를 props로 내려줍니다. -->
          <book-card :book="book"></book-card>
        </div>
      </div>
      <!-- books -->
      <!-- 페이지네이션 구현 -->
      <book-pagination :total-page="totalPage" @pageFirst="pagination" @pagination="pagination" @pageLast="pagination"></book-pagination>
    </div>
    <!-- 책 데이터가 존재하지 않으면 해당 컴포넌트를 보여줍니다. -->
    <div v-else>
      <book-empty></book-empty>
    </div>
  </div>
</template>
```
- `book-empty` `component`를 구현하여, 책 데이터가 없을 경우, 해당 컴포넌트를 보여주도록 구현하였습니다.

```html
<!-- ~/components/book/Empty.vue -->
<template>
  <div class="empty_book">
    <h3>책이 없어요</h3>
  </div>
</template>
```

#### <div id="fetchmxin">3. `페이지네이션`으로 구현하여 공통적으로 사용된 내용은 믹스인으로 처리하였습니다.</div>
<br>

#### 해당 라우터에서는 믹스인으로 처리하였습니다.
|라우터|구현 내용|
|---|---|
|books/_page|<a href="#books">내가 추가한 책 보여주기</a>|
|books/others/_page|<a href="#other_books">다른 사용자가 추가한 책 보여주기</a>|
|books/hashtags/_page|<a href="#tags">태그별로  책 보여주기</a>|
|ooks/search/_page|<a href="#other_search">다른 사용자의 책 검색하여 보여주기</a>|
<br>

#### <div style="color:red;">3-1. books/_page</div>
```js
// ~/pages/books/_page.vue
import PaginationFetchMixin from '~/mixins/PaginationFetchMixin'
export default {
  mixins: [PaginationFetchMixin],
  data () {
    return {
      showMenu: false
    }
  }

}
```
<br>

#### <div style="color:red;">3-2. books/others/_page</div>
```js
// ~/pages/books/others/_page.vue
import PaginationFetchMixin from '~/mixins/PaginationFetchMixin'
export default {
  mixins: [PaginationFetchMixin]

}
```
<br>

#### <div style="color:red;">3-3. books/hashtags/_page</div>
```js
// ~/pages/books/hashtags/_page.vue
import PaginationFetchMixin from '~/mixins/PaginationFetchMixin'
export default {
  mixins: [PaginationFetchMixin],
  watchQuery: ['name']

}
```
<br>

#### <div style="color:red;">3-4. books/search/_page</div>
```js
// ~/pages/books/search/_page.vue
import { mapGetters, mapMutations } from 'vuex'
import PaginationFetchMixin from '~/mixins/PaginationFetchMixin'
export default {
  mixins: [PaginationFetchMixin],
  computed: {
    ...mapGetters('books', ['getSearch'])
  },
  watch: {
    '$route.query': {
      handler (query) {
        this.searchOption(query.search)
        this.searchData(query.target)
      },
      deep: true,
      immediate: true
    }
  },
  watchQuery: ['search', 'target'],
  methods: {
    ...mapMutations('books', ['searchOption', 'searchData'])

  }

}
```
<br>

#### 믹스인
```js
import { mapGetters, mapActions } from 'vuex'
export default {
  // 해당 라우터에 진입시 api를 호출합니다.
  async asyncData ({ store, params, route }) {
    try {
      let total
      let totalPage
      const page = params.page
        // 라우터가 /books/_page 와 /books/others/_page 일 때,
      let data = { page: page - 1, route: route.name }
      // 라우터가  /books/search/_page 일 때,
      if (route.name === 'books-search-page') {
        data = { ...data, search: encodeURIComponent(route.query.search), target: encodeURIComponent(route.query.target) }
         // 라우터가 /books/hashtags/_page 일 때,
      } else if (route.name === 'hashtags-page') {
        data = { ...data, name: encodeURIComponent(route.query.name) }
      }
      // store의 actions 함수 fetchBooks 호출
      await store.dispatch('books/fetchBooks', data)
        .then((res) => {
          total = res.data.totalCount
          totalPage = res.data.totalPage
        })
      return { total, totalPage }
    } catch (error) {
      console.error(error)
    }
  },
  computed: {
    ...mapGetters('books', ['getBooks']),
    // 책 데이터가 존재하는지 여부 확인
    hasBook () {
      return this.getBooks.length
    }
  },
  methods: {
    ...mapActions('books', ['fetchBooks']),
    // 페이지네이션 버튼을 눌렀을 때 호출
    pagination (index) {
      switch (this.$route.name) {
        // 라우터가 /books/_page 일때,
        case 'books-page':
          return this.$router.push(`/books/${index + 1}`)
        // 라우터가 /books/others/_page 일때,
        case 'books-others-page':
          return this.$router.push(`/books/others/${index + 1}`)
        // 라우터가 /books/search/_page 일때,
        case 'books-search-page':
          return this.$router.push(`/books/search/${index + 1}?search=${this.getSearch.selectedOption}&target=${this.getSearch.data}`)
        // 라우터가 /books/hashtags/_page 일때,
        case 'hashtags-page':
          return this.$router.push(`/books/hashtags/${index + 1}?name=${this.$route.query.name}`)
        default:
          break
      }
    }

  }

}

```
<br>

#### 4. 해당 라우터에 진입시, api를 호출하도록 구현하였습니다.
|라우터|
|---|
|books/_page|
|books/others/_page|
|books/hashtags/_page|
|ooks/search/_page|

#### 4-1. store
|<div>actions</div>|
|---|
|fetchBooks|

```js
//store/book.js actions
   async fetchBooks ({ commit }, bookInfo) {
    let res
    switch (bookInfo.route) {
      // 라우터가 /books/_page 일때,
      case 'books-page':
        res = await this.$axios.get(`/books?page=${bookInfo.page}`)
        break
      // 라우터가 /books/others/_page 일때,
      case 'books-others-page':
        res = await this.$axios.get(`books/others/book?page=${bookInfo.page}`)
        break
      // 라우터가 /books/search/_page 일때,
      case 'books-search-page':
        res = await this.$axios.get(`books/others/book?page=${bookInfo.page}&search=${bookInfo.search}&target=${bookInfo.target}`)
        break
      // 라우터가 /books/hashtags/_page 일때,
      case 'hashtags-page':
        res = await this.$axios.get(`books/hashtags/${bookInfo.name}/?page=${bookInfo.page}`)
        break
      default:
        break
    }

    commit('loadBooks', { books: res.data.books, page: bookInfo.page })
    return res
  },
```
- `axios`를 이용해 api를 호출합니다.
- `commit`를 이용해 `mutatonis`을 호출합니다.


|mutations|
|---|
|loadBooks|
```js
//store/book.js  mutations
  loadBooks (state, { books, page }) {
    // state의 books 배열에 책 데이터를 저장합니다.
    state.books = [...books]
    // 현재페이지는 `state`의 currentPage에 저장합니다.
    state.currentPage = page
  },
```
-  `state`의 `book` 객체에 데이터를 저장합니다.
<br><br>

|getters|
|---|
|getBook |
```js
//store/book.js  getters
  getBooks (state) {
    return state.books
  }
```
- `state`의 `books` 배열을 가져옵니다.

|state|
|---|
|books|
```js
//store/book.js state
 books: [],
 currentPage: 0
```
 - <div id="state_user"> state의 books 배열에는 아래 정보를 저장합니다.</div>
```js
// books 배열에 저장되는 정보 예시
  books: [{
    // 코멘트
    Comments: Array[0]
    // 해시태그
    Hashtags: Array[0],
    // 좋아요 누른 닉네임
    Likers:Array[0],
    UserId: 7
    authors: "dd"
    bookmark: false
    contents: "dd"
    createdAt: "2021-06-14T15:39:04.810Z"
    datetime: "2021-06-14T15:38:52.000Z"
    id: 144
    isbn: ""
    publisher: ""
    thumbnail: null
    title: "d"
    updatedAt: "2021-06-14T15:39:04.810Z"
    url: ""
  }, ...]
```
<br>

#### <div id="#page" >4. `페이지네이션`은 컴포넌트를 따로 구현하였습니다.</div>
- 상위컴포넌트에서 `props`로 `totalPage`를 받아 총 페이지 갯수를 구현하였습니다.
```html
<!-- ~/components/book/Pagination.vue -->
<template>
...
<div v-if="showPage" class="pagination">
    ...
    <div class="page">
      <button
        v-for="(page,index) in totalPage"
        :key="page"
        :class="{'active':getCurrentPage=== index}"
        @click=" onPagination(index)"
      >
        {{ page }}
      </button>
    </div>
  ...
  </div>
</template>
```
```js
 props: {
    totalPage: {
      type: Number,
      required: true
    }
  },
    computed: {
      // 책데이터가 1페이지안에 존재하는 경우에는 페이지네이션을 보이지 않도록 했습니다.(한번에 12개씩 데이터를 불러오고,13개 미만이면 페이지네이션을 보여주지 않는다.)
    showPage () {
      return this.getBooks && this.totalPage > 1
    }
  },
  methods:{
        onPagination (index) {
          // 현재페이지와 내가 클릭한 페이지네이션의 인덱스가 같으면, 굳이 다시 데이터를 불러올 필요없이 리턴해줍니다.
      if (this.getCurrentPage === index) {
        return
      }
      this.$emit('pagination', index)
    }
  }
```
#### computed
|showPage|
|---|
|데이터가 13개 미만일 경우에는 1페이지만 존재하므로, 페이지네이션 버튼이 보이지 않도록 구현하였습니다.|
#### methods
|onPagination |
|---|
|현재페이지와 인덱스 번호가 같으면, 같은 페이지를 누른것으로, api가 호출되지 않도록 리턴해줍니다.|
|예시)2페이지에서 다시 2페이시 클릭시 데이터를 호출하지 않는다.|

<br><br>


### <div id="other_books" style="color:blue;"><b>5. 다른 사용자가 추가한 책 보여주기</b></div>
|컴포넌트|라우터|
|---|---|
|components/books/Card.vue|books/others/_page|
|components/books/Empty.vue|books/others/_page|
|components/books/Pagination.vue|books/others/_page|

#### 1. `페이지네이션`으로 구현하여 공통적으로 사용된 내용은 믹스인으로 처리하였습니다.<br>(<a href="#fetchmxin">믹스인</a> 및 <a href="page">페이지네이션</a> 바로가기)
<br>

### <div id="tags" style="color:blue;"><b>6. 태그별로  책 보여주기</b></div>
|컴포넌트|라우터|쿼리|
|---|---|---|
|components/books/Card.vue|hashtags/_page|name|
|components/books/Empty.vue|hashtags/_page|name|
|components/books/Pagination.vue|hashtags/_page|name|

#### 1. `페이지네이션`으로 구현하여 공통적으로 사용된 내용은 믹스인으로 처리하였습니다.<br>(<a href="#fetchmxin">믹스인</a> 및 <a href="page">페이지네이션</a> 바로가기)
<br>

#### 2. 태그이름을 쿼리로 받아 라우터를 구현하였습니다.
- /hashtags/페이지번호/?name="태그 이름"
```html
<!-- ~/components/hashtag/List.vue -->
<template>
  <ul class="hashtags tagList">
    <li v-for="(tag,index) in hashtags" :key="index" class="tag" @mouseenter="onChangeState(tag,index)" @mouseleave="tagNum=''">
      <nuxt-link :to="`/hashtags/1/?name=${tag.name}`">
        #{{ tag.name }}
      </nuxt-link>
    ...
    </li>
  </ul>
</template>
```

### <div id="other_search" style="color:blue;"><b>5. 다른 사용자의 책 검색하여 보여주기</b></div>
|컴포넌트|라우터|쿼리|
|---|---|---|
|components/books/Card.vue|books/search/_page|search,target|
|components/books/Empty.vue|books/search/_page|search,target|
|components/books/Pagination.vue|books/search/_page|search,target|

#### 1. `페이지네이션`으로 구현하여 공통적으로 사용된 내용은 믹스인으로 처리하였습니다.<br>(<a href="#fetchmxin">믹스인</a> 및 <a href="page">페이지네이션</a> 바로가기)
<br>

#### 2. `메뉴`에서 `다른 사용자 책 검색`을 클릭하면 입력폼이 보여지고, `책제목` , `저자` 두가지 옵션으로 검색할 수 있도록 구현하였습니다.
- - /books/search/페이지번호/?search="검색옵션"&target="검색 내용"
```html
<!-- ~/components/AppHeader.vue -->
<template>
  <header class="header">
    <div class="m_menu">
      <div class="menu" :class="{'active':activeMenu}" @click="onactive">
        <h3>{{ getUser.username }}<br><span>my Profile</span></h3>
        <ul>
          ...
          <li>
            <a href="#" @click.prevent="showSearchForm"> <img src="/images/settings.png">다른 사용자 책 검색</a>
          </li>
          ....
        </ul>
      </div>
      ....
      <!-- 검색창은 로그인 되어 사용자 정보가 있을 경우에만 보이도록 구현하였습니다.(로그인을 하지 않았다면 검색창은 보이지 않는다.) -->
      <div v-if="getUser && search.showsearchState" class="search_area">
        <div class="btn">
          <a href="#" @click.prevent="search.showsearchState=false">검색창 끄기</a>
        </div>
        <!-- 검색폼 -->
        <form-search v-model="search.input" :options="search.options" @searchBook="onsearchBook"
          @selectedOption="onselectedOption"></form-search>
      </div>
    </div>
    </div>
  </header>
</template>
```
- `v-model`로 바운딩 시켜준 `data` `search.input`의 `value`값을 `props`로 내려줍니다.

- 공통 컴포넌트인 `AppHeader.vue`에 검색 기능을 구현하였으므로, 라우터에 상관없이 책을 검색할 수 있도록 하였습니다.(단, 로그인 시에만 보이고, 로그아웃 시에는 검색창이 보이지 않도록 구현하였습니다.)

- 검색 옵션과 검색 내용은 `store`를 이용해 관리되도록 구현하였습니다.

#### store
|mutations|
|---|
|loadBooks|
```js
//store/book.js  mutations
// 검색 옵션 "책제목","저자" 중 선택 요소 저장
  searchOption (state, option) {
    state.search.selectedOption = option
  },
  // 검색 내용 저장
  searchData (state, input) {
    state.search.data = input
  }
```
-  `state`의 `book` 객체에 데이터를 저장합니다.
<br><br>


|state|
|---|
|books|
```js
//store/book.js state
  search: {
    // 검색 내용
    data: '',
    // 검색 옵션(디폴트값은 책제목으로 설정)
    selectedOption: '책제목'
  }
```
<br>

- `watch`로 `쿼리` 변화를 감지하여 `책제목` , `저자` 중 옵션과 검색 내용을 `state`의 `search` 객체에 저장합니다.
```js
// ~/pages/search/_page.vue
 watch: {
  //  라우트의 쿼리를 감지하여 state 의 search 객체에 값을 저장합니다.
    '$route.query': {
      handler (query) {
        this.searchOption(query.search)
        this.searchData(query.target)
      },
      deep: true,
      immediate: true
    }
  },
  watchQuery: ['search', 'target'],
    methods: {
    ...mapMutations('books', ['searchOption', 'searchData'])
  }
```

|문제점|
|---|
|`nuxt`의 `asyncData`나 `fetch` 훅은 기본적으로 쿼리 문자열 변경에 대해서 호출되지 않습니다.|

|해결|
|---|
|` watchQuery: ['search', 'target']` 속성으로 쿼리 변경 사항을 확인하고, 해당 쿼리 변경시 `astncData`훅이 호출될 수 있도록 구현하였습니다.|
(<a href="https://nuxtjs.org/docs/2.x/components-glossary/pages-watchquery">`nuxt` `watchQuery`에 관한 문서</a>)
<br>

#### 3. 검색 결과의 데이터의 갯수를 보여주고, 검색 결과가 없다면 검색 결과가 없다고 사용자에게 보여지도록 구현하였습니다.
```html
<!-- ~/pages/book/search/_page.vue -->
<template>
  ...
  <div class="head search_result other_books">
    <h2>{{ getSearch.data }} 검색 결과</h2>
    <b>{{ dataCount }}</b>
  </div>
  ...
</template>
```
```js
// ~/pages/book/search/_page.vue
 computed: {
    dataCount () {
      // Books 배열에 데이터가 있다면 해당 데이터의 갯수를 보여주고, 데이터가 없다면 검색된 결과가 없다고 알려줍니다.
      return this.getBooks.length ? this.total : '검색된 책이 없습니다.'
    }
  },
```
<br><br>

### <div id="bookmark" style="color:blue;"><b>7. 내 책 북마크 추가 및 삭제</b></div>
|컴포넌트|라우터|
|---|---|
|components/books/Card.vue|books/_page|
#### <div id="book_card_props">1. api 호출 후, 데이터를 받아 `BookCard.vue` `컴포넌트`에 props로 책 데이터를 내려주었습니다.</div>
(해당 내용은 아래 <a href="#like_props">8. 다른 유저의 책 좋아요 생성 및 삭제의 `props`와 같습니다.</a>)
```html
<!-- ~/pages/books/_page.vue -->
<!-- ~/pages/books/others/_page.vue -->
<template>
...
<!-- api 호출 후 가져온 데이터를 상위 컴포넌트에서 props로 데이터를 내려줍니다. -->
 <div v-for="book in getBooks" :key="book.id" class="book">
   <book-card :book="book"></book-card>
 </div>
 </div>
...
</template>
```
```js
// ~/components/book/Card.vue
  props: {
    book: {
      type: Object,
      required: true
    }
  }
 }
```
<br>

#### <div id="book_props">props</div>
|book|
|---|
|`api` 호출하여 가져온 데이터를 `props` 객체로 내려받았습니다.|
```js
// props로 받은 book 객체 정보 예시
book: {
  // 코멘트
  Comments: Array[0]
  // 해시태그
  Hashtags: Array[0]
  UserId: 7
  authors: "dd"
  bookmark: false
  contents: "dd"
  createdAt: "2021-06-14T15:39:04.810Z"
  datetime: "2021-06-14T15:38:52.000Z"
  id: 144
  isbn: ""
  publisher: ""
  thumbnail: null
  title: "d"
  updatedAt: "2021-06-14T15:39:04.810Z"
  url: ""
}
```
<br>

#### 2. 내가 추가한 책인지 다른 사용자가 추가한 책인지 구분하여, 내 책이면 북마크 표시를 보여주고, 다른 사용자의 책이라면 하트 표시를 보여줍니다.
```html
<!-- ~/components/book/Card.vue -->
<template>
  <div class="book_inner">
    <nuxt-link :to="changeBook">
    <!-- 북마크 여부 -->
      <div class="marks">
      <!-- 내 책이고 북마크가 되어 있다면 해당 마크 표시를 보여줍니다. -->
        <span v-if=" ismybook && onBookmarked" class="top_bookmark">
          {{ isbookmark }}
        </span>
        <!-- 코멘트가 작성이 하나라도 되어 있다면 "c"표시를 보여줍니다. -->
        <span v-if="book.Comments && book.Comments.length>0" class="c_bookmark">c</span>
      </div>
      <div class="photo" :style="{'background-image':setbackground}">
        <div class="overlay"></div>
      </div>
    </nuxt-link>
    <div class="txt">
     ...
     <!-- 북마크 -->
     <!-- 내 책이라면 북마크를 보여줍니다. -->
      <span v-if=" ismybook " class="bookmark" :class="{'bookmarked':onBookmarked}" @click="onClickBookmark(book.id)"><i class="fas fa-bookmark"></i></span>
      <!-- 내 책이 아니라면 하트를 보여줍니다. -->
      <span v-else class="heart" @click="onClickLike(book.id)"><i :class="isheart"></i></span>
    </div>
  </div>
</template>
```
```js
 computed: {
    ...mapGetters('user', ['getUser']),
    ismybook () {
      return this.book.UserId === this.getUser.id
    },
    onBookmarked () {
      return !!(this.book && this.book.bookmark)
    },
    isbookmark () {
      return this.onBookmarked ? 'B' : ''
    }
    ...
  },
```
#### computed

|ismybook|
|---|
|`props`로 받은 `book`의 사용자 아이디와 로그인 할 때 저장한 사용자의 아이디가 같은 지 확인하여 내 책을 구분합니다.|
(<a href="#book_props">`props` `book` 데이터 </a>)

|onBookmarked|
|---|
|`props`로 받은 `book`의 `bookmark` 속성으로 북마크 여부를 구분합니다. |
|`props`로 받은 `book`의 `bookmark` 속성이 `true` 라면, 이미 북마크가 추가된 것 |
|`props`로 받은 `book`의 `bookmark` 속성이 `false` 라면, 북마크가 추가되지 않은 것 |
(<a href="#book_props">`props` `book` 데이터 </a>)

| isbookmark |
|---|
|`computed`인 `onBookmarked`로 북마크 여부 확인하여, 해당 값이 `true`라면 "B" 글자를 리턴해줍니다.
(북마크된다면 "B"마크 표시가 보이도록 구현)|

- `class`를 바운딩하여 북마크 여부 표시도 따로 포현하였습니다.
```html
<!-- ~/components/book/Card.vue -->
<template>
...
 <span v-if=" ismybook " class="bookmark" :class="{'bookmarked':onBookmarked}">..</span>
...
</template>
```
```css
/* 기존북마크 */
.bookshelf .book .bookmark {
    position: absolute;
    right: -5%;
    top: -2%;
    z-index: 2;
    font-size: 25px;
    color: rgba(62,161,219,1);
    transform: rotate(-2deg);
    cursor: pointer;
}
/* 클래스명에 bookmarked가 추가되면 색깔 변경 */
.bookshelf .book .bookmark.bookmarked {
    color: rgb(7, 82, 126);
}
```
<br>

#### 3. 이미 북마크가 추가되어 있다면, 북마크를 삭제시키는 api를 호출하고, 북마크가 되어 있지 않다면, 북마크를 추가하는 api를 호출하도록 구현하였습니다.
#### 3-1. store

- 북마크 추가

|<div>actions</div>|
|---|
|createBookmark|

```js
//store/book.js actions
   async createBookmark ({ commit }, { bookId }) {
    try {
      await this.$axios.patch(`books/${bookId}/addbookmark`)
      commit('addBookmark', bookId)
    } catch (error) {
      console.error(error)
    }
```
- `axios`를 이용해 api를 호출합니다.
- `commit`를 이용해 `mutatonis`을 호출합니다.


|mutations|
|---|
|addBookmark|
```js
//store/book.js  mutations
  addBookmark (state, bookId) {
    const index = state.books.findIndex(book => book.id === bookId)
    state.books[index].bookmark = true
  }
```
-  `state`의 `books` 배열에서 `id`로 해당 책을 찾아 `bookmark` 속성을 바꿔줍니다.
<br><br>


|state|
|---|
|books|
```js
//store/book.js state
 books: []
```
<br>
- 북마크 삭제

|<div>actions</div>|
|---|
|deleteBookmark|

```js
//store/book.js actions
  async deleteBookmark ({ commit }, { bookId }) {
    try {
      await this.$axios.patch(`books/${bookId}/removebookmark`)
      commit('removeBookmark', bookId)
    } catch (error) {
      console.error(error)
    }
  }
```
- `axios`를 이용해 api를 호출합니다.
- `commit`를 이용해 `mutatonis`을 호출합니다.


|mutations|
|---|
|removeBookmark|
```js
//store/book.js  mutations
  removeBookmark (state, bookId) {
    const index = state.books.findIndex(book => book.id === bookId)
    state.books[index].bookmark = false
  }
```
-  `state`의 `books` 배열에서 `id`로 해당 책을 찾아 `bookmark` 속성을 바꿔줍니다.
<br><br>


|state|
|---|
|books|
```js
//store/book.js state
 books: []
```
<br>

#### 3-2. 북마크 여부에 따라 api 호출하도록 구현하였습니다.
```js
// components/Book/Card.vue
  methods: {
    ...mapActions('books', ['createBookmark', 'deleteBookmark']),
    onClickBookmark (id) {
      if (this.onBookmarked) {
        // 이미 북마크가 되어 있다면 북마크 삭제 api 호출
        this.deleteBookmark({ bookId: id })
      } else {
         //북마크가 되어 있지 않다면 북마크 추가 api 호출
        this.createBookmark({ bookId: id })
      }
    },
  }
```
<br>

### <div id="heart" style="color:blue;"><b>8. 다른 유저의 책 좋아요 생성 및 삭제</b></div>
|컴포넌트|라우터|
|---|---|
|components/books/Card.vue|books/others/_page|


#### <div id="like_props">1. api 호출 후, 데이터를 받아 `BookCard.vue` `컴포넌트`에 props로 책 데이터를 내려주었습니다.</div>
(<a href="#book_card_props">해당 내용 바로가기</a>)
#### <div id="book_props_like">props</div>
|book|
|---|
|`api` 호출하여 가져온 데이터를 `props` 객체로 내려받았습니다.|
 * 여기서는 `좋아요` 여부를 확인하기 위해 `Likers` 배열을 추가적으로 저장했습니다.
```js
// props로 받은 book 객체 정보 예시
book: {
  // 코멘트
  Comments: Array[0]
  //  해시태그
  Hashtags: Array[0]
  // 좋아요
  Likers: Array[0]
  User: Object
  UserId: 2
  authors: "편집부 편"
  bookmark: false
  contents: ""
  createdAt: "2021-06-11T15:34:11.070Z"
  datetime: "1993-10-31T15:00:00.000Z"
  id: 135
  isbn: " 2004223004539"
  publisher: "아름출판사"
  thumbnail: ""
  title: "아름 피스(2000)"
  updatedAt: "2021-06-11T15:34:11.070Z"
  url: "https://search.daum.net/search?w=bookpage&bookId=151500&q=%EC%95%84%EB%A6%84+%ED%94%BC%EC%8A%A4%282000%29"
}
```

#### 2. 내가 추가한 책인지 다른 사용자가 추가한 책인지 구분하여, 내 책이면 북마크 표시를 보여주고, 다른 사용자의 책이라면 하트 표시를 보여줍니다.
```html
<!-- ~/components/book/Card.vue -->
<template>
  <div class="book_inner">
    <nuxt-link :to="changeBook">
    <!-- 북마크 여부 -->
      <div class="marks">
      <!-- 내 책이고 북마크가 되어 있다면 해당 마크 표시를 보여줍니다. -->
        <span v-if=" ismybook && onBookmarked" class="top_bookmark">
          {{ isbookmark }}
        </span>
        <!-- 코멘트가 작성이 하나라도 되어 있다면 "c"표시를 보여줍니다. -->
        <span v-if="book.Comments && book.Comments.length>0" class="c_bookmark">c</span>
      </div>
      <div class="photo" :style="{'background-image':setbackground}">
        <div class="overlay"></div>
      </div>
    </nuxt-link>
    <div class="txt">
     ...
     <!-- 북마크 -->
     <!-- 내 책이라면 북마크를 보여줍니다. -->
      <span v-if=" ismybook " class="bookmark" :class="{'bookmarked':onBookmarked}" @click="onClickBookmark(book.id)"><i class="fas fa-bookmark"></i></span>
      <!-- 내 책이 아니라면 하트를 보여줍니다. -->
      <span v-else class="heart" @click="onClickLike(book.id)"><i :class="isheart"></i></span>
    </div>
  </div>
</template>
```
```js
 computed: {
    ...mapGetters('user', ['getUser']),
    ismybook () {
      return this.book.UserId === this.getUser.id
    },
    onclickHearted () {
      return !!(this.book.Likers || []).find(liker => this.getUser.id === liker.id)
    },
    isheart () {
      // 폰트 어썸
      return this.onclickHearted ? 'fas fa-heart' : 'far fa-heart'
    },
  },
```
#### computed

|ismybook|
|---|
|`props`로 받은 `book`의 사용자 아이디와 로그인 할 때 저장한 사용자의 아이디가 같은 지 확인하여 내 책을 구분합니다.|
(<a href="#book_props_like">`props` `book` 데이터 </a>)

|onclickHearted|
|---|
| - `props`로 받은 `book`에서 `Likers` 배열로 좋아요를 누른 사용자의 id 속성 값을 받도록 구현하였습니다.|
예시)`Likers:[{Like:{...},id:6}...]`: `Likers`배열에는 좋아요를 추가한 사용자의 `id`와 사용자의 `username`을 받도록 구현(id가 6인 사용자가 해당 책에 좋아요를 추가함)|
| - 로그인할 때 저장된 나의 `id`와 `Likers`배열의 `userId` 정보를 이용해 해당 책에 대한 좋아요 추가 여부를 확인할 수 있도록 구현하였습니다. |
|배열 아닌 요소에서 `find` 메서드가 작동하지 못하도록 `Likers`배열이 없다면 빈배열을 넣어주었습니다.|
(<a href="#book_props_like">`props` `book` 데이터 </a>)

|  isheart |
|---|
|`computed`인 `onclickHearted`로 좋아요 여부 확인하여, 
`class`에 바운딩하여 `좋아요`가 추가된 상태이면 색이 채워진 하트를 보여주고,그렇지 않다면 빈 하트를 보여줍니다.|

<br>

#### 3. 이미 좋아요가 추가되어 있다면, 좋아요를 삭제시키는 api를 호출하고, 좋아요가 되어 있지 않다면, 좋아요를 추가하는 api를 호출하도록 구현하였습니다.
#### 3-1. store

- 좋아요 추가

|<div>actions</div>|
|---|
|otheraddLike|

```js
//store/book.js actions
  async otheraddLike ({ commit }, { bookId }) {
    const res = await this.$axios.post(`/books/others/book/${bookId}/addLike`)
    commit('addlike', { bookId, userId: res.data.userId })
    return res
  }
```
- `axios`를 이용해 api를 호출합니다.
- `commit`를 이용해 `mutatonis`을 호출합니다.


|mutations|
|---|
|addlike|
```js
//store/book.js  mutations
  addlike (state, likeInfo) {
    const { bookId, userId } = likeInfo
    const index = state.books.findIndex(book => book.id === bookId)
    state.books[index].Likers.push({ id: userId })
  }
```
-  `state`의 `books` 배열에서 `id`로 해당 책을 찾아 
`Likers`배열에 `userId`를 추가해줍니다.
<br><br>


|state|
|---|
|books|
```js
//store/book.js state
 books: []
```
<br>
- 좋아요 삭제

|<div>actions</div>|
|---|
|otherremoveLike|

```js
//store/book.js actions
  async otherremoveLike ({ commit }, { bookId }) {
    const res = await this.$axios.delete(`/books/others/book/${bookId}/removeLike`)
    commit('removelike', { bookId, userId: res.data.userId })
    return res
  }
```
- `axios`를 이용해 api를 호출합니다.
- `commit`를 이용해 `mutatonis`을 호출합니다.


|mutations|
|---|
|removelike|
```js
//store/book.js  mutations
  removelike (state, likeInfo) {
    const { bookId, userId } = likeInfo
    const index = state.books.findIndex(book => book.id === bookId)
    state.books[index].Likers = state.books[index].Likers.filter(like => like.id !== userId)
  }
```
-   `state`의 `books` 배열에서 `id`로 해당 책을 찾아 
`Likers`배열에 `id`를 비교해 제거해줍니다.
<br><br>


|state|
|---|
|books|
```js
//store/book.js state
 books: []
```
<br>

#### 3-2. 좋아요 여부에 따라 api 호출하도록 구현하였습니다.
```js
// components/Book/Card.vue
  methods: {
    ...mapActions('books', ['otheraddLike', 'otherremoveLike']),
    onClickLike (id) {
      if (this.onclickHearted) {
         // 이미 좋아요를 클릭했다면 좋아요 삭제 api 호출
        this.otherremoveLike({ bookId: id })
      } else {
          // 좋아요가 추가되어 있지 않다면 좋아요 추가 api 호출
        this.otheraddLike({ bookId: id })
      }
    }
  }
```
<br>

#### 4. `좋아요` 누른 사용자의 닉네임과 몇 명이 좋아요를 눌렀는지 보여주도록 구현하였습니다.

#### 4-1. `books/_page`, `books/others/_page` 라우터에서는 좋아요 갯수만 보이도록 구현하였습니다.
```html
<!-- ~/components/book/Card.vue -->
<template>
  <div class="book_inner">
    <nuxt-link :to="changeBook">
      ...
      <!-- 책 이미지 -->
      <div class="photo" :style="{'background-image':setbackground}">
        <div class="overlay"></div>
        <!-- 좋아요 받은 갯수 -->
        <div v-if=" book.Likers && book.Likers.length" class="like heart_count">
          <i class="fas fa-heart"></i>
          <span>{{ book.Likers.length }}</span>
        </div>
      </div>
    </nuxt-link>
    <div class="txt">
        ...
    </div>
  </div>
</template>
```
```js
  props: {
    book: {
      type: Object,
      required: true
    }
  }
```
```js
// book.Likers 배열에 담긴 정보 예시
Likers: [{
  Like: Object
  username: "1234"
}, ...]

```
- `props`로 내려준 `book`객체에서 `Likers`의 배열의 갯수로 `좋아요` 받은 갯수를 보여줍니다.
<br>

#### 4-2. `books/b/_id` 라우터에서는 좋아요 갯수와 좋아요를 누른 사용자의 닉네임이 보이도록 구현하였습니다.
```html
<!-- ~/components/book/CardDetail.vue -->
<template>
  <div>
    <div v-if="book">
      <h2 v-if="book.User" class="book_user">
        {{ book.User.username }}님의 책장
      </h2>
      <div class="book_detail">
        ...
        <!-- 좋아요  -->
        <!-- 마우스를 호버할때만 좋아요 누른 사용자의 닉네임을 보여줍니다. -->
        <div v-if=" book.Likers && book.Likers.length" class="heart_count" @mouseenter="showLikers=true"
          @mouseleave="showLikers=false">
          <i class="fas fa-heart"></i>{{ book.Likers.length }}
          <!-- 좋아요 누른 사용자의 닉네임 -->
          <div v-if="showLikers" class="heart_list">
            <ul>
              <li v-for="(liker,index) in book.Likers" :key="index">
                {{ liker.username }}님이 좋아요
              </li>
            </ul>
          </div>
        </div>
      </div>
    </div>
</template>

```
```js
  props: {
    book: {
      type: Object,
      required: true
    }
  },
    data () {
    return {
      showLikers: false
    }
  }
```
- `하트`에 마우스를 올리면, `좋아요` 누른 사용자의 닉네임을 보여줍니다. `좋아요` 누른 사용자가 많다면 무한히 길어지는 것을 방지하기 위해 `높이`가 `300px` 이상인 경우에는 높이가 더이상 길어지지 않고 스크롤바가 생기도록 구현하였습니다.
```css
/* 최소 높이를 주어 300px 이상으로 높이가 길어지면 스크롤이 생깁니다.*/
.heart_count .heart_list{... max-height: 300px; overflow-y: auto;}
```


<br>
<br>

### <div id="comment" style="color:blue;"><b>10. 댓글 보기 및 추가 및 삭제</b></div>
|컴포넌트|라우터|
|---|---|
|components/form/Comment.vue|books/b/_id|
|components/comment/Edit.vue|books/b/_id|
|components/comment/List.vue|books/b/_id|

#### <div id="comment" style="color:red;">1-3. 댓글 보기</div>

#### 1. `댓글 보기` 버튼을 누르면 `댓글 작성란`과 `댓글`이 보여지도록 구현하였습니다.
- `댓글 보기` 버튼을 누르면 `api`를 호출하여 데이터를 가져옵니다.

#### 1-1. store
|<div>actions</div>|
|---|
|fetchComments|

```js
//store/comments.js actions
  async fetchComments ({ commit, state }, comments) {
    try {
      let res
      // 처음으로 댓글보기 버튼을 클릭했을 때,
      if (comments.init) {
        // 삭제시 다시 10개 댓글 가져오도록 하기(단 총 댓글갯수가 10개 미만이면 굳이 가져올 필요없음)
        if (comments.removeState && state.commentPage.commentCount < 10) { return }
        res = await this.$axios.get(`books/${comments.bookId}/comments?limit=10`)
        // 처음이 아닌 다음 댓글을 가져올 때,
      } else {
        const lastComment = state.comments && state.comments[state.comments.length - 1]
        res = await this.$axios.get(`books/${comments.bookId}/comments?lastId=${lastComment && lastComment.id}&limit=10&page=${comments.page}`)
      }

      commit('loadComments', { data: res.data, init: comments.init })
      return res
    } catch (error) {
      console.error(error)
    }
  },
```
- `axios`를 이용해 api를 호출합니다.
- `IntersectionObserver`를 이용하여 댓글을 불러오도록 구현하였습니다.


- `store`의 `actions`함수 `fetchComments` 호출시,
`{init:true}`객체로 처음으로 댓글을 불러오는 지 여부를 확인하도록 구현하였습니다.
<br>

|처음으로 댓글을 불러오는지 여부를 확인하는 이유|
|---|
|맨 처음으로 `댓글 보기 버튼`을 클릭한다면, 최근에 생성된 댓글 기준 내림 차순으로 10개씩 데이터를 불러오는 api를 호출합니다.|
| `화면 하단`에 스크롤이 내려온다면, 이어서 다음 댓글 10개를 불러오는 api를 호출합니다|
|`화면 하단`에 스크롤이 내려올시에만, 다음 데이터를 불러오도록 구현하였기 때문에,처음으로 데이터를 호출했는지 여부를 구분하여 `api`를 호출합니다.|

|`댓글보기`버튼 클릭|
|---|
|`{init:true}`일 때, 처음으로 데이터(댓글 10개)를 불러오는 api를 호출합니다.|
```js
 if (comments.init) {
      ...
      // 10개씩 데이터 호출
        res = await this.$axios.get(`books/${comments.bookId}/comments?limit=10`)
 }      
```
<br>

|`화면 하단`에 스크롤 진입|
|---|
|데이터의 마지막 `id`(마지막 댓글의 `id`)를 찾아서 그 이후 다음 데이터 10개((다음 댓글 10개))를 불러오는 api를 호출합니다. |
```js
....
 else {
  //  마지막 댓글를 가져옵니다.
        const lastComment = state.comments && state.comments[state.comments.length - 1]
        // 마지막 댓글의 id 를 기준으로 그 이후 10개의 데이터를 불러옵니다.
        res = await this.$axios.get(`books/${comments.bookId}/comments?lastId=${lastComment && lastComment.id}&limit=10&page=${comments.page}`)
      } 
```



- `commit`를 이용해 `mutatonis`을 호출합니다.


|mutations|
|---|
|loadComments|
```js
//store/comments.js  mutations
  loadComments (state, commentInfo) {
    // const { end, commentCount, comments } = commentInfo
    const { data, init } = commentInfo
    // 처음 데이터를 불러올 때 배열에 저장합니다.
    if (init) {
      state.comments = [...data.comments]
    } else {
      // 처음이 아니라면 배열에 누적시켜 데이터를 보여줍니다.
      data.comments.forEach((comment) => {
        state.comments.push(comment)
      })
    }
    // 코멘트를 10개씩 호출했을 때, 더이상 호출할 코멘트가 남아있지않다면, 호출하지 않기위해 데이터를 저장합니다.
    state.commentPage.end = data.end
    // 코멘트의 갯수를 보여주기위해 코멘트의 갯수 데이터를 저장합니다.
    state.commentPage.commentCount = data.commentCount
  }
```
<br>

|처음으로 데이터를 불러올 때|
|---|
|처음으로 데이터(댓글)을 불러올 때 `state`의 `comments`배열에 데이터를 저장합니다.|

|처음이 아닌 이후 데이터를 불러올 때|
|---|
|처음이 아니라면  기존 `state`의 `comments`배열에 데이터를 누적시킵니다. |

- `api`를 호출할때 `마지막 페이지 여부`와 `총 댓글의 갯수`를 받아 저장합니다.
<br>

|getters|
|---|
|getComments |
|getCommentPage |
```js
//store/comments.js  getters
  getComments (state) {
    return state.comments
  },
  getCommentPage (state) {
    return state.commentPage
  }
```
|getComments|
|---|
|`state`의 `comments`배열을 가져옵니다.|

|getCommentPage|
|---|
|`state`의 `commentPage`객체를 가져옵니다|
<br>

|state|
|---|
|comments|
|commentPage|
```js
//store/comments.js state
  comments: [],
  commentPage: {
    commentCount: 0,
    end: false
  }
```
|comments|
|---|
|댓글 데이터를 저장합니다.|

|commentPage|
|---|
|`마지막 페이지 여부`를 `end`에 `Boolean`값으로 저장하고, `commentCount`에 `총 댓글의 갯수`를 저장합니다.|

- <div id="c_api"> api 호출시,  아래 정보를 저장합니다.</div>
```js
comments: [{
    BookId: 138
    User: Object
    UserId: 7
    comments: "안녕하세요~"
    createdAt: "2021-06-16T08:12:56.704Z"
    id: 203
    rating: 0
    updatedAt: "2021-06-16T08:12:56.704Z"
  }, ...],
  commentPage: {
    commentCount: 1
    end: true
  }
```

#### 1-2. `댓글 보기 버튼` 클릭시 api 호출
- `store`의 `actions` 함수 `fetchComments`를 호출합니다.
```html
<!-- ~/component/comment/Edit.vue -->
<template>
   <div class="comment_area">
  <button class="round-btn yellow" @click.prevent="onshowComments">
  <!-- computed로 '댓글보기' ,'댓글접기' 보이도록 구현-->
    {{ onStateComment }}
  </button>
  ...
  <!-- 댓글 -->
   <div class="comment_area">
        <div>댓글</div>
        <ul>
          <comment-list v-for="(comment) in getComments" :key="comment.id" :comment="comment" @onRemoveComment="onRemoveComment"></comment-list>
        </ul>
      </div>
    </div>
    <!-- 옵저버 시킬 대상 -->
    <div ref="trigger" class="trigger">
      <i v-if="loading" class="fas fa-spinner fa-spin"></i>
    </div>
    ...
  </div>
</template>
```
```js
// ~/component/comment/Edit.vue
 mounted () {
    this.onaddComments()
  },
 methods: {
   onshowComments() {
     this.ontoggleComment()
   //  댓글 보기 버튼을 클릭할 때 api 호출
     if (this.showComment) {
       this.loading = true
      //  처음 데이터를 호출하므로 page는 1로 초기화
       this.page = 1
       this.fetchComments({
           bookId: this.$route.params.id,
           init: true
         })
         .then(() => {
           this.loading = false
         })
     }
   },
   // IntersectionObserver 로 다음 데이터를 가져오는 api 호출
   onaddComments() {
     const observer = new IntersectionObserver((entries) => {
       entries.forEach((entry) => {
         // 댓글 보기 버튼을 클릭하고, 스크롤이 화면 하단에 위치하고, 댓글이 이미 10개가 호출이 되어 있을 때 다음 댓글를 호출하여 누적시킵니다.
         // 다음 댓글를 호출했을 때 댓글이 더이상 남아 있는지 확인하여 더이상 불러올 댓글이 존재하지 않는다면 호출하지 않습니다.
         if (this.showComment && entry.isIntersecting && this.getComments && this.getComments.length > 9 && !this.getCommentPage.end) {
           this.loading = true
          // page는 호출될때마다 증가시킵니다.
           this.page++
           // 처음 호출되는 댓글이 아니므로  {init}속성은 여기서는 사용하지 않았습니다.
           this.fetchComments({
               bookId: this.$route.params.id,
               page: this.page
             })
             .then((res) => {
               this.loading = false
             })
         }
       })
     })
      // $ref로 <div classs="trigeer"></div> 태그를 옵저버시킵니다.
     observer.observe(this.$refs.trigger)
   },
 }
```
- IntersectionObserver API를 지원하지 않는 브라우저에서도 사용할 수 있도록 
`IntersectionObserver polyfill` 라이브러리를 사용 하였습니다.(IE에서는 적용되지 않습니다.)
- <a href="https://developer.mozilla.org/en-US/docs/Web/API/Intersection_Observer_API">IntersectionObserver 참고자료</a>
- <a href="https://github.com/w3c/IntersectionObserver/tree/main/polyfill">IntersectionObserver 라이브러리</a>

<br>

#### 2. `댓글 보기` 버튼을 클릭했을 때, 해당 버튼은 `댓글 접기`버튼으로 보여지고,`댓글 접기`버튼을 클릭했을 때, `댓글 보기`버튼이 보이도록 구현하였습니다.
```html
<template>
  <div class="comment_area">
    <button class="round-btn yellow" @click.prevent="onshowComments">
      {{ onStateComment }}
    </button>
    ...
  </div>
</template>
```
```js
// ~/components/comment/Edit.vue
  data () {
    return {
      showComment: false,
    }
  },
  computed: {
    onStateComment() {
      return !this.showComment ? '코멘트 보기' : '코멘트 접기'
    }
  },
  methods: {
    ontoggleComment() {
      this.showComment = !this.showComment
    },
     onshowComments () {
      this.ontoggleComment()
    //  이후 데이터를 불러오는 api 호출
    },
  }
```

#### methods
|ontoggleComment|
|---|
|`댓글 보기`버튼 클릭시, `data`의 `showComment`값을 변 화시킵니다.|

#### computed
|onStateComment|
|---|
| `data`의 `showComment` 변화에 따라 버튼을 다르게 보여줍니다.|
<br>

#### 3. `api` 호출을 통해 불러온 데이터(데이터는) 보여주기
```html
<!-- ~/components/comment/Edit.vue -->
<template>
  <div class="comment_area">
    <button class="round-btn yellow" @click.prevent="onshowComments">
      {{ onStateComment }}
    </button>
    <div v-if="showComment" class="comment">
      <div class="comment_head">
        <h2>댓글 쓰기</h2>
        <div v-if="loading" class="loading-spin">
          <i class="fas fa-spinner fa-spin"></i>
        </div>
        <div v-else>
          댓글 :{{ getCommentPage.commentCount }}
        </div>
      </div>
      <!-- 댓글 작성란 -->
      <form-comment></form-comment>
      <div class="comment_area">
        <div>댓글</div>
        <ul>
        <!-- 댓글 리스트 -->
          <comment-list v-for="(comment) in getComments" :key="comment.id" :comment="comment" @onRemoveComment="onRemoveComment"></comment-list>
        </ul>
      </div>
    </div>
  ...
  </div>
</template>
```
```js
// ~/components/comment/List.vue 
  props: {
    comment: {
      type: Object,
      required: true
    }
  }
```
#### props
|comment|
|---|
|`상위 컴포넌트`에서 `하위 컴포넌트로` `api 호출`로 불러온 데이터(댓글)를 `props`로 내려주었습니다.|
<a href="#c_api">데이터 바로 가기</a>
<br>

#### 3-1. 댓글을 작성한 사용자의 프로필(썸네일) 이미지가 있다면, 그 이미지를 보여주고, 없다면 사용자의 닉네임의 첫글자를 보여주도록 구현하였습니다.
```html
<!-- ~/components/comment/List.vue -->
<template>
  <li>
     <!-- 댓글 썸네일 이미지 -->
    <div class="c_thumbnail">
    <!-- 사용자의 프로필(썸네일) 이미지가 있다면 이미지를 보여줍니다.-->
      <span v-if="comment.User && comment.User.thumbnail"><img :src="comment.User.thumbnail" alt=""></span>
      <!-- 사용자의 프로필(썸네일) 이미지가 없다면 사용자의 첫글자를 보여줍니다. -->
      <span v-else>{{ String(comment.User.username)[0] }}</span>
      <p>{{ comment.User.username }}</p>
    </div>
    <!-- 댓글 내용 -->
    <div class="comment_txt">
      {{ comment.comments }}
    </div>
    <!-- 댓글 별점 -->
    ...
    </div>
    <!-- 댓글 날짜 -->
    ...
  </li>
</template>
```
<br>

#### 3-2. 댓글 날짜는 라이브러리를 사용하여 포맷하여 보여주었습니다.
```html
<!-- ~/components/comment/List.vue -->
<template>
  <li>
     <!-- 댓글 썸네일 이미지 -->
    <div class="c_thumbnail">
    <!-- 사용자의 프로필(썸네일) 이미지가 있다면 이미지를 보여줍니다.-->
      <span v-if="comment.User && comment.User.thumbnail"><img :src="comment.User.thumbnail" alt=""></span>
      <!-- 사용자의 프로필(썸네일) 이미지가 없다면 사용자의 첫글자를 보여줍니다. -->
      <span v-else>{{ String(comment.User.username)[0] }}</span>
      <p>{{ comment.User.username }}</p>
    </div>
    <!-- 댓글 내용 -->
    <div class="comment_txt">
      {{ comment.comments }}
    </div>
    <!-- 댓글 별점 -->
    ...
    </div>
    <!-- 댓글 날짜 -->
     <div>{{ $moment(`${comment.updatedAt}`).format("LLL") }}</div>
    ...
  </li>
</template>
```
|사용한 라이브러리|
|---|
|`@nuxtjs/moment`|
<a href="https://github.com/nuxt-community/moment-module#readme">@nuxtjs/moment 참고</a>

<br>

#### 3-3. `textarea`에 입력받은 데이터를 그대로 보여주도록 구현하였습니다.


|문제점|
|---|
|`textarea`태그는 여러줄의 데이터를 입력할 수 있지만,엔터로 줄바꿈을 할 경우 `\n`으로 인식하여, 그대로 출력하면 줄바꿈이 적용되지 않습니다. |



|해결|
|---|
| `css` 속성 중 입력값을 그대로 출력해주는 `white-space: pre-line; `속성을 사용하여 줄바꿈이 적용되도록 구현하였습니다.  |

```css
/* components/comment/CommentList.vue */

/* 줄바꿈이 적용되도록 내용을 그대로 출력하고, 폰트는 상속받아 적용시켜줍니다. */
li .comment_txt{ margin: 15px 0; white-space: pre-line; word-wrap: break-word; font-family: inherit;}
```

#### <div id="comment" style="color:red;">1-2. 댓글 추가</div>
|컴포넌트|라우터|
|---|---|
|components/form/Comment.vue|books/b/_id|
|components/comment/Edit.vue|books/b/_id|


#### 1. `댓글 입력폼`은 작성한 길이에 따라 가변적으로 높이가 늘어나도록 구현하였습니다.
```html
<!-- components/form/Comment.vue -->
<template>
  <form class="comment_form" @submit.prevent="onaddComment">
  <!-- 코멘트 작성란 -->
    <div>
      <p>
        <textarea
          v-model="textcomments"
          class="comments"
          name="comments"
          cols="30"
          rows="2"
          :class="{'invalid':textLengthChk}"
          @input="resize($event)"
        ></textarea>
      </p>
    </div>
    <!-- 댓글작성시, 실시간으로 작성된 길이를 보여줍니다. -->
      <div>{{ commentLen }}/100</div>
      <!-- 댓글작성시,작성된 길이를 체크합니다. -->
    <div v-if="textLengthChk" class="err">
      코멘트는 100자 이하여야 합니다.
    </div>
      ...
      <button type="submit" class="round-btn fill comment_btn" :disabled="textLengthChk || !textcomments">
        코멘트 추가
      </button>
    </div>
  </form>
</template>
```
```js
data () {
    return {
      textcomments: '',
    }
  },
computed:{
  // v-model로 바운딩 시켜준 data의 textcomments의 길이를 구합니다.
  commentLen () {
      return this.textcomments.length
    },
  textLengthChk () {
    // textcomments의 길이가 100자 이상인지 여부에 따라 길이를 체크합니다.
      return this.commentLen > 100
    }
}
methods:{
      resize (e) {
        // 길이가 200자 이상이면 높이를 변화시키지 않기위해 return해줍니다.
      if (this.textLengthChk) { return }
      // 입력값에 따라 높이값을 다시 설정해
      e.target.style.height = 'auto'
      e.target.style.height = `${e.target.scrollHeight}px`
    },
}
```

#### methods
|resize|
|---|
|해당 입력폼에 값이 작성될 때마다, `resize`함수를 호출하도록 하였습니다.|
|`computed`인 `textLengthChk`를 이용해, 해당 입력폼에 작성한 글의 길이가 100자 이상이면 입력폼의 높이가 더이상  늘어나지 않도록 `return` 시켜줍니다.|
|`event`의 `target`속성을 이용해 입력값에 따라 높이값을 조정해줍니다.|

<br>

####  <div id="c_add">2. `댓글 추가`버튼을 클릭시, 댓글을 추가하는 api를 호출합니다.</div>

#### 2-1. store
|<div>actions</div>|
|---|
|createComment|

```js
//store/comments.js actions
   async createComment ({ commit }, { bookId, comments, rating }) {
    try {
      const res = await this.$axios.post(`books/${bookId}/comment`, { comments, rating: parseInt(rating, 10) })
      commit('createComment', res.data.comment)
      return res
    } catch (error) {
      console.error(error)
    }
  },
```
- `axios`를 이용해 api를 호출합니다.
- `댓글 작성한 내용`과 `별점`인 `rating`데이터도 함께 서버에 보내주도록 구현하였습니다.<br>
(<a href="#star">별점 주기 기능 바로 가기</a>)
- `commit`를 이용해 `mutatonis`을 호출합니다.


|mutations|
|---|
|createComment|
```js
//store/comments.js mutations
  createComment (state, comment) {
    state.comments.unshift(comment)
    state.commentPage.commentCount++
  },
```
-  `state`의 `comments` 배열에 누적시켜 저장합니다.
(최근에 작성된 댓글이 제일 앞에 보이도록 `unshift`를 이용해 데이터를 저장합니다.)
- `댓글 추가`시, 댓글의 갯수를 증가시켜줍니다.
<br><br>

|getters|
|---|
|getComments |
|getCommentPage |
```js
//store/comments.js  getters
  getComments (state) {
    return state.comments
  },
  getCommentPage (state) {
    return state.commentPage
  }
```
|getComments|
|---|
|`state`의 `comments`배열을 가져옵니다.|

|getCommentPage|
|---|
|`state`의 `commentPage`객체를 가져옵니다|
<br>

|state|
|---|
|comments|
|commentPage|
```js
//store/comments.js state
  comments: [],
  commentPage: {
    commentCount: 0,
    end: false
  }
```
|comments|
|---|
|댓글 데이터를 저장합니다.|

|commentPage|
|---|
|`마지막 페이지 여부`를 `end`에 `Boolean`값으로 저장하고, `commentCount`에 `총 댓글의 갯수`를 저장합니다.|

#### 2-2. `댓글 추가`버튼을 클릭시, 댓글을 추가하는 api를 호출
- `store`의 `actions`함수 `createComment`를 호출합니다.
```js
methods:{
   onaddComment () {
    //  댓글 작성폼에 아무것도 입력되지 않았다면,리턴시켜줍니다.
      if (this.textcomments.trim().length <= 0) {
        return
      }
      this.createComment({ bookId: this.$route.params.id, comments: this.textcomments, rating: this.rating })
      this.resetForm()
    },
    resetForm () {
      // 댓글 작성란 초기화
      this.textcomments = ''
      // 별점 초기화
      this.rating = 0
    }
}
```
<br>

#### <div id="comment" style="color:red;">1-3. 댓글 삭제</div>
|컴포넌트|라우터|
|---|---|
|components/comment/List.vue|books/b/_id|

#### 1. `삭제 버튼`을 클릭하면 한번 더 확인하는 알림창이 뜨고, 확인 버틀을 누를시에만 삭제하도록 구현하였습니다.
<br>

#### 1-1. `삭제 버튼`은 다른 사람의 책이 아닌 내 책에서만 `버튼`이 보이도록 구현하였습니다.
```html
<!--  -->
<template>
  <li>
  <!-- 댓글 프로필(썸네일)이미지 -->
    <div class="c_thumbnail">
    ....
    </div>
    <!-- 댓글 내용 -->
    <div class="comment_txt">
      {{ comment.comments }}
    </div>
    <!-- 댓글 별점 -->
    <div class="comment_star">
      <div v-for="star in comment.rating" :key="star" class="star">
        <i class="fas fa-star">
        </i>
      </div>
    </div>
    <!-- 댓글 날짜 -->
    <div>{{ $moment(`${comment.updatedAt}`).format("LLL") }}</div>
    <!-- 삭제 버튼 -->
    <!-- 내가 작성한 댓글일 때에만 삭제버튼이 보이도록 구현하였습니다. -->
    <div v-if="myComment(comment.User.username)" class="remove_btn">
      <button class="round-btn fill" @click="onRemoveComment(comment.id)">
        삭제
      </button>
    </div>
  </li>
</template>
```
```js
 computed: {
    ...mapGetters('user', ['getUser'])
  },
  methods: {
    ...
    myComment (user) {
      return user === this.getUser.username
    }
  }
```

#### methods
| myComment|
|---|
|`state`의 `user`객체에 저장된 사용자의 닉네임과 `댓글`을 작성한 사용자의 닉네임을 비교하여 내가 쓴 댓글인지 확인합니다.|
<br>

#### 1-2. `삭제 버튼`시, 확인 알림창을 보여줍니다.
```html
<!-- ~/pates/book/b/_id.vue -->
<template>
  <div>
    <!-- 책이 있을 경우에만 보여준다. -->
    <div v-if="getBook">
      ...
      <form-alert v-if="alert" :data="getBook && getBook.title" :confirm="`삭제`" @onagree=" agree"
        @ondisagree="disagree"></form-alert>
    </div>
    ...
  </div>
</template>
```
```html
<!-- ~/components/form/Alert.vue -->
<template>
  <common-modal class="alert_main">
    <div slot="header">
      <b>{{ data }}</b> 을(를) {{ confirm }}하시겠어요??
    </div>
    <div slot="body" class="body">
    <!-- "네" 버튼 클릭 시, onagree 이벤트를 상위 컴포넌트에 보내줍니다. -->
      <button class="primary-btn" @click="$emit('onagree')">
        네
      </button>
        <!-- "아니오" 버튼 클릭 시, ondisagree 이벤트를 상위 컴포넌트에 보내줍니다. -->
      <button class="primary-btn red" @click="$emit('ondisagree')">
        아니오
      </button>
    </div>
    <div slot="footer"></div>
  </common-modal>
</template>
```
```js
  props: {
    data: {
      type: String,
      required: true
    },
    confirm: {
      type: String,
      required: true
    }
  }
```
- 상위컴포넌트에서 `data`와 `confirm`을 `props`로 내려주었습니다.
<br><br>

#### 2. 확인 알림창에서 "네" 클릭시 삭제 api를 호출합니다.
#### 2-1. store
|<div>actions</div>|
|---|
|deleteComment|

```js
//store/comments.js actions
async fetchComments ({ commit, state }, comments) {
    try {
      let res
      if (comments.init) {
        // 삭제시 다시 10개 댓글 가져오도록 하기(단 총 댓글갯수가 10개 미만이면 굳이 가져올 필요없음)
        if (comments.removeState && state.commentPage.commentCount < 10) { return }
        res = await this.$axios.get(`books/${comments.bookId}/comments?limit=10`)
      } else {
        ...
      }

      commit('loadComments', { data: res.data, init: comments.init })
      return res
    } catch (error) {
      console.error(error)
    }
  },
   async deleteComment ({ commit, dispatch, state }, comment) {
    try {
      const res = await this.$axios.delete(`books/${comment.bookId}/comment/${comment.commentId}`)
      commit('removeComment', comment.commentId)
      if (state.comments.length < 10) {
        dispatch('fetchComments', { bookId: comment.bookId, init: true, removeState: true })
      }
      return res
    } catch (error) {
      console.error(error)
    }
  }
```
- `axios`를 이용해 api를 호출합니다.
<br><br>

* <div style="color:blue">댓글을 삭제할 대의 문제점</div>

|문제점|
|---|
|처음 `댓글 보기 버튼`을 클릭했을 때, `api`를 호출하여 데이터(댓글) 10개를 가져오고, 스크롤를 하단으로 내렸을 때, 다음 댓글을 가져올 수 있도록 `api`를 호출하도록 구현하였습니다. 만약 내가 댓글을 삭제했을 때, 스크롤이 화면 하단에 위치하지 않는다면, 더 불러올 데이터(댓글)이 존재함에도 불구하고, 호출되지 않는 문제가 발생했습니다.|
|예시)보여줄 코멘트가 10개 이상 존재하고,스크롤을 하단에 내리지 않은 상태에서 댓글을 삭제한다면, 보여줄 코멘트가 있어도, `화면 하단`으로 스크롤을 내리지 않는 이상`api`를 호출하지 않기 때문에,댓글을 보여주지 않습니다.|


|해결|
|---|
|댓글을 삭제했을 때,존재하는 댓글이 10개 이상일 경우,스크롤을 하단으로 내려 api를 추가적으로 호출하지 않은 상태일 때에만 추가적으로 api를 호출하도록 하였습니다.  |

```js
 async deleteComment ({ commit, dispatch, state }, comment) {
    try {
      ...
      // 댓글의 갯수가 10개 미만일때에만 actions함수 fetchComments를 호출합니다. {init: true, removeState: true} 로 데이터를 넘겨줍니다. 
      if (state.comments.length < 10) {
        dispatch('fetchComments', { bookId: comment.bookId, init: true, removeState: true })
      }
      return res
    } catch (error) {
      console.error(error)
    }
  },
  async fetchComments ({ commit, state }, comments) {
    try {
      let res
      if (comments.init) {
        // 댓글을 처음 불러오고,스크롤 변경 없이, 댓글이 삭제가 된다면 다시 댓글을 처음 불러오는는 것처럼 api를 호출합니다.(총 댓글의 갯수가 10개 미만이라면,다시 불러올 필요없이 리턴해줍니다.)
        if (comments.removeState && state.commentPage.commentCount < 10) { return }
        res = await this.$axios.get(`books/${comments.bookId}/comments?limit=10`)
      } else {
        ...
      }
      ....
    }
  },
```
<br>

- `commit`를 이용해 `mutatonis`을 호출합니다.

<br>

|mutations|
|---|
|removeComment|
```js
//store/comments.js mutations
   removeComment (state, id) {
    state.comments = state.comments.filter(comment => comment.id !== id)
    state.commentPage.commentCount--
  }
```
-  `state`의 `comments` 배열에 `id` 값을 비교하여, 해당 댓글을 삭제합니다.
- `댓글 삭제`시, 댓글의 갯수를 감소시켜줍니다.
<br><br>

|getters|
|---|
|getComments |
|getCommentPage |
```js
//store/comments.js  getters
  getComments (state) {
    return state.comments
  },
  getCommentPage (state) {
    return state.commentPage
  }
```
|getComments|
|---|
|`state`의 `comments`배열을 가져옵니다.|

|getCommentPage|
|---|
|`state`의 `commentPage`객체를 가져옵니다|
<br>

|state|
|---|
|comments|
|commentPage|
```js
//store/comments.js state
  comments: [],
  commentPage: {
    commentCount: 0,
    end: false
  }
```
|comments|
|---|
|댓글 데이터를 저장합니다.|

|commentPage|
|---|
|`마지막 페이지 여부`를 `end`에 `Boolean`값으로 저장하고, `commentCount`에 `총 댓글의 갯수`를 저장합니다.|

<br>

#### 2-2. 확인 알림창에서 "네" 클릭 시, `agree`함수를 호출합니다.
-`store`의 `actions`함수 `deleteComment`를 호출합니다.
```js
// ~/pages/books/b/_id.vue
       agree () {
      try {
        this.deleteComment({ bookId: this.$route.params.id, commentId: this.commendId })
          .then(() => {
            this.alert = false
          })
      } catch (error) {
        console.error(error)
      }
    }
```
<br><br>

### <div id="star" style="color:blue;"><b>11. 책에 별점 주기 기능 구현</b></div>
|컴포넌트|라우터|
|---|---|
|components/form/Comment.vue|books/b/_id|
|components/comment/Edit.vue|books/b/_id|

#### 1.총 별점은 5개까지 줄 수 있고, 마우스로 원하는 별점을 클릭할 수 있도록 구현하였습니다.
```html
<!-- ~/components/form/Comment.vue -->
<template>
  <form class="comment_form" @submit.prevent="onaddComment">
      ...
    <div class="comment_btm">
    <!-- 별점 주기 -->
      <div class="rating">
        <p v-for="(star,index) in stars" :key="index" :class="{'active':rating=== index+1}" @click=" ratingStar(index) ">
          <span :id="`star${index}`" :class="{'init':rating === 0}" name="star"><i class="fas fa-star"></i></span>
        </p>
        <b>
          <template v-if="rating === 0">
            별점 주기
          </template>
          <template v-else>
            <img :src="starChange" alt="">
          </template>
        </b>
      </div>
      ...
    </div>
  </form>
</template>
```
```js
// ~/components/form/Comment.vue
 data () {
    return {
      stars: 5,
      rating: 0
    }
  },
```

#### 별점 주기 구현

|초기|
|---|
| 처음에 아무것도 클릭하지 않았을 때 기본적으로 별의 색깔을 노란색으로 구현하였습니다.|
|초기 별의 색깔을 노란색으로 구현하였으므로,아무것도 클릭하지 않았을 때의 상태에서는 노란색이 아닌 검정색으로 보여주기 위해, `class` 를 바운딩시켜 별을 아예 클릭하지 않았을 경우에는 `init`으로 구분할수 있도록 구현하였습니다.|

```html
<!-- ~/components/form/Comment.vue -->
<template>
  <form class="comment_form" @submit.prevent="onaddComment">
    ...
    <div class="comment_btm">
      <!-- 별점 주기 -->
      <div class="rating">
        ...
        <!-- 별을 클릭하지 않았을 경우, 클래스명에 init를 추가해줍니다.별점을 하나라도 주게된다면 클래스명에 init을 삭제합니다. -->
        <span :id="`star${index}`" :class="{'init':rating === 0}" name="star"><i class="fas fa-star"></i></span>
        ...
      </div>
      ...
    </div>
  </form>
</template>
```
```css
/* 노란색의 별을 보여줍니다. */
.rating span{position: relative; cursor: pointer; display: inline-block; position: relative; color:gold; text-shadow: 0 0 5px yellow;}
/* 클래스명에 init 이 있다면 검은색의 별을 보여줍니다. */
.rating span.init{ text-shadow: none; color: #222;}
```
|별 클릭시|
|---|
| 해당 별을 클릭할 시, 클릭한 요소는 클래스명에 `active`가 추가되고, 클릭한 별의 이후의 별들은 모두 검은색으로 바꿔줍니다.|
```css
.rating p.active~ p span {color:#222; text-shadow: none;}
```


|위처럼 구현한 이유|
|---|
|내가 클릭한 별에 `class`명에 `active`를 붙여줍니다.|
|`css` `~` 형제선택자는 태그 뒤에 오는 모든 요소를 선택하는 것이기 때문에 `class`명에 `active` 요소가 붙은 전의 별들의 색깔을 `css`만으로는 수정이 어려워, 위처럼 반대로 구현하였습니다.<br>(초기 별의 색깔은 노란색으로 해주고, `class`명에 `active`가 붙는다면, 해당 태그 뒤에 오는 모든 별들의 색깔을 검은색으로 바꿔주어 `css`로만 별점 보여주기 기능을 구현 하였습니다.)|

<br>

#### 2.별점마다 보여주는 이미지가 변경되도록 구현하였습니다.

```js
data() {
    return {
      stars: 5,
      rating: 0
    }
  },
  computed: {
    // 별점마다 보여주는 이미지가 변경되도록 구현
    starChange() {
      switch (this.rating) {
        case 1:
          return '/images/star-lv1.png'
        case 2:
          return '/images/star-lv2.png'
        case 3:
          return '/images/star-lv3.png'
        case 4:
          return '/images/star-lv4.png'
        case 5:
          return '/images/star-lv5.png'
        default:
          return false
      }
    }
  },
  methods: {
    // 내가 클릭한 별의 갯수와 index의  숫자가 동일하도록 구현
    ratingStar(index) {
      this.rating = index + 1
    }
  }
```

#### computed

|starChange|
|---|
|`v-model`에 `data`인 `rating`를 바운딩시켜, `rating`의 숫자에 따라 보여지는 이미지가 달라지도록 구현하였습니다.|
```html
<!-- rating의 초기값은 0으로 설정하고, rating이 0이 아니라면 rating의 숫자에 따라 바운딩시켜준 이미지를 보여줍니다 -->
 <template v-if="rating === 0">
   별점 주기
 </template>
 <template v-else>
   <img :src="starChange" alt="#">
 </template>
```

#### 3.별점은 댓글를 추가할 때, 함께 서버에 전송하도록 구현하였습니다.
<a href="#c_add">댓글 추가 바로 가기</a>

<br><br>

### <div id="tag" style="color:blue;"><b>12. 내가 추가한 책 태그 추가 및 삭제</b></div>
|컴포넌트|라우터|
|---|---|
|components/form/Hashtag.vue|books/b/_id|
|components/book/CardDetail.vue|books/b/_id|

#### <div  style="color:red;">1-1. 태그 보여주기</div>
#### <div id="tag_props">1. api 호출 후, 데이터를 받아 `hashtagList.vue` `컴포넌트`에 props로 책 데이터를 내려주었습니다.</div>
```html
<!-- ~/components/book/CardDetail.vue -->
<template>
  <div>
    <div v-if="book">
      <h2 v-if="book.User" class="book_user">
        {{ book.User.username }}님의 책장
      </h2>
        ...
          <hashtag-list :hashtags="book.Hashtags" :book-id="book.id" :user-id="book.UserId"></hashtag-list>
         ....
    </div>
  </div>
</template>
```
```html
<template>
  <!-- 태그 이름 보여주기 -->
  <ul class="hashtags tagList">
    <li v-for="(tag,index) in hashtags" :key="index" class="tag" @mouseenter="onChangeState(tag,index)" @mouseleave="tagNum=''">
      <!-- 태그 클릭 시, 해당 태그를 가지고 있는 책들을 보여줍니다. -->
      <nuxt-link :to="`/hashtags/1/?name=${tag.name}`">
        #{{ tag.name }}
      </nuxt-link>
      ...
    </li>
  </ul>
</template>
```
```js
// ~/components/hashtagList.vue
  props: {
    hashtags: {
      type: Array,
      required: false
    },
    bookId: {
      type: Number,
      required: false
    },
    userId: {
      type: Number,
      required: false
    }
  }
```

#### props
|hashtags|bookId|userId|
|---|---|---|
|`api` 호출하여 가져온 데이터 `book`의 객체에서 `hashtags` 배열을 `props`로 내려받았습니다.|책의 `id`값으로,`해시태그 삭제`시 사용|사용자의 `id`값으로 내 책인지 확인하기위 해 사용|

- 해시태그가 없을 경우, `hashtags` 배열안에 데이터가 존재하지 않기 때문에 ` required: false`로 설정해주었습니다.
<br>

```js
// props로 받은 Hashtags 배열 정보 예시
Hashtags: [{
  BookHashtag: Object,
  createdAt: "2021-05-03T17:42:27.087Z"
  id: 36
  name: "추천"
  updatedAt: "2021-05-03T17:42:27.087Z"

}, ...]
```
<br>

#### <div  style="color:red;">1-2. 태그 추가</div>
|컴포넌트|라우터|
|---|---|
|components/form/Hashtag.vue|books/b/_id|

#### 1. 해시태그는 최대 10개까지 추가 가능하도록 구현하였습니다.
```html
<!-- ~/components/form.Hashtag.vue -->
<template>
  <div class="hashtag_form">
    <form @submit.prevent="onaddHashtag">
      <div class="text">
        <input v-model="hashtag" placeholder="ex)#추천#에세이#소설.." type="text">
      </div>
      <div>
        <button class="round-btn fill hash_htn" type="submit" :disabled="invalidHashtag || !hashtag ||hashtags.length>=10 ">
          추가
        </button>
        <p v-if="invalidHashtag" class="err">
          태그는 최대 10개까지 등록 가능합니다.
        </p>
        <p v-if="ishashtags && ishashtags.length===0" class="err">
          추가할 태그에 #을 붙여주세요.
        </p>
        <p v-if="msg" class="err">
          {{ msg }}
        </p>
      </div>
    </form>
    <!-- 기존에 중복된 해시태그를 제거하고 새롭게 추가될 예정인 해시태그를 미리 보여줍니다. -->
    <ul v-if="!invalidHashtag" class="hashtags">
      <li v-for="(tag,index) in newtagList " :key="index" class="tag">
        {{ tag }}
      </li>
    </ul>
  </div>
```
```js
computed: {
  // 입력폼에 작성한 태그의 이름이 중복되지 않도록 걸러줍니다.
    ishashtags () {
      const tags = new Set(this.hashtag.match(/#[^\s#]+/g))
      return [...tags]
    },
    // 기존에 가지고 있는 해시태그의 갯수와, 새롭게 추가될 예정인 해시태그의 갯수의 합이 10개인지 확인합니다.
    invalidHashtag () {
      return ((this.hashtags && this.hashtags.length) || 0) + ((this.newtagList && this.newtagList.length) || 0) > 10
    },
    // 기존에 추가된 해시태그의 이름과 새롭게 추가될 해시태그의 이름을 비교하여 중복을 제거한 요소를 newtagnames 배열에 저장합니다.
    newtagList () {
      if (!this.hashtag) { return }
      const tagnames = []
      const newtagnames = []
      this.hashtags.forEach((tag) => {
        tagnames.push(tag.name)
      })
      const news = (this.ishashtags || []).map((tag) => {
        return String(tag).slice(1).toLowerCase()
      })
      news.forEach((newtag) => {
        if (!tagnames.includes(newtag)) {
          newtagnames.push(newtag)
        }
      })
      return newtagnames
    }
  },
```


#### computed
|ishashtags|
|---|
|`set`으로 중복을 제거하고, <a href="https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/match">match 메서드</a>를 이용해 정규식을 사용하여 `#`글자가 포함된 문자열을 찾아 확인합니다.|

<br>


- `placeholder` 속성을 사용하여, 사용자가 입력폼의 양식을 알수 있도록 구현하였습니다.
```html
<!-- ~/components/form.Hashtag.vue -->
<template>
  <div class="hashtag_form">
    <form @submit.prevent="onaddHashtag">
      <div class="text">
        <p>
          <input v-model="hashtag" placeholder="ex)#추천#에세이#소설.." type="text">
        </p>
      </div>
      ....
    </form>
  </div>
</template>
```

<br>

|invalidHashtag|
|---|
|`set`으로 중복을 제거하고, <a href="https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/match">match 메서드</a>를 이용해 정규식을 사용하여 `#`글자가 포함된 문자열을 찾아 확인합니다.|
<br>

|newtagList|
|---|
|기존에 추가된 해시태그의 이름과 새롭게 추가될 해시태그의 이름을 비교하여 중복을 제거한 요소를 newtagnames 배열에 저장합니다.|

<br>

- `기존에 존재하는 해시태그의 갯수와 추가될 예정인 해시태그의 갯수의 합이 10개 이상인 경우`, `입력폼에 아무것도 작성하지 않은 경우`, `기존에 존재하는 해시태그의 갯수가 10개 이상인 경우` 버튼을 클릭하지 못하도록 `disabled`에 바운딩시켜주었습니다.

```html
<template>
  ...
  <button class="round-btn fill hash_htn" type="submit" :disabled="invalidHashtag || !hashtag ||hashtags.length>=10 ">
    추가
  </button>
  ...
</template>
```
<br>

#### 2. 해시태그 추가 api

#### 2-1. store
|<div>actions</div>|
|---|
|createHashtag|

```js
//store/book.js actions
  async createHashtag ({ commit }, { bookId, hashtags }) {
    try {
      const res = await this.$axios.post(`books/${bookId}/addhashtags`, { hashtags })
      commit('addHashtag', res.data.hashtagList)
    } catch (error) {
      console.error(error)
    }
  },
```
- `axios`를 이용해 api를 호출합니다.
- `commit`를 이용해 `mutatonis`을 호출합니다.


|mutations|
|---|
|addHashtag|
```js
//store/book.js  mutations
addHashtag (state, hashtagList) {
    state.book.Hashtags = state.book.Hashtags.concat(hashtagList)
  }
```
-  `state`의 `book`객체의 `Hashtags`배열에 새롭게 추가된 데이터를 누적시켜 보여줍니다.
<br>


|state|
|---|
|book|
```js
//store/book.js state
  book: {}
```
```js
// book의 Hashtags 배열에 저장되는 정보의 예시
book:{
Hashtags:[{
BookHashtag:Object
createdAt:"2021-06-18T09:06:49.752Z"
id:109
name:"에세이"
updatedAt:"2021-06-18T09:06:49.752Z"

},...]
}
```
#### 2-2. `추가` 버튼을 클릭할 시, 해시태그 추가 api를 호출합니다.
- `store`의 `actions`함수 `createHashtag` 를 호출합니다.
```js
// ~/components/form/Hashtag.vue
 methods: {
    ...mapActions('books', ['createHashtag']),
    onaddHashtag () {
      if (this.newtagList.length > 0) {
        this.createHashtag({ bookId: this.$route.params.id, hashtags: this.newtagList })
        this.resetHashtag()
      } else {
        this.msg = '이미 등록된 태그가 포함되어 있습니다.'
      }
    },
    //입력폼 초기화
    resetHashtag () {
      this.hashtag = ''
      this.msg = ''
    }
  }
```
#### methods
|onaddHashtag|
|---|
|`computed`로 기존 해시태그와 비교하여, 중복을 제거하고 추가될 예정인 해시태그를 `newtagList` 배열에 저장하였습니다.
`newtagList`에 데이터가 없다면 추가될 예정인 해시태그가 존재하지 않으므로 `return`시켜줍니다.|

<br>

#### <div  style="color:red;">1-3. 태그 삭제</div>
|컴포넌트|라우터|
|---|---|
|components/hashtag/List.vue|books/b/_id|

#### 1. 내 책에서만 해시태그를 삭제 가능하도록 구현하였습니다.

#### 1-1. 내가 추가된 책에서만 `엑스 버튼`이 보이도록하여 다른 사용자의 책에서는 해시태그를 삭제할 수 없도록 하였습니다.


```html
<!-- ~/components/Hashtag.List.vue -->
<template>
  <ul class="hashtags tagList">
    <li v-for="(tag,index) in hashtags" :key="index" class="tag" @mouseenter="onChangeState(tag,index)" @mouseleave="tagNum=''">
      <nuxt-link :to="`/hashtags/1/?name=${tag.name}`">
        #{{ tag.name }}
      </nuxt-link>
      <!-- 삭제 버튼 -->
      <span v-if="ismybook && bookId && index === tagNum " @click.prevent="onRemoveHashtag(tag.id)"><i class="fas fa-plus-circle"></i></span>
    </li>
  </ul>
</template>
```

```js
  computed: {
    ...mapGetters('user', ['getUser']),
    ismybook () {
      return this.getUser.id === this.userId
    }
  }
```
#### computed
|ismybook|
|---|
|로그인할 때 저장된 사용자의 `id`와 `props`로 내려준 `userId`를 비교해 내 책인지 다른 사용자의 책인지 비교합니다.|
```html
<!-- ~/components/Hashtag.List.vue -->
<template>
  ...
  <!-- 삭제 버튼 -->
  <!-- 내 책에서만 엑스버튼이 보이도록 구현 -->
  <span v-if="ismybook && bookId && index === tagNum " @click.prevent="onRemoveHashtag(tag.id)"><i
      class="fas fa-plus-circle"></i></span>
  ...
</template>
```
<br>

#### 1-2. 마우스를 올린 대상에서만 `엑스 버튼`이 표시되고, 해당 버튼을 누르면 삭제되도록 구현하였습니다.
```html
<!-- ~/components/Hashtag.List.vue -->
<template>
  <ul class="hashtags tagList">
    <li v-for="(tag,index) in hashtags" :key="index" class="tag" @mouseenter="onChangeState(tag,index)" @mouseleave="tagNum=''">
      <nuxt-link :to="`/hashtags/1/?name=${tag.name}`">
        #{{ tag.name }}
      </nuxt-link>
      <!-- 내책이고, 마우스를 올린 대상에서만 엑스버튼이 보이도록 구현 -->
      <span v-if="ismybook && bookId && index === tagNum " @click.prevent="onRemoveHashtag(tag.id)"><i class="fas fa-plus-circle"></i></span>
    </li>
  </ul>
</template>
```

```js
// ~/components/Hashtag.List.vue
  data () {
    return {
      tagNum: ''
    }
  },
  methods: {
    onChangeState(tag) {
      const tagNum = this.hashtags.findIndex(hashtag => hashtag.id === tag.id)
      this.tagNum = tagNum
    }
  }
```
#### methods
|onChangeState|
|---|
|마우스를 올리면, 마우스를 올린 태그의 `id`값과 `hashtags`배열에서 `id` 값이 같은 것을 찾아 `index`값을 찾습니다.|
|`data`의 `tagNum`에 `index`값을 저장하여 마우스를 올린 대상에서만 `엑스 버튼`이 보이도록 구현하였습니다.|

<br>

#### 2. 해시태그 삭제 api

#### 2-1. store
|<div>actions</div>|
|---|
|deleteHashtag|

```js
//store/book.js actions
async deleteHashtag ({ commit }, { bookId, hashtagId }) {
    try {
      await this.$axios.delete(`books/${bookId}/removehashtag/${hashtagId}`)
      commit('removeHashtag', hashtagId)
    } catch (error) {
      console.error(error)
    }
  },
```
- `axios`를 이용해 api를 호출합니다.
- `commit`를 이용해 `mutatonis`을 호출합니다.


|mutations|
|---|
|addHashtag|
```js
//store/book.js  mutations
  removeHashtag (state, hashtagId) {
    state.book.Hashtags = state.book.Hashtags.filter(tag => tag.id !== hashtagId)
  },
```
-  `state`의 `book`객체의 `Hashtags`배열에 `id`를 비교하여 삭제합니다.
<br>


|state|
|---|
|book|
```js
//store/book.js state
  book: {}
```

#### 2-2. `엑스` 버튼을 클릭할 시, 해시태그 삭제 api를 호출합니다.
- `store`의 `actions`함수 `deleteHashtag` 를 호출합니다.
```js
// ~/components/Hashtag/List.vue
 methods: {
     ...mapActions('books', ['deleteHashtag']),
     onRemoveHashtag (id) {
      console.log(id)
      this.deleteHashtag({ bookId: this.bookId, hashtagId: id })
    }
  }
  }
```


<br><br>


### <div id="sum" style="color:blue;"><b>13. 통계</b></div>
|컴포넌트|라우터|
|---|---|
|components/book/form/Hashtag.vue|books/b/_id|
|components/hashtag/List.vue|books/_id|


#### 1. 차트는  <a href="https://vue-chartjs.org/guide/">vue-chartjs</a> 리이브러리를 사용하였습니다.

####  총 4개의 차트를 구현하였습니다.


|나의 책|나의 북마크|좋아요|나의 댓글|
|---|---|---|---|
|내가 생성한 책의 갯수|내가 북마크한 갯수|내가좋아요한 갯수/내가 좋아요 받은 갯수|내가 생성한 코멘트의 갯수|
<br>

#### 3. 차트 데이터를 불러오는 api를 호출합니다.

#### 3-1. store
|<div>actions</div>|
|---|
|getCounts|

```js
//store/profile.js actions
  async getCounts () {
    try {
      const res = await this.$axios.get('/profiles')
      return res
    } catch (error) {
      console.error(error)
    }
  }
```
- `axios`를 이용해 api를 호출합니다.

#### 3-2. `nuxt`의 `asyncData`훅으로 차트데이터를 불러오는 api를 호출합니다.

```js
  async asyncData ({ store }) {
    try {
      let books
      let bookmarks
      let likes
      let likers
      let comments
      await store.dispatch('profile/getCounts')
        .then((res) => {
          books = res.data.books
          bookmarks = res.data.bookmarks
          likes = res.data.likes
          likers = res.data.likers
          comments = res.data.comments
        })
      return { books, bookmarks, likes, likers, comments }
    } catch (error) {
      console.error(error)
    }
  }

```

- <div id="state_user"> 성공적으로 api 호출시, 아래 정보를 저장합니다.</div>
```js
// 월별 북마크한 갯수
bookmarks: [{
    months: '5',
    value: '2'
  }, {
    months: '6',
    value: '2'
  }],
  // 월별 좋아요 받은 갯수
  likes: [{
    value: '7',
    months: '6'
  }],
  // 월별 좋아요한 갯수
  likers: [],
  // 월별 댓글 작성한 갯수
  comments: [{
    months: '6',
    value: '21'
  }],
  // 월별 내가 추가한 책의 갯수
  books: [{
    months: '5',
    value: '2'
  }, {
    months: '6',
    value: '12'
  }]
```
- 각각 `months` 월과 `value` 갯수로 구성된 객체형식으로 배열에 저장하도록 구현하였습니다.
<br>

#### 4. 차트 형식에 맞게 데이터를 포맷해주도록 구현하였습니다.
```js
  methods: {
    formatData (label, values, bgColor) {
      //데이터는 배열을 만들어 인덱스 숫자와 months의 숫자가 같은 위치에 value를 넣어줍니다.
      const books = Array.from({ length: 12 }, (v, index) => {
        return 0
      })
      values.forEach((v, index) => {
        books.splice(parseInt(v.months, 10) - 1, 1, parseInt(v.value, 10))
      })
      return [{ label, data: books, backgroundColor: bgColor }]
    }
  }
```

### methods

|formatData|
|---|
|`label` 차트의 소제목, `values` 갯수, `bgColor` 차트의 색 을 인자로 받아 차트를 구성하도록 포맷하였습니다|

|포맷 전 데이터|
|---|
|`bookmarks: [{months: '5',value: '2'}, {months: '6',value: '2'}]`|

|포맷 후 데이터|
|---|
|`배열`의 `index`번호에 `월`의 숫자가 매칭되도록 포맷해주었습니다.|
|`bookmarks: [0,0,0,0,2,2,0,0,0,0,0,0]`|
|해당 배열에서 ["1월","2월",...."12월"]의 데이터를 의미합니다.|
|`5월`에는 `2`, `6월`에는 `2`,나머지 월은 `0`|

#### 4. 포맷한 데이터로 차트를 구현하도록 하였습니다.
```html
<!-- ~/pages/user/profile.vue -->
<template>
...
    <div class="box">
      <h2>통계박스</h2>
      <div class="charts">
        <bar-chart :datas="formatData('내가 생성한 책의 갯수',books,'#EC407A')" :title-name="`나의 책`"></bar-chart>
        <bar-chart :datas="formatData('내가 북마크한 갯수',bookmarks,'royalblue')" :title-name="`나의 북마크`"></bar-chart>
        <bar-chart :datas="[...formatData('내가 좋아요 한 갯수',likes,'#EF9A9A'),...formatData('좋아요 받은 갯수',likers,'#29B6F6')]" :title-name="`좋아요`"></bar-chart>
        <bar-chart :datas="formatData('내가 생성한 댓글 갯수',comments,'#26C6DA')" :title-name="`나의 댓글`"></bar-chart>
      </div>
    </div>
  </div>
</template>

```
```html
<template>
  <!-- ~/components/chart/Bar.vue -->
  <div>
    <bar-chart-base :data="barChartData" :options="barChartOptions" :height="400"></bar-chart-base>
  </div>
</template>
```
```js
// components/chart/BarChart.vue
  props: {
    datas: {
      type: Array,
      required: true
    },
    titleName: {
      type: String,
      required: true
    }
  }
```

### props
|datas|
|---|
|차트를 구성하는 배열형식의 데이터|

|titleName|
|---|
|차트의 제목|

- <a href="https://www.chartjs.org/docs/latest/charts/bar.html">chart.js 공식 문서 참고</a>
- <a href="https://vue-chartjs.org/guide/#creating-your-first-chart">vue-chart.js 공식 문서 참고</a>



## server
### node + express

### 1. 사용한 라이브러리

||<a href="http://expressjs.com/">express</a>|<a href="https://github.com/expressjs/morgan#readme">morgan</a>|<a href="https://github.com/remy/nodemon">nodemon</a>|<a href="https://github.com/expressjs/cors#readme">cors</a>|
|---|---|:---|:---:|:---:|
|버전|v4.17.1|v1.10.0|v2.0.7|v2.8.5|
|_|node.js 프레임 워크|http 요청 로그 확인 미들웨어|서버 재시작하지 않아도 구동 도와줌| <a href="https://en.wikipedia.org/wiki/Cross-origin_resource_sharing">CORS</a> 를 활성화하는 데 사용할 수 있는 Connect / Express 미들웨어|


||<a href="https://github.com/expressjs/session#readme">express-session</a>|<a href="https://github.com/expressjs/cookie-parser#readme">cookie-parser</a>|<a href="https://github.com/request/request#readme">request</a>|<a href="https://github.com/motdotla/dotenv#readme">dotenv</a>|
|---|---|:---|:---:|:---:|
|버전|v1.17.1|v1.4.5|v2.88.2|v8.2.0|
|_|세션 데이터|쿠키 헤더를 분석해주는 라이브러리|http 요청을 도와주는 라이브러리(2020 년 2 월 11일 이후 지원 중단)|환경 변수를 .env파일에서 process.env로 불러올 수 있도록 하는 라이브러리|



||<a href="https://github.com/aws/aws-sdk-js">aws-sdk</a>|<a href="https://github.com/expressjs/multer#readme">multer</a>|<a href="https://github.com/badunk/multer-s3#readme">multer-s3</a>|<a href="https://github.com/kelektiv/node.bcrypt.js#readme">bcrypt</a>|
|---|---|:---|:---:|:---:|
|버전|v2.888.0|v1.4.2|v2.9.0|v5.0.1|
|_|Node.js의 JavaScript용 AWS SDK를 지원해주는 라이브러리|파일 업로드를 위해 사용되는 multipart/form-data 를 다루기 위한 node.js 의 미들웨어|AWS S3를 위한 multer 라이브러리| 내용|비밀번호 암호화하는데 도와주는 라이브러리|



||<a href="https://www.passportjs.org/">passport</a>|<a href="https://www.passportjs.org/packages/passport-google-oauth20/">passport-google-oauth20</a>|<a href="https://www.passportjs.org/packages/passport-kakao/">passport-kakao</a>|<a href="https://www.passportjs.org/packages/passport-local/">passport-local</a>|
|:---:|:---:|:---:|:---:|:---:|
|버전|v0.4.1|v2.0.0|v1.0.1|v1.0.0|
|-|전략 개념을 사용하여 사용자 정보를 인증하는 것을   도와주는 Node.js를 위한 미들웨어|구글 인증을 위한 passport 미들웨어|카카오 인증을 위한 passport 미들웨어|이메일 사용 인증을 위한 passport 미들웨어|

||<a href="https://github.com/brianc/node-postgres">pg</a>|<a href="https://sequelize.org/master/">sequdlize</a>|<a href="https://github.com/sequelize/cli">sequelize-cli</a>|
|:---:|:---:|:---:|:---:|
|버전|v8.5.1|v6.6.2|v6.2.0|
|-|Node.js 용 PostgreSQL|Postgres , MySQL , MariaDB , SQLite 및 Microsoft SQL Server를 위한  Node.js ORM|sequelize 용 cli|

### 2. 구현 목표

1. <a href="s_login">로그인/로그아웃 구현</a>
   - passport.js 이용하여 로그인 구현
   - 이메일/비밀번호 로그인 
   - 카카오 로그인 
   - 네이버 로그인 
2. <a href="s_userinfo">사용자 정보 수정</a>
   - 비밀번호 변경
   - 사용자 프로필(썸네일) 수정
3. <a href="s_search">책 검색</a>
  - 카카오 api 책 검색 
4. 책 추가 및 수정 및 삭제
5. 내 책 및 다른 사용자의 책 가져오기
6. 북마크 추가 및 삭제
7. 책 좋아요 및 좋아요 삭제
8. 코멘트 추가 및 삭제
9. 코멘트 가져오기
10. 해시태그 추가 및 삭제
11. 해시태그 가져오기
12. <a href="s_img">이미지 업로드</a>
   - 책 이미지(썸네일) 
   - 사용자 프로필(썸네일) 이미지
13. <a href="s_sum">통계 데이터</a>




### 3. 구현 세부 내용

#### 3-1 구현 내용 공통 요소

- 사용한 DB : <a href="https://www.postgresql.org/">postgreSQL</a> 
- sequlize(Postgres , MySQL , MariaDB , SQLite 및 Microsoft SQL Server를 위한 Node.js ORM) 사용

#### 3-2. 사용한 라이브러리


|<a href="https://github.com/brianc/node-postgres">pg</a>|<a href="https://sequelize.org/master/">sequdlize</a>|<a href="https://github.com/sequelize/cli">sequelize-cli</a>|
|:---:|:---:|:---:|
|Node.js 용 PostgreSQL|Postgres , MySQL , MariaDB , SQLite 및 Microsoft SQL Server를 위한  Node.js ORM|sequelize 용 cli|
#### 3-3. DB

#### 테이블

이미지 삽입


#### 관계
이미지 삽입

### 4. 구현 세부 내용 정리
<br>

### <div id="s_login" style="color:blue;"><b>1. 로그인/로그아웃 구현</b></div>
#### 1. 사용한 라이브러리
|<a href="https://www.passportjs.org/">passport</a>|<a href="https://www.passportjs.org/packages/passport-google-oauth20/">passport-google-oauth20</a>|<a href="https://www.passportjs.org/packages/passport-kakao/">passport-kakao</a>|<a href="https://www.passportjs.org/packages/passport-local/">passport-local</a>|<a href="https://github.com/kelektiv/node.bcrypt.js#readme">bcrypt</a>|
|:---:|:---:|:---:|:---:|:---:|
|전략 개념을 사용하여 사용자 정보를 인증하는 것을   도와주는 Node.js를 위한 미들웨어|구글 인증을 위한 passport 미들웨어|카카오 인증을 위한 passport 미들웨어|이메일 사용 인증을 위한 passport 미들웨어|비밀번호 암호화하는데 도와주는 라이브러리|

- 이메일 로그인 : `passort-local`
- 카카오 로그인 : `passort-kakao`
- 카카오 로그인 : `passport-google-oauth20`
- 비밀번호 암호화
```js
// server/Controller/auth.js
    async register(req,res,next){
        try {
            const {email,password,username}=req.body;
            // 비밀번호 암호화
            const hash=await bcrypt.hash(password,12);
            const user =await db.User.findOne({where:{email}})
            // 가입된 유저 있는지 확인
            if(user){
               return res.status(403).json({
                    success:false,
                    msg:'이미 회원가입된 유저입니다.'
                });
            };
            // 회원가입
            await db.User.create({email,password:hash,username});
            loginConfirm(req,res,next);
        } catch (error) {
            console.error(error);
            return next(error);
        }
    },
```

### <div id="s_userinfo" style="color:blue;"><b>2. 사용자 정보 수정</b></div>
#### 2-1. 비밀번호 변경
```js
// server/Controller/auth.js
  async changePassword(req,res,next){
      try {
        const {password} = req.body;
        const hash=await bcrypt.hash(password,12);
        await db.User.update({password:hash},{where:{id:req.user.id}})
        return  res.json({
          success:true,
          msg:'비밀번호 수정 완료되었습니다.',
        })
      } catch (error) {
        console.error(error);
        return next(error);
      }

     }
```
#### 2-2. 사용자 프로필(썸네일) 수정
```js
// server/Controller/auth.js
 async changeUserinfo(req,res,next){
      try {
        // 사용자 이름(닉네임) 변경
        if(req.body.username){
          await db.User.update({
            username:req.body.username,
          },{
            where:{id:req.user.id}
          });
        }
        // 사용자 프로필(썸네일) 이미지 변경
        if(req.body.thumbnail){
          await db.User.update({
            thumbnail:req.body.thumbnail
          },{
            where:{id:req.user.id}
          });
        }
        const newUser=await db.User.findOne({where:{id:req.user.id},attributes:['id','email','username','thumbnail']})
         
      return  res.json({
        success:true,
        msg:'프로필 수정 완료되었습니다.',
        user:newUser
      })
      } catch (error) {
        console.error(error);
       return next(error);
      }
     }
```

### <div id="s_search" style="color:blue;"><b>3.책 검색</b></div>
#### 1. 사용한 라이브러리
|<a href="https://github.com/request/request#readme">request</a>|
|:---:|
|http 요청을 도와주는 라이브러리(2020 년 2 월 11일 이후 지원 중단)|

- <a href="https://developers.kakao.com/docs/latest/ko/daum-search/dev-guide#search-book"></a>카카오 개발자 센터 바로가기 및 <a href="https://developers.naver.com/docs/serviceapi/datalab/search/search.md#node-js">네이버 개발자 센터 바로가기</a>
```js
// server/Controller/book.js
  kakaosearch(req,res,next){
        //통합 검색
        const api_url='https://dapi.kakao.com/v3/search/book?query=' + encodeURI(req.query.query);
        let  option={
            size:req.query.size,
            page:req.query.page
        };
        //타이틀 검색,isbn검색,저자 검색,출판사 검색
        if(req.query.target){
            option={...option,target:encodeURI(req.query.target)}
        }

        let options={
            url:api_url,
            qs:option,
            headers: {"Authorization":` KakaoAK ${process.env.KAKAO_APIKEY}`}
        };

        request.get(options,function(error,response,body){
            if(!error && response.statusCode == 200){
                res.writeHead(200, {'Content-Type': 'text/json;charset=utf-8'});
               res.end(body);
            }else{
                res.status(response.statusCode).end();
                console.log('error = ' + response.statusCode);
            }
        })
    },
```
#### <b style="color:red;">* 이슈</b>
- `2020년 2월 11일 이후` `request`라이브러릴 지원 중단.
<br>

### <div id="s_img" style="color:blue;"><b>11. 이미지 업로드</b></div>
#### 1. 사용한 라이브러리
|<a href="https://github.com/aws/aws-sdk-js">aws-sdk</a>|<a href="https://github.com/expressjs/multer#readme">multer</a>|<a href="https://github.com/badunk/multer-s3#readme">multer-s3</a>|
|:---:|:---:|:---:|
|Node.js의 JavaScript용 AWS SDK를 지원해주는 라이브러리|파일 업로드를 위해 사용되는 multipart/form-data 를 다루기 위한 node.js 의 미들웨어|AWS S3를 위한 multer 라이브러리|

```js
// server/Controller/Image.js
const aws= require('aws-sdk');
const multer=require('multer');
const multer3=require('multer-s3');
// 암호 같은 중요한 정보는  .env 에 저장하여 불러오도록 구현
require("dotenv").config();
// Amazon S3 버킷에 이미지 저장
aws.config.update({
    secretAccessKey:process.env.AWSSECRETKEY,
    accessKeyId:process.env.AWSACCESSKEYID,
    region:'ap-northeast-2'
})
let s3= new aws.S3();

upload=multer({
    storage:multer3({
        s3:s3,
        bucket:"am-clone",
        acl:'public-read',
        metadata:function(req,file,cb){
            cb(null,{fieldName:file.fieldname})
        },
        key:function(req,file,cb){
            cb(null,Date.now().toString())
        }
    })
}),
```
### <div id="s_sum" style="color:blue;"><b>12. 통계 데이터</b></div>

#### 1. 통계를 위해 해당 양식에 맞게 포맷시키도록 구현하였습니다.

- 양식
```js
// 월별 북마크한 갯수
bookmarks: [{
    months: '5',
    value: '2'
  }, {
    months: '6',
    value: '2'
  }],
  // 월별 좋아요 받은 갯수
  likes: [{
    value: '7',
    months: '6'
  }],
  // 월별 좋아요한 갯수
  likers: [],
  // 월별 댓글 작성한 갯수
  comments: [{
    months: '6',
    value: '21'
  }],
  // 월별 내가 추가한 책의 갯수
  books: [{
    months: '5',
    value: '2'
  }, {
    months: '6',
    value: '12'
  }]
```


```js
// server/Controller/profile.js
// 통계
const db = require('../models');
const sequelize=require('sequelize');
const moment=require('moment');

// 날짜 포맷 함수
const Format=(data,obj)=>{
    if(!obj){
        return data.filter(value=>value.dataValues).map(value=>value.dataValues.months=moment(value.dataValues.months).format("M"))
    }
    return data.map(value=>value.months=moment(value.months).format("M"))

}
module.exports={
    async Counts(req,res,next){
        try {
            //생성한 책의 수
            const books=await db.Book.findAll({
                attributes: [[ sequelize.fn('date_trunc', 'month', sequelize.col('createdAt')), 'months'], 
                  [sequelize.fn('count', sequelize.col('id')), 'value']], 
                  where:{UserId:req.user.id},
                  group: ['months']
            })
            // 북마크한 수
           const bookmarks=await db.Book.findAll({
                attributes: [[ sequelize.fn('date_trunc', 'month', sequelize.col('createdAt')), 'months'], 
                  [sequelize.fn('count', sequelize.col('bookmark')), 'value']], 
                  where:{[sequelize.Op.and]:[
                      {bookmark:true},
                      {UserId:req.user.id}
                  ]},
                  group: ['months']
            })
            //좋아요한 수
            //직접 작성하여 구현
           const likeList=await db.sequelize.query(`SELECT 
           count(*) as value,date_trunc('month', "createdAt")::date as months
       from "Like"
       where   "Like"."UserId"=${req.user.id}
       GROUP BY date_trunc('month',"createdAt");`)
       //좋아요 받은 수
        //직접 작성하여 구현
       const likerList=await db.sequelize.query(`SELECT 
       count(*) as value,date_trunc('month', "Like"."createdAt")::date as months
   from "Like","Books"
   where   "Books"."UserId"=${req.user.id} and "Books".id ="Like"."BookId"
   GROUP BY date_trunc('month',"Like"."createdAt")`)
   //작성한 코멘트수
   const comments=await db.Comment.findAll({
    attributes: [[ sequelize.fn('date_trunc', 'month', sequelize.col('createdAt')), 'months'], 
      [sequelize.fn('count', sequelize.col('UserId')), 'value']], 
      where: {UserId:req.user.id},
      group: ['months']
})   
        const likes=likeList[0]
        const likers=likerList[0]
            Format(books)
            Format(bookmarks)
            Format(comments)
            Format(likes,true)
            Format(likers,true)
            console.log()
            res.json({
                bookmarks,   
                likes,
                likers,
                comments,
                books
            })
        
        } catch (error) {
            console.error(error);
            return next(error);
        }
    }

}
```
- `date_trunc` 함수를 이용해 `월`을 추출하여,
월별로 데이터를 가져올 수 있도록 하였습니다.
(<a href="https://www.postgresqltutorial.com/postgresql-date_trunc/">postgreSQL `date_trunc` 관련 문서 바로가기</a>)







