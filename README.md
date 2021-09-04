# libraryApp
# client

## vue +nuxt
- SSR(서버사이드렌더링)를 위해 nuxt 프레임워크를 사용하였습니다.
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

#### input 태그 엑스 박스 제거
>  참고로 IE(인터넷 익스플로러) 10 이상에서 Input text Box에 포커스 되면 우측에 x 표시가 생기는데, 이는 직접 구현하였으므로, IE(인터넷 익스플로러) 우측에 x 표시가 생기지 않도록 초기화시켜주었습니다.</div>

```css
/* static/css/style.css */
/* ms 인풋요소 엑스박스제거 */
::-ms-clear{display: none;}
```

### 3-3. `vuex` `store` 사용


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

#### 유효성 검사
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



### 3-5. 공통 컴포넌트
#### 알림창(알림 메세지)
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

```html
<!-- ~/components/common/alertMsg.vue -->
<!-- transition으로 애니메이션 효과 -->
  <transition name="upSlide">
    <div v-if="alertState" class="alertmsg" :style="{ 'background-color': bgcolor }">
      {{ data }}
    </div>
  </transition>
```
> <a href="https://vuejs.org/v2/api/#transition">vue transitioin 문서 바로 가기</a>

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

> `select` 태그는 브라우저마다 다르게 보이기 때문에 통일되게 보일 수 있도록 `select` 태그를 사용하지않고, `ul`태그를 이용해 커스텀하여 구현했습니다.

```html
<!-- ~/components/form/Search.vue -->
<template>
  <form class="search_form" @submit.prevent="$emit('searchBook')">
    <div class="main_select" @mouseenter="selected=!selected" @mouseleave="selected=false">
      <div>
       <!-- props로 받은  options: ['통합', '제목', '저자', '출판사', 'isbn'] 중 선택한 한개의 요소를 보여줍니다. -->
        <p>{{ selectedOption }}<i class="fas fa-chevron-down"></i></p>
      </div>
      <!-- 옵션 리스트 -->
      <ul v-if="selected" class="custom_select">
        <li v-for="(option,index) in options" :key="index" @click="changeSelect(option)">
          {{ option }}
        </li>
      </ul>
    </div>
    <!-- 입력 -->
    <input ref="searchInput" :value="value" type="text" placeholder="검색" @input="$emit('input',$event.target.value)">
    <!-- 버튼 -->
    <button type="submit">
      <i class="fas fa-search"></i>
    </button>
  </form>
</template>
```
<br>

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
|options|Array|검색 옵션<br> ex)책제목,저자 등 검색 카데고리|
|value|String / Number|검색 입력폼에 입력한 값(`input`태그에 입력한 값)|
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

|events|설명|
|:---|:---|
|searchBook|`submit`이벤트 발생시, `$emit`을 이용해 상위 컴포넌트에 전달해줍니다. |
|input|`input`이벤트 발생시,`$emit`을 이용해 입력폼에 입력된 값을 상위 컴포넌트에 전달해줍니다. |


<br>

#### 로딩 스피너

```html
<!-- ~/component/LoaindBar.vue -->
<template>
  <div class="loading-spin" :class="{'absolute' : 'position' }">
    <i class="fas fa-spinner fa-spin" :style="{ color }"></i>
  </div>
</template>
```
<br>

<b>props</b>

```js
export default {
  props: {
    color: {
      type: String,
      required: false,
      default: '#677eff'
    },
    position: {
      type: Boolean,
      required: false
    }
  }
}
```
|props|타입|설명|
|:---|:---|:---|
|color|String|로딩 스피너 색|
|position|Boolean|로딩 스피너 `css` `position` 속성 여부 확인|

<br>

####  IE 체크 확인 컴포넌트
```html
<template>
  <div v-if="showAlert" class="ie-chk">
    해당 브라우저(IE)는 곧 서비스가 종료될 예정입니다. 다른 브라우저를 이용해주세요.
    <button @click="closeAlert">
      <i class="fas fa-times"></i>
    </button>
  </div>
</template>
```

<br>

## 4. 구현 세부 내용 정리
<br>

### <div id="register"><b>1. 회원가입/로그인 구현</b></div>

|컴포넌트|라우터|
|---|---|
|components/form/Register.vue|auth/register|
|components/form/Login.vue|auth/login|

<br>

#### <div>1-1. 회원가입</div>

|컴포넌트|라우터|
|---|---|
|components/form/Register.vue|auth/register|

<b>1. 이메일,닉네임,비밀번호를 입력해야 가입될 수 있도록 구현하였습니다.</b>

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
  }
