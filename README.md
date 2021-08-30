# libraryApp
# client

## vue +nuxt
- SSR(서버사이드렌더링)과 SEO(검색엔진최적화) 를 위해 nuxt 프레임워크를 사용하였습니다.
- vue 는 안정화버전인 2.x 버전을 사용하였습니다.
- Visual Studio(vscode)를 이용해 작업하였습니다.
## 1. 버전

|node|Nuxt|vue|
|---|:---|:---:|
|v12.13.0|v2.15.4|v2.x|
<br>
- 사용한 라이브러리

||<a href="https://axios.nuxtjs.org/">nuxtjs/axios</a>|<a href="https://vue-chartjs.org/">chart.js / vue-chartjs</a>|<a href="https://bootstrap-vue.org/docs">bootstrap-vue</a>|<a href="https://github.com/nuxt-community/moment-module">@nuxtjs/moment</a>|<a href="https://www.npmjs.com/package/intersection-observer">intersection-observer</a>|<a href="https://www.npmjs.com/package/is-iexplorer">is-iexplorer</a>|<a href="https://pm2.keymetrics.io/docs/usage/quick-start/">pm2</a>|<a href="https://docs.cypress.io/guides/overview/why-cypress#In-a-nutshell">cypress</a>|
|---|---|:---|:---|:---|:---|:---|:---|:---|
|버전|v5.13.1|v2.9.4|v2.21.2|v1.6.1|v0.12.0|v1.0.0|v5.1.0|v8.0.0|
|이유|HTTP 클라이언트 라이브러리로 서버와의 통신을 위해 사용|데이터를 이용해 chart 사용|페이지네이션과 달력 사용|날짜 포맷 변경|IE 에서 `intersection-observer`를 사용할 수 있도록 해주는 라이브러리|IE 브라우저인지 확인|node.js 에서 실행한 프로세스를 관리해주는 프로세스 매니져로 관리해주는 라이브러리 |E2E 테스트(종단간 테스트)|



## 2. 구현 목표

1. <a href="#register">회원가입/로그인 구현</a>
   - 이메일/비밀번호 구현(이메일,비밀번호,닉네임)
   - 소셜로그인(카카오,구글 로그인) 구현
2. <a href="#user_info">사용자 정보 수정</a>
   - 사용자의 비밀번호,닉네임 수정 구현
   - 프로파일 이미지 추가 및 수정 구현
3. <a href="#book_search">원하는 책 검색 및 추가</a>
    - 카카오 검색 api로 원하는 책을 검색하여 추가할 수 있도록 구현
4. <a href="#book_create">직접 책 추가 및 추가한 책 수정 및 삭제</a>
  - 직접 작성하여 책을 추가할 수 있도록 구현
  - 내가 추가한 책들 중에서 원하지 않는 책 수정 및 삭제 기능 구현
5. <a href="#books">내가 추가한 책들 보여주기</a>
5. <a href="#book">내가 추가한 책 상세 보기</a>
6. <a href="#bookmark">내 책 북마크 추가 및 삭제</a>

7. <a href="#heart">다른 사용자의 책 좋아요 생성 및 삭제</a>
8. <a href="#tag">내가 추가한 책 태그 추가 및 삭제</a>
9. <a href="#tags">태그별로 책 모아보기</a>
10. <a href="#other_books">다른 사용자가 추가한 책 보여주기</a>
11. <a href="#comment">댓글 추가 및 삭제</a>
    - 다른 사용자의 책과 내가 추가한 책에 댓글 쓰기 기능 구현
    - 내가 추가한 댓글만 삭제 가능하도록 구현
12. <a href="#star">책에 별점 주기 가능</a>
13. <a href="#other_search">다른 사용자의 책 검색 구현</a>
    - 책 제목과 책의 저자를 선택하여 검색할 수 있도록 구현
14. <a href="#sum">통계</a>


## 3. 구현 세부 내용

### 3-1 라우터 구조

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
    |    |----search
    |           |-----_page
    |
    |--hastags/_page
```
### 3-2. css
|구현 방법|구현 내용|
|---|---|
|CSS|static/css/common.css 만들어 공통 요소로 초기화|
|CSS|static/css/style.css 만들어 css 요소 정리|

### 3-3. `vuex` `store` 사용
#### 공통 요소
```js
// ~/store/index.js
export const state = () => ({
  // 로딩
  initLoading: false
})
export const mutations = {
  // 로딩 여부
  changeLoading (state, value) {
    state.initLoading = value
  }
}
export const actions = {
  // 서버에서 사용자 정보 가져오기
  async nuxtServerInit ({ dispatch }) {
    await dispatch('user/fetchUser')
  }
}
```

### 3-4. 구현 공통 요소
####  component(컴포넌트)
- `component`(컴포넌트)는 `import`해서 가져오지 않아도, `nuxt` 에서  `component`(컴포넌트)를 자동으로 가져올 수 있습니다.(`nuxt` v2.13 버전 이상)
 - <a href="https://nuxtjs.org/docs/2.x/directory-structure/components">`nuxt` 컴포넌트 디렉토리 공식 문서 바로 가기</a>
```js
// nuxt.config.js
export default {
  // 해당 속성을 true 하여, 컴포넌트 경로에서 자동으로 가져올수 있도록 하였습니다.
 components: true
}
```
<br>

#### axios

- `axios Interceptors`를 이용해 요청을 보내기 전에 내용을 처리할 수 있도록 하였습니다.
```js
// ~/plugins/axios.js
import ischk from 'is-iexplorer'

export default function ({ $axios, error, redirect }) {
  $axios.onRequest((config) => {
  // IE 에서 get 메서드 호출시 캐시문제로 재호출이 되지 않는 문제 발생으로 브라우저가 IE일경우, 요청헤더에 아래 내용 추가
    if (ischk) {
      config.headers['Cache-Control'] = 'no-cache'
      config.headers.Pragma = 'no-cache'
    }
    return config
  })
}
```
|IE 캐시 이슈 |
|:---|
|브라우져가 캐시하는 대상은 GET 메소드에 한정되는데 IE일 경우 GET 메소드 호출시, 캐시를 사용하여 메서드가 재호출되지 않는 문제가 발생했습니다.|

|IE 캐시 이슈 해결|
|:---|
|IE일 경우, 요청 헤더에 `Cache-Control: no-cache`를 담아 서버로 전송하면 브라우져는 캐시를 사용하지 않고 서버로 요청합니다.|

<br>

#### 오류 처리

- 데이터 요청시, 오류가 발생할 때 처리할 수 있도록 하였습니다.

```js
// ~/plugins/axios.js
export default function ({ $axios, error, redirect }) {
  ...
  // 오류 처리
  $axios.onError((err) => {
    const code = parseInt(err.response && err.response.status)
    if (code === 401) {
      if (err.response.data.auth) {
        // 인증이 필요한 상태이고,로그인이 되어 있지 않을 때 로그인 페이지로 이동
        redirect('auth/login')
      } else if (err.response.data.authed) {
        // 인증이 필요한 상태이고,로그인이 이미 되어 있으면 리로드하여 새로고침 실행
        window.location.reload()
      }
      return
    }
    if (code === 404 || code === 400 || code === 500) {
      // 해당 에러가 발생할 때, 에러페이지를 보여주도록 구현
      return error({
        statusCode: err.response.status,
        msg: err.response.data.msg
      })
    }
  })
}
```
<br>

- `nuxt`에서 제공하는 `layout/error.vue`를 구성하여, 오류가 발생할 때, 해당 페이지를 보여주도록 하였습니다.
<br>
<a href="https://nuxtjs.org/docs/2.x/directory-structure/layouts#error-page">`nuxt` error page 공식 문서 바로 가기</a>

```html
<!-- layouts/error.vue -->
<template>
  <div class="err_container">
    <div class="txt">
      <h3 v-if="error.msg">
        {{ error.msg }}
      </h3>
      <h2 v-else>
        오류가 발생하여 다시 시도해주세요.
      </h2>
      <p>{{ error.statusCode }}</p>
    </div>
  </div>
</template>

<script>
export default {
  props: ['error']
}
</script>

```
<br>

### 3-5. 구현 공통 컴포넌트
#### 알림창
***
 `eventbus` 를 이용하여 구현하였습니다.

```html
<!-- ~/layouts/default.vue -->
<template>
  ...
    <CommonAlertMsg :alert-state="alertState" :data="data" />
  ...
</template>

```
```js
//  ~/layouts/default.vue
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

<b>common-alert-msg 컴포넌트</b>

`transition`으로 애니메이션 효과를 주었습니다.
(<a href="https://vuejs.org/v2/api/#transition">vue transitioin 문서 바로 가기</a>)

```html
<!-- ~/components/common/alertMsg.vue -->
<!-- transition으로 애니메이션 효과 -->
  <transition name="upSlide">
    <div v-if="alertState" class="alertmsg" :style="{ 'background-color': bgcolor }">
      {{ data }}
    </div>
  </transition>
```

<br>

<b>props</b>

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
    },
    bgcolor: {
      type: String,
      required: false,
      default: '#222'
    }
  }
```
|props|타입|설명|
|:---|:---|:---|
|alertState|Boolean|알람메세지 여부|
|data|String|`데이터 추가`,`데이터 삭제` 등에 상관없이 알람메세지를 공통으로 적용해주기 위해 알림메세지 내용을  `props`로 받도록 구현했습니다.|
|bgcolor|String|알림창 배경색|


<br>

#### 삭제/수정 확인 알림창
***
```html
<!-- ~/components/form/Alert.vue -->
<template>
  <CommonModal class="alert_main">
    <div slot="header">
      <b>{{ data }}</b> 을(를) {{ confirm }}하시겠어요??
    </div>
    <div slot="body" class="body">
      <button class="primary-btn" @click="$emit('onagree')">
        네
      </button>
      <button class="primary-btn red" @click="$emit('ondisagree')">
        아니오
      </button>
    </div>
    <div slot="footer"></div>
  </CommonModal>
</template>

```

<br>

<b>props</b>

```js
//  ~/components/form/Alert.vue
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

|props|타입|설명|
|:---|:---|:---|
|data|String|삭제/수정할 데이터의 제목|
|confirm|String|삭제/수정 여부|

<br>

#### 검색 폼
***
<b>props</b>

```js
// ~/components/form/Search.vue
export default {
  props: {
    options: {
      type: Array,
      required: true
    },
    value: {
      type: String || Number,
      required: false
    }
  },
  ...
}
```
|props|타입|설명|
|:---|:---|:---|
|options|Array|검색 옵션<br> ex)책제목,저자 등 검색 옵션|
|value|String / Number|검색 입력폼에 입력한 값(`input`태그의 값)|
<br>

<b>methods && data</b>

```js
// ~/components/form/Search.vue
export default {
  data () {
    return {
      selected: false,
      selectedOption: this.options[0]
    }
  },
  methods: {
    changeSelect (option) {
      this.selectedOption = option
      this.selected = false
      this.$refs.searchInput.focus()
      this.$emit('selectedOption', option)
    }
  }
}
```

|data|설명|
|:---|:---|
|selected|옵션창에 마우스가 진입했는지 여부|
|selectedOption|`props`로 받은 `options` 배열 중 내가 클릭하여 선택한 데이터|

|methods|설명|
|:---|:---|
|changeSelect|검색 옵션 수정시, 수정한 옵션을 `$emit`을 이용해 상위컴포넌트에 전달해줍니다. |

<br>

<b>events</b>

```html
<!-- ~/components/form/Search.vue -->
<template>
  <form class="search_form" @submit.prevent="$emit('searchBook')">
    <div class="main_select" @mouseenter="selected=!selected" @mouseleave="selected=false">
      ...
      <ul v-if="selected" class="custom_select">
        <li v-for="(option,index) in options" :key="index" @click="changeSelect(option)">
          {{ option }}
        </li>
      </ul>
    ...
    <input ref="searchInput" :value="value" type="text" placeholder="검색" @input="$emit('input',$event.target.value)">
    <button type="submit">
      <i class="fas fa-search"></i>
    </button>
  </form>
</template>

```

|events|설명|
|:---|:---|
|searchBook|`submit`이벤트 발생시, `$emit`을 이용해 상위 컴포넌트에 전달해줍니다. |
|input|`input`이벤트 발생시,`$emit`을 이용해 입력폼에 입력된 값을 상위 컴포넌트에 전달해줍니다. |

<br>

## 4. 구현 세부 내용 정리

<br>

### <div id="register"><b>1. 회원가입/로그인 구현</b></div>

|컴포넌트|라우터|
|---|---|
|components/form/Register.vue|auth/register|
|components/form/Login.vue|auth/login|

<br>