```

<b>data</b>

|data|설명|
|:---|:---|
|user|사용자의 `email`과 `username` 정보|
|password|비밀번호|
|confirm_password|data의 `password`값과 비교하기 위한 데이터|
|errmsg|에러 발생시, 보여줄 데이터|

> `v-model`를 이용해 데이터를 양방향 바운딩해주었습니다.<br>

<br>

<b>2. 유효성 검사</b>

> <a href="#">유효성 검사</a>는 공통 구현 요소에 정리하였습니다. <br>
> <a href="#">validata.js</a>

<br>


|유효성 검사 리스트|
|:---|
|비밀번호 길이 검사|
|이메일 유효성 검사|
|`data`의 `password` 의 값과 `data`의 `confirm_password` 의 값이 일치하는지 검사|
|이메일과 닉네임의 길이 검사|

<br>

<b>computed</b>

> `computed`를 통해 데이터 유효성을 확인합니다.

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
|computed|설명|
|:---|:---|
|isvalidLength|`data`의 `password`의 길이가 8자 이상인지 30자 이하인지 확인합니다.|
|isvalidEmail|`data`의 `email`의 양식이 이메일인지 확인합니다.|
|isconfirmPassword|`data`의 `password`와 `data`의 `confirm_password`가 완전히 일치하는지 확인합니다.|
|isuserInfoLength|`data`의 `user` 객체 데이터의 길이가 20자 이하인지 확인합니다.|
|inputErrMsg|`computed`인 `isuserInfoLength`를 확인하여 입력데이터에 따라 에러 메세지를 보여줄지 확인합니다.|
|disabledBtn|위의 유효성 검사를 모두 통과하는지 확인합니다. <br>`<button>`태그의 `disabled`속성을 바운딩시켜, 유효성 검사르 모두 통과될 때에만 버튼이 활성화됩니다.|

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

<br><br>

<b>5. 회원가입 API</b>

5-1. store

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


5-2. 회원가입 버튼 클릭
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
<br>

<br>

#### <div>1-2. 로그인</div>

<b> 1. 로그인 구현은 이메일로 로그인.카카오로 로그인.구글로 로그인 세가지 방법으로 구현하였습니다.</b>
 > 서버에서 passport를 사용하여 구현하였는데 해당내용은 아래의 서버 구현 내용에서 정리하였습니다.<br>
 (<a href="https://www.passportjs.org/packages/passport-kakao/">`passport`카카오 로그인 참고 문서 바로 가기</a>)<br>
 (<a href="https://www.passportjs.org/packages/passport-google-oauth20/">`passport`구글 로그인 참고 문서 바로 가기</a>)<br>

<b>2. 로그인 API</b>

2-1. store

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


2-2. 로그인 버튼 클릭
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


** <b>사용자 정보 저장시 문제점</b>

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

<br>

***

<br>

### <div id="user_info"><b>2. 사용자 정보 수정</b></div>

|라우터|
|---|
|user/info|


#### <div>1-1. 프로필 정보 수정</div>

<b>1. 닉네임(이름) 수정</b>
> 기존에 있는 정보를 수정하므로, 기존 정보를 보여줍니다.

```html
<!-- ~/page/user/info.vue -->
<template>
  ...
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
         <div v-if="isusernamevalid" class="err">
          닉네임은 20자 이하로 입력해주세요.
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
      username: '',
      ...
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
> `created`훅으로 인스턴스가 생성된 후, `username`에 기존 사용자의 닉네임을 저장하여 기존 데이터를 보여줍니다.

<br>

<b>2. 사용자 프로필 이미지 수정</b>

이미지 업로드 API를 호출하여  `amazon s3 버킷`에 이미지를 저장 후, 주소를 불러와서 이미지를 보여줍니다.

>`프로필 정보 수정` 버튼을 눌러야 최종적으로 사용자의 프로필 이미지가 수정됩니다.

<br>

<b>3. 이미지 업로드 API</b>

3-1. store

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
        // 사용자 프로필(썸네일) 이미지 업로드 API
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

>  사용자의 프로필(썸네일) 이미지 뿐만 아니라, 책의 썸네일 이미지를 업로드할때 사용하기 때문에, `payload.user`가 `true`인 경우에는 사용자 정보 썸네일 이미지를, 그렇지 않다면 책의 썸네일 이미지를 업로드하는 API 함수를 호출 할 수 있도록 구현하였습니다.

`commit`를 이용해 `mutatonis`을 호출합니다.

<br>

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

<br>

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
<br>


<div id="image_add">3-2. 이미지 수정 버튼 클릭</div>

> `store`의 `actioins` 함수 `uploadImg`를 호출합니다.
<br>
```html
<!-- ~/page/user/info.vue -->
<template>
  ...
       <!-- 포르필 이미지 수정 -->
       <div class="file_container add">
          <!-- 로딩 스피너 -->
          <LoadingBar v-if="$store.state.initLoading" position />
          <div class="txt">
            <label for="fileinput"><span class="round-btn yellow"><i class="far fa-file-image"></i>프로필 이미지
              수정</span></label>
              <!-- input type=file로 설정하여 파일창으로 파일을 선택할 시, onChangeImage 함수가 호출됩니다.-->
            <input id="fileinput" ref="file" style="display:none" type="file" @change="onChangeImage">
          </div>
          <div class="photos">
            <div class="images" :class="{'imgErr':hasErr}">
              <img v-if="hasThumbnail" :src="getUser.thumbnail" alt="thumbnail">
              <img v-if="hasImage" :src="`${getImagePath}`" alt="thumbnail">
            </div>
          </div>
        </div>
    ....
  </div>
  <button class="round-btn" type="submit" :disabled="hasErr || isusernamevalid">
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
 |`onChangeImage`함수로 이미지를 업로드하는 API를 호출하여 이미지의 주소를 가져와 `state`의 `imagePath`에 저장하도록 구현하였습니다. 다른 라우터로 이동시에도 `imagePath`에 값이 그대로 저장되어 있기 때문에, 프로필 정보 수정하기 위해 해당 라우터로 진입시,`imagePath`에 저장된 이미지가 그대로 보여지게 됩니다.  |

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
    state.imagePath = ''
  }
 ```
<br>

<b>4. 이미지 보여주기</b>

사용자의 프로필 이미지를 보여줍니다.

 ```html
<!-- ~/page/user/info.vue -->
<template>
  ...
       <!-- 포르필 이미지 수정 -->
       <div class="file_container add">
          <!-- 로딩 스피너 -->
          <LoadingBar v-if="$store.state.initLoading" position />
          ....
          <!-- 이미지 사진 보여주기 -->
         <div class="photos">
            <div class="images" :class="{'imgErr':hasErr}">
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
<br>

<b>computed</b>

```js
  computed: {
    hasImage () {
      return this.getImagePath.length > 0
    },
    hasThumbnail () {
      return !this.hasImage && this.getUser.thumbnail
    },
      hasErr () {
      return this.errmsg.length > 0
    }
  }
```
|computed|설명|
|:---|:---|
|hasImage|이미지 업로드 API를 호출하여 저장한 `state`의 `imagePath` 데이터가 있는지 확인합니다.|
|hasThumbnail|`state`의 `user` 객체에 사용자의 썸네일 이미지 데이터가 있는지 확인합니다.|
|hasErr|`input`태그를 이용해 이미지를 불러올 때, 오류 유무를 확인합니다.|

> `computed`를 통해 사용자의 썸네일 이미지가 있다면, 해당 이미지를 보여주고, 이미지 업로드 API를 호출하여 이미지를 업로드 했다면, 해당 이미지를 보여줍니다.

 <br>

<b>5. 프로필 정보 수정 API</b>

> 이미지 업로드 API를 통해 저장된 이미지와, 사용자의 닉네임과 함께
프로필 정보를 수정합니다.

5-1. store

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

<br>

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

5-2. 프로필 정보 수정버튼 클릭

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

#### <div>1-2. 비밀번호 수정</div>

<b>1. 카카오 로그인과 구글 로그인시에는 비밀번호 수정이 필요 없기 때문에 로그인 시 `state`의 `user` 객체에 저장한 `provier` 속성을 이용해, 비밀번호 변경 가능 여부를 확인하도록 구현하였습니다.</b>
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

 <br>

 ***

 <br>

### <div id="book_search" >3. 원하는 책 검색 및 추가</div>

#### 3-1. 원하는 책 검색

|컴포넌트|라우터|
|---|---|
|components/form/Search.vue|books/search|
|components/book/CardDetail.vue|books/search|

<b>1. 카카오 책검색 API를 이용하여 검색한 데이터를 가져옵니다.</b>
([카카오 개발자 센터](https://developers.kakao.com/docs/latest/ko/daum-search/dev-guide#search-book) 참고)

- 해당 API는 `title(제목)`, `isbn(ISBN)`, `publisher(출판사)`, `person(인명)`로 검색 필드를 제공해주기 때문에 옵션에 따라, 검색 필드를 제한할 수 있도록 구현했습니다.
```html
<!-- ~pages/search/index.vue -->
<template>
  <!-- v-model 를 이용해 data 인 search 를 바운딩시켜줍니다. -->
  <form-search v-model="search" :options="options" @searchBook="onSearchBook" @selectedOption="onSeletedOption"></form-search>
</template>
```
<b>data</b>

```js
// ~pages/search/index.vue
data(){
  options: ['통합', '제목', '저자', '출판사', 'isbn'],
   search: '',
   ...
}
```
<b>data</b>

|data|설명|
|:---|:---|
|options|검색 옵션|
|search| `input`태그에 입력된 값|

<br>

> <a href="#">form-search 컴포넌트</a>는 공통 컴포넌트에서 정리했습니다

<br>

<b>3. 카카오 검색 API</b>
> 옵션에 따라 카카오 검색 API 호출합니다.

3-1. store
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
> `axios`를 이용해 책 검색 API를 호출합니다.

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
> 카카오 검색 API 호출시,meta, documents로 구성된 JSON 객체를 가져옵니다.

> 책에 관련된 정보는 `state` 의 `books` 배열에 저장하고, 검색된 문서 수,마지막페이지인지 여부의 정보는 `state`의 `meta`에 저장하였습니다.



<br>

<b>** 책 검색 시, 불러온 책 데이터를 보여줄 때 문제점</b>

|문제점|
|---|
|검색되는 정보를 `state`의 `books`배열에 저장하다보니, 옵션에 따라 검색정보를 다르게 검색했을 때, 기존 배열에 누적되는 문제가 발생했습니다.
예시)옵션을 "통합"으로 설정하여 검색한 후,"제목"이나 "출판사" 등과 같은 다른 옵션으로 다시 재검색시, 기존에 존재하는 책의 정보가 없어지지않고 누적이 됩니다.|

|해결|
|---|
|`{reset:true}` 별도의 객체 속성을 주어, 해당 속성이 `true`일 경우에만 `books` 배열을 초기화하고, 그러지 않다면 기존 배열에 계속 누적시킵니다.
예시)옵션을 "통합"으로 설정하여 검색한 후,"제목"이나 "출판사" 등과 같은 다른 옵션으로 다시 재검색시, 기존에 존재하는 책의 정보를 초기화시켜줍니다.|

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
 > <div >성공적으로 API 호출 시, 아래 데이터 정보를 저장합니다.</div>
 > <a href="https://developers.kakao.com/docs/latest/ko/daum-search/dev-guide#search-book">카카오 개발자 센터 책 검색 참고</a>
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

<br>

3-2. 책 검색 버튼 클릭
> `store`의 `actioins` 함수 `SearchBooks`를 호출합니다.


<b>data & methods</b>

```js
// ~/pages/books/search/index.vue
      data () {
    return {
      errmsg: false,
      size: 20,
      isend: false,
      reset: false,
      currentpage: 1,
      showbtn: false,
      currentCount: 0
    }
  },
      methods:{
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
          resetBook (boolean) {
            this.reset = boolean
          },
          // 마지막페이지라면 더보기 버튼이 보여지지 않도록 합니다.
          showbutton () {
            this.isend || this.books.length === 0 ? this.showbtn = false : this.showbtn = true
          },
      }