### <div><b>1-1. 회원가입</b></div>

|컴포넌트|라우터|
|---|---|
|components/form/Register.vue|auth/register|

#### <b>구현 내용</b>
***
<b>1. 이메일,닉네임,비밀번호를 입력해야 가입될 수 있도록 구현하였습니다</b>
```html
<!-- ~/components/form/Register.vue -->
<template>
  <div class="user signupbox">
    <div class="formbx">
      <form @submit.prevent="UserRegister">
        <h2>회원가입</h2>
        <!-- 이메일 -->
        <div :class="{'invalid':!email}">
          <label for="email">email</label>
          <input id="email" ref="emailinput" v-model="email" type="text" placeholder="이메일">
        </div>
        <!-- 이메일이 존재하고, 이메일 형식이 아니라면 아래 메세지를 보여줍니다. -->
        <div v-if="!isvalidEmail && email" class="err">
          이메일 형식으로 입력해주세요.
        </div>
        <!-- 닉네임 -->
        <div :class="{'invalid':!username}">
          <label for="username">username</label>
          <input id="username" v-model="username" type="text" placeholder="닉네임">
        </div>
        <!-- 비밀번호 -->
        <div :class="{'invalid':!password}">
          <label for="password">password</label>
          <input id="password" v-model="password" type="password" placeholder="비밀번호">
        </div>
         <div v-if="!isvalidLength && password" class="err">
          비밀번호는 8자리 이상 30자 이하여야 합니다.
        </div>
        <!-- 비밀번호 확인 -->
        <div :class="{'invalid':!confirm_password}">
          <label for="comfirm_password">comfirm_password</label>
          <input id="comfirm_password" v-model="confirm_password" type="password" placeholder="비밀번호 확인">
          <div v-if="!isconfirmPassword && confirm_password" class="err">
            비밀번호가 일치하지 않습니다
          </div>
        </div>
        <!-- 에러 메세지 -->
        <!--
        회원가입 시 에러가 발생했을 때(이메일이 이미 등록되어 있는 경우 등의 오류가 발생했을 때)
        이를 사용자가 확인할 수 있도록 구현했습니다.-->
        <div v-if="errmsg" class="errmsg" :class="{'visible':errmsg}">
            {{ errmsg }}
          </div>
          <!-- 입력값에 따른 에러 메세지를 보여줍니다. -->
          <div v-if="!isuserInfoLength " class="errmsg" :class="{'visible':!isuserInfoLength }">
            {{ inputErrMsg }}
          </div>
          <!-- disabled를 바운딩시켜, 유효성 검사를 모두 다 통과한다면 버튼을 클릭할 수 있도록 합니다.
          유효성 검사를 모두 다 통과한다면 disabled속성을 사라져 버튼을 클릭할 수 있습니다. -->
        <button class="primary-btn" type="submit" :disabled="!disabledBtn">
            회원가입
          </button>
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
      user: {
        email: '',
        username: ''
      },
      password: '',
      confirm_password: '',
      errmsg: ''
    }
  },
```


|data|설명|
|:---|:---|
|user|사용자의 `email`과 `username` 정보|
|password|비밀번호|
|confirm_password|data의 `password`값과 비교하기 위한 데이터|
|errmsg|에러 발생시, 보여줄 데이터|
> `v-model`를 이용해 데이터를 양방향 바운딩해주었습니다.<br>

<br>

<b>2. 유효성 검사</b>

- 별도의 유효성 검사 함수를 만들어 활용하였습니다.

```js
// ~/utils/validate.js

// 이메일 유효성 검사
const validEmail = (mail) => {
  if (/^[a-zA-Z0-9.!#$%&'*+/=?^_`{|}~-]+@[a-zA-Z0-9-]+(?:\.[a-zA-Z0-9-]+)*$/.test(mail)) {
    return true
  }
  return false
}
// 압력값의 길이 범위를 확인(몇자 이상 이하 인지 확인)
const validLength = (value, min = 0, max = 500) => {
  let data = false
  const valueData = value.trim().length
  if (min <= valueData && valueData <= max) {
    data = true
  }
  return data
}
// 입력값 데이터(객체) 전부의 길이를 확인
const inputLen = (value, data, len) => {
  return data.every(key => value[key].length < len)
}

export { validLength, validEmail, inputLen }

```

<br>

- 유효성 검사 리스트

1. 비밀번호 길이 검사
2. 이메일 유효성 검사
3. `data`의 `password` 의 값과 `data`의 `confirm_password` 의 값이 일치하는지 검사
4. 이메일과 닉네임의 길이 검사


```js
// ~/components/form/Register.vue
import { validLength, validEmail } from '~/utils/validate'
export default {
 computed: {
   //  1. 비밀번호 길이 검사
    isvalidLength () {
      return validLength(this.password, 8, 30)
    },
    // 2. 이메일 유효성 검사
    isvalidEmail () {
      return validEmail(this.user.email)
    },
    // 3. `data`의 `password` 의 값과 `data`의 `confirm_password` 의 값이 일치하는지 검사
    isconfirmPassword () {
      return this.password === this.confirm_password
    },
    // 4. 이메일과 닉네임의 길이 검사
    isuserInfoLength () {
      return Object.keys(this.user).every((key) => {
        return validLength(this.user[key], 0, 20)
      })
    },
    // 입력데이터에 따른 에러 메세지
    inputErrMsg () {
      if (!this.user.email && !this.user.username) { return }
      return !this.isuserInfoLength ? '입력값은 20자리 이하로 입력해주세요.' : ''
    },
    // 유효성 검사 확인하여 로그인 버튼 활성화 여부
    disabledBtn () {
      return this.isvalidLength && this.isvalidEmail && this.isconfirmPassword && this.isuserInfoLength
    }
  }
}
```


<p>computed</p>

|computed|설명|
|:---|:---|
|isvalidLength|`data`의 `password`의 길이가 8자 이상인지 30자 이하인지 확인합니다.|
|isvalidEmail|`data`의 `email`의 양식이 이메일인지 확인합니다.|
|isconfirmPassword|`data`의 `password`와 `data`의 `confirm_password`가 완전히 일치하는지 확인합니다.|
|isuserInfoLength|`data`의 `user` 객체 데이터의 길이가 20자 이하인지 확인합니다.|
|inputErrMsg|`computed`인 `isuserInfoLength`를 확인하여 입력데이터에 따라 에러 메세지를 보여줄지 확인합니다.|
|disabledBtn|위의 유효성 검사를 모두 통과하는지 확인합니다. <br>`<button>`태그의 `disabled`속성을 바운딩시켜, 유효성 검사르 모두 통과될 때에만 버튼이 활성화됩니다.|

> `computed`를 통해 데이터 유효성 검사


<br>

<b>3. 필수 입력폼을 사용자가 확인할 수 있게 하기 위해, 별도의 표시를 보여주도록 구현하였습니다.</b>
```html
<!-- ~/components/form/Register.vue -->
<template>
  ...
  <div class="formbx">
   <!-- 이메일 -->
        <div :class="{'invalid':!user.email}">
          ....
        </div>
  </div>
  ...
</template>
```

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
<br>

<b>4. 편의성을 위해 해당 페이지에 진입시, 이메일 입력폼에 포커스 될 수 있도록 구현하였습니다.</b>
```html
<template>
   ...
  <div class="formbx">
   <!-- 이메일 -->
        <div :class="{'invalid':!user.email}">
          ....
             <!-- ref를 사용해 태그를 참조합니다. -->
            <input id="email" ref="emailinput" v-model="email" type="text" placeholder="이메일">
        </div>
  </div>
  ...
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
> `mounted ` 라이프사이클 훅 함수를 이용해 해당 컴포넌트에 진입시 이메일 input 입력폼에 포커스되도록 구현하였습니다.

<br>

#### <b>회원가입 API</b>

<br>
<b>store</b>

|actions|
|---|
|register|

```js
//store/user.js actions
   async register ({ commit }, userData) {
    const res = await this.$axios.post('user/register', userData)
    commit('setUser', res.data)
  },
```
> `axios`를 이용해 회원가입 `API`를 호출합니다. <br>
> `API`를 호출해 데이터를 가져온다면 `commit`으로 `mutations`를 호출하여 `state`의 `user` 객체에 사용자의 정보를 저장합니다.

|회원가입시에도 사용자의 정보를 저장한 이유|
|---|
|편의성을 위해, 사용자가 회원가입을 한 후, 로그인을 따로 진행하지 않고, 바로 사용자의 정보를 `state`에 저장하여 로그인을 생략할 수 있도록 구현하였습니다.|

<br>

|mutations|
|---|
|setUser|
```js
//store/user.js mutations
 setUser (state, user) {
    state.user = user
  }
```
> `state`의 `user`객체에 사용자의 정보를 저장합니다.

<br>

|getters|
|---|
|getUser|
```js
//store/user.js getters
  getUser (state) {
    return state.user
```
> `state`의 `user` 객체를 가져옵니다.

<br>

|state|
|---|
|user|

```js
//store/user.js state
 user: {}
```
<br>

 > <div id="state_user">성공적으로 회원가입 시, 아래 사용자 정보를 저장합니다.</div>
```js
// user 객체에 저장되는 사용자의 정보 예시
  user:{
    // 이메일
    email:"q@q.com"
    // 아이디
    id:7
    // 카카오로그인인지,구글로그인인지 구분하는 속성
    // 카카오로 로그인할시, 해당 속성은 null 이 아닌 kakao로 저장된다
    // 구글로 로그인할시, 해당 속성은 null 이 아닌 google로 저장된다
    provider:null
    // 썸네일 이미지로, 없다면 빈 문자열이 출력된다.
    thumbnail:"https://am-clone.s3.ap-northeast-2.amazonaws.com/1623252830675"
    // 사용자이름(닉네임)
    username:"1234"
  }
```
<br>

<b>회원가입 버튼 클릭</b>

***

> `store`의 `actioins` 함수 `register`를 호출합니다.
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
      Object.keys(this.user).forEach((key) => { this.user[key] = '' })
      this.password = ''
      this.confirm_password = ''
    },
    inputfocus () {
      this.$refs.emailinput.focus()
    }

  }
```

<br><br>

### <div><b>1-2. 로그인</b></div>
#### <b>구현 내용</b>

***

<b>1. 로그인 구현은 이메일로 로그인.카카오로 로그인.구글로 로그인 세가지 방법으로 구현하였습니다.</b>

> 서버에서 passport를 사용하여 구현하였는데 해당내용은 아래의 서버 구현 내용에서 정리하였습니다.<br>
 (<a href="https://www.passportjs.org/packages/passport-kakao/">`passport`카카오 로그인 참고 문서 바로 가기</a>)<br>
 (<a href="https://www.passportjs.org/packages/passport-google-oauth20/">`passport`구글 로그인 참고 문서 바로 가기</a>)<br>

<br>

#### <b>로그인 API</b>
***
<b>store</b>

|actions|
|---|
|register|

```js
//store/user.js actions
   async login ({ commit }, userData) {
    const res = await this.$axios.post('user/login', userData)
    commit('setUser', res.data)
  },
```
> `axios`를 이용해 로그인 `API`를 호출합니다.<br>
> `API`를 호출해 데이터를 가져온다면 `commit`으로 `mutations`를 호출하여 `state`의 `user` 객체에 사용자의 정보를 저장합니다.


|mutations|
|---|
|setUser|
```js
//store/user.js mutations
 setUser (state, user) {
    state.user = user
  }