```
|data|설명|
|:---|:---|
|onSearchBook|`책 검색 버튼`클릭 시, 기존데이터를 초기화하고 책 검색 API를 호출합니다.|
|isend|마지막 페이지인지 확인|
|reset|검색 데이터 초기화가 필요한지 확인|
|currentpage|현재 페이지|
|showbtn|`더보기 버튼` 보여줄 건지 확인|
|currentCount|현재 보여지는 페이지 수|


<br>

3-3. 책 검색 후, 더보기 버튼 클릭
> 책 검색 API 호출시 한번에 `20`개씩 데이터를 불러오고, `더보기 버튼`을 누를 시, 다음 데이터 `20`개를 가져오도록 구현하였습니다.
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
<br>

<b>metohds</b>

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
|methods|설명|
|:---|:---|
|addFetchBook|더 불러올 데이터가 있다면, 현재페이지를 증가시켜주고, 책 검색 API를 호출합니다.( 초기화할 필요 없으므로 `resetBook`에 `false`를 전달해줍니다.)|
<br><br>


<b>** 책 검색 시, 불러온 책 데이터를 보여줄 때 문제점</b>
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
      console.log(this.books)
      return this.initsearchBook()
    }
  }
 ```
 ```js
//  ~/store/book.js mutations
// 책의 정보와 meta에 저장된 정보를 초기화시켜줍니다.
  initsearchBook (state) {
    state.books = []
    state.meta = {}
  },
 ```
 <br>

#### 3-2. 원하는 책 검색 후 추가

검색한 책 중 원하는 책을 골라 책을 추가할 수 있도록 구현하였습니다.

<b>1. 검색한 책 추가 API</b>

1-1. store
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

 <b>책을 추가한 후, 추가한 책은 다른 라우터 `/books/_page`에 보여주도록 구현하였고, 해당 라우터에 진입시 `nuxt`의`asyncData` 훅을 사용해, 데이터를 불러오도록 구현하였으므로, 여기서는 별도의 `mutations`를 호출하여 `state` 값을 변화시키지 않았습니다. </b>

<br><br>

1-2 추가하기 버튼 클릭
> `store`의 `actioins` 함수 `createBook`를 호출합니다.


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
> <a href="#">알림 메세지 관련 내용</a>은 공통 컴포넌트 내용에 정리하였습니다.

<br>

***

<br>

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

<b>1. 믹스인으로 공통 요소 구현</b>

```js
import BookFetchMixin from '~/mixins/BookFetchMixin'
export default {
  mixins: [BookFetchMixin]
  ...
}
```
<br>

<b>`BookFetchMixin`</b>

<b>moputed</b>

```js
// ~/mixins/BookFetchMixin.js
  //  마운트될 때, 타이틀입력폼 태그에 포커스되도록 구현
 mounted () {
    setTimeout(() => {
      this.$refs.titleInput.focus()
    }, 400)
  }
```
<br>

<b>data</b>

```js
  data () {
      return {
        errmsg: ''
      }
    }
```

|data|설명|
|:---|:---|
|errmsg|오류 발생시 보여줄 메세지|

<br>

<b>computed</b>

```js
  computed: {
    ...mapGetters('books', ['getImagePath']),
    // 유효성 검사
    InputLenValid () {
      const data = ['title', 'isbn', 'authors', 'publisher']
      return inputLen(this.newBook, data, 100)
    },
    disabledBtn () {
      return !this.newBook.title || !this.newBook.authors || !this.newBook.contents || !this.InputLenValid
    },
    hasErr () {
      return this.errmsg.length > 0
    },
    hasImage () {
      return this.getImagePath.length
    }
  },
```

|computed|설명|
|:---|:---|
|InputLenValid|`title`, `isbn`, `authors`, `publisher` 데이터들의 입력값 길이가 100자 이내인지 확인합니다.<br>`InputLenValid`를 통해 유효성 검사를 통과하지 못 했을 경우, 알림창으로 에러 메세지를 보여줍니다.|
|disabledBtn|유효성 검사를 모두 통과하는지 확인합니다.<br>`<button>`태그의 `disabled`속성을 바운딩시켜, 유효성 검사를 모두 통과될 때에만 버튼을 클릭할 수 있도록 구현했습니다.|
|hasErr|`errmsg`데이터를 확인하여 유효성 검사에 어긋나는 오류가 있는지 확인합니다.|
|hasImage|`store`의 `getImagePath`에 이미지 데이터가 저장되어 있는지 확인합니다.|

<br>

> 유효성 검사에서 사용한 함수는 공통 구현 요소에 정리하였습니다. (<a href="#">validate.js</a>)

<br>


<b>methods</b>

```js
  methods: {
    // 데이터 불러오기
     async fetchData () {
      try {
        const data = new FormData()
        // 이미지(썸네일)
        const imageFile = this.mybook && this.mybook.thumbnail && !this.selectedFile ? this.mybook.thumbnail : this.selectedFile
        // 날짜
        const date = this.newBook && this.newBook.datetime ? new Date(this.newBook.datetime) : new Date()
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
      const imageFormData = new FormData()
      this.selectedFile = e.target.files[0]
      const maxSize = 1024 * 1024
      const imageType = /^image/.test(this.selectedFile && this.selectedFile.type)
      if (!imageType) {
        this.selectedFile = ''
        this.errmsg = '이미지 타입만 업로드해주세요.'
        return
      }
      if (this.selectedFile.size > maxSize) {
        this.selectedFile = ''
        this.errmsg = '용량을 초과하였습니다. 1mb 이하로 업로드해주세요.'
        return
      }

      imageFormData.append('photo', this.selectedFile)
      this.uploadImg(imageFormData)
        // eslint-disable-next-line no-return-assign
        .then(() => this.errmsg = '')
    },
    // 엑스버튼을 클릭할 시, 초기화 시켜주는 함수
    resetInput (e, data) {
      const target = e.target.parentNode.firstChild
      this.newBook[data] = ''
      target.focus()
    }
  }
```
|methods|설명|
|:---|:---|
|fetchData|책 데이터 추가/수정시 `state`의 `actions`함수를 호출하여 책 추가/수정 API 호출합니다.<br>`imageFile(이미지)`는 책 이미지(썸네일) 업로드 API로 가져온 데이터가 있다면 해당 이미지를 저장하고,기존에 가지고 있는 이미지가 있다면 기존 이미지를 저장합니다.<br>`date(날짜)`는 날짜 데이터가 없다면 `오늘 날짜`로 입력되도록 하였습니다.|
|onChangeImage|FormData 형식을 이용해 이미지 정보를 저장하여 책 이미지(썸네일) 업로드 API 호출합니다.(여기서는 이미지는 1개만 선택할수 있도록 하였습니다.)|
|resetInput|`data`의 `newBook`에서 해당 되는 속성을 찾아 초기화합니다.<br> `v-model`로 `newBook`의 값을 양방향 바운딩시켜주었기 때문에 해당 값을 빈값으로 초기화 할시, 입력폼에 있는 입력값도 초기화됩니다.<br>`엑스 버튼`으로 입력값을 초기화 시켜주고, 다시 해당 입력폼에 `focus`되도록 구현하였습니다.(`target`인 `input`태그에 `focus`)|
<br>

<b>세부 사항</b>

- 날짜는 `booktstrap-vue`에서 제공하는 <a hreef="#">b-form-datepicker</a> 사용

<br>

#### <div><b>4-2. 직접 책 추가</b></div>

<b>1. 책 추가 시, 썸네일 이미지 처리</b>

> <a href="#">책 업로드 API</a>로 가져온 이미지를 보여줍니다.

```html
<!-- ~/components/form/BookAdd.vue -->
<template>
  ...
 <!-- 책 이미지 추가 -->
    <div class="file_container add">
      <!-- 로딩 스피너 -->
      <LoadingBar v-if="initLoading" position />
      ...
      <!-- 책 업로드 API로 가져온 이미지가 있다면 해당 이미지를 보여줍니다. -->
      <div v-if="hasImage" class="photos">
        <div class="images" :class="{'imgErr':hasErr}">
          <img :src="getImagePath" alt="썸네일 이미지">
        </div>
        <div>
          <!-- 이미지 삭제 버튼 -->
          <button class="deletebtn" type="button" @click="onRemoveImage">
            <i class="fas fa-plus-circle"></i>
          </button>
        </div>
      </div>
    </div>
    ...
</template>

```
<br>

> 책의 썸네일 이미지를 미리 보여줄 때, `엑스 버튼`이 보이고, 해당 버튼을 누르면 미리 보여주고 있는 책의 썸네일 이미지를 없애줍니다.
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
    state.imagePath = ''
  },
```
<br>


<b>2. 책 추가 API</b>

책 이미지(썸네일) 업로드 API를 호출하여 저장한 이미지와 함께 책를 추가하는 API를 호출하여 데이터를 저장합니다.

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
> `axios`를 이용해 책 추가 API를 호출합니다.

> <b>책을 추가한 후, 추가한 책은 다른 라우터 `/books/_page`에 보여주도록 구현하였고, 성공적으로 책 추가시, 라우터를 변경하도록 구현하였습니다.<br>
` this.$router.push('/books/1')`

> <b>해당 라우터에 진입시 `nuxt`의`asyncData` 훅을 사용해, 데이터를 불러오도록 구현하였으므로, 여기서는 별도의 `mutations`를 호출하여 `state` 값을 변화시키지 않았습니다. </b>

<br>
1-2. 추가하기 버튼 클릭

> 책 추가 버튼 클릭 시, 공통으로 구현한 `BookFetchMixin` 의 `fetchData` 함수를 호춣하여 책 데이터 추가 API를 호출합니다.

```html
<!-- ~/components/form/BookAdd.vue -->
<template>
  <form class="form_content addform" @submit.prevent="onaddBook">
    ...
    <button type="submit" class="round-btn red addbtn" :disabled="disabledBtn || errmsg.length !== 0">
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
> `fetchData`함수는 <a href="#">BookFetchMixin.js</a>에 공통적으로 구현하였습니다.

#### <div><b>4-3. 추가한 책 수정</b></div>
|컴포넌트|라우터|
|---|---|
|components/book/Edit.vue|books/b/_id|
<br>

<b>1. 책 수정 시, 썸네일 이미지 처리</b>

> <a href="#">책 업로드 API</a>로 가져온 이미지를 보여줍니다.

```html
<!-- ~/components/form/BookAdd.vue -->
<template>
  ...
         <!-- 책 이미지 수정 -->
        <div class="file_container edit">
            <!-- 로딩 스피너 -->
          <LoadingBar v-if="initLoading" position />
         ...
          <div class="photos">
            <div v-if="hasThunbnail" class="images" :class="{'imgErr':hasErr}">
              <img :src="mybook.thumbnail" alt="썸네일 이미지">
            </div>
            <div v-else class="images" :class="{'imgErr':hasErr}">
              <img :src="hasImagePath" alt="썸네일 이미지">
            </div>
          </div>
        </div>
    ...
</template>

```
<br>