```
> `state`의 `user`객체에 사용자의 정보를 저장합니다.

<br>

|getters|
|---|
|getUser|
```js
//store/user.js getters
  getUser (state) {
    return state.user
```
> `state`의 `user` 객체를 가져옵니다.

<br>

|state|
|---|
|user|

```js
//store/user.js state
 user: {}
```
 > <div id="state_user">성공적으로 로그인 시, 아래 사용자 정보를 저장합니다.</div>
```js
// user 객체에 저장되는 사용자의 정보 예시
  user:{
    // 이메일
    email:"q@q.com"
    // 아이디
    id:7
    // 카카오로그인인지,구글로그인인지 구분하는 속성
    // 카카오로 로그인할시, 해당 속성은 null 이 아닌 kakao로 저장된다
    // 구글로 로그인할시, 해당 속성은 null 이 아닌 google로 저장된다
    provider:null
    // 썸네일 이미지로, 없다면 빈 문자열이 출력된다.
    thumbnail:"https://am-clone.s3.ap-northeast-2.amazonaws.com/1623252830675"
    // 사용자이름(닉네임)
    username:"1234"
  }
```
<br>

<b>로그인 버튼 클릭</b>

***
> `store`의 `actioins` 함수 `register`를 호출합니다.
```js
// <!--components/form/Login.vue -->

  methods: {
    ...mapActions('user', ['login']),
    async UserLogin () {
      try {
        const userinfo = {
          email: this.email,
          password: this.password
        }
        await this.login(userinfo)
        // 성공적으로 로그인되면 메인페이지로 이동
        this.$router.push('/')
      } catch (error) {
        // 에러 발생시 에러메세지 출력될 수 있도록 구현
        console.log(error)
        this.errmsg = error.response.data.msg
      }
    }
  }
```


* 사용자 정보 저장시 문제점</b>

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

### <div id="user_info"><b>2. 사용자 정보 수정</b></div>

|라우터|
|---|
|user/info|

### <div>2-1. 프로필 정보 수정</div>
#### <b>구현 내용</b>
***
<b>1. 닉네임(이름),프로필 이미지를 수정할 수 있도록 구현했습니다.</b>
 - 기존에 있는 정보를 수정하므로, 기존 정보를 보여줍니다.
```html
<!-- ~/page/user/info.vue -->
<template>
  <div class="profile_box">
    <!-- profile -->
    <h2>프로필 정보 수정</h2>
    <form class="form_content" @submit.prevent="onChangeProfile">
      <!-- 이메일 -->
      <div>
        <label for="email">이메일</label>
        <!-- getter를 이용해 사용자의 정보를 가져와서 value 를 바운딩시켜 사용자의 이메일을 보여줍니다. -->
        <p><input id="email" class="readonly" readonly :value="getUser.email" type="text"></p>
      </div>
      <!-- 닉네임 -->
      <div>
          <label for="username">닉네임</label>
          <p><input id="username" v-model="username" type="text"></p>
        </div>
        ....
       <button class="round-btn" type="submit" :disabled="isusernamevalid">
          프로필 정보 수정
        </button>
    </form>
  </div>
</template>
```

```js
   data () {
    return {
      email: '',
      username: '',
      isChangePassword: false,
      password: '',
      confirm_password: '',
      selectedFile: ''
    }
  },
 computed: {
  //  getter를 이용해 store의 state 의 user 정보를 가져옵니다.
    ...mapGetters('user', ['getUser']),
 },
  created () {
  // 기존 사용자의 닉네임을 보여줍니다.
  this.username = this.getUser.username
}
```
<br>

#### <b>이미지 업로드 API</b>
***
이미지를 `amazon s3 버킷`에 저장하여 해당 이미지를 보여줍니다.

> 해당 API는  `amazon s3 버킷`에 이미지를 저장 후, 주소를 불러와서 이미지를 보여주므로, `프로필 정보 수정` 버튼을 누르지 않는 이상 사용자의 이미지를 수정하지 않습니다.
<br>

<b>store</b>

|<div id="uploadImg">actions</div>|
|---|
|uploadImg|

```js
//store/book.js actions
async uploadImg ({ commit }, payload) {
    try {
      // 로딩 시작
      commit('changeLoading', true, { root: true })
      let res
      if (payload && payload.user) {
        // 사용자 썸네일 이미지 업로드 API
        res = await this.$axios.post('user/thumbnail', payload)
      } else {
        // 책 썸네일 이미지 업로드 API
        res = await this.$axios.post('books/thumbnail', payload)
      }
      commit('setThumbnail', res.data)
    } catch (error) {
      console.error(error)
    } finally {
      // 로딩 종료
      commit('changeLoading', false, { root: true })
    }
  }
```
> `axios`를 이용해 이미지 업로드 API 를 호출합니다. <br>
>  사용자의 프로필(썸네일) 이미지 뿐만 아니라, 책의 썸네일 이미지를 업로드할때 사용하기 때문에, `payload.user`가 `true`인 경우에는 사용자 정보 썸네일 이미지를, 그렇지 않다면 책의 썸네일 이미지를 업로드하는 API 함수를 호출 할수 있도록 구현하였습니다.

`commit`를 이용해 `mutatonis`을 호출합니다.


|mutations|
|---|
|setThumbnail|
```js
//store/book.js  mutations
 setThumbnail (state, image) {
    state.imagePath = image
  },
```
> `state`의 `imagePath`에 이미지 정보를 저장합니다.(문자열로 저장)

<br>


|getters|
|---|
|getUser|
```js
//store/book.js  getters
  getImagePath (state) {
    return state.imagePath
  }
```
> `state`의 `imagePath`을 가져옵니다.

|state|
|---|
|imagePath|
```js
//store/book.js state
 imagePath: ''
```
 > 성공적으로 이미지 업로드 시, 아래 이밎 정보를 저장합니다.
```js
// imagePath에 저장되는 사용자의 정보 예시
// 아마존 버킷(s3)에 저장된 이미지의 주소를 저장합니다.
 imagePath:"https://am-clone.s3.ap-northeast-2.amazonaws.com/1623598375400"
```
<br><br>


<b>프로필 이미지 수정 버튼 클릭</b>
***
> `store`의 `actioins` 함수 `uploadImg`를 호출합니다.
<br>
```html
<!-- ~/page/user/info.vue -->
<template>
  ...
       <!-- 포르필 이미지 수정 -->
       <div class="file_container add">
          <!-- 로딩바 -->
          <LoadingBar v-if="$store.state.initLoading" position />
          <div class="txt">
            <label for="fileinput"><span class="round-btn yellow"><i class="far fa-file-image"></i>프로필 이미지
              수정</span></label>
              <!-- input type=file로 설정하여 파일창으로 파일을 선택할 시, onChangeImage 함수가 호출됩니다.-->
            <input id="fileinput" ref="file" style="display:none" type="file" @change="onChangeImage">
          </div>
          <!-- 이미지 사진 보여주기 -->
          <div class="photos">
            <div class="images">
              <img v-if="hasThumbnail" :src="getUser.thumbnail" alt="thumbnail">
              <img v-if="hasImage" :src="`${getImagePath}`" alt="thumbnail">
            </div>
          </div>
        </div>
    ....
  </div>
 <button class="round-btn" type="submit" :disabled="errmsg.length !== 0 || isusernamevalid">
          프로필 정보 수정
  </button>
  ...
</template>
```

```js
// ~/page/user/info.vue
 data() {
     return {
       selectedFile: '',
       errmsg: ''
     }
   },
   methods: {
     ...mapActions('books', ['uploadImg']),
      // 이미지 업로드
    // 이미지 업로드
    onChangeImage (e) {
      const imageFormData = new FormData()
      let selectedFile = e.target.files[0]
      const maxSize = 1024 * 1024
      const imageType = /^image/.test(selectedFile && selectedFile.type)
      // 이미지 타입인지 확인
      if (!imageType) {
        selectedFile = ''
        this.errmsg = '이미지 타입만 업로드해주세요.'
        return
      }
      // 이미지 사이즈 확인
      if (selectedFile.size > maxSize) {
        selectedFile = ''
        this.errmsg = '용량을 초과하였습니다. 1mb 이하로 업로드해주세요.'
        return
      }
      // 이미지 업로드 API 호출
      imageFormData.append('photo', selectedFile)
      this.uploadImg(imageFormData, { user: true })
        // eslint-disable-next-line no-return-assign
        .then(() => this.errmsg = '')
    },
```

<b>data</b>

|data|설명|
|:---|:---|
|selectedFile|`<input type="file">`로 선택한 파일의 정보를 저장합니다.|
|errmsg|이미지 유효성을 확인해 에러 메세지를 보여줍니다.|
<br>

<b>methods</b>

|methods|설명|
|:---|:---|
|onChangeImage|FormData 형식을 이용해 이미지 정보를 저장합니다.(여기서는 이미지는 1개만 선택할수 있도록 하였습니다.) <br>`{user:true}`속성으로, 책의 썸네일 이미지가 아닌 사용자의 프로필(썸네일) 이미지를 업로드하는 API를 호출합니다. (<a href="#uploadImg">`actions` 함수 `uploadImg` 바로가기</a>)|
<br><br>



<b>* 이미지를 업로드 할 때 문제점</b>

 |문제점|
 |---|
 |`onChangeImage`함수로 이미지를 업로드하는 API 함수를 호출하여 이미지의 주소를 가져와 `state`의 `imagePath`에 저장하도록 구현하였습니다. 다른 라우터로 이동시에도 `imagePath`에 값이 그대로 저장되어 있기 때문에, 프로필 정보 수정하기 위해 해당 라우터로 진입시,`imagePath`에 저장된 이미지가 그대로 보여지게 됩니다.  |

|해결|
 |---|
 |`created`훅을 이용해 인스턴스가 생성된 후 `state`의 `imagePath`값을 초기화시켜줍니다.|

 ```js
//  ~components/user/info.vue
   created () {
    //  이미지 데이터 초기화
    if (this.getImagePath.length !== 0) {
      return this.resetImgagePath()
    }
  },
   methods: {
    ...mapMutations('books', ['resetImgagePath'])
   }
 ```
 ```js
//  ~/store/book.js mutations
   removeThumbnail (state) {
    state.imagePath = []
  }
 ```
<br>

 <b>프로필 이미지 보여주기</b>
 ***
 ```html
<!-- ~/page/user/info.vue -->
<template>
  ...
       <!-- 포르필 이미지 수정 -->
       <div class="file_container add">
          <!-- 로딩바 -->
          <LoadingBar v-if="$store.state.initLoading" position />
          ....
          <!-- 이미지 사진 보여주기 -->
          <div class="photos">
            <div class="images">
              <img v-if="hasThumbnail" :src="getUser.thumbnail" alt="thumbnail">
              <img v-if="hasImage" :src="`${getImagePath}`" alt="thumbnail">
            </div>
          </div>
        </div>
    ....
  </div>
  ...
</template>
```

```js
  computed: {
    hasImage () {
      return this.getImagePath.length > 0
    },
    hasThumbnail () {
      return !this.hasImage && this.getUser.thumbnail
    }
  }
```

<b>computed</b>

|computed|설명|
|:---|:---|
|hasImage|이미지 업로드 API를 호출하여 저장한 `state`의 `imagePath` 데이터가 있는지 확인합니다.|
|hasThumbnail|`state`의 `user` 객체에 사용자의 썸네일 이미지 데이터가 있는지 확인합니다.|

> `computed`를 통해 사용자의 썸네일 이미지가 있다면, 해당 이미지를 보여주고, 이미지 업로드 API를 호출하여 이미지를 업로드 했다면, 해당 이미지를 보여줍니다.

 <br><br>

#### <b>프로필 정보 수정 API</b>
***
<br>
<b>store</b>

|<div id="#">actions</div>|
|---|
|updateProfile|

```js
//store/user.js actions
  async updateProfile ({ commit }, userData) {
    try {
      const res = await this.$axios.put('user', userData)
      commit('setUser', res.data.user)
    } catch (error) {
      console.error(error)
    }
  },
```
> `axios`를 이용해 프로필 정보 수정 API를 호출합니다. <br>

`commit`를 이용해 `mutatonis`을 호출합니다.


|mutations|
|---|
|setUser|
```js
//store/user.js  mutations
 setUser (state, user) {
    state.user = user
  }
```
> `state`의 `user`객채에 사용자 정보를 저장합니다.

<br>

|state|
|---|
| user|
```js
//store/user.js state
  user: {}
```

<br>

<b>프로필 정보 수정버튼 클릭</b>

> `store`의 `actioins` 함수 `updateProfile`를 호출합니다.
```js
  methods: {
    // 프로파일 정보 수정(닉네임,프로파일 이미지 수정)
    onChangeProfile () {
      const userinfo = { username: this.username, thumbnail: this.getImagePath }
      this.updateProfile(userinfo)
      // 프로파일 정보 수정 후 라우터 이동
      this.$router.push('/user/profile')
    }
```
> 이미지 업로드 API를 호출하여 저장한 이미지와
사용자 닉네임 데이터를 보내 프로필 정보를 수정합니다.

<br>

### <div>2-2. 비밀번호 수정</div>
#### <b>구현 내용</b>
***
<b>1. 비밀번호 수정 내용 보여주기</b>

카카오 로그인과 구글 로그인시에는 비밀번호 수정이 필요 없기 때문에 로그인 시 `state`의 `user` 객체에서 `provier` 속성을 이용해 값이 존재하지 않을 경우에만 비밀번호 변경할 수 있도록 구현하였습니다.
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
 > 유효성 검사는 위의 <a href="register">`회원가입/로그인 구현`</a> 시 구현했던 방법과 같습니다.

 <br><br>

### <div id="book_search" >3. 원하는 책 검색 및 추가</div>

### 3-1. 원하는 책 검색

|컴포넌트|라우터|
|---|---|
|components/form/Search.vue|books/search|
|components/book/CardDetail.vue|books/search|

#### <b>구현 내용</b>
***
<b>1. 카카오 책검색 API를 이용하여 검색한 데이터를 가져옵니다.
([카카오 개발자 센터](https://developers.kakao.com/docs/latest/ko/daum-search/dev-guide) 참고)</b>

> 해당 API는 `title(제목)`, `isbn(ISBN)`, `publisher(출판사)`, `person(인명)`로 검색 필드를 제공해주기 때문에 옵션에 따라, 검색 필드를 제한할 수 있도록 구현했습니다.
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
<br>

> 검색 폼(`form-search 컴포넌트`) 내용 바로가기

<br>

<b>2. `select` 태그는 브라우저마다 다르게 보이기 때문에 통일되게 보일 수 있도록 `select` 태그를 사용하지않고, `ul`태그를 이용해 커스텀하여 구현했습니다.</b>

```html
<!-- ~/components/form/search.vue -->
<template>
  <form class="search_form" @submit.prevent="$emit('searchBook')">
    <!-- 해당 태그에 마우스를 올리면 selected 를 true로 하여 옵션이 보이고, 마우스를 올리지 않으면 selected 를 false로 하여 옵션을 보이지 않게 합니다. -->
    <div class="main_select" @mouseenter="selected=!selected" @mouseleave="selected=false">
      <div>
        <!-- props로 받은  options: ['통합', '제목', '저자', '출판사', 'isbn'] 중 선택한 한개의 요소를 보여줍니다. -->
        <p>{{ selectedOption }}<i class="fas fa-chevron-down"></i></p>
      </div>
      <!-- options 메뉴 -->
      <ul v-if="selected" class="custom_select">
        <li v-for="(option,index) in options" :key="index" @click="changeSelect(option)">
          {{ option }}
        </li>
      </ul>
    </div>
    <!-- input 입력폼의 값이 변할 때마다 이벤트를 상위 컴포넌트에 보내주어 value값을 변경해줍니다. -->
    <input ref="searchInput" :value="value" type="text" placeholder="검색"
    @input="$emit('input',$event.target.value)">
    <button type="submit">
      <i class="fas fa-search"></i>
    </button>
  </form>
</template>
```
<br>

#### <b>카카오 책 검색 API</b>
***
옵션에 따라 카카오 책 검색 API 호출합니다.

<b>store</b>
|<div>actions</div>|
|---|
|SearchBooks|

```js
//store/book.js actions
   async SearchBooks ({ commit }, bookData) {
     const { size, query, reset, page } = bookData
    try {
      if (rootState.inintLoading) { return }
      // 맨 처음에 데이터를 불러올 때 로딩 스피너를 보여줍니다.
      if (reset) { commit('changeLoading', true, { root: true }) }
      const api = `/books/search/kakao?query=${query}&size=${size}&page=${page}`
      let res
      // target의 내용에 따라 검색하는 API를 호출합니다.
      // target이 "제목"이라면, 제목에서 검색한 API를 호출합니다.
      // target이 "출판사"이라면, 출판사에서 검색한 API를 호출합니다.
      if (bookData.target) {
        res = await this.$axios.get(`${api}&target=${bookData.target}`)
      } else {
        // 통합검색(target과 상관없이 "제목","출판사","저자" 등 과 관련없이 모든 요소에서 검색한 API를 호출합니다.)
        res = await this.$axios.get(api)
      }
      commit('addBook', { data: res.data, reset })
    } catch (error) {
      console.error(error)
    } finally {
     // 로딩 스피너 종료
      if (reset) {
        setTimeout(() => commit('changeLoading', false, { root: true }), 400)
      }
  }
```
> `axios`를 이용해 책 검색 API를 호출합니다. <br>
> <b>통합검색/옵션에 따른 검색</b> 두가지 방법으로 구현하기 위해, 호출하는 API를 구분하였습니다.


`commit`를 이용해 `mutatonis`을 호출합니다.

<br>

|mutations|
|---|
|addBook|
```js
//store/book.js  mutations
 addBook (state, bookData) {
    const { data, reset } = bookData
    // 초기화
    if (reset) {
      state.books = []
      state.meta = {}
    }
    data.documents.forEach((book) => {
      state.books.push(book)
    })
    state.meta = data.meta
  }
```
> 카카오 검색 API 호출시, `documents`와 `meta` 로 값을 받아옵니다. <br>
> 책에 관련된 정보는 `state` 의 `books` 배열에 저장하고, 검색된 문서 수,마지막페이지인지 여부의 정보는 `state`의 `meta`에 저장하였습니다. <br>


<br>

<b>* 책 검색 시, 불러온 책 데이터를 보여줄 때 문제점</b>

|문제점|
|---|
|검색되는 정보를 `state`의 `books`배열에 저장하다보니, 옵션에 따라 검색정보를 다르게 검색했을 때, 기존 배열에 누적되는 문제가 발생했습니다.
예시)옵션을 "통합"으로 설정하여 검색한 후,"제목"이나 "출판사" 등과 같은 다른 옵션으로 다시 재검색시, 기존에 존재하는 책의 정보가 없어지지않고 누적이 됩니다.|

|해결|
|---|
|`{reset:true}` 별도의 객체 속성을 주어, 해당 속성이 `true`일 경우에만 `books` 배열을 초기화하고, 그러지 않다면 기존 배열에 계속 누적시킵니다.
예시)옵션을 "통합"으로 설정하여 검색한 후,"제목"이나 "출판사" 등과 같은 다른 옵션으로 다시 재검색시, 기존에 존재하는 책의 정보를 초기화시켜줍니다.|

<br>

|getters|
|---|
| getBooks |
```js
//store/book.js  getters
  getBooks (state) {
    return state.books
  }
```
- `state`의 ` books`배열을 가져옵니다.

|state|
|---|
|books|
```js
//store/book.js state
 books: [],
 meta: {},
```
 - <div >성공적으로 API 호출 시, 아래 데이터 정보를 저장합니다.</div>
  <a href="https://developers.kakao.com/docs/latest/ko/daum-search/dev-guide">카카오 개발자 센터 책 검색 참고</a>
```js
    books: [{
     authors: Array[1]
     contents: "단짝 친구와 갈등을 겪는 아이의 심리를 섬세하게 ... ",
     datetime: "2018-06-01T00:00:00.000+09:00"
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
> API 호출시 한번에 `20`개씩 데이터를 불러옵니다.

<br><br>

<b>책 검색 버튼 클릭</b>
***

> `store`의 `actioins` 함수 `SearchBooks`를 호출합니다.
```js
// ~/pages/books/search/index.vue
methods:{
      data(){
          reset: false,
          ...
      },
      // 입력폼에 원하는 입력값을 작성하여 엔터를 클릭할시 onSearchBook함수를 호출합니다.
      onSearchBook () {
      // 기존 데이터 초기화
      this.resetBook(true)
      this.currentpage = 1
      this.currentCount = 0
      // 검색한 책 데이터 불러오기
      this.onFetchbook()
    },
      onFetchbook () {
        // 입력폼에 아무것도 작성하지 않고 엔터를 누른다면 사용자에게 에러메세지로 알려주고,return 해줍니다.
      if (this.search.length <= 0) {
        this.errmsg = true
        return
      }
      let bookinfo = { size: this.size, query: encodeURIComponent(this.search), page: this.currentpage, reset: this.reset }
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
      this.SearchBooks(bookinfo)
        .then(() => {
          this.errmsg = false
          // 데이터를 호출할때마다 현재페이지를 증가시킵니다.
          this.currentCount += this.size
          // 마지막 페이지인지 여부인지 확인하는 데이터를 저장시킵니다.
          this.isend = this.meta.is_end
          this.showbutton()
        })
    },
    // 기존 데이터 초기화
     resetBook (boolean) {
      this.reset = boolean
    },
      showbutton () {
        // 마지막페이지라면 더보기 버튼이 보여지지 않도록 합니다.
      this.isend ? this.showbtn = false : this.showbtn = true
    },
}

```
<br>

<b>책 검색 후, 더보기 버튼 클릭</b>
***

> API 호출시 한번에 `20`개씩 데이터를 불러오고, `더보기 버튼`을 누를 시, 다음 데이터 `20`개를 가져오도록 구현하였습니다.
```html
<!-- ~/pages/books/search/index.vue -->
<template>
  ...
   <!-- 더보기 버튼 -->
      <div v-if="showbtn" ref="btn" class="more">
        <button class="round-btn fill more-btn" @click=" addFetchBook">
           <!-- 현재페이지수/총페이지수(중복제거한 총 페이지 수) -->
          {{ currentCount }} / {{ meta.pageable_count }}
        </button>
      </div>
  ...
</template>
```
```js
//  ~/pages/books/search/index.vue
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

<b>methods</b>

|methods|설명|
|:---|:---|
|addFetchBook|더 불러올 데이터가 있다면, 현재페이지를 증가시켜주고, 책 검색 API를 호출합니다.( 초기화할 필요 없으므로 `resetBook`에 `false`를 전달해줍니다.)|
<br><br>


<b>* 책 검색 시, 불러온 책 데이터를 보여줄 때 문제점</b>
 |문제점|
 |---|
 |라우터 이동시 데이터를 초기화시켜주지 않으면, 다시 검색하기 위해 해당 라우터에 이동했을 때, 전의 데이터가 그대로 남아 보여지게 됩니다.  |

|해결|
 |---|
 |`created`훅을 이용해 인스턴스가 생성된 후 `state`의 `books`값을 초기화시켜줍니다.|

 ```js
  created () {
    // 책 데이터 초기화
    if (this.books.length !== 0) {
      return this.initsearchBook()
    }
  }
 ```
 ```js
//  ~/store/book.js mutations
// 책의 정보와 meta에 저장된 정보를 초기화시켜줍니다.
  initsearchBook (state) {
    state.searchbooks = []
    state.meta = {}
  },
 ```
 <br>

### 3-2. 원하는 책 검색 후 추가

검색한 책 중 원하는 책을 골라 책을 추가할 수 있도록 구현하였습니다.

<b>검색한 책 추가 API 호출</b>
***
<b>store</b>
|<div>actions</div>|
|---|
|createBook|

```js
//store/book.js actions
    async createBook (_,bookData) {
    const res = await this.$axios.post('/books/add', bookData)
    return res
  }
```
> `axios`를 이용해 책 추가 API를 호출합니다.

><b>책을 추가한 후, 추가한 책은 다른 라우터 `/books/_page`에 보여주도록 구현하였고, 해당 라우터에 진입시 `nuxt`의`asyncData` 훅을 사용해, 데이터를 불러오도록 구현하였으므로, 여기서는 별도의 `mutations`를 호출하여 `state` 값을 변화시키지 않았습니다. </b>

<br><br>

<b>추가하기 버튼 클릭</b>
***
>`store`의 `actioins` 함수 `createBook`를 호출합니다.

`추가하기 버튼`을 클릭한 후, 사용자에게  알람메세지를 띄워 알려줍니다.(알림창 내용 바로가기)
```html
<!-- ~/pages/books/search/index.vue -->
<template>
  ...
  <div v-for="(book,index) in books" :key="index" class="search_book">
        <!-- book-card-detail -->
        <BookCardDetail :book="book" />
        <!-- 추가하기 버튼 -->
        <button class="round-btn addbtn red" @click="onaddBook(book)">
          추가하기
        </button>
      </div>
  ...
</template>
```
```js
// ~/pages/books/search/index.vue
import bus from '~/utils/bus.js'
export default {
  methods:{
          async onaddBook (book) {
        try {
          const bookData = { title: book.title, contents: book.contents, datetime: book.datetime, isbn: book.isbn, publisher: book.publisher, thumbnail: book.thumbnail, url: book.url, authors: this.bookauthors(book.authors) }
          await this.createBook(bookData)
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
      ...
  }
}
```
<br><br>

### <div id="book_create"><b>4. 직접 책 추가 및 수정 및 삭제</b></div>

|컴포넌트|라우터|
|---|---|
|components/form/BookAdd.vue|books/add|
|components/book/Edit.vue|books/b/_id|
<br>

#### <div><b>4-1. 직접 책 추가 및 수정 공통 내용</b></div>
|컴포넌트|라우터|
|---|---|
|components/form/BookAdd.vue|books/add|

1. 믹스인으로 공통 요소 구현
```js
import BookFetchMixin from '~/mixins/BookFetchMixin'
export default {
  mixins: [BookFetchMixin]
  ...
}
```
<br>

- `BookFetchMixin`
```js
// ~/mixins/BookFetchMixin.js
  //  마운트될 때, 타이틀입력폼 태그에 포커스되도록 구현
 mounted () {
    setTimeout(() => {
      this.$refs.titleInput.focus()
    }, 400)
  },
  computed: {
  ...mapGetters('books', ['getImagePath']),
  // 유효성 검사
  InputLenValid () {
    const data = ['title', 'isbn', 'authors', 'publisher']
    return inputLen(this.newBook, data, 50)
  },
  disabledBtn () {
    return !this.newBook.title || !this.newBook.authors || !this.newBook.contents || !this.InputLenValid
  }
},
  methods: {
    // 데이터 불러오기
     async fetchData () {
      try {
        const data = new FormData()
        const imageFile = this.selectedFile || this.mybook.thumbnail
        data.append('title', this.newBook.title)
        data.append('contents', this.newBook.contents)
        data.append('datetime', new Date(this.newBook.datetime))
        data.append('isbn', this.newBook.isbn)
        data.append('publisher', this.newBook.publisher)
        data.append('photo', imageFile)
        data.append('url', this.newBook.url)
        data.append('authors', this.newBook.authors)
        if (!this.$route.params.id) {
          // 책을 추가할 때
          await this.createBook(data)
            .then((res) => {
              this.$router.push('/books/1')
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
              bus.$emit('on:alert', res.data.msg)
              setTimeout(() => {
                bus.$emit('off:alert')
              }, 3000)
            })
        }
      } catch (error) {
        console.log(error)
        bus.$emit('on:alert', error.response.data.msg)
        setTimeout(() => {
          bus.$emit('off:alert')
        }, 3000)
      }
    },
   // 이미지 미리보기 업로드
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

- `computed`

|computed|설명|
|:---|:---|
|InputLenValid|입력 값 길이 확인합니다.|
|disabledBtn|유효성 검사를 모두 통과하는지 확인합니다.<br>`<button>`태그의 `disabled`속성을 바운딩시켜, 유효성 검사르 모두 통과될 때에만 버튼을 클릭할 수 있도록 구현했습니다.|

<br>

- `methods`

|methods|설명|
|:---|:---|
|fetchData|책 데이터 추가/수정시 `state`의 `actions`함수를 호출하여 책 추가/수정 API 호출합니다.|
|onChangeImage|`state`의 `actions`함수를 호출하여 책 이미지(썸네일) 추가 API 호출|
|resetInput|입력데이터 초기화합니다.|

<br>
<br><br>
<br>

2. 유효성 검사

|필수 입력 값|입력 값 길이 제한|
|---|---|
|`책 제목(title)`|`책 제목(title)`|
|`책 내용(contents)`|`isbn`|
|`책 저자(authors)`|`책 저자(authors)`|
|_|`책 출판사(publisher)`|

```js
data () {
    return {
      // 책 데이터
       newBook: {
        title: '',
        isbn: '',
        authors: '',
        publisher: '',
        contents: '',
        url: '',
        datetime: ''
      },
      // 추가할 이미지
      selectedFile: ''
  }
```
<br>

-  `computed`를 통해 유효성 검사를 통과하지 못 했을 경우, 에러 메세지를 보여줍니다.

```js
// ~/mixins/BookFetchMixin
import { inputLen } from '~/utils/validate'
  computed: {
    InputLenValid () {
      const data = ['title', 'isbn', 'authors', 'publisher']
      return inputLen(this.newBook, data, 50)
    },
    disabledBtn () {
      return !this.newBook.title || !this.newBook.authors || !this.newBook.contents || !this.InputLenValid
    }
  },
```
```js
// ~/utils/validate.js
// 유효섬 검사
// 입력값 데이터(객체) 전부의 길이를 확인하는 함수
const inputLen = (value, data, len) => {
  return data.every(key => value[key].length < len)
}

export { validLength, validEmail, inputLen }

```
- `computed`

|computed|설명|
|:---|:---|
|InputLenValid|`title`,`isbn`,`authors`,`publisher`의 길이가 50자 이하인지 확인합니다.|
|disabledBtn|유효성 검사를 모두 통과하는지 확인합니다.<br>`<button>`태그의 `disabled`속성을 바운딩시켜, 유효성 검사르 모두 통과될 때에만 버튼을 클릭할 수 있도록 구현했습니다.|


<br>

3. 입력폼에 입력된 값이 하나라도 있다면 `엑스 버튼`이 생기고, 해당 버튼을 클릭하면 해당  입력폼을 초기화시켜줍니다.
```html
<!-- ~/components/form/BookAdd.vue -->
...
  <form class="form_content addform" @submit.prevent="onaddBook">
    <p :class="{'invalid':!newBook.title}" >
      <input ref="titleInput" v-model="newBook.title"type="text">
      <!-- 엑스버튼 -->
      <i v-if="newBook.title" class="fas fa-plus-circle" @click="resetInput($event,'title')"></i>
    </p>
  </form>
...
```
```js
// ~/mixins/BookFetchMixin
 resetInput (e, data) {
  //  target은 input태그가 된다
      const target = e.target.parentNode.firstChild
      // 입력값 초기화
      this.newBook[data] = ''
      // 입력값을 초기화 한후 target 태그 포커스
      target.focus()
    }
```
<br>

- `methods`

|methods|설명|
|:---|:---|
|resetInput|`data`의 `newBook`에서 해당 되는 속성을 찾아 초기화합니다.<br> `v-model`로 `newBook`의 값을 양방향 바운딩시켜주었기 때문에 해당 값을 빈값으로 초기화 할시, 입력폼에 있는 입력값도 초기화됩니다.<br>`엑스 버튼`으로 입력값을 초기화 시켜주고, 다시 해당 입력폼에 `focus`되도록 구현하였습니다.(`target`인 `input`태그에 `focus`)|

<br>

>  참고로 IE(인터넷 익스플로러) 10 이상에서 Input text Box에 포커스 되면 우측에 x 표시가 생기는데, 이는 직접 구현하였으므로, IE(인터넷 익스플로러) 우측에 x 표시가 생기지 않도록 초기화시켜주었습니다.</div>

```css
/* static/css/style.css */
/* ms 인풋요소 엑스박스제거 */
::-ms-clear{display: none;}
```

<br><br>

4. 책의 썸네일 이미지를 추가 및 수정할 수 있도록 구현하였습니다.

4-1. 책의 썸네일 이미지 추가
- 책의 썸네일 이미지를 선택하면, 이미지를 미리 보여줍니다.
(<b>`추가하기 버튼`을 클릭해야 책의 썸네일 이미지가 저장됩니다.</b>)
```html
<!-- ~/components/form/BookAdd.vue -->
<template>
  ...
  <!-- 이미지 추가 -->
  <div class="file_container add">
    <div class="txt">
      <label for="fileinput"><span class="round-btn yellow"><i class="far fa-file-image"></i>책 이미지 추가</span></label>
      <input id="fileinput" style="display:none" type="file" @change="onChangeImage">
      <!-- 책의 썸네일 이미지를 미리 보여줍니다. -->
    </div>
          <div v-if="getImagePath.length !== 0" class="photos">
        <div class="images">
          <img :src="getImagePath" alt="썸네일 이미지">
        </div>
        <div>
         ...
        </div>
      </div>
   ....
  </div>
  ...
</template>
```
- 이미지 추가 관련 내용은 위의 사용자 프로필(썸네일) 업로드 내용과 유사합니다.
(<a href="#image_add">이미지 업로드 관련 내용 바로가기</a>)

<br>

```js
// ~/mixins/BookFetchMixin
 export default {
  methods: {
     ...mapActions('books', ['uploadImg']),
       // 이미지 미리보기 업로드
    onChangeImage (e) {
      this.selectedFile = e.target.files[0]
      const imageFormData = new FormData()
      imageFormData.append('photo', this.selectedFile)
      this.uploadImg(imageFormData)
    }
  }
 }
```

- `methods`

|methods|설명|
|:---|:---|
|onChangeImage|FormData 형식을 이용해 이미지 정보를 저장합니다.(여기서는 이미지는 1개만 선택할수 있도록 하였습니다.)|

<br>

4-2. 책의 썸네일 이미지 수정(삭제)
- 책의 썸네일 이미지를 미리 보여줄 때, `엑스 버튼`이 보이고, 해당 버튼을 누르면 미리 보여주고 있는 책의 썸네일 이미지를 없애줍니다.
```js
export default {
  methods: {
    ...mapMutations('books', ['removeThumbnail']),
}
```
<br>


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

5. 날짜는 <a href="https://bootstrap-vue.org/docs/components/form-datepicker/">buutstrap-vue</a>에서 `datepicker`를 사용하였습니다.

|해당 라이브러리를 사용한 이유|
|---|
|`<input type="date">`는 브라우저마다 다르게 보여지기 때문에 브라우저에 상관없이 동일하게 보여주기 위해 사용하였습니다.|
<br><br>


#### <div><b>4-2. 직접 책 추가</b></div>

1. 책 추가 API 호출

1-1. store
|<div>actions</div>|
|---|
|createBook|

```js
//store/book.js actions
  async createBook (_, bookData) {
    const res = await this.$axios.post('/books/add', bookData)
    return res
  }
```
- `axios`를 이용해 책 추가 API를 호출합니다.
- <b>책을 추가한 후, 추가한 책은 다른 라우터 `/books/_page`에 보여주도록 구현하였고, 성공적으로 책 추가시, 라우터를 변경하도록 구현하였습니다.<br>
` this.$router.push('/books/1')`
 - 해당 라우터에 진입시 `nuxt`의`asyncData` 훅을 사용해, 데이터를 불러오도록 구현하였으므로, 여기서는 별도의 `mutations`를 호출하여 `state` 값을 변화시키지 않았습니다. </b>

<br>
1-2. 추가하기 버튼 클릭

- 책 추가 버튼 클릭 시, 공통으로 구현한 `BookFetchMixin` 의 `fetchData` 함수를 호춣하여 책 데이터 추가 API를 호출합니다.

```html
<!-- ~/components/form/BookAdd.vue -->
<template>
  <form class="form_content addform" @submit.prevent="onaddBook">
    ...
    <button type="submit" class="round-btn red addbtn" :disabled="disabledBtn">
      추가하기
    </button>
    ...
  </form>
</template>
```

```js
// ~/components/form/BookAdd.vue
  methods: {
    ...
     onaddBook () {
       this.fetchData()
    }
  }
```

#### <div><b>4-3. 추가한 책 수정</b></div>
|컴포넌트|라우터|
|---|---|
|components/book/Edit.vue|books/b/_id|
<br>


1. 책 수정 API 호출

1-1. store
|<div>actions</div>|
|---|
|updateBook|

```js
//store/book.js actions
    async updateBook ({ commit }, bookData) {
    const res = await this.$axios.put(`/books/${bookData.id}`, bookData.data)
    commit('loadbook', res.data.book)
    return res
  }
```
- `axios`를 이용해  책 수정 API를 호출합니다.
- `commit`를 이용해 `mutatonis`을 호출합니다.


|mutations|
|---|
|loadbook|
```js
//store/book.js  mutations
  loadbook (state, bookData) {
    state.book = bookData
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

1-2. 수정하기 버튼 클릭

- 책 수정 버튼 클릭 시, 공통으로 구현한 `BookFetchMixin` 의 `fetchData` 함수를 호춣하여 책 데이터 수정 API를 호출합니다.

```html
<!-- ~/components/book/Edit.vue -->
<template>
<CommonModal class="book_form">
    ...
    <div slot="body">
      <form class="form_content" @submit.prevent="onEditBook">
        ...
        <button type="submit" class="round-btn red editbtn" :disabled="disabledBtn">
          수정하기
        </button>
        ...
      </form>
    </div>
  </CommonModal>
</template>
```

```js
// ~/components/book/Edit.vue
  methods: {
    ...
     onEditBook () {
       this.fetchData()
    },
  }
```

<br>

#### <div><b>4-4. 추가한 책 삭제</b></div>
|라우터|
|---|
|books/b/_id|


1. 책 삭제 API 호출

1-1. store
|<div>actions</div>|
|---|
|deleteBook|

```js
//store/book.js actions
  async deleteBook (_, { id }) {
    const res = await this.$axios.delete(`/books/${id}`)
    return res
  },
```
- `axios`를 이용해 책 삭제 API를 호출합니다.
- <b>책을 삭제한 후, 성공적으로 책 삭제시, 라우터를 변경하도록 구현하였습니다.<br>
` this.$router.push('/books/1')`
- 해당 라우터에 진입시 `nuxt`의`asyncData` 훅을 사용해, 데이터를 불러오도록 구현하였으므로, 여기서는 별도의 `mutations`를 호출하여 `state` 값을 변화시키지 않았습니다. </b>

<br>

1-2. 삭제 버튼 클릭
- 삭제 확인 알림창에서 "네" 클릭 시, `store`의 `actioins` 함수 `deleteBook`를 호출합니다.
(<a href="#alert">삭제 알림창 내용 바로가기</a>)

```html
<!-- ~/pages/books/b/_id.vue -->
<template>
  <div class="book-details">
    <div>
      ...
      <div class="control_btns">
        <div class="left_btn">
          <!-- 삭제 버튼 -->
          <button class="primary-btn red" @click="onremoveBook">
            <i class="fas fa-trash-alt"></i>삭제
          </button>
          <!-- 수정 버튼 -->
          <button class="primary-btn" @click="onEditBook">
            <i class="fas fa-pen-square"></i>수정
          </button>
        </div>
        ...
      </div>
      <!-- 삭제 확인 알림창 -->
      ...
      <FormAlert v-if="alert" :data="getBook && getBook.title" :confirm="`삭제`" @onagree=" agree"
        @ondisagree="disagree" />
    </div>
  </div>
</template>
```
```js
// ~/pages/books/b/_id.vue
 data () {
    return {
      alert: false
    }
  },
  methods:{
    ...mapActions('books', ['deleteBook']),
    // 삭제 버튼 클릭시 삭제 알림창 보여줍니다.
    onremoveBook () {
      this.alert = true
    },
    agree () {
      try {
        this.deleteBook({ id: this.$route.params.id })
          .then(() => {
            // 삭제 후 라우터 이동
            this.$router.push('/books/1')
          })
      } catch (error) {
        console.error(error)
      }
    }
  }
```
<br>

### <div id="get_data"><b>5. 책 보여주기</b></div>

#### <div>5-1. 공통 내용</div>

|라우터|구현 내용|
|---|---|
|books/_page|<a href="#books">내가 추가한 책 보여주기</a>|
|books/others/_page|<a href="#other_books">다른 사용자가 추가한 책 보여주기</a>|
|books/hashtags/_page|<a href="#tags">태그별로  책 보여주기</a>|
|books/search/_page|<a href="#other_search">다른 사용자의 책 검색하여 보여주기</a>|

1. 페이지네이션 구현
- `bootstrap-vue`에서 제공하는 `BPagination` 을 사용하여 페이지네이션 구현(<a href="https://bootstrap-vue.org/docs/components/pagination#pagination">bootstrap-vue 페이지네이션 바로가기</a>)
```html
<template>
  ...
   <BookPagination :total-page="totalPag" @pagination="pagination" />
</template>
```
<br>

 - `Pagination` 커포넌트
```html
<!-- ~/components/book/Pagination.vue -->
<template>
  <div v-if="showPage" class="pagination_inner">
    <BPagination
      v-model="currentPage"
      :total-rows="rows"
      :per-page="perPage"
      first-number
      @change="pageClick"
    />
  </div>
</template>
```
```js
import { BPagination } from 'bootstrap-vue'
import { mapGetters } from 'vuex'
export default {
  components: { BPagination },
  // 전체 페이지
  props: {
    totalPage: {
      type: Number,
      required: true
    }
  },
  data () {
    return {
      perPage: 1,
      // 현재 페이지
      currentPage: 1
    }
  },
  computed: {
    ...mapGetters('books', ['getBooks', 'getCurrentPage']),
    // 전체 페이지의 수가 1페이지면 페이지네이션을 보여주지 않고, 2페이지 이상일 경우에만 페이지네이션을 보여줍니다.
    showPage () {
      return this.getBooks && this.totalPage > 1
    },
    // 전체 페이지
    rows () {
      return this.totalPage
    }
  },
  watch: {
    $route: {
      handler (to) {
        const currentPage = parseInt(to.params.page, 10) - 1
        // 현재 페이지 활성화
        if (this.getCurrentPage === currentPage) {
          this.currentPage = parseInt(to.params.page, 10)
        }
      },
      deep: true,
      immediate: true
    }
  },
  methods: {
    pageClick (page) {
      this.$emit('pagination', page)
    }
  }
}
```
<br>

- `props`

|props|타입|설명|
|:---|:---|:---|
|totalPage|Number|책 데이터를 불러올 때, 전체 페이지 수인 `totalPage`를  `props`로 내려줍니다.|

<br>

- `computed`

|computed|설명|
|:---|:---|
|showPage|`props`로 받은 전체페이지의 수가 1페이지면 페이지네이션을 보여주지 않고, 2페이지 이상일 경우에만 페이지네이션을 보여줍니다.|
| rows|전체 페이지 수|
<br>

- `watch`

|watch|설명|
|:---|:---|
|$route|라우터를 관찰하여 페이지네이션의 번호를 클릭시, 책 데이터를 불러올 때 `state`의 `currentPage`에 저장한 현재페이지와 비교하여 현재 페이지를 활성화시켜줍니다. |

- ` methods`

|methods|설명|
|:---|:---|
|pageClick|페이지네이션의 페이지 클릭 시, 상위 컴포넌트에 클릭한 페이지의 번호와 함께 이벤트를 전달해줍니다.|

<br>

2. 믹스인으로 공통 요소 구현
```js
import PaginationFetchMixin from '~/mixins/PaginationFetchMixin'
export default {
  mixins: [PaginationFetchMixin]
}
```
<br>

- `PaginationFetchMixin`
```js
import { mapGetters, mapActions } from 'vuex'
export default {
  async asyncData ({ store, params, route }) {
    try {
      let total
      let totalPage
      const page = params.page
      // 나의 책/다른 사용자의 책 데이터
      let data = { page: page - 1, route: route.name }
      // 검색한 책 데이터
      if (route.name === 'books-search-page') {
        data = { ...data, search: encodeURIComponent(route.query.search), target: encodeURIComponent(route.query.target) }
        // 해시태그 책 데이터
      } else if (route.name === 'hashtags-page') {
        data = { ...data, name: encodeURIComponent(route.query.name) }
      }
      await store.dispatch('books/fetchBooks', data)
        .then((res) => {
          // 총 책의 수
          total = res.data.totalCount
          // 전체 페이지 수
          totalPage = res.data.totalPage
        })
      return { total, totalPage }
    } catch (err) {
      console.error(err)
    }
  },
  computed: {
    ...mapGetters('books', ['getBooks']),
    hasBook () {
      return this.getBooks.length
    }
  },
  methods: {
    ...mapActions('books', ['fetchBooks']),
    pagination (page) {
      switch (this.$route.name) {
        // 나의 책
        case 'books-page':
          return this.$router.push(`/books/${page}`)
        // 다른 사용자의 책
        case 'books-others-page':
          return this.$router.push(`/books/others/${page}`)
        // 검색한 책
        case 'books-search-page':
          return this.$router.push(`/books/search/${page}?search=${this.getSearch.selectedOption}&target=${this.getSearch.data}`)
        // 해시태그별로 검색한 책
        case 'hashtags-page':
          return this.$router.push(`/books/hashtags/${page}?name=${this.$route.query.name}`)
        default:
          break
      }
    }

  }
}
```
- `computed`

|computed|설명|
|:---|:---|
| hasBook|불러온 책 데이터가 있는지 확인합니다.|

<br>

- `methods`

|methods|설명|
|:---|:---|
|pagination|하위 컴포넌트인 `Pagination.vue`에서 페이지를 클릭 시, 이벤트를 발생시키면 해당 함수를 호출합니다. <br> 라우터의 `name`속성을 이용해, 페이지 라우터를 이동시킵니다. |

<br>

3. 책 조회 API 호출

3-1. store
|<div>actions</div>|
|---|
|fetchBooks|

```js
//store/book.js actions
  // 책 데이터 가져오기
  async fetchBooks ({ commit }, bookData) {
    const { route, page, search, target, name } = bookData
    let res
    switch (route) {
      // 나의 책 데이터
      case 'books-page':
        res = await this.$axios.get(`/books?page=${page}`)
        break
      // 다른 사용자의 책 데이터
      case 'books-others-page':
        res = await this.$axios.get(`books/others/book?page=${page}`)
        break
      // 검색한 책 데이터
      case 'books-search-page':
        res = await this.$axios.get(`books/others/book?page=${page}&search=${search}&target=${target}`)
        break
      // 태그별 겸색한 데이터
      case 'hashtags-page':
        res = await this.$axios.get(`books/hashtags/${name}/?page=${page}`)
        break
      default:
        break
    }
    commit('loadBooks', { books: res.data.books, page })
    return res
  }
```
- `axios`를 이용해 책 조회 API를 호출합니다.
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
-  `state`의 `books` 배열에 데이터를 저장합니다.
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
    // 북마크 여부
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

3-2. 책 데이터 가져오기
- `nuxt`의 `asyncData`훅으로 데이터를 가져옵니다.
-  `1페이지당` 12개의 데이터를 호출하여 가져오도록 구현하였습니다.
- `params`를 `page`로 설정하여 라우터를 변경할 수 있도록 구현하였습니다.
예시) 1페이지: /books/1, 2페이지: /books/2 ...
```js
// ~/mixins/PaginationFetchMixin
 async asyncData ({ store, params, route }) {
    try {
      let total
      let totalPage
      const page = params.page
      // 나의 책/다른 사용자의 책 데이터
      let data = { page: page - 1, route: route.name }
      // 검색한 책 데이터
      if (route.name === 'books-search-page') {
        data = { ...data, search: encodeURIComponent(route.query.search), target: encodeURIComponent(route.query.target) }
        // 해시태그 책 데이터
      } else if (route.name === 'hashtags-page') {
        data = { ...data, name: encodeURIComponent(route.query.name) }
      }
      await store.dispatch('books/fetchBooks', data)
        .then((res) => {
          // 총 책의 수
          total = res.data.totalCount
          // 전체 페이지 수
          totalPage = res.data.totalPage
        })
      return { total, totalPage }
    } catch (err) {
      console.error(err)
    }
  }
```
<br>

4. 가져온 책 데이터가 존재한다면, 책 데이터를 보여줍니다.
```html
<!-- ~/components/books/_page.vue -->
<template>
  ...
  <div class="books">
      <div v-for="book in getBooks" :key="book.id" class="book">
        <BookCard :book="book" />
      </div>
    </div>
  ...
</template>
```
<br>

- `bookCard` 컴포넌트

```js
  props: {
    book: {
      type: Object,
      required: true
    }
  }
```

 - `props`

 |props|타입|설명|
|:---|:---|:---|
|book|Object|책 조회 API를 호출하여 가져온 책 데이터|

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

5. 책 데이터가 존재하지 않는다면,
다른 컴포넌트를 보여줍니다.
-  `book-empty` `component`를 구현하여, 책 데이터가 없을 경우, 해당 컴포넌트를 보여주도록 구현하였습니다.
```html
<!-- ~/components/books/_page.vue -->
<template>
  <div class="bookshelf">
    ...
    <div v-if="hasBook">
      <div class="books">
        <div v-for="book in getBooks" :key="book.id" class="book">
          <BookCard :book="book" />
        </div>
      </div>
      <!-- books -->
      <BookPagination :total-page="totalPage" @pagination="pagination" />
    </div>
    <!-- 책 데이터가 없다면 해당 컴포넌트를 보여줍니다. -->
    <div v-else>
      <BookEmpty />
    </div>
  </div>
</template>
```
<Br>

- `book-empty` 컴포넌트

```html
<!-- ~/components/books/Empty.vue -->
<template>
  <div class="empty_book">
    <h3>책이 없어요</h3>
  </div>
</template>
```

#### <div>5-2. 내가 추가한 책 보여주기</div>

|컴포넌트|라우터|
|---|---|
|components/books/Card.vue|books/_page|
|components/books/Empty.vue|books/_page|
|components/books/Pagination.vue|books/_page|
<br>

#### <div>5-3. 다른 사용자가 추가한 책 보여주기</div>
|컴포넌트|라우터|
|---|---|
|components/books/Card.vue|books/others/_page|
|components/books/Empty.vue|books/others/_page|
|components/books/Pagination.vue|books/others/_page|
<br>

#### <div>5-4. 검색한 책 보여주기</div>

|컴포넌트|라우터|쿼리|
|---|---|---|
|components/books/Card.vue|books/search/_page|search,target|
|components/books/Empty.vue|books/search/_page|search,target|
|components/books/Pagination.vue|books/search/_page|search,target|

1. 쿼리를 이용하여 책을 검색하도록 구현하였습니다.

|쿼리|설명|
|:---:|:---|
|search|검색 옵션 <br>ex)책제목,저자|
|target|검색 내용|

- `watch`로 `쿼리` 변화를 감지하여 `책제목` , `저자` 중 옵션과 검색 내용을 `state`의 `search` 객체에 저장합니다.

```js
// ~/pages/book/search/index.vue
export default {
  watch: {
    '$route.query': {
      handler (query) {
       this.updateSearch({
          data: query.target,
          selectedOption: query.search
        })
      },
      deep: true,
      immediate: true
    }
  },
  watchQuery: ['search', 'target'],
  methods: {
   ...mapMutations('books', ['updateSearch'])
  }
}
```

|문제점|
|---|
|`nuxt`의 `asyncData`나 `fetch` 훅은 기본적으로 쿼리 문자열 변경에 대한 감지에 대해 비활성화 되어 있습니다.|

|해결|
|---|
|` watchQuery: ['search', 'target']` 속성으로 쿼리 변경 사항을 확인하고, 해당 쿼리 변경시 `astncData`훅이 호출될 수 있도록 구현하였습니다.|
(<a href="https://nuxtjs.org/docs/2.x/components-glossary/pages-watchquery">`nuxt` `watchQuery`에 관한 문서</a>)
<br>

2. 다른 사용자의 책 검색 메뉴 클릭
- `메뉴`에서 `다른 사용자 책 검색`을 클릭하면 입력폼이 보여지고, `책제목` , `저자` 두가지 옵션으로 검색할 수 있도록 구현하였습니다.
> /books/search/페이지번호/?search="검색옵션"&target="검색 내용"
```html
<!-- ~/components/AppHeader.vue -->
<template>
    <header class="header">
      ...
        <!--  메뉴 -->
        <div class="m_menu">
            ...
            <!-- 검색창은 로그인 되어 사용자 정보가 있을 경우에만 보이도록 구현하였습니다.(로그인을 하지 않았다면 검색창은 보이지 않습니다.) -->
           <div v-if="getUser" class="action_menu">
             ...
              <ul>
                ...
                <li>
                  <a href="#" @click.prevent="showSearchForm">
                    <img src="/images/settings.png" />다른 사용자 책 검색</a>
                </li>
              </ul>
            </div>
        </div>
      ...
    <div v-if="getUser && search.showsearchState" class="search_area">
      <div class="btn">
        <a href="#" @click.prevent="search.showsearchState = false">검색창 끄기</a>
      </div>
      <!-- 검색폼 -->
      <FormSearch
        v-model="search.input"
        :options="search.options"
        @searchBook="onsearchBook"
        @selectedOption="onselectedOption"
      />
    </div>
  </header>
</template>
```
```js
import { mapGetters, mapActions, mapMutations } from 'vuex'
export default {
  data () {
    return {
      search: {
        input: '',
        options: ['책제목', '저자'],
        showsearchState: false
      }
    }
  },
  computed: {
    ...mapGetters('books', ['getSearch'])
  },
  methods: {
    ...mapMutations('books', ['updateSearch']),
    // 기존 검색 데이터 초기화
    showSearchForm () {
      this.search.showsearchState = !this.search.showsearchState
      this.updateSearch({
        data: '',
        selectedOption: this.search.options[0]
      })
    },
    onselectedOption (option) {
      this.updateSearch({
        selectedOption: option
      })
    },
    onsearchBook () {
      // 입력값이 없으면 리턴해준다.
      if (this.search.input.length === 0) {
        return
      }
      this.updateSearch({
        data: this.search.input
      })
      this.$router.push(
        `/books/search/1?search=${this.getSearch.selectedOption}&target=${this.search.input}`
      )
    }
  }
}
```

- `methods`

|methods|설명|
|:---|:---|
|showSearchForm|검색창을 보여줍니다.|
| onselectedOption |검색 옵션을 `state`의 `search`객체에 저장합니다.|
|onsearchBook|검색 내용을 `state`의 `search`객체에 저장하고 라우터를 이동시킵니다.|


|mutations|
|---|
|updateSearch|
```js
//store/book.js  mutations
  updateSearch (state, payload) {
    Object.keys(payload).forEach(key => state.search[key] = payload[key])
  }
```
-  `state`의 `search` 객체에 데이터를 저장합니다.
<br><br>


|state|
|---|
|search|
```js
//store/book.js state
search: {
  // 검색 내용
    data: '',
  // 검색 옵션
    selectedOption: '책제목'
  }
```

<br>


#### <div>5-5. 태그별로 책 보여주기</div>
|컴포넌트|라우터|쿼리|
|---|---|---|
|components/books/Card.vue|hashtags/_page|name|
|components/books/Empty.vue|hashtags/_page|name|
|components/books/Pagination.vue|hashtags/_page|name|

1. 쿼리를 이용하여 해시태그별 책을 검색하도록 구현하였습니다.

|쿼리|설명|
|:---:|:---|
|name|태그 이름|
> /hashtags/페이지번호/?name="태그 이름"

- `watch`로 `쿼리` 변화를 감지하도록 하였습니다.
```js
// ~/pagees/hashtags/_page.vue
export default {
  watchQuery: ['name']
}
```

```html
<!-- ~/components/hashtag/List.vue -->
<template>
  <ul class="hashtags tagList">
    <li v-for="(tag,index) in hashtags" :key="index" class="tag" @mouseenter="onChangeState(tag,index)" @mouseleave="tagNum=''">
      <!-- 라우터 이동 -->
      <nuxt-link :to="`/hashtags/1/?name=${tag.name}`">
        #{{ tag.name }}
      </nuxt-link>
    ...
    </li>
  </ul>
</template>
```
<br>

### <div id="get_data"><b>6. 책 상세 보기</b></div>
|컴포넌트|라우터|
|---|---|
|components/books/CardDetail.vue|books/_page|
|components/books/CardDetail.vue|books/others/_page|

#### <div>6-1. 공통 내용</div>
1. `nuxt`의 `asyncData`훅을 이용해
책 데이터를 가져오도록 구현하였습니다.

2. 성공적으로 책 데이터를 가져왔다면, 책 데이터를 보여줍니다.
```html
<!-- ~/components/books/_page.vue -->
<template>
  ...
  <div class="book-details">
    <div>
      <BookCardDetail :book="getBook" />
    </div>
  ...
</template>
```
<br>

- `bookCard` 컴포넌트

```js
  props: {
    book: {
      type: Object,
      required: true
    }
  }
```

 - `props`

 |props|타입|설명|
|:---|:---|:---|
|book|Object|책 조회 API를 호출하여 가져온 책 데이터|

<br>

#### <div>6-2. 나의 책 상세보기</div>
|컴포넌트|라우터|
|---|---|
|components/books/CardDetail.vue|books/_page|

1. 단일 책 데이터 조회 API 호출

1-1. store
|<div>actions</div>|
|---|
|fetchBook|

```js
//store/book.js actions
  async fetchBook ({ commit }, { id }) {
    const res = await this.$axios.get(`/books/${id}`)
    commit('loadbook', res.data.book)
    return res
  }
```
- `axios`를 이용해 책 조회 API를 호출합니다.
- `commit`를 이용해 `mutations`을 호출합니다.
<br>

|mutations|
|---|
|loadbook|
```js
//store/book.js  mutations
  loadbook (state, bookData) {
    state.book = bookData
  }
```
-  `state`의 `book` 객체에 데이터를 저장합니다.
<br>

|getters|
|---|
|getBook|
```js
//store/book.js  getters
  getBook (state) {
    return state.book
  }
```
- `state`의 `book` 객체를 가져옵니다.

|state|
|---|
|book|
```js
//store/book.js state
  book: {}
```
<br>

 -  `state`의 `book` 객체에는 아래 정보를 저장합니다.
```js
book: {
  // 코멘트
  Comments: Array[0]
  // 해시태그
  Hashtags: Array[0]
  // 사용자의 id
  UserId: 7
  // 책 저자
  authors: "dd"
  // 북마크 여부
  bookmark: false
  // 책 내용
  contents: "dd"
  // 생성 날짜
  createdAt: "2021-06-14T15:39:04.810Z"
  // 책 출간 날짜
  datetime: "2021-06-14T15:38:52.000Z"
  // 책 id
  id: 144
  // 책 isbn
  isbn: ""
  // 책 출판사
  publisher: ""
  // 책 이미지
  thumbnail: null
  // 책 제목
  title: "d"
  // 업데이트 날짜
  updatedAt: "2021-06-14T15:39:04.810Z"
  // 책 url
  url: ""
}
```
<br>

1-2. `nuxt`의 `asyncData`훅으로 데이터를 가져옵니다.
```js
//  ~/pages/books/b/_id.vue
  async asyncData ({ store, params }) {
    try {
      await store.dispatch('books/fetchBook', { id: params.id })
    } catch (err) {
      console.log(err)
    }
  }
```
<br>

#### <div>6-3. 다른 사용자의 책 상세보기</div>
|컴포넌트|라우터|
|---|---|
|components/books/CardDetail.vue|books/others/_page|

1. 단일 책 데이터 조회 API 호출

1-1. store
|<div>actions</div>|
|---|
|otherFetchBook|

```js
//store/book.js actions
  // 다른 사용자의 책(단일) 불러오기
  async otherFetchBook ({ commit }, { id }) {
    const res = await this.$axios.get(`/books/others/book/${id}`)
    commit('loadbook', res.data.book)
    return res
  }
```
- `axios`를 이용해 책 조회 API를 호출합니다.
- `commit`를 이용해 `mutations`을 호출합니다.
<br>

|mutations|
|---|
|loadbook|
```js
//store/book.js  mutations
  loadbook (state, bookData) {
    state.book = bookData
  }
```
-  `state`의 `book` 객체에 데이터를 저장합니다.
<br>

|getters|
|---|
|getBook|
```js
//store/book.js  getters
  getBook (state) {
    return state.book
  }
```
- `state`의 `book` 객체를 가져옵니다.

|state|
|---|
|book|
```js
//store/book.js state
  book: {}
```
<br>

 -  `state`의 `book` 객체에는 아래 정보를 저장합니다.
```js
book: {
  // 해시태그
  Hashtags: Array[0]
  // 좋아요 누른 사람
  Likers:Array[0]
  // 책의 사용자 정보
  User:Object
  // 사용자의 id
  UserId: 7
  // 책 저자
  authors: "dd"
  // 북마크 여부
  bookmark: false
  // 책 내용
  contents: "dd"
  // 생성 날짜
  createdAt: "2021-06-14T15:39:04.810Z"
  // 책 출간 날짜
  datetime: "2021-06-14T15:38:52.000Z"
  // 책 id
  id: 144
  // 책 isbn
  isbn: ""
  // 책 출판사
  publisher: ""
  // 책 이미지
  thumbnail: null
  // 책 제목
  title: "d"
  // 업데이트 날짜
  updatedAt: "2021-06-14T15:39:04.810Z"
  // 책 url
  url: ""
}
```
<br>

1-2. `nuxt`의 `asyncData`훅으로 데이터를 가져옵니다.
```js
//  ~/pages/books/others/b/_id.vue
  async asyncData ({ store, params }) {
    try {
      let otherBookList
      await store.dispatch('books/otherFetchBook', { id: params.id })
        .then((res) => {
          otherBookList = res.data.book
        })
      return { otherBookList }
    } catch (err) {
      console.log(err)
    }
  }
```
<br>

### <div id="bookmark"><b>7. 북마크 및 좋아요 기능</b></div>

#### 7-1.  공통 내용

|_|나의 책|다른 사용자의 책|
|:---:|:---:|:---:|
|기능|북마크 추가/삭제|하트(좋아요) 추가/삭제|

- 내가 추가한 책인지 다른 사용자가 추가한 책인지 구분하여, 내 책이면 북마크 표시를 보여주고, 다른 사용자의 책이라면 하트 표시를 보여줍니다.
```html
<!-- ~/components/book/Card.vue -->
<template>
  <div class="book_inner">
    ...
    <div class="txt">
     ...
     <!-- 북마크 및 좋아요 -->
     <!-- 내 책이라면 북마크를 보여줍니다. -->
      <span v-if=" ismybook " class="bookmark" :class="{'bookmarked':onBookmarked}" @click="onClickBookmark(book.id)"><i class="fas fa-bookmark"></i></span>
      <!-- 내 책이 아니라면 하트를 보여줍니다. -->
      <span v-else class="heart" @click="onClickLike(book.id)"><i :class="isheart"></i></span>
    </div>
  </div>
</template>
```
```js
// ~/components/book/Card.vue
 computed: {
    ...mapGetters('user', ['getUser']),
    ismybook () {
      return this.book.UserId === this.getUser.id
    }
    ...
  },
```
- `computed`

|computed|설명|
|:---:|:---|
|ismybook|`props`로 받은 `book`의 `UserId`(사용자 아이디)와 로그인 할 때 저장한 사용자의 아이디가 같은 지 확인하여 내 책을 구분합니다|


#### 7-2. 북마크

|컴포넌트|라우터|
|---|---|
|components/books/Card.vue|books/_page|

#### 7-2-1. 북마크 보여주기

```js
// ~/components/books/Card.vue
computed: {
    onBookmarked () {
      return !!(this.book && this.book.bookmark)
    },
    isbookmark () {
      return this.onBookmarked ? 'B' : ''
    }
  },

```
- `computed`

|computed|설명|
|:---:|:---|
|onBookmarked|`props`로 받은 `book`의 `bookmark` 속성으로 북마크 여부를 구분합니다.|
|isbookmark|`computed`인 `onBookmarked`로 북마크 여부 확인하여, 해당 값이 `true`라면 "B" 글자를 리턴해줍니다. <br>(북마크된다면 "B"마크 표시가 보이도록 구현)|

<br>

- 북마크 표시 보여주기
> `class`를 바운딩하여 북마크 여부 표시도 따로 표현하였습니다.
```html
<!-- ~/components/book/Card.vue -->
<template>
...
 <span v-if=" ismybook " class="bookmark" :class="{'bookmarked':onBookmarked}">..</span>
...
</template>
```
```css
/* stlye.css */
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


#### 7-2-2. 북마크 추가

1. 북마크 추가 API 호출

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
- `axios`를 이용해 북마크 추가 API를 호출합니다.
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

2. 북마크 추가 버튼 클릭

- 이미 북마크가 추가되어 있다면, 북마크를 삭제시키는 API를 호출하고, 북마크가 추가되어 있지 않다면, 북마크를 추가하는 API를 호출하도록 구현하였습니다.
```html
<!-- ~/components/book/Card.vue -->
<template>
...
 <div class="book_inner">
<span
  v-if="ismybook"
  class="bookmark"
  :class="{ bookmarked: onBookmarked }"
  @click="onClickBookmark(book.id)"
  ><i class="fas fa-bookmark"></i>
  </span>
  </div>
</template>
```
```js
// components/Book/Card.vue
  methods: {
    ...mapActions('books', ['createBookmark', 'deleteBookmark']),
    onClickBookmark (id) {
      if (this.onBookmarked) {
        // 이미 북마크가 되어 있다면 북마크 삭제 API 호출
        this.deleteBookmark({ bookId: id })
      } else {
         //북마크가 되어 있지 않다면 북마크 추가 API  호출
        this.createBookmark({ bookId: id })
      }
    },
  }
```

#### 7-2-3. 북마크 삭제

1. 북마크 삭제 API 호출

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

<br>

2. 북마크 해제 버튼 클릭

- 이미 북마크가 추가되어 있다면, 북마크를 삭제시키는 API를 호출하고, 북마크가 추가되어 있지 않다면, 북마크를 추가하는 API를 호출하도록 구현하였습니다.
```html
<!-- ~/components/book/Card.vue -->
<template>
...
 <div class="book_inner">
<span
  v-if="ismybook"
  class="bookmark"
  :class="{ bookmarked: onBookmarked }"
  @click="onClickBookmark(book.id)"
  ><i class="fas fa-bookmark"></i>
  </span>
  </div>
</template>
```
```js
// components/Book/Card.vue
  methods: {
    ...mapActions('books', ['createBookmark', 'deleteBookmark']),
    onClickBookmark (id) {
      if (this.onBookmarked) {
        // 이미 북마크가 되어 있다면 북마크 삭제 API 호출
        this.deleteBookmark({ bookId: id })
      } else {
         //북마크가 되어 있지 않다면 북마크 추가 API  호출
        this.createBookmark({ bookId: id })
      }
    },
  }
```
<br>

#### 7-3. 좋아요

|컴포넌트|라우터|
|---|---|
|components/books/Card.vue|books/others/_page|

#### 7-3-1. 좋아요 보여주기

```js
// ~/components/books/Card.vue
computed: {
    onclickHearted () {
      // 배열 아닌 요소에서 `find` 메서드가 작동하지 못하도록 `Likers`배열이 없다면 빈배열을 넣어주었습니다.
      return !!(this.book.Likers || []).find(
        liker => this.getUser.id === liker.id
      )
    },
    isheart () {
      return this.onclickHearted ? 'fas fa-heart' : 'far fa-heart'
    }
  },

```
- `computed`

|computed|설명|
|:---:|:---|
|onclickHearted|로그인할 때 저장된 나의 `id`와 `Likers`배열의 `userId` 정보를 이용해 해당 책에 대한 좋아요 추가 여부를 확인할 수 있도록 구현하였습니다.<br>ex)`Likers:[{Like:{...},id:6}...]`: `Likers`배열에는 좋아요를 추가한 사용자의 `id`와 사용자의 `username`을 받도록 구현(id가 6인 사용자가 해당 책에 좋아요를 추가함)|
|isheart|`onclickHearted`로 좋아요 여부 확인합니다.|

<br>

- 좋아요 표시 보여주기
> `class`에 바운딩하여 `좋아요`가 추가된 상태이면 색이 채워진 하트를 보여주고,그렇지 않다면 빈 하트를 보여줍니다
```html
<!-- ~/components/book/Card.vue -->
<template>
...
  <span v-if="ismybook" class="bookmark" :class="{ bookmarked: onBookmarked }" @click="onClickBookmark(book.id)"><i class="fas fa-bookmark"></i></span>
  <span v-else class="heart" @click="onClickLike(book.id)"><i :class="isheart"></i></span>
...
</template>
```
<br>

#### 7-3-2. 좋아요 추가

1. 좋아요 추가 API 호출

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
  addlike (state, likeData) {
    const { bookId, userId } = likeData
    const index = state.books.findIndex(book => book.id === bookId)
    state.books[index].Likers.push({ id: userId })
  }
```
-  `state`의 `books` 배열에서 `id`로 해당 책을 찾아
`Likers`배열에 `userId`를 추가해줍니다.

<br>

2. 좋아요 추가 버튼 클릭

- 이미 좋아요가 추가되어 있다면, 좋아요를 삭제시키는 api를 호출하고, 좋아요가 되어 있지 않다면, 좋아요를 추가하는 api를 호출하도록 구현하였습니다.
```html
<!-- ~/components/book/Card.vue -->
<template>
...
 <div class="book_inner">
   ...
 <span v-else class="heart" @click="onClickLike(book.id)"><i :class="isheart"></i></span>
  </div>
</template>
```
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

#### 7-3-3. 좋아요 삭제

1. 좋아요 삭제 API 호출

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
  removelike (state, likeData) {
    const { bookId, userId } = likeData
    const index = state.books.findIndex(book => book.id === bookId)
    state.books[index].Likers = state.books[index].Likers.filter(like => like.id !== userId)
  }
```
-   `state`의 `books` 배열에서 `id`로 해당 책을 찾아
`Likers`배열에 `id`를 비교해 제거해줍니다.

<br>

2. 좋아요 해제 버튼 클릭

- 이미 좋아요가 추가되어 있다면, 좋아요를 삭제시키는 api를 호출하고, 좋아요가 되어 있지 않다면, 좋아요를 추가하는 api를 호출하도록 구현하였습니다.
```html
<!-- ~/components/book/Card.vue -->
<template>
...
 <div class="book_inner">
   ...
 <span v-else class="heart" @click="onClickLike(book.id)"><i :class="isheart"></i></span>
  </div>
</template>
```
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


### <div id="get_data"><b>8. 댓글 보기 및 추가 및 삭제</b></div>
|컴포넌트|
|---|
|components/form/Comment.vue|
|components/comment/Edit.vue|
|components/comment/List.vue|
#### 8-1. 댓글 보기
1. 댓글 조회 API 호출

1-1. store
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
- `axios`를 이용해 댓글 조회 API를 호출합니다.
- `IntersectionObserver`를 이용하여 댓글을 불러오도록 구현하였습니다.


- `store`의 `actions`함수 `fetchComments` 호출시,
`{init:true}`객체로 처음으로 댓글을 불러오는 지 여부를 확인하도록 구현하였습니다.
<br>

|처음으로 댓글을 불러오는지 여부를 확인하는 이유|
|---|
|맨 처음으로 `댓글 보기 버튼`을 클릭한다면, 최근에 생성된 댓글 기준 내림 차순으로 10개씩 데이터를 불러오는 API를 호출합니다.|
| `화면 하단`에 스크롤이 내려온다면, 이어서 다음 댓글 10개를 불러오는 API를 호출합니다|
|`화면 하단`에 스크롤이 내려올시에만, 다음 데이터를 불러오도록 구현하였기 때문에,처음으로 데이터를 호출했는지 여부 확인이 필요합니다.|

<br>

>`댓글보기`버튼 클릭

- `{init:true}`일 때, 처음으로 데이터(댓글 10개)를 불러오는 api를 호출합니다.
```js
 if (comments.init) {
      ...
      // 10개씩 데이터 호출
        res = await this.$axios.get(`books/${comments.bookId}/comments?limit=10`)
 }
```
<br>

>`화면 하단`에 스크롤 진입

- 데이터의 마지막 `id`(마지막 댓글의 `id`)를 찾아서 그 이후 다음 데이터 10개(다음 댓글 10개)를 불러오는 API를 호출합니다.
```js
....
 else {
  //  마지막 댓글를 가져옵니다.
        const lastComment = state.comments && state.comments[state.comments.length - 1]
        // 마지막 댓글의 id 를 기준으로 그 이후 10개의 데이터를 불러옵니다.
        res = await this.$axios.get(`books/${comments.bookId}/comments?lastId=${lastComment && lastComment.id}&limit=10&page=${comments.page}`)
      }
```
<br>

- `commit`를 이용해 `mutatonis`을 호출합니다.

<br>

|mutations|
|---|
|loadComments|
```js
//store/comments.js  mutations
  loadComments (state, commentData) {
    const { data, init } = commentData
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

|처음으로 데이터를 불러올 때|처음이 아닌 이후 데이터를 불러올 때|
|---|---|
|처음으로 데이터(댓글)을 불러올 때 `state`의 `comments`배열에 데이터를 저장합니다.|처음이 아니라면  기존 `state`의 `comments`배열에 데이터를 누적시킵니다.|

<br>

- 댓글 조회 API를 호출 후, 데이터를 가져올 때, `마지막 페이지 여부`와 `총 댓글의 갯수`를 받아 저장합니다.
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
|getters|설명|
|:---:|:---|
|getComments|`state`의 `comments`배열을 가져옵니다.|
|getCommentPage|`state`의 `commentPage`객체를 가져옵니다.|

<br>

|state|
|---|
|comments|
|commentPage|
```js
//store/comments.js state
  // 코멘트
  comments: [],
  // 코멘트 정보
  commentPage: {
    commentCount: 0,
    end: false
  }
```

|state|설명|
|:---:|:---|
|comments|댓글 데이터를 저장합니다.|
|commentPage|`마지막 페이지 여부`를 `end`에 `Boolean`값으로 저장하고, `commentCount`에 `총 댓글의 갯수`를 저장합니다.|


- <div id="c_api">댓글 조회 API 호출시,  아래 정보를 저장합니다.</div>
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


1-2. 댓글 보기 버튼 클릭
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
          <CommentList v-for="comment in getComments" :key="comment.id" :comment="comment" @onRemoveComment="onRemoveComment" />
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
  computed: {
  ...mapGetters('comments', ['getComments', 'getCommentPage']),
  onStateComment () {
    return !this.showComment ? '댓글 보기' : '댓글 접기'
  },
  // 코멘트 추가 확인
  isaddComment () {
    return this.showComment && this.getComments && this.getComments.length > 9 && !this.getCommentPage.end
  }
},
 methods: {
   onshowComments() {
     this.ontoggleComment()
   //  댓글 보기 버튼을 클릭할 때 댓글 조회 API 호출
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
   // IntersectionObserver 로 다음 데이터를 가져오는 API 호출
   onaddComments() {
     const observer = new IntersectionObserver((entries) => {
       entries.forEach((entry) => {
         // 댓글 보기 버튼을 클릭하고, 스크롤이 화면 하단에 위치하고, 댓글이 이미 10개가 호출이 되어 있을 때 다음 댓글를 호출하여 누적시킵니다.
         // 다음 댓글를 호출했을 때 댓글이 더이상 남아 있는지 확인하여 더이상 불러올 댓글이 존재하지 않는다면 호출하지 않습니다.
         if (this.isaddComment && entry.isIntersecting) {
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



#### 8-2. 댓글 추가
#### 8-3. 댓글 삭제












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
        <h3>댓글 쓰기</h3>
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