<b>computed</b>

```js
 computed: {
    hasThunbnail () {
      return this.mybook.thumbnail && !this.hasImage
    },
    hasImagePath () {
      return this.hasImage ? this.getImagePath : '/images/sample_book.jpg'
    }
  }
```

|computed|설명|
|:---|:---|
|hasThunbnail|기존 데이터가 이미 이미지(썸네일) 데이터 유무를 확인하여, 기존 이미지를 보여줍니다.|
|hasImagePath|`이미지 업로드 API`를 호출하여 저장한 이미지가 있다면, `state`의 `imagePath`에 저장된 이미지를 보여줍니다.<br>기존 데이터에 저장된 이미지가 없고,`state`의 `imagePath`에 저장된 이미지도 없다면,샘플 이미지를 보여줍니다.|

<br>


<b>2. 책 수정 API</b>

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
> `axios`를 이용해  책 수정 API를 호출합니다.

`commit`를 이용해 `mutatonis`을 호출합니다.


|mutations|
|---|
|loadbook|
```js
//store/book.js  mutations
  loadbook (state, bookData) {
    state.book = bookData
  }
```
>`state`의 `book` 객체에 데이터를 저장합니다.

<br>

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
|book|
```js
//store/book.js state
  book: {}
```

<br>

1-2. 수정하기 버튼 클릭

> 책 수정 버튼 클릭 시, 공통으로 구현한 `BookFetchMixin` 의 `fetchData` 함수를 호춣하여 책 데이터 수정 API를 호출합니다.

```html
<!-- ~/components/book/Edit.vue -->
<template>
<CommonModal class="book_form">
    ...
    <div slot="body">
      <form class="form_content" @submit.prevent="onEditBook">
        ...
        <button type="submit" class="round-btn red editbtn" :disabled="disabledBtn || errmsg.length !== 0">
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
> `fetchData`함수는 <a href="#">BookFetchMixin.js</a>에 공통적으로 구현하였습니다.

<br>

#### <div><b>4-4. 추가한 책 삭제</b></div>
|라우터|
|---|
|books/b/_id|


<b>1. 책 삭제 API</b>

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
> `axios`를 이용해 책 삭제 API를 호출합니다.

> <b>책을 삭제한 후, 성공적으로 책 삭제시, 라우터를 변경하도록 구현하였습니다.<br>
` this.$router.push('/books/1')`

> 해당 라우터에 진입시 `nuxt`의`asyncData` 훅을 사용해, 데이터를 불러오도록 구현하였으므로, 여기서는 별도의 `mutations`를 호출하여 `state` 값을 변화시키지 않았습니다. </b>

<br>

1-2. 삭제 버튼 클릭

삭제 확인 알림창에서 "네" 클릭 시, `store`의 `actioins` 함수 `deleteBook`를 호출합니다.

> <a href="#">삭제 확인 알림창</a>은 공통 구현 요소에 정리하였습니다.

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
    <FormAlert v-if="alert" :title="getBook && getBook.title" :confirm="`삭제`" @onagree=" agree" @ondisagree="disagree" />
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

***

<br>

### <div id="get_data"><b>5. 책 보여주기</b></div>

#### <div>5-1. 공통 내용</div>

|라우터|구현 내용|
|---|---|
|books/_page|<a href="#books">내가 추가한 책 보여주기</a>|
|books/others/_page|<a href="#other_books">다른 사용자가 추가한 책 보여주기</a>|
|books/hashtags/_page|<a href="#tags">태그별로  책 보여주기</a>|
|books/search/_page|<a href="#other_search">다른 사용자의 책 검색하여 보여주기</a>|

<br>

<b>1. 페이지네이션 구현</b>
> `bootstrap-vue`에서 제공하는 `BPagination` 을 사용하여 페이지네이션 구현(<a href="https://bootstrap-vue.org/docs/components/pagination#pagination">bootstrap-vue 페이지네이션 바로가기</a>)
```html
<template>
  ...
   <BookPagination :total-page="totalPag" @pagination="pagination" />
</template>
```
<br>

<b>Pagination 커포넌트</b>
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
<br>

<b>props</b>

```js
  props: {
    // 전체 페이지
    totalPage: {
      type: Number,
      required: true
    }
  }
```
|props|타입|설명|
|:---|:---|:---|
|totalPage|Number|책 데이터를 불러올 때, 전체 페이지 수인 `totalPage`를  `props`로 내려줍니다.|

<br>


<b>data</b>
```js
  data () {
    return {
      perPage: 1,
      // 현재 페이지
      currentPage: 1
    }
  }
```

|data|설명|
|:---|:---|
|perPage| 페이지당 행(전체 페이지) 수|
|currentPage|현재 페이지|

<br>

<b>computed</b>

```js
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
  }
 ```
 |computed|설명|
|:---|:---|
|showPage|`props`로 받은 전체페이지의 수가 1페이지면 페이지네이션을 보여주지 않고, 2페이지 이상일 경우에만 페이지네이션을 보여줍니다.|
|rows|전체 페이지 수|

<br>

<b>watch</b>

 ```js
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
  }
 ```
 |watch|설명|
|:---|:---|
|$route|라우터를 관찰하여 페이지네이션의 번호를 클릭시, 책 데이터를 불러올 때 `state`의 `currentPage`에 저장한 현재페이지와 비교하여 현재 페이지를 활성화시켜줍니다. |

<br>

<b>methods</b>

 ```js
  methods: {
    pageClick (page) {
      this.$emit('pagination', page)
    }
  }
}
```
|methods|설명|
|:---|:---|
|pageClick|페이지네이션의 페이지 클릭 시, 상위 컴포넌트에 클릭한 페이지의 번호와 함께 이벤트를 전달해줍니다.|

<br>

<b>2. 믹스인으로 공통 요소 구현</b>
```js
import PaginationFetchMixin from '~/mixins/PaginationFetchMixin'
export default {
  mixins: [PaginationFetchMixin]
}
```
<br>

<b>`PaginationFetchMixin`</b>

> `nuxt`의 `asyncData`훅을 이용해 책 데이터를 가져옵니다.

>  `1페이지당` 12개의 데이터를 호출하여 가져오도록 구현하였습니다.

> `params`를 `page`로 설정하여 라우터를 변경할 수 있도록 구현하였습니다.
예시) 1페이지: /books/1, 2페이지: /books/2 ...

```js
   async asyncData ({ store, params, route }) {
    try {
      let total
      let totalPage
      const page = params.page
      let data = { page: page - 1, route: route.name }
      // 검색한 책 데이터 보여주기
      if (route.name === 'books-search-page') {
        data = { ...data, search: encodeURIComponent(route.query.search), target: encodeURIComponent(route.query.target) }
        // 해당 해시태그를 가지고 있는 책 데이터 보여주기
      } else if (route.name === 'hashtags-page') {
        data = { ...data, name: encodeURIComponent(route.query.name) }
      }
      await store.dispatch('books/fetchBooks', data)
        .then((res) => {
          // 전체 데이터 갯수
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

<b>computed</b>

 ```js
  computed: {
    ...mapGetters('books', ['getBooks']),
    hasBook () {
      return this.getBooks.length
    }
  }
 ```
|computed|설명|
|:---|:---|
|hasBook|책 데이터가 있는지 확인합니다.|

<br>

<b>methods</b>

 ```js
  methods: {
    ...mapActions('books', ['fetchBooks']),
   pagination (page) {
      switch (this.$route.name) {
        // 내 책 페이지
        case 'books-page':
          return this.$router.push(`/books/${page}`)
        // 다른 사용자의 책 페이지
        case 'books-others-page':
          return this.$router.push(`/books/others/${page}`)
        // 검색 페이지
        case 'books-search-page':
          return this.$router.push(`/books/search/${page}?search=${this.getSearch.selectedOption}&target=${encodeURIComponent(this.getSearch.data)}`)
        // 해시태그로 검색한 페이지
        case 'hashtags-page':
          return this.$router.push(`/hashtags/${page}?name=${this.$route.query.name}`)
        default:
          break
      }
    }
  }
```
|methods|설명|
|:---|:---|
|pagination|하위 컴포넌트인 `Pagination.vue`에서 페이지를 클릭 시, 이벤트를 발생시키면, 해당 함수를 호출합니다. <br> 라우터의 `name`속성을 이용해, 페이지 라우터를 이동시킵니다. |

<br>

<b>3. 책 조회 API</b>

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
        res = await this.$axios.get(`books?page=${page}`)
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
        res = await this.$axios.get(`hashtags?page=${page}&name=${name}`)
        break
      default:
        break
    }
    commit('loadBooks', { books: res.data.books, page })
    return res
  }
```
> `axios`를 이용해 책 조회 API를 호출합니다.

`commit`를 이용해 `mutatonis`을 호출합니다.

<br>

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
>  `state`의 `books` 배열에 데이터를 저장합니다.

<br>

|getters|
|---|
|getBook |
```js
//store/book.js  getters
  getBooks (state) {
    return state.books
  }
```
> `state`의 `books` 배열을 가져옵니다.

<br>


|state|
|---|
|books|
```js
//store/book.js state
 books: [],
 currentPage: 0
```
 > <div id="state_user"> state의 books 배열에는 아래 정보를 저장합니다.</div>

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

<br>

3-2. 책 데이터 가져오기
> `nuxt`의 `asyncData`훅으로 데이터를 가져오고 해당 내용은 <a href="#">PaginationFetchMixin.js</a>에 공통적으로 구현하였습니다.



<br>

<b>4. 책 조회 API를 통해 가져온 책 보여주기</b>

- 가져온 책 데이터가 존재한다면, 책 데이터를 보여줍니다.
```html
<!-- ~/components/books/_page.vue -->
<template>
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
    <div v-else>
      <BookEmpty />
    </div>
  ...
</template>
```
<br>

<b>bookCard 컴포넌트</b>

<b>props</b>

```js
  props: {
    book: {
      type: Object,
      required: true
    }
  }
```

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

- 가져온 책 데이터가 존재하지 않는다면,
다른 컴포넌트를 보여줍니다.

> `book-empty component`를 구현하여, 책 데이터가 없을 경우, 해당 컴포넌트를 보여주도록 구현하였습니다.

<br>

<b>book-empty 컴포넌트</b>

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

> 책 보여주기 공통 내용에서 정리하였습니다.

<br>

#### <div>5-3. 다른 사용자가 추가한 책 보여주기</div>
|컴포넌트|라우터|
|---|---|
|components/books/Card.vue|books/others/_page|
|components/books/Empty.vue|books/others/_page|
|components/books/Pagination.vue|books/others/_page|
<br>

> 책 보여주기 공통 내용에서 정리하였습니다.

<br>

#### <div>5-4. 검색한 책 보여주기</div>

|컴포넌트|라우터|쿼리|
|---|---|---|
|components/books/Card.vue|books/search/_page|search,target|
|components/books/Empty.vue|books/search/_page|search,target|
|components/books/Pagination.vue|books/search/_page|search,target|

<b>1. 쿼리를 이용하여 책을 검색하도록 구현하였습니다.</b>

|쿼리|설명|
|:---:|:---|
|search|검색 옵션 <br>ex)책제목,저자|
|target|검색 내용|

<br>

<b>wartch</b>
> `watch`로 `쿼리` 변화를 감지하여 `책제목` , `저자` 중 옵션과 검색 내용을 `state`의 `search` 객체에 저장합니다.

```js
// ~/pages/book/search/index.vue
export default {
 watch: {
    '$route.query': {
      handler (query) {
        this.updateSearch({
          data: encodeURIComponent(query.target),
          selectedOption: query.search
        })
      },
      deep: true,
      immediate: true
    }
  },
  watchQuery: ['search', 'target']
  methods: {
   ...mapMutations('books', ['updateSearch'])
  }
}
```

|문제점|
|---|
|`nuxt`의 `asyncData`나 `fetch` 훅은 기본적으로 쿼리 문자열 변경에 대한 감지에 대해 비활성화 되어있어, 쿼리가 변경되도 인식하지 못합니다.|

|해결|
|---|
|` watchQuery: ['search', 'target']` 속성으로 쿼리 변경 사항을 확인하고, 해당 쿼리 변경시 `astncData`훅이 호출될 수 있도록 구현하였습니다.<br>(<a href="https://nuxtjs.org/docs/2.x/components-glossary/pages-watchquery">`nuxt` `watchQuery`에 관한 문서</a>)|

<br>

<b>2. 다른 사용자의 책 검색하기</b>
> `메뉴`에서 `다른 사용자 책 검색`을 클릭하면 입력폼이 보여지고, `책제목` , `저자` 두가지 옵션으로 검색할 수 있도록 구현하였습니다.

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
<br>

<b>methods</b>

```js
  methods: {
    ...mapMutations('books', ['updateSearch']),
    showSearchForm () {
      this.search.showsearchState = !this.search.showsearchState
       // 기존 검색 데이터 초기화
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
        `/books/search/1?search=${this.getSearch.selectedOption}&target=${encodeURIComponent(this.search.input)}`
      )
    }
  }
}
```
|methods|설명|
|:---|:---|
|showSearchForm|검색창을 보여주고 기존 검색 데이터는 초기화합니다.|
| onselectedOption |검색 옵션(책 제목, 저자 등)을 `state`의 `search`객체에 저장합니다.|
|onsearchBook|검색 내용을 `state`의 `search`객체에 저장하고 라우터를 이동시킵니다.|

<br>


|mutations|
|---|
|updateSearch|
```js
//store/book.js  mutations
 updateSearch (state, payload) {
    Object.keys(payload).forEach(key => state.search[key] = payload[key])
  }
```
>  `state`의 `search` 객체에 데이터를 저장합니다.

<br>


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

<b>1. 쿼리를 이용하여 해시태그별 책을 검색하도록 구현하였습니다.</b>

|쿼리|설명|
|:---:|:---|
|name|태그 이름|
> /hashtags/페이지번호?name="태그 이름"

> `watch`로 `쿼리` 변화를 감지하도록 하였습니다.
```js
// ~/pagees/hashtags/_page.vue
export default {
  watchQuery: ['name']
}
```


### <div id="get_data"><b>6. 책 상세 보기</b></div>
|컴포넌트|라우터|
|---|---|
|components/books/CardDetail.vue|books/_page|
|components/books/CardDetail.vue|books/others/_page|

#### <div>6-1. 공통 내용</div>
> `nuxt`의 `asyncData`훅을 이용해
책 데이터를 가져오도록 구현하였습니다.

<br>

<b>1. 성공적으로 책 데이터를 가져왔다면, 책 데이터를 보여줍니다.</b>
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

<b>BookCardDetai 컴포넌트</b>

<b>props</b>

```js
  props: {
    book: {
      type: Object,
      required: true
    }
  }
```
|props|타입|설명|
|:---|:---|:---|
|book|Object|책 조회 API를 호출하여 가져온 책 데이터|

<br>

#### <div>6-2. 나의 책 상세보기</div>
|컴포넌트|라우터|
|---|---|
|components/books/CardDetail.vue|books/_page|

<b>1. 단일 책 데이터 조회 API</b>

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
> `axios`를 이용해 단일 책 조회 API를 호출합니다.

`commit`를 이용해 `mutations`을 호출합니다.
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
> `state`의 `book` 객체에 데이터를 저장합니다.
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
> `state`의 `book` 객체를 가져옵니다.

|state|
|---|
|book|
```js
//store/book.js state
  book: {}
```
<br>

 >  `state`의 `book` 객체에는 아래 정보를 저장합니다.
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

<b>1. 단일 책 데이터 조회 API</b>

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
> `axios`를 이용해 책 조회 API를 호출합니다.

`commit`를 이용해 `mutations`을 호출합니다.

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
>  `state`의 `book` 객체에 데이터를 저장합니다.
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
> `state`의 `book` 객체를 가져옵니다.

|state|
|---|
|book|
```js
//store/book.js state
  book: {}
```
<br>

 >  `state`의 `book` 객체에는 아래 정보를 저장합니다.
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

***
<br>