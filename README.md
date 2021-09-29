# libraryApp
# client

## vue +nuxt
-  nuxt 프레임워크를 사용하였습니다.
- vue 는 nuxt 사용하기 위해 2.x 버전을 사용하였습니다.
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
   - 내가 추가한 책들 중에서 책 수정 및 삭제 기능 구현
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
<br>

### 3-2. css
|구현 방법|구현 내용|
|---|---|
|CSS|static/css/common.css 만들어 공통 요소로 초기화|
|CSS|static/css/style.css 만들어 css 요소 정리|

#### css 공통 요소
- input 태그 엑스 박스 제거
>  참고로 IE(인터넷 익스플로러) 10 이상에서 Input text Box에 포커스 되면 우측에 x 표시가 생기는데, 이는 직접 구현하였으므로, IE(인터넷 익스플로러) 우측에 x 표시가 생기지 않도록 초기화시켜주었습니다.</div>

```css
/* static/css/style.css */
/* ms 인풋요소 엑스박스제거 */
::-ms-clear{display: none;}
```
<br>


### 3-3. `vuex` `store` 사용

<br>



[test](https://nuxtjs.org/docs/directory-structure/components/)
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
        // 인증이 필요한 상태이고,로그인이 이미 되어 있으면 메인페이지로 이동
        return redirect('/')
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
      <div v-else>
        <p>
          <b>방문하시려는 페이지의 주소가 잘못 입력되었거나,
            변경 혹은 삭제되어 찾을 수 없습니다.</b>
        </p>
        <p>홈으로 이동해주세요</p>
        <NuxtLink class="primary" to="/">
          <span>홈</span>
        </NuxtLink>
      </div>
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

export { validLength, validEmail}
```

<br>

#### 이미지
```js
// ~/utils/image.js
// 카카오 api 호출시 가져오는 썸네일 이미지 크기 수정
const resizeImage = (url, size = 800) => {
  const src = url.replace('/thumb/R120x174', `/thumb/R${size}x${size}`)
  return src
}
// 이미지 로드 확인
const loadImage = (src) => {
  return new Promise((resolve) => {
    const img = document.createElement('img')
    img.src = src
    img.addEventListener('load', () => {
      // 완료!
      resolve()
    })
  })
}
export { resizeImage, loadImage }

```


<br>

### 3-5. 공통 컴포넌트
#### 알림창(알림 메세지)
 `eventbus` 를 이용하여 구현하였습니다.

`bus.js`
```js
// ~/utils/bus.js
import Vue from 'vue'
export default new Vue()
```
<br>

`default.vue`
```html
<!-- ~/layouts/default.vue -->
<template>
  ...
    <CommonAlertMsg :alert-state="alertState" :data="data" :bgcolor="bgcolor" />
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
      data: '',
      bgcolor: '',
    }
  },
    created () {
    // 알림창 보이기
    bus.$on('on:alert', ({ data, bgcolor }) => {
      this.bgcolor = bgcolor
      this.data = data
      this.onalert()
    })
    // 알림창 끄기
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

`common-alert-msg 컴포넌트`

```html
<!-- ~/components/common/alertMsg.vue -->
<!-- transition으로 애니메이션 효과 -->
  <transition name="upSlide">
    <div v-if="alertState" class="alertmsg" :style="{ 'background-color': bgcolor }">
      {{ data }}
    </div>
  </transition>
```
> <a href="https://vuejs.org/v2/api/#transition">vue transition으로 애니메이션 효과를 주어 알림창을 보여줍니다.</a>

<br>

props
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
|alertState|Boolean|알람메세지 보여줄지 여부|
|data|String|알림 메세지 내용|
|bgcolor|String|알림 메세지 창 배경색|


<br>

#### 삭제/수정 확인 알림창

`form-alert 컴포넌트`

```html
<!-- ~/components/form/Alert.vue -->
<template>
  <CommonModal class="alert_main">
    <div slot="header">
      <b class="title">{{ title }}</b> 을(를) {{ confirm }}하시겠어요??
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

props

```js
//  ~/components/form/Alert.vue
  props: {
    title: {
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
|title|String|삭제/수정할 데이터의 제목|
|confirm|String|삭제/수정 여부|

<br>

#### 검색 폼


`form-search 컴포넌트`

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


props
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


methods && data
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
|selected|옵션창에 마우스로 선택했는지 여부|
|selectedOption|`props`로 받은 `options` 배열 중 내가 클릭하여 선택한 데이터(`props`로 받은 `options` 배열중 첫번째 값을 기본값으로 설정하여 보여줍니다.)|

|methods|설명|
|:---|:---|
|changeSelect|검색 옵션 수정시, 수정한 옵션을 `$emit`을 이용해 상위 컴포넌트에 전달해줍니다. |

<br>

events

|events|설명|
|:---|:---|
|searchBook|`submit`이벤트 발생시, `$emit`을 이용해 상위 컴포넌트에 전달해줍니다. |
|input|`input`이벤트 발생시,`$emit`을 이용해 입력폼에 입력된 값을 상위 컴포넌트에 전달해줍니다. |


<br>

#### 이미지 미리보기(보여주기)
> 책 이미지(썸네일)와 사용자의 이미지(썸네일)를 보여줍니다.

> 이미지는 `amazon s3`에 저장하여 해당 이미지를 보여줍니다.

`form-image 컴포넌트`
```html
<!-- ~/form/image.vue -->
<template>
  <div>
    <div class="file_container">
      <!-- 로딩 -->
      <LoadingBar v-if="initLoading" position />
      <div class="txt">
        <label for="fileinput"><span class="round-btn yellow"><i class="far fa-file-image"></i>{{ imageTitle }}</span></label>
        <input id="fileinput" ref="file" style="display:none" type="file" @change="onChangeImage">
      </div>
      <!-- 이미지 사진 보여주기 -->
      <div class="photos">
        <template v-if="!showThumbnail">
          <div class="images" :class="{'imgErr':hasErr}">
            <img :src="hasImagePath" alt="썸네일 이미지">
          </div>
        </template>
        <template v-else>
          <div class="images" :class="{'imgErr':hasErr}">
            <img v-if="hasThunbnail" :src="thumbnail" alt="썸네일 이미지">
            <img v-else :src="hasImagePath" alt="썸네일 이미지">
          </div>
        </template>
      </div>
    </div>
    <div class="err">
      {{ imageErr }}
    </div>
  </div>
</template>
```
<br>

props

```js
  props: {
    imageTitle: {
      type: String,
      required: true
    },
    thumbnail: {
      type: String,
      required: false
    },
    editProfile: {
      type: Boolean,
      required: false
    },
    showThumbnail: {
      type: Boolean,
      required: false
    }
  }
```
|props|타입|설명|
|:---|:---|:---|
|imageTitle|String|라벨(label) 제목|
|thumbnail|String|썸네일(이미지) 주소<br>ex)https://myimageslist.s3.us-west-1.amazonaws.com/1632219858319|
|editProfile|Boolean|사용자 이미지를 수정하는 지 여부|
|showThumbnail|Boolean|썸네일(이미지)을 보여줄지 여부|

<br>

computed

```js
  computed: {
    ...mapState(['initLoading']),
    ...mapState('books', ['imageErr', 'imagePath']),
    hasImage () {
      return !!this.imagePath
    },
    hasThunbnail () {
      return this.thumbnail && !this.hasImage
    },
    hasImagePath () {
      if (this.hasImage) { return this.imagePath } else if (this.editProfile) { return '/images/user3.png' } else { return '/images/sample_book.jpg' }
    },
    hasErr () {
      return !!this.imageErr
    }
  },
```
|computed|설명|
|:---|:---|
|hasImage|`state`의 `imagePath`에 이미지 주소가 저장되어 있는지 확인|
|hasThunbnail|`props`로 내려준 썸네일(이미지)가 있는지 확인|
|hasImagePath|조건에 따라 보여줄 이미지|
|hasErr|이미지 유효성 검사를 진행하여, 보여줄 에러 메세지가 있는지 확인|

<br>


`이미지 업로드 API`
> `actions`함수 `uploadImg`를 호출합니다.
```js
  methods: {
    ...mapMutations('books', ['updateState']),
    ...mapActions('books', ['uploadImg']),
    // 이미지 미리보기 업로드
    onChangeImage (e) {
      const imageFormData = new FormData()
      this.selectedFile = e.target.files[0]
      const maxSize = 1024 * 1024
      const imageType = /^image/.test(this.selectedFile && this.selectedFile.type)
      // 이미지 타입 확인
      if (!imageType) {
        this.selectedFile = ''
        this.updateState({ imageErr: '이미지 타입만 업로드해주세요.' })
        return
      }
      // 이미지 용량 확인
      if (this.selectedFile.size > maxSize) {
        this.selectedFile = ''
        this.updateState({ imageErr: '용량을 초과하였습니다. 1mb 이하로 업로드해주세요.' })
        return
      }
      // 유효성 검사 확인 후, 이미지 amazon s3에 저장하기
      imageFormData.append('photo', this.selectedFile)
      this.uploadImg(imageFormData)
        .then(() => this.updateState({ imageErr: '' }))
    }
  }
```

<br>

store

|actions|
|---|
|uploadImg|

```js
//store/books.js actions
  async uploadImg ({ commit }, payload) {
    try {
      // 로딩 시작
      commit('changeLoading', true, { root: true })
      const res = await this.$axios.post('thumbnail', payload)
      commit('updateState', {
        imagePath: res.data
      })
      // 이미지 로드된 후 이미지 보여주기
      await loadImage(res.data || '')
    } catch (error) {
      console.error(error)
    } finally {
      // 로딩 종료
      commit('changeLoading', false, { root: true })
    }
  },
```
> `axios`를 이용해 이미지 업로드 `API`를 호출합니다. <br>
> `API`를 호출해 데이터를 가져온다면 `commit`으로 `mutations`를 호출하여 `state`의 `imagePath` 에 이미지 주소를 저장합니다.

> `loadImage` 함수는 위의 공통 구현 요소에서 정리하였습니다.

<br>

|mutations|
|---|
|updateState|
```js
//store/books.js mutations
 // state 업데이트
  updateState (state, payload) {
    Object.keys(payload).forEach((key) => { state[key] = payload[key] })
  },
```

<br>


|state|
|---|
|imagePath|
|imageErr|

```js
//store/books.js state
  // 이미지 파일
  imagePath: '',
  // 이미지 오류
  imageErr: '',
```
<br>

 > <div id="state_user">성공적으로 이미지 업로드 API 호출 시, 아래  정보를 저장합니다.</div>
```js
// imagePath에저장되는 사용자의 정보 예시.
  imagePath:"https://am-clone.s3.ap-northeast-2.amazonaws.com/1623252830675"
```
<br>

<b>* 이미지를 업로드 할 때 문제점</b>

 |문제점|
 |---|
 |`onChangeImage`함수로 이미지를 업로드하는 API를 호출하여 이미지의 주소를 가져와 `state`의 `imagePath`에 저장하도록 구현하였습니다. 다른 라우터로 이동시에도 `imagePath`에 값이 그대로 저장되어 있기 때문에, 프로필 정보 수정하기 위해 해당 라우터로 진입시,`imagePath`에 저장된 이미지가 그대로 보여지게 됩니다.  |

|해결|
 |---|
 |`created`훅을 이용해 인스턴스가 생성된 후 `state`의 `imagePath`값을 초기화시켜줍니다.|

 ```js
//  ~components/form/image.vue
  created () {
    // 이미지 초기화
    if (this.hasImage) {
      this.updateState({
        imagePath: ''
      })
    }
  },
   methods: {
 ...mapMutations('books', ['updateState']),
   }
 ```

<br>

#### 로딩 스피너

`loading-bar 컴포넌트`
```html
<!-- ~/component/LoaindBar.vue -->
<template>
  <div class="loading-spin" :class="{'absolute' : 'position' }">
    <i class="fas fa-spinner fa-spin" :style="{ color }"></i>
  </div>
</template>
```
<br>

props
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

####  IE 체크 확인창
`ie-check 컴포넌트`
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
> <a hreef="#">is-iexplorer</a> 라이브러리를 이용하여 익스플로러 유무를 확인합니다.

<br>

## 4. 구현 세부 내용 정리
<br>

### <div id="register"><b>1. 회원가입/로그인 구현</b></div>

|컴포넌트|라우터|
|---|---|
|components/form/Register.vue|auth/register|
|components/form/Login.vue|auth/login|

<br>

### <div>회원가입</div>

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
<br>

data
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


<br>


|유효성 검사 리스트|
|:---|
|이메일,닉네임,비밀번호 길이 검사|
|이메일 유효성 검사|
|`data`의 `password` 의 값과 `data`의 `confirm_password` 의 값이 일치하는지 검사|


<br>

computed

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
|disabledBtn|위의 유효성 검사를 모두 통과하는지 확인합니다. <br>`<button>`태그의 `disabled`속성을 바운딩시켜, 유효성 검사를 모두 통과될 때에만 버튼이 활성화됩니다.|

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
> `class`를 바운딩하여 필수 입력폼에 입력값이 없다면 * 표시를 보여줍니다.

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
> `mounted ` 라이프사이클 훅 함수를 이용해 마운트 된 후 이메일 input 입력폼에 포커스되도록 구현하였습니다.

<br><br>

<b>5. 회원가입 API</b>

5-1. 회원가입 버튼 클릭
> `store`의 `actioins` 함수 `register`를 호출합니다.
```js
// <!--components/form/Register.vue -->

  methods: {
    ...mapActions('user', ['register']),
    async UserRegister () {
      try {
        const userData = {
          email: this.user.email,
          username: this.user.username,
          password: this.password
        }
        await this.register(userData)
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
    // 입력값 초기화
    resetInput () {
        Object.keys(this.user).forEach((key) => { this.user[key] = '' })
        this.password = ''
        this.confirm_password = ''
      },
      // 이메일 입력폼에 포커스
      inputfocus () {
        this.$refs.emailinput.focus()
      }
  }
```
<br>

5-2. store

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


### <div>로그인</div>

<b> 1. 로그인 구현은 이메일로 로그인,카카오로 로그인,구글로 로그인 세가지 방법으로 구현하였습니다.</b>
 > 서버에서 passport를 사용하여 구현<br>
 (<a href="https://www.passportjs.org/packages/passport-kakao/">`passport`카카오 로그인 참고 문서 바로 가기</a>)<br>
 (<a href="https://www.passportjs.org/packages/passport-google-oauth20/">`passport`구글 로그인 참고 문서 바로 가기</a>)

 <br>

<b>2. 로그인 API</b>

2-1. 로그인 버튼 클릭
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

<br>


2-2. store

<b>store</b>

|actions|
|---|
|login|

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


** <b>사용자 정보 저장시 문제점</b>

 |문제점|
 |---|
 |`store`의 `state`에 저장된 사용자의 정보로 로그인 유무를 확인합니다. <br>새로고침시, `state`에 저장된 `user`의 정보는 유지 되지 않는 문제가 발생합니다.|

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

### <div id="user_info"><b>2. 사용자 정보(프로필 정보, 비밀번호) 수정</b></div>

|라우터|
|---|
|user/info|


### <div>프로필 정보 수정</div>

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
        <!-- 사용자의 정보를 가져와서 value 를 바운딩시켜 사용자의 이메일을 보여줍니다. -->
        <p><input id="email" class="readonly" readonly :value="user.email" type="text"></p>
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
  //  state 의 user 정보를 가져옵니다.
    ...mapState('user', ['user']),
 },
  created () {
  // 기존 사용자의 닉네임을 보여줍니다.
    this.username = this.user.username
}
```
> `created`훅으로 인스턴스가 생성된 후, `username`에 기존 사용자의 닉네임을 저장하여 기존 데이터를 보여줍니다.

<br>

<b>2. 사용자 프로필 이미지 수정</b>

이미지 업로드 API를 호출하여  `amazon s3 버킷`에 이미지를 저장 후, 주소를 불러와서 이미지를 보여줍니다.

> <a href="#">해당 내용은 공통 컴포넌트 이미지 미리보기(보여주기)에서 정리하였습니다.</a>
```html
<!-- ~/pages/user/info.vue -->
<template>
  <div class="profile_content">
    <div class="profile_box">
      <h2>프로필 정보 수정</h2>
      <form class="form_content" @submit.prevent="onChangeProfile">
        ...
        <!-- 프로필 이미지 수정 및 이미지 보여주기 -->
        <FormImage image-title="프로필 이미지" :thumbnail="user.thumbnail || ''" show-thumbnail edit-profile />
        <button class="round-btn" type="submit" :disabled="disabledBtn">
          프로필 정보 수정
        </button>
      </form>
    </div>
    ...
</template>
```

 <br>

<b>3. 프로필 정보 수정 API</b>

> 이미지 업로드 API를 통해 저장된 이미지와, 사용자의 닉네임과 함께
프로필 정보를 수정합니다.

3-1. 프로필 정보 수정 버튼 클릭

> `store`의 `actioins` 함수 `updateProfile`를 호출합니다.
```js
// ~/pages/user/info.vue
...mapActions('user', ['updateProfile']),
  methods: {
    // 프로파일 정보 수정(닉네임,프로파일 이미지 수정)
    onChangeProfile () {
      const userinfo = { username: this.username, thumbnail: this.imagePath}
      this.updateProfile(userinfo)
      // 프로파일 정보 수정 후 라우터 이동
      this.$router.push('/user/profile')
    }
```
> 이미지 업로드 API를 호출하여 저장한 이미지와
사용자 닉네임과 함께 프로필 정보를 수정합니다.

<br>

3-2. store

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


### <div>비밀번호 수정</div>

<b>1. 카카오 로그인과 구글 로그인시에는 비밀번호 수정이 필요 없기 때문에 로그인 시 `state`의 `user` 객체에 저장한 `provier` 속성을 이용해, 비밀번호 변경 가능 여부를 확인하도록 구현하였습니다.</b>

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
      <div v-if="user && !user.provider" class="profile_box">
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

 <b>2. 비밀번호 수정 API</b>

> 이미지 업로드 API를 통해 저장된 이미지와, 사용자의 닉네임과 함께
프로필 정보를 수정합니다.

2-1. 비밀번호 수정 버튼 클릭

> `store`의 `actioins` 함수 `updatePassword`를 호출합니다.
```js
// ~/pages/user/info.vue
...mapActions('user', ['updatePassword']),
  methods: {
    // 비밈번호 변경
    onChangePassword () {
      this.updatePassword({ password: this.password })
      this.$router.push('/user/profile')
    },
```
> 이미지 업로드 API를 호출하여 저장한 이미지와
사용자 닉네임과 함께 프로필 정보를 수정합니다.

<br>

2-2. store

|<div id="#">actions</div>|
|---|
|updatePassword|

```js
//store/user.js actions
  async updatePassword (_, userData) {
    try {
      await this.$axios.patch('user', userData)
    } catch (error) {
      console.error(error)
    }
  }
```
> `axios`를 이용해 비밀번호 수정 API를 호출합니다.


<br>



 ***

 <br>

### <div id="book_search" >3. 원하는 책 검색 및 추가</div>

### 원하는 책 검색

|컴포넌트|라우터|
|---|---|
|components/form/Search.vue|books/search|
|components/book/CardDetail.vue|books/search|

<b>1. 카카오 책검색 API를 이용하여 검색한 데이터를 가져옵니다.</b>
([카카오 개발자 센터](https://developers.kakao.com/docs/latest/ko/daum-search/dev-guide#search-book) 참고)

> 해당 API는 `title(제목)`, `isbn(ISBN)`, `publisher(출판사)`, `person(인명)`로 검색 필드를 제공해주기 때문에 옵션에 따라, 검색 필드를 제한할 수 있도록 구현했습니다.
```html
<!-- ~/pages/search/index.vue -->
<template>
  <!-- v-model 를 이용해 data 인 search 를 바운딩시켜줍니다. -->
    <!-- form-search -->
    <FormSearch v-model="search" :options="options" @searchBook="onSearchBook" @selectedOption="onSeletedOption" />
    <div v-if="errmsg" class="err book_search">
      검색 내용을 입력해주세요.
    </div>
</template>
```
<br>

data
```js
// ~pages/search/index.vue
data(){
  options: ['통합', '제목', '저자', '출판사', 'isbn'],
  option: '',
  search: '',
   ...
}
```
|data|설명|
|:---|:---|
|options|검색 옵션 리스트|
|option|검색 옵션 리스트 중 내가 선택한 검색 옵션|
|search| `input`태그에 입력된 값|

<br>

> <a href="#">form-search 컴포넌트</a>는 공통 컴포넌트에서 정리했습니다

<br>

<b>2. 카카오 검색 API</b>
> 옵션에 따라 카카오 검색 API 호출합니다.

2-1. 책 검색 버튼 클릭
> `store`의 `actioins` 함수 `SearchBooks`를 호출합니다.

data
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
  }
```
|data|설명|
|:---|:---|
|errmsg|입력폼에 아무것도 작성하지 않았을 때 보여줄 에러 메세지|
|size|한번에 검색할 데이터 수|
|isend|마지막 페이지인지 확인|
|reset|검색 데이터 초기화가 필요한지 확인|
|currentpage|현재 페이지|
|showbtn|`더보기 버튼` 보여줄 건지 확인|
|currentCount|현재 보여지는 데이터의 갯수|

<br>


```js
// ~/pages/books/search/index.vue
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
          if (!this.search.length) {
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
              // 마지막 페이지인지 여부
              this.isend = this.meta.is_end
              this.showbutton()
            })
        },
        // 초기화 필요한지 확인
        resetBook (boolean) {
          this.reset = boolean
        },
        // 마지막페이지라면 더보기 버튼이 보여지지 않도록 합니다.
      showbutton () {
        this.isend || !this.books.length ? this.showbtn = false : this.showbtn = true
      }
    }
```

<br>

2-2. 책 검색 후, 더보기 버튼 클릭
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


2-3. store
|<div>actions</div>|
|---|
|SearchBooks|

```js
//store/book.js actions
  async SearchBooks ({ commit, rootState }, bookData) {
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
      state.books = [...state.books, book]
    })
    state.meta = data.meta
  }
```

> 책에 관련된 정보는 `state` 의 `books` 배열에 저장하고, 검색된 문서 수,마지막페이지인지 여부의 정보는 `state`의 `meta`에 저장하였습니다.



<br>

<b>** 책 검색 시, 문제점</b>

|문제점|
|---|
|검색되는 정보를 `state`의 `books`배열에 저장하다보니, 옵션에 따라 검색정보를 다르게 검색했을 때, 기존 배열에 누적되는 문제가 발생했습니다.
예시)옵션을 "통합"으로 설정하여 검색한 후,"제목"이나 "출판사" 등과 같은 다른 옵션으로 다시 재검색시, 기존에 존재하는 책의 정보가 없어지지않고 누적이 됩니다.|

|해결|
|---|
|`{reset:true}` 별도의 객체 속성을 주어, 해당 속성이 `true`일 경우에만 `books` 배열을 초기화하고, 그러지 않다면 기존 배열에 계속 누적시킵니다.
예시)옵션을 "통합"으로 설정하여 검색한 후,"제목"이나 "출판사" 등과 같은 다른 옵션으로 다시 재검색시, 기존에 존재하는 책의 정보를 초기화시켜줍니다.|

<br>

|state|
|---|
|books|
|meta|

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



<b>** 책 검색 시, 불러온 책 데이터를 보여줄 때 문제점</b>
 |문제점|
 |---|
 |라우터 이동시 데이터를 초기화시켜주지 않으면, 다시 검색하기 위해 해당 라우터에 이동했을 때, 전의 데이터가 그대로 남아 보여지게 됩니다.  |

|해결|
 |---|
 |`created`훅을 이용해 인스턴스가 생성된 후 `state`의 `books`값을 초기화시켜줍니다.|

 ```js
  // 책 데이터 초기화
  created () {
    if (this.books.length) { return this.initsearchBook() }
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

###  원하는 책 검색 후 추가

> 검색한 책 중 원하는 책을 골라 책을 추가할 수 있도록 구현하였습니다.

<b>1. 검색한 책 추가 API</b>

1-1 추가하기 버튼 클릭
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
              bus.$emit('on:alert', { data: res.data.msg, bgcolor: '#5C6BC0' })
            setTimeout(() => {
              bus.$emit('off:alert')
            }, 3000)
          })
        } catch (error) {
          // 이미 저장된 데이터를 또 추가하려고 할 때, 알림메세지로 알려주도록 구현했습니다.
          console.log(error)
          bus.$emit('on:alert', { data: error.response.data.msg, bgcolor: '#880E4F' })
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
> <a href="#">알림 메세지</a>는 공통 컴포넌트에 정리하였습니다.

<br>


1-2. store
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

 ** <b>책을 추가한 후, 추가한 책은 다른 라우터 `/books/_page`에 보여주도록 구현하였고, 해당 라우터에 진입시 `nuxt`의`asyncData` 훅을 사용해 데이터를 불러오도록 구현하였으므로, 여기서는 별도의 `mutations`를 호출하여 `state` 값을 변화시키지 않았습니다. </b>

<br><br>


***

<br>

### <div id="book_create">4. 직접 책 추가 및 수정 및 삭제</div>

|컴포넌트|라우터|
|---|---|
|components/form/Book.vue|books/add|
|components/form/Book.vue|books/b/_id|
<br>

### <div>직접 책 추가 및 수정 공통 내용</div>

<br>

<b>1. 책 추가/수정 시 공통으로 사용한 컴포넌트</b>

`form-Book 컴포넌트`

```html
<!-- ~/components/form/Book.vue -->
<template>
  <form class="form_content" @submit.prevent="onsubmitBook">
  <!-- 책 정보 -->
    <div v-for="newBook in newBooks" :key="newBook.label">
      <div>
        <label for="">{{ newBook.label }}</label>
        <p :class="{'invalid':!newBook.name}">
          <textarea v-if="newBook.textarea" v-model="newBook[newBook.name]" cols="30" rows="10"></textarea>
          <input v-else v-model="newBook[newBook.name]" :class="{'titleInput':newBook.focus}" type="text">
          <i v-if="newBook[newBook.name]" class="fas fa-plus-circle" @click="resetInput($event,newBook.name)"></i>
        </p>
      </div>
      <div v-if="newBook.require && !newBook[newBook.name]" class="err">
        {{ newBook.label }}은/는 필수입니다.
      </div>
    </div>
    <!-- 책 날짜 -->
    <div class="date_area">
      <label for="">출간날짜</label>
      <b-form-datepicker id="datepicker" v-model="datetime"></b-form-datepicker>
    </div>
    <!-- 책 이미지 추가/수정 -->
    <FormImage image-title="책 이미지" :thumbnail="book.thumbnail || ''" :show-thumbnail="isEditform" />
    <button type="submit" class="round-btn red addbtn" :disabled="disabledBtn">
      {{ bookBtn }}
    </button>
    <CommonAlertMsg :alert-state="!inputLenValid" data="100자 이내로 작성해주세요" />
  </form>
</template>
```
> 날짜는 `booktstrap-vue`에서 제공하는 <a hreef="#">b-form-datepicker</a> 사용하였습니다.

> 책 이미지는 공통 구현 컴포넌트인 <a href="#">form-image</a> 에서 정리하였습니다.

<br>

data
```js
  data () {
    return {
      newBooks: [
        {
          label: '책제목',
          name: 'title',
          title: '',
          focus: true,
          require: true
        }, {
          label: '책 내용',
          name: 'contents',
          contents: '',
          require: true,
          notlimit: true,
          textarea: true
        }, {
          label: '책 url',
          name: 'url',
          url: '',
          notlimit: true
        }, {
          label: 'isbn',
          name: 'isbn',
          isbn: ''
        }, {
          label: '저자',
          name: 'authors',
          authors: '',
          require: true
        }, {
          label: '출판사',
          name: 'publisher',
          publisher: ''
        }],
      datetime: ''
    }
  },
```
|data|설명|
|:---|:---|
|newBooks|책의 정보|
|datetime|책 출간 날짜|

<br>

computed
```js
  computed: {
    isEditform () {
      return !!this.$route.params.id
    },
    bookBtn () {
      return this.isEditform ? '책 수정' : '책 추가'
    },
    inputLenValid () {
      return this.newBooks.filter(newBook => !newBook.notlimit).every(newBook => newBook[newBook.name].length <= 100)
    },
    inputReuired () {
      return this.newBooks.filter(newBook => newBook.require && newBook[newBook.name]).length >= 3
    },
    disabledBtn () {
      return !this.inputLenValid || !this.inputReuired
    }
  },
```
|computed|설명|
|:---|:---|
|isEditform|라우터의 `params` 값으로 책 수정/책 추가 구분<br>책 추가시 라우터:`books/add`<br>책 수정시 라우터:`books/b/_id`<br>책 수정시에만 라우터의 `params`값이 존재하므로 해당 값으모 책 수정/책 추가를 구분합니다.|
|bookBtn|책 추가/수정 시 보여줄 버튼의 글자|
|inputLenValid| `newBooks`의 `notlimit`속성으로 글자 제한이 필요한 데이터는 100자 이하로만 입력되는지 확인|
|inputReuired |`newBooks`의 `require`속성으로 필수 데이터가 모두 입력되었는지 확인|
|disabledBtn |`button`의 `disabled` 속성을 바운딩하여 유효성 검사를 모두 통과하는지 확인|

<br>

created
```js
  created () {
    // 책 수정시, v-model에 기존 데이터 바운딩시켜주기
    if (this.book && this.$route.params.id) {
      this.newBooks.forEach((value) => {
        value[value.name] = this.book[value.name]
      })
      this.contents = this.book.contents
      this.datetime = this.book.datetime
    }
  },
```
> `created`훅으로 인스턴스가 생성된 후, 책 수정시에만 기존 데이터를 보여줍니다.

<br>

<b>2. 믹스인으로 공통 요소 구현</b>

```js
import BookFetchMixin from '~/mixins/BookFetchMixin'
export default {
  mixins: [BookFetchMixin]
  ...
}
```
<br>

`BookFetchMixin`

methods
```js
  methods: {
      async fetchData () {
      try {
        let data = {}
        const date = this.datetime ? new Date(this.datetime) : new Date()
        this.newBooks.forEach((newBook) => {
          data[newBook.name] = newBook[newBook.name]
        })
        data = { ...data, datetime: date, thumbnail: this.imagePath }
        if (!this.$route.params.id) {
          // 책을 추가할 때
          await this.createBook(data)
            .then((res) => {
              this.$router.push('/books/1')
              bus.$emit('on:alert', { data: res.data.msg, bgcolor: '#5C6BC0' })
              setTimeout(() => {
                bus.$emit('off:alert')
              }, 3000)
            })
        } else {
          // 책을 수정할 때
          await this.updateBook({ id: this.$route.params.id, data })
            .then((res) => {
              this.updateEdit(false)
              bus.$emit('on:alert', { data: res.data.msg, bgcolor: '#FF7043' })
              setTimeout(() => {
                bus.$emit('off:alert')
              }, 3000)
            })
        }
      } catch (error) {
        console.log(error)
        bus.$emit('on:alert', { data: error.response.data.msg, bgcolor: '#880E4F' })
        setTimeout(() => {
          bus.$emit('off:alert')
        }, 3000)
      }
    }
```
|methods|설명|
|:---|:---|
|fetchData|책 데이터 추가/수정시 `state`의 `actions`함수를 호출하여 책 추가/수정 API 호출합니다.<br>`thumbnail(이미지)`는 책 이미지(썸네일) 업로드 API로 가져온 이미지를 저장합니다.<br>`date(날짜)`는 날짜 데이터가 없다면 `오늘 날짜`로 입력되도록 하였습니다.|

<br>



### <div>직접 책 추가</div>

<b>1. 책 추가 API</b>

> 책 이미지(썸네일) 업로드 API를 호출하여 저장한 이미지와 함께 책를 추가하는 API를 호출하여 데이터를 저장합니다.

<br>

1-1. 추가하기 버튼 클릭

> 책 추가 버튼 클릭 시, 공통으로 구현한 `BookFetchMixin` 의 `fetchData` 함수를 호출하여 책 데이터 추가 API를 호출합니다.

```js
// ~/components/form/Book.vue
  methods: {
    ...
     onsubmitBook () {
       this.fetchData()
    }
  }
```

1-2. store
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

> 책을 추가한 후, 라우터를 변경합니다.(`/books/_page`로 이동)<br>
해당 라우터에 진입시 `nuxt`의`asyncData` 훅을 사용해, 데이터를 불러오도록 구현하였으므로, 여기서는 별도의 `mutations`를 호출하여 `state` 값을 변화시키지 않았습니다.

<br>


### <div>추가한 책 수정</div>

<b>1. 책 수정 API</b>

1-1. 수정하기 버튼 클릭

> 책 수정 버튼 클릭 시, 공통으로 구현한 `BookFetchMixin` 의 `fetchData` 함수를 호춣하여 책 데이터 수정 API를 호출합니다.

```js
// ~/components/form/book.vue
  methods: {
    ...
     onsubmitBook () {
       this.fetchData()
    },
  }
```

<br>

1-2. store
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


|state|
|---|
|book|
```js
//store/book.js state
  book: {}
```

<br>


### <div>추가한 책 삭제</div>

<b>1. 책 삭제 API</b>

1-1. 삭제 버튼 클릭

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
      <FormAlert v-if="alert" :title="book && book.title" :confirm="`삭제`" @onagree=" agree" @ondisagree="disagree" />
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
        // 책 삭제 API 호출
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

1-2. store
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

> 책을 삭제한 후, 라우터를 변경합니다.(`/books/_page`로 이동)<br>
해당 라우터에 진입시 `nuxt`의`asyncData` 훅을 사용해, 데이터를 불러오도록 구현하였으므로, 여기서는 별도의 `mutations`를 호출하여 `state` 값을 변화시키지 않았습니다.


<br>

***

<br>

### <div id="get_data">5. 책 보여주기</div>

### <div>공통 내용</div>

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

`Pagination 컴포넌트`
```html
<!-- ~/components/book/Pagination.vue -->
<template>
  <div v-if="showPage" class="pagination_inner">
    <BPagination
      v-model="getCurrentPage"
      :total-rows="rows"
      :per-page="perPage"
      first-number
      @change="pageClick"
    />
  </div>
</template>
```
<br>


props
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


data
```js
  data () {
    return {
      perPage: 1,
      // 현재 페이지
      getCurrentPage: 1
    }
  }
```

|data|설명|
|:---|:---|
|perPage| 페이지당 행(전체 페이지) 수|
|getCurrentPage|현재 페이지|

<br>


computed
```js
  computed: {
    ...mapState('books', ['books', 'currentPage']),
    // 전체 페이지의 수가 1페이지면 페이지네이션을 보여주지 않고, 2페이지 이상일 경우에만 페이지네이션을 보여줍니다.
    showPage () {
      return this.books && this.totalPage > 1
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


watch
 ```js
  watch: {
    $route: {
      handler (to) {
        const page = parseInt(to.params.page, 10)
        // 현재 페이지 활성화
        if (this.currentPage === page) {
          this.getCurrentPage = page
        }
      },
      deep: true,
      immediate: true
    }
  }
 ```
|watch|설명|
|:---|:---|
|$route|라우터를 관찰하여 페이지네이션의 번호를 클릭시, 책 데이터를 불러올 때 `state`의 `currentPage`에 저장한 현재페이지 번호와 비교하여, 페이지네이션의 페이지 번호를 활성화시켜줍니다. |

<br>

methods
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
|pageClick|페이지네이션의 페이지 번호 클릭 시, 상위 컴포넌트에 클릭한 페이지의 번호와 함께 이벤트를 전달해줍니다.|

<br>

<b>2. 믹스인으로 공통 요소 구현</b>

```js
import PaginationFetchMixin from '~/mixins/PaginationFetchMixin'
export default {
  mixins: [PaginationFetchMixin]
}
```
<br>

`PaginationFetchMixin`

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
           if (!res.data) { return }
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


computed
 ```js
  computed: {
    ...mapState('books', ['books']),
    hasBook () {
      return this.books.length
    }
  }
 ```
|computed|설명|
|:---|:---|
|hasBook|책 데이터가 있는지 확인합니다.|

<br>

methods
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

3-1. 책 데이터 가져오기
> `nuxt`의 `asyncData`훅으로 데이터를 가져오고 해당 내용은 <a href="#">PaginationFetchMixin.js</a>에 공통적으로 구현하였습니다.

<br>

3-2. store
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
    commit('loadBooks', { books: res.data.books || [], page: res.data.page || 0 })
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


|state|
|---|
|books|
|currentPage|
```js
//store/book.js state
// 책 데이터
 books: [],
// 현재 페이지 번호
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

<b>4. 책 조회 API를 통해 가져온 책 보여주기</b>

> 가져온 책 데이터가 존재한다면, 책 데이터를 보여줍니다.
```html
<!-- ~/components/books/_page.vue -->
<template>
  ...
     <div v-if="hasBook">
      <div class="books">
        <div v-for="book in books" :key="book.id" class="book">
          <BookCard :book="book" />
        </div>
      </div>
      <BookPagination :total-page="totalPage" @pagination="pagination" />
    </div>
    <div v-else>
      <BookEmpty />
    </div>
  ...
</template>
```
<br>

> 책 데이터가 있다면 `bookCard 컴포넌트`를 보여줍니다.

`bookCard 컴포넌트`

props
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

> 가져온 책 데이터가 존재하지 않는다면,
`book-empty` 컴포넌트를 보여줍니다.

`book-empty 컴포넌트`

```html
<!-- ~/components/books/Empty.vue -->
<template>
  <div class="empty_book">
    <h3>책이 없어요</h3>
  </div>
</template>
```

<br>

### <div>내가 추가한 책 보여주기</div>

|컴포넌트|라우터|
|---|---|
|components/books/Card.vue|books/_page|
|components/books/Empty.vue|books/_page|
|components/books/Pagination.vue|books/_page|
<br>

> 책 보여주기 공통 내용에서 정리하였습니다.

<br>

### <div>다른 사용자가 추가한 책 보여주기</div>
|컴포넌트|라우터|
|---|---|
|components/books/Card.vue|books/others/_page|
|components/books/Empty.vue|books/others/_page|
|components/books/Pagination.vue|books/others/_page|
<br>

> 책 보여주기 공통 내용에서 정리하였습니다.

<br>

### <div>검색한 책 보여주기</div>

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

watch
> `watch`로 `쿼리` 변화를 감지하여 `책제목` , `저자` 중 옵션과 검색 내용을 `state`의 `search` 객체에 저장합니다.

```js
// ~/pages/book/search/_page.vue
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
```html
<!-- ~/components/AppHeader.vue -->
<template>
    <header class="header">
      ...
        <!--  메뉴 -->
        <div class="m_menu">
            ...
            <!-- 검색창은 로그인 되어 사용자 정보가 있을 경우에만 보이도록 구현하였습니다.(로그인을 하지 않았다면 검색창은 보이지 않습니다.) -->
           <div v-if="user" class="action_menu">
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
    <div v-if="user && searchData.showsearchState" class="search_area">
      <div class="search_btn">
        <a href="#" @click.prevent="searchData.showsearchState = false">검색창 끄기</a>
      </div>
      <!-- 검색폼 -->
      <FormSearch
        v-model="searchData.input"
        :options="searchData.options"
        @searchBook="onsearchBook"
        @selectedOption="onselectedOption"
      />
    </div>
  </header>
</template>
```
<br>

methods
```js
  methods: {
    ...mapMutations('books', ['updateSearch']),
    showSearchForm () {
      this.searchData.showsearchState = !this.searchData.showsearchState
      // 기존 검색 데이터 초기화
      this.updateSearch({
        data: '',
        selectedOption: this.searchData.options[0]
      })
    },
    onselectedOption (option) {
      this.updateSearch({
        selectedOption: option
      })
    },
    onsearchBook () {
      // 입력값이 없으면 리턴해준다.
      if (!this.searchData.input) { return }
      this.updateSearch({
        data: this.searchData.input
      })
      this.$router.push(
        `/books/search/1?search=${this.search.selectedOption}&target=${encodeURIComponent(this.searchData.input)}`
      )
    }
  }
}
```
|methods|설명|
|:---|:---|
|showSearchForm|검색창을 보여주고 기존 검색 데이터는 초기화합니다.|
| onselectedOption |검색 옵션(책 제목, 저자 등) 중 내가 선택한 옵션을 `state`의 `search`객체에 저장합니다.|
|onsearchBook|검색 내용을 `state`의 `search`객체에 저장하고 라우터를 이동시킵니다.|

<br>

`store`
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


### <div>태그별로 책 보여주기</div>
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
|components/books/CardDetail.vue|books/b/_id|
|components/books/CardDetail.vue|books/others/b/_id|

### <div>공통 내용</div>

<b>1. `nuxt`의 `asyncData`훅으로 데이터를 가져옵니다.</b>

<br>

<b>2. 성공적으로 책 데이터를 가져왔다면, 책 데이터를 보여줍니다.</b>
```html
<!-- ~/components/books/b/_id.vue -->
<template>
  ...
  <div class="book-details">
    <div>
      <BookCardDetail :book="book" />
    </div>
  ...
</template>
```
<br>

`book-card-detail 컴포넌트`

props
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

### <div>나의 책 상세보기</div>

<b>1. 단일 책 데이터 조회 API</b>

1-1. `nuxt`의 `asyncData`훅으로 데이터를 가져옵니다.
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

1-2. store
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


### <div>다른 사용자의 책 상세보기</div>

<b>1. 단일 책 데이터 조회 API</b>

1-1. `nuxt`의 `asyncData`훅으로 데이터를 가져옵니다.
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

1-2. store
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


***
<br>

### <div id="bookmark"><b>7. 북마크 및 좋아요 기능</b></div>

### 공통 내용

|_|나의 책|다른 사용자의 책|
|:---:|:---:|:---:|
|기능|북마크 추가/삭제|하트(좋아요) 추가/삭제|

> 내가 추가한 책인지 다른 사용자가 추가한 책인지 구분하여, 내 책이면 북마크 표시를 보여주고, 다른 사용자의 책이라면 하트 표시를 보여줍니다.
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
<br>

computed
```js
// ~/components/book/Card.vue
 computed: {
    ...mapState('user', ['user']),
    // 내 책인지  확인
    ismybook () {
      return this.book.UserId === this.user.id
    },
    // 다른 사용자의 책인지 확인
    isNotmybook () {
      return this.book.User && this.book.User.username && !this.ismybook
    },
    ...
  },
```

|computed|설명|
|:---:|:---|
|ismybook|`props`로 받은 `book`의 `UserId`(사용자 아이디)와 로그인 할 때 저장한 사용자의 아이디가 같은 지 확인하여 내 책을 구분합니다|
|isNotmybook|`props`로 받은 `book`의 `username`을 확인하여 다른 사용자의 책인지 확인|



### 북마크 보여주기

computed
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
|computed|설명|
|:---:|:---|
|onBookmarked|`props`로 받은 `book`의 `bookmark` 속성으로 북마크 여부를 구분합니다.|
|isbookmark|`computed`인 `onBookmarked`로 북마크 여부 확인하여, 해당 값이 `true`라면 "B" 글자를 리턴해줍니다. <br>(북마크된다면 "B"마크 표시가 보이도록 구현)|

<br>


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
<br>

###  북마크 추가

<b>1. 북마크 추가 API</b>

1-1. 북마크 추가 버튼 클릭

> 이미 북마크가 추가되어 있다면, 북마크를 삭제시키는 API를 호출하고, 북마크가 추가되어 있지 않다면, 북마크를 추가하는 API를 호출하도록 구현하였습니다.
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
// components/book/Card.vue
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

1-2. store

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
> `axios`를 이용해 북마크 추가 API를 호출합니다.

`commit`를 이용해 `mutatonis`을 호출합니다.

<br>


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
>  `state`의 `books` 배열에서 `id`로 해당 책을 찾아 `bookmark` 속성을 바꿔줍니다.

<br>


#### 7-4. 북마크 삭제

<b>1. 북마크 삭제 API</b>

1-1. 북마크 해제 버튼 클릭

> 이미 북마크가 추가되어 있다면, 북마크를 삭제시키는 API를 호출하고, 북마크가 추가되어 있지 않다면, 북마크를 추가하는 API를 호출하도록 구현하였습니다.
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


1-2. store

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
> `axios`를 이용해 북마크 삭제 API를 호출합니다.

`commit`를 이용해 `mutatonis`을 호출합니다.

<br>


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
>  `state`의 `books` 배열에서 `id`로 해당 책을 찾아 `bookmark` 속성을 바꿔줍니다.

<br>


#### 7-5. 좋아요 보여주기

computed
```js
// ~/components/books/Card.vue
computed: {
    onclickHearted () {
      // 배열 아닌 요소에서 `find` 메서드가 작동하지 못하도록 `Likers`배열이 없다면 빈배열을 넣어주었습니다.
      return !!(this.book.Likers || []).find(
        liker => this.user.id === liker.id
      )
    },
    isheart () {
      return this.onclickHearted ? 'fas fa-heart' : 'far fa-heart'
    }
  },

```
|computed|설명|
|:---:|:---|
|onclickHearted|로그인할 때 저장된 사용자 정보의 `id`와 `Likers`배열의 `userId` 정보를 이용해 해당 책에 대한 좋아요 추가 여부를 확인할 수 있도록 구현하였습니다.<br>ex)`Likers:[{Like:{...},id:6}...]`: `Likers`배열에는 좋아요를 추가한 사용자의 `id`와 사용자의 `username`을 받도록 구현(id가 6인 사용자가 해당 책에 좋아요를 추가함)|
|isheart|`onclickHearted`로 좋아요 여부 확인합니다.|

<br>


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

### 좋아요 추가

<b>1. 좋아요 추가 API</b>

1-1. 좋아요 추가 버튼 클릭

> 이미 좋아요가 추가되어 있다면, 좋아요를 삭제시키는 API를 호출하고, 좋아요가 되어 있지 않다면, 좋아요를 추가하는 API를 호출하도록 구현하였습니다.
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
         // 이미 좋아요를 클릭했다면 좋아요 삭제 API 호출
        this.otherremoveLike({ bookId: id })
      } else {
          // 좋아요가 추가되어 있지 않다면 좋아요 추가 API 호출
        this.otheraddLike({ bookId: id })
      }
    }
  }
```
<br>

1-2. store

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
> `axios`를 이용해 좋아요 추가 API를 호출합니다.

`commit`를 이용해 `mutatonis`을 호출합니다.

<br>


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
> `state`의 `books` 배열에서 `id`로 해당 책을 찾아
`Likers`배열에 `userId`를 추가해줍니다.

<br>


###  좋아요 삭제

<b>1. 좋아요 삭제 API</b>

1-1.  좋아요 해제 버튼 클릭

> 이미 좋아요가 추가되어 있다면, 좋아요를 삭제시키는 api를 호출하고, 좋아요가 되어 있지 않다면, 좋아요를 추가하는 api를 호출하도록 구현하였습니다.
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

1-2. store

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
> `axios`를 이용해 좋아요 삭제 API를 호출합니다.

`commit`를 이용해 `mutatonis`을 호출합니다.

<br>

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
> `state`의 `books` 배열에서 `id`로 해당 책을 찾아
`Likers`배열에 `id`를 비교해 제거해줍니다.

<br>


### <div id="get_data"><b>8. 댓글 보기 및 추가 및 삭제</b></div>
|컴포넌트|
|---|
|components/form/Comment.vue|
|components/comment/Edit.vue|
|components/comment/List.vue|

### 댓글 보기

<b>1. 댓글 조회 API</b>

1-1. 댓글 보기 버튼 클릭

> IntersectionObserver 를 이용하여 댓글 데이터를 가져옵니다.

> `store`의 `actions` 함수 `fetchComments`를 호출합니다.
```html
<!-- ~/component/comment/Edit.vue -->
<template>
   <div class="comment_area">
  <button class="round-btn yellow" @click.prevent="onshowComments">
  <!-- computed로 '댓글보기' ,'댓글접기' 보이도록 구현-->
    {{ onStateComment }}
  </button>
  ...
   <!-- 댓글 리스트 -->
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

<br>

mounted
```js
// ~/component/comment/Edit.vue
 mounted () {
    this.onaddComments()
  }
```
> 해당 컴포넌트가 마운트 된 후, 댓글 데이터를 가져옵니다.

<br>

computed
```js
  computed: {
  ...mapGetters('comments', ['getComments', 'getCommentPage']),
  onStateComment () {
    return !this.showComment ? '댓글 보기' : '댓글 접기'
  },
  // 코멘트 추가 확인
  isaddComment () {
    return this.showComment && this.getComments && this.getComments.length > 9 && !this.getCommentPage.end
  }
}
```
|computed|설명|
|:---|:---|
|onStateComment|`data`의 `showComment`로 댓글 보기 버튼 클릭 여부를 확인합니다.|
|isaddComment|`댓글 조회 API`를 호출하여 저장한 데이터를 확인하여 더 불러올 댓글 데이터가 있는지 확인합니다.|

<br>

methods
```js
 methods: {
    ontoggleComment () {
      this.showComment = !this.showComment
    },
   fetchData () {
      //  댓글 보기 버튼을 클릭할 때 댓글 조회 API 호출
      if (this.showComment) {
        this.loading = true
        //  처음 데이터를 호출하므로 page는 1로 초기화
        this.page = 1
        this.fetchComments({ bookId: this.$route.params.id, init: true })
          .then(() => {
            this.loading = false
          })
      }
    },
    onshowComments () {
      this.ontoggleComment()
      this.fetchData()
    },
   // IntersectionObserver 로 다음 데이터를 가져오는 API 호출
   onaddComments() {
     const observer = new IntersectionObserver((entries) => {
       entries.forEach((entry) => {
         // 댓글 보기 버튼을 클릭한 후, 스크롤이 화면 하단에 위치하고, 댓글이 이미 10개가 호출이 되어 있을 때 다음 댓글를 호출하여 누적시킵니다.
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
            .then(_ => this.loading = false)
         }
       })
     })
      // $ref로 <div classs="trigeer"></div> 태그를 옵저버시킵니다.
     observer.observe(this.$refs.trigger)
   },
 }
```
|methods|설명|
|:---|:---:
|ontoggleComment|댓글 창 보여주기 유무|
|fetchData|댓글 보기 버튼 클릭 시,댓글 조회 API 호출하여 댓글 데이터 가져옵니다.|
|onshowComments|댓글 보기 버튼 클릭 시, 댓글 조회 API 호출하고,댓글 데이터를 보여줍니다.|
|onaddComments|다음 댓글 데이터를 추가로 가져옵니다.|

<br>

> IntersectionObserver API를 지원하지 않는 브라우저에서도 사용할 수 있도록
`IntersectionObserver polyfill` 라이브러리를 사용 하였습니다.(IE에서는 적용되지 않습니다.)<br>
<a href="https://developer.mozilla.org/en-US/docs/Web/API/Intersection_Observer_API">IntersectionObserver 참고자료</a><br>
<a href="https://github.com/w3c/IntersectionObserver/tree/main/polyfill">IntersectionObserver 라이브러리</a>

<br>

1-2. store
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
> `axios`를 이용해 댓글 조회 API를 호출합니다.


> `store`의 `actions`함수 `fetchComments` 호출시,
`{init:true}`객체로 처음으로 댓글을 불러오는 지 여부를 확인하도록 구현하였습니다.


<br>

|처음으로 댓글을 불러오는지 여부를 확인하는 이유|
|---|
|맨 처음으로 `댓글 보기 버튼`을 클릭한다면, 최근에 생성된 댓글 기준 내림 차순으로 10개씩 데이터를 불러오는 API를 호출합니다.<br>`화면 하단`에 스크롤이 내려온다면, 이어서 다음 댓글 10개를 불러오는 API를 호출합니다<br>`화면 하단`에 스크롤이 내려올시에만, 다음 데이터를 불러오도록 구현하였기 때문에,처음으로 데이터를 호출했는지 여부 확인이 필요합니다.|

<br>


- `{init:true}`일 때, 처음으로 데이터(댓글 10개)를 불러오는 api를 호출합니다.

```js
//store/comments.js actions
 async fetchComments ({ commit, state }, comments) {
  ...
 if (comments.init) {
      ...
      // 10개씩 데이터 호출
        res = await this.$axios.get(`books/${comments.bookId}/comments?limit=10`)
 }
 ...
```
<br>

- `화면 하단`에 스크롤 진입

> 데이터의 마지막 `id`(마지막 댓글의 `id`)를 찾아서 그 이후 다음 데이터 10개(다음 댓글 10개)를 불러오는 API를 호출합니다.
```js
....
...
//store/comments.js actions
 async fetchComments ({ commit, state }, comments) {
  ...
 else {
  //  마지막 댓글를 가져옵니다.
        const lastComment = state.comments && state.comments[state.comments.length - 1]
        // 마지막 댓글의 id 를 기준으로 그 이후 10개의 데이터를 불러옵니다.
        res = await this.$axios.get(`books/${comments.bookId}/comments?lastId=${lastComment && lastComment.id}&limit=10&page=${comments.page}`)
      }
```
<br>

`commit`를 이용해 `mutatonis`을 호출합니다.

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

> 댓글 조회 API를 호출 후, 데이터를 가져올 때, `마지막 페이지 여부`와 `총 댓글의 갯수`를 받아 저장합니다.

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
|commentPage|`마지막 페이지 여부`와  `총 댓글의 갯수`를 저장합니다.|


> <div id="c_api">댓글 조회 API 호출시,  아래 정보를 저장합니다.</div>
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


<b>2. 댓글 조회 API로 가져온 데이터 보여주기</b>

`comment-list 컴포넌트`

```html
<!-- ~/components/comment/List.vue -->
<template>
  <li>
    <!-- 댓글 썸네일 이미지 -->
    <div class="c_thumbnail">
      <!-- 사용자의 프로필(썸네일) 이미지가 있다면 이미지를 보여줍니다.-->
      <span v-if="comment.User && comment.User.thumbnail"><img :src="comment.User.thumbnail" alt="썸네일"></span>
      <!-- 사용자의 프로필(썸네일) 이미지가 없다면 사용자의 닉네임 첫글자를 보여줍니다. -->
      <span v-else>{{ String(comment.User.username)[0] }}</span>
      <p>{{ comment.User.username }}</p>
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
    <div class="date">
      {{ $moment(`${comment.updatedAt}`).format("LLL") }}
    </div>
    <div v-if="myComment(comment.User.username)" class="remove_btn">
      <button class="round-btn fill" @click="onRemoveComment(comment.id)">
        삭제
      </button>
    </div>
  </li>
</template>
```
<br>

props
```js
// ~/components/comment/List.vue
  props: {
    comment: {
      type: Object,
      required: true
    }
  }
```
|props|타입|설명|
|:---|:---|:---|
|comment|Object|`댓글 조회 API`로 불러온 데이터|

<br>


<br>

>  댓글 날짜는 라이브러리를 사용하여 포맷하여 보여주었습니다.<br>`@nuxtjs/moment`
<a href="https://github.com/nuxt-community/moment-module#readme">@nuxtjs/moment 참고</a>

<br>

** 댓글을 보여줄 때 문제점
|문제점|
|---|
|`textarea`태그는 여러줄의 데이터를 입력할 수 있지만, 엔터로 줄바꿈을 할 경우 `\n`으로 인식하여, 그대로 출력하면 줄바꿈이 적용되지 않습니다. |



|해결|
|---|
| `css` 속성 중 입력값을 그대로 출력해주는 `white-space: pre-line; `속성을 사용하여 줄바꿈이 적용되도록 구현하였습니다.  |

```css
/* components/comment/CommentList.vue */

/* 줄바꿈이 적용되도록 내용을 그대로 출력하고, 폰트는 상속받아 적용시켜줍니다. */
li .comment_txt{ margin: 15px 0; white-space: pre-line; word-wrap: break-word; font-family: inherit;}
```

<br>

###  댓글 추가

|컴포넌트|라우터|
|---|---|
|components/form/Comment.vue|books/b/_id|
|components/comment/Edit.vue|books/b/_id|


<b>1. 댓글 입력폼은 작성한 길이에 따라 가변적으로 높이가 늘어나도록 구현하였습니다.</b>
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
<br>

data && computed
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
    // textcomments의 길이가 100자 이상인지 체크
      return this.commentLen > 100
    }
}
```
|data|설명|
|:---|:---|
|textcomments|v-model로 바운딩 시켜준 data|


<br>

|computed|설명|
|:---|:---|
|commentLen|v-model로 바운딩 시켜준 data의 textcomments의 길이 확인|
|textLengthChk| textcomments의 길이가 100자 이상인지 체크|

<br>

methods
```js
methods:{
      resize (e) {
      if (this.textLengthChk) { return }
      // 입력값에 따라 높이값을 다시 설정
      e.target.style.height = 'auto'
      e.target.style.height = `${e.target.scrollHeight}px`
    },
}
```

|methods|설명|
|:---|:---|
|resize|해당 입력폼(`tesxtarea` 태그)에 값이 작성될 때마다, `resize`함수를 호출하도록 하였습니다.<br>`computed`인 `textLengthChk`를 이용해, 해당 입력폼에 작성한 글의 길이가 100자 이상이면 입력폼의 높이가 더이상  늘어나지 않도록 `return` 시켜줍니다.<br>`event`의 `target`속성을 이용해 입력값에 따라 높이값을 조정해줍니다.|

<br>

<b>2. 댓글 추가 API</b>

2-1. 댓글 추가 버튼 클릭

> `store`의 `actions`함수 `createComment`를 호출합니다.
d
```js
// ~/component/form/Comment.vue
methods:{
   onaddComment () {
      //  댓글 작성폼에 아무것도 입력되지 않았다면,리턴시켜줍니다.
      if (!this.textcomments.trim()) { return }
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

2-2. store

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
> `axios`를 이용해 댓글 추가 API를 호출합니다.

> `댓글 작성한 내용`과 `별점`인 `rating`데이터도 함께 서버에 보내주도록 구현하였습니다.<br>
(<a href="#star">별점 주기 기능 바로 가기</a>)

`commit`를 이용해 `mutatonis`을 호출합니다.

<br>

|mutations|
|---|
|createComment|
```js
//store/comments.js mutations
  createComment (state, comment) {
    state.comments = [comment, ...state.comments]
    // 댓글 갯수 증가
    state.commentPage.commentCount++
  },
```
> `state`의 `comments` 배열에 누적시켜 저장합니다.


<br>


###  댓글 삭제

|컴포넌트|라우터|
|---|---|
|components/comment/List.vue|books/b/_id|

<b>1. 댓글 삭제 버튼 보여주기</b>
> 댓글 삭제 버튼은 다른 사람의 책이 아닌 내 책에서만  보이도록 구현하였습니다.
```html
<!-- ~/components/comment/List.vue -->
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
<br>

methods

```js
 computed: {
   ...mapState('user', ['user'])
  },
  methods: {
    ...
    myComment (user) {
      return myname === this.user.username
    }
  }
```
|myComment|설명|
|:---|:---|
|myComment|`state`의 `user`객체에 저장된 사용자의 닉네임과 `댓글`을 작성한 사용자의 닉네임을 비교하여 내가 쓴 댓글인지 확인합니다.|

<br>

<b>2. 댓글 삭제 API</b>

2-1. 댓글 삭제 버튼 클릭

삭제 확인 알림창에서 "네" 클릭 시, `store`의 `actioins` 함수 `deleteComment`를 호출합니다.

> <a href="#">삭제 확인 알림창</a>은 공통 구현 요소에 정리하였습니다.

```js
// ~/components/comment/Edit.vue
  methods:{
     ...mapActions('comments',['deleteComment']),
     agree () {
      try {
        // 댓글 삭제
        this.deleteComment({ bookId: this.$route.params.id, commentId: this.commendId })
          .then(() => {
            // 댓글 삭제 후 삭제 알림창 끄기
            this.alert = false
          })
      } catch (error) {
        console.error(error)
      }
    }
  }
```
<br>


2-2. store
|<div>actions</div>|
|---|
|deleteComment|

```js
//store/comments.js actions
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
> `axios`를 이용해 댓글 삭제 API를 호출합니다.

<br>

** 댓글을 삭제할 때 문제점

|문제점|
|---|
|처음 `댓글 보기 버튼`을 클릭했을 때, `댓글 조회 API`를 호출하여 데이터(댓글) 10개를 가져오고, 스크롤를 하단으로 내렸을 때, 다음 댓글을 가져오기 위해 `댓글 조회 API`를 추가로 호출하도록 구현하였습니다.<br>만약 내가 댓글을 삭제했을 때, 스크롤이 화면 하단에 위치하지 않는다면, 더 불러올 데이터(댓글)이 존재함에도 불구하고, 해당 데이터를 보여주지 않는 문제가 발생했습니다.<br>예시)보여줄 코멘트가 10개 이상 존재하고,스크롤을 하단에 내리지 않은 상태에서 댓글을 삭제한다면, 보여줄 코멘트가 있어도 `화면 하단`으로 스크롤을 내리지 않는 이상 `댓글 조회 API`를 호출하지 않기 때문에 댓글을 보여주지 않습니다.|


|해결|
|---|
|댓글을 삭제했을 때,존재하는 댓글이 10개 이상일 경우,스크롤을 하단으로 내려 `댓글 조회 API`를 추가적으로 호출하지 않은 상태일 때에만 `댓글 조회 API`를 호출합니다.|

```js
 async deleteComment ({ commit, dispatch, state }, comment) {
    try {
      ...
      // 댓글의 갯수가 10개 미만일때에만 actions함수 fetchComments를 호출합니다.
      if (state.comments.length < 10) {
        dispatch('fetchComments', { bookId: comment.bookId, init: true, removeState: true })
      }
      return res
    } catch (error) {
      console.error(error)
    }
  }
```

`commit`를 이용해 `mutatonis`을 호출합니다.

<br>

|mutations|
|---|
|removeComment|
```js
//store/comments.js mutations
   removeComment (state, id) {
    state.comments = state.comments.filter(comment => comment.id !== id)
    // 댓글 갯수 감소
    state.commentPage.commentCount--
  }
```
>`state`의 `comments` 배열에 `id` 값을 비교하여, 해당 댓글을 삭제합니다.

<br>


***
<br>

### <div id="star"  ><b>11. 책에 별점 주기 기능 구현</b></div>
|컴포넌트|라우터|
|---|---|
|components/form/Comment.vue|books/b/_id|
|components/comment/Edit.vue|books/b/_id|

<b>1. 별점 보여주기</b>
> 총 별점은 5개까지 줄 수 있고, 마우스로 원하는 별점을 클릭할 수 있도록 구현하였습니다.
```html
<!-- ~/components/form/Comment.vue -->
<template>
  <form class="comment_form" @submit.prevent="onaddComment">
      ...
    <div class="comment_btn">
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

<b>data</b>

```js
// ~/components/form/Comment.vue
 data () {
    return {
      stars: 5,
      rating: 0
    }
  },
```
|data|설명|
|:---:|:---|
|stars|총 별점 수|
|rating|내가 줄 별점 수|

<br>

computed
```js
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
  }
```

|computed|설명|
|:---:|:---|
|stars|`v-model`에 `data`인 `rating`를 바운딩시켜, `rating`의 숫자에 따라 보여지는 이미지가 달라지도록 구현하였습니다.|

<br>

methods
```js
  methods: {
    // 내가 클릭한 별의 갯수와 index의  숫자가 동일하도록 구현
    ratingStar(index) {
      this.rating = index + 1
    }
  }
```
|methods|설명|
|:---:|:---|
|ratingStar|`data`의 `ratging`에 내가 클릭한 별의 갯수(별점)를 저장|

<br>

** 별 클릭 전
> 처음에 아무것도 클릭하지 않았을 때 기본적으로 별의 색깔을 노란색으로 구현하였습니다.<br>초기 별의 색깔을 노란색으로 구현하였으므로,아무것도 클릭하지 않았을 때의 상태에서는 노란색이 아닌 검정색으로 보여주기 위해, `class` 를 바운딩시켜 별을 아예 클릭하지 않았을 경우에는 `init`으로 구분할수 있도록 구현하였습니다.|

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
<br>

** 별 클릭 후
> 해당 별을 클릭할 시, 클릭한 요소는 클래스명에 `active`가 추가되고, 클릭한 별의 이후의 별들은 모두 검은색으로 바꿔줍니다.
```css
.rating p.active~ p span {color:#222; text-shadow: none;}
```


|위처럼 구현한 이유|
|---|
|`css` `~` 형제선택자는 태그 뒤에 오는 모든 요소를 선택하는 것이기 때문에 `class`명에 `active` 요소가 붙은 전의 별들의 색깔을 `css`만으로는 수정이 어려워, 위처럼 반대로 구현하였습니다.<br>(초기 별의 색깔은 노란색으로 해주고, `class`명에 `active`가 붙는다면, 해당 태그 뒤에 오는 모든 별들의 색깔을 검은색으로 바꿔주어 `css`로만 별점 보여주기 기능을 구현 하였습니다.)|

<br>



> 별점은 댓글를 추가할 때, 함께 서버에 전송하도록 구현하였습니다.
<a href="#c_add">댓글 추가 바로 가기</a>

<br>

***

<br>

### <div id="tag"><b>12. 해시태그</b></div>
|컴포넌트|라우터|
|---|---|
|components/form/Hashtag.vue|books/b/_id|
|components/book/CardDetail.vue|books/b/_id|

###  해시태그 보여주기

`hashtah-list 컴포넌트`

```html
<!-- ~/components/hashtag/List.vue -->
<template>
  <!-- 태그 이름 보여주기 -->
  <ul class="hashtags tagList">
    <li v-for="(tag,index) in hashtags" :key="index" class="tag" @mouseenter="onChangeState(tag,index)" @mouseleave="tagNum=''">
      <!-- 태그 클릭 시, 해당 태그를 가지고 있는 책들을 보여줍니다. -->
      <nuxt-link :to="`/hashtags/1?name=${tag.name}`">
        #{{ tag.name }}
      </nuxt-link>
      ...
    </li>
  </ul>
</template>
```
<br>

props
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
|props|타입|설명|
|:---:|:---|:---|
|hashtags|Array|`책 조회 API`를 호출하여 가져온 데이터(해시태그 리스트)|
|bookId|Number|책의 `id`값으로,`해시태그 삭제`시 사용|
|userId|Number|사용자의 `id`값으로 내 책인지 확인하기위 해 사용|


> 해시태그가 없을 경우, `hashtags` 배열안에 데이터가 존재하지 않기 때문에 ` required: false`로 설정해주었습니다.

```js
// props로 받은 Hashtags 배열 정보 예시
Hashtags: [{
  BookHashtag: Object,
  createdAt: "2021-09-03T17:42:27.087Z"
  id: 36
  name: "추천"
  updatedAt: "2021-09-03T17:42:27.087Z"

}, ...]
```

<br>

###  해시태그 추가
|컴포넌트|라우터|
|---|---|
|components/form/Hashtag.vue|books/b/_id|

<b>1. 해시태그 유효성 검사</b>

|유효성 검사 리스트|
|:---|
|추가할 해시태그에 # 추가 유무 확인|
|해시태그 글자 길이 검사|
|해시태그의 갯수 검사|

<br>

computed

> `computed`를 통해 데이터 유효성을 확인합니다.

```js
// ~/form/Hashtag.vue
computed: {
  // 입력폼에 작성한 태그의 이름이 중복되지 않도록 걸러줍니다.
    ishashtags () {
      const tags = new Set(this.hashtag.match(/#[^\s#]+/g))
      return [...tags]
    },
    hashtagchk () {
      return this.ishashtags && this.ishashtags.length === 0
    },
    // 기존에 가지고 있는 해시태그의 갯수와, 새롭게 추가될 예정인 해시태그의 갯수의 합이 10개인지 확인합니다.
     // 해시태그 유효성 검사
    invalidHashtag () {
      return ((this.hashtags && this.hashtags.length) || 0) + ((this.newtagList && this.newtagList.length) || 0) > 5
    },
    // 기존에 추가된 해시태그의 이름과 새롭게 추가될 해시태그의 이름을 비교하여 중복을 제거한 요소를 newtagnames 배열에 저장합니다.
     // 추가할 새로운 해시태그 리스트
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
    },
    newtagChk () {
      return this.newtagList && this.newtagList.length > 0
    },
      // 기존 해시태그 갯수 확인
    currentHashtagsLen () {
      return this.hashtags.length >= 5 || this.invalidHashtag
    },
    // 해시태그 글자 길이 확인
    ishashtagLen () {
      if (!this.newtagList) { return }
      return this.newtagList.every(value => value.length < 11)
    },
    hashtagErrMsg () {
      if (!this.hashtag) { return }
      if (this.currentHashtagsLen) { return '해시태그는 최대 5개까지 추가 가능합니다.' }
      if (!this.ishashtagLen) { return '해시태그는 10자 이내로 작성해주세요' }
      return ''
    },
    disabledHashtag () {
      return this.invalidHashtag || !this.hashtag || this.currentHashtagsLen || !this.ishashtagLen || !this.newtagChk
    }
  }
```
|computed|설명|
|:---|:---|
|ishashtags|`set`으로 중복을 제거하고, <a href="https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/match">match 메서드</a>를 이용해 정규식을 사용하여 `#`글자가 포함된 문자열을 찾아 확인합니다.|
|hashtagchk|`computed`인 `ishashtags`를 확인하여 추가할 태그에 `#`글자가 포함되어 있는지 확인|
|invalidHashtag|이미 추가된 해시태그와 추가할 해시태그의 갯수를 확인하여 총 해시태그 갯수 유효성 검사 |
|newtagList|기존에 추가된 해시태그의 이름과 새롭게 추가될 해시태그의 이름을 비교하여 중복을 제거해 추가할 해시태그 리스트만 추출|
|newtagChk |`computed`인 `newtagList`를 확인하여 추가할 해시태그가 있는지 확인 |
|currentHashtagsLen|총 해시태그의 갯수 확인|
| ishashtagLen|해시태그 글자 길이 확인|
|hashtagErrMsg|유효성 검사에 따른 에러 메세지|
|disabledHashtag|유효성 검사를 모두 통과하는지 확인합니다.<br>`<button>`태그의 `disabled`속성을 바운딩시켜, 유효성 검사를 모두 통과될 때에만 버튼을 클릭할 수 있도록 구현했습니다.|

<br>


<b>2. 해시태그 추가 API</b>

2-1. 해시태그 추가 버튼 클릭

> `store`의 `actions`함수 `createHashtag` 를 호출합니다.
```js
// ~/components/form/Hashtag.vue
 methods: {
    ...mapActions('books', ['createHashtag']),
    onaddHashtag () {
      // 추가할 해시태그가 있을 경우에만 해시태그 추가
      if (this.newtagChk) {
        this.createHashtag({ bookId: this.$route.params.id, hashtags: this.newtagList })
        this.resetHashtag()
      }
    },
    //입력폼 초기화
   resetHashtag () {
      this.hashtag = ''
    }
  }
```


<br>

2-2. store
|<div>actions</div>|
|---|
|createHashtag|

```js
//store/book.js actions
 async createHashtag ({ commit }, { bookId, hashtags }) {
    try {
      const res = await this.$axios.post(`hashtags/${bookId}`, { hashtags })
      commit('addHashtag', res.data.hashtagList)
    } catch (error) {
      console.error(error)
    }
  },
```
> `axios`를 이용해 해시태그 추가 API를 호출합니다.

`commit`를 이용해 `mutatonis`을 호출합니다.

<br>

|mutations|
|---|
|addHashtag|
```js
//store/book.js  mutations
addHashtag (state, hashtagList) {
    state.book.Hashtags = state.book.Hashtags.concat(hashtagList)
  }
```
> `state`의 `book`객체의 `Hashtags`배열에 새롭게 추가된 데이터를 누적시켜 보여줍니다.
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
<br>


### 12-3. 해시태그 삭제

|컴포넌트|라우터|
|---|---|
|components/hashtag/List.vue|books/b/_id|

<b>1. 해시태그 삭제 버튼 보여주기</b>

>  내가 추가된 책에서만 `엑스 버튼`이 보이도록하여 다른 사용자의 책에서는 해시태그를 삭제할 수 없도록 하였습니다.

>  마우스를 올린 대상에서만 `엑스 버튼`이 보여지도록 구현하였습니다.

```html
<!-- ~/components/Hashtag.List.vue -->
<template>
  <ul class="hashtags tagList">
    <li v-for="(tag,index) in hashtags" :key="index" class="tag" @mouseenter="onChangeState(tag,index)" @mouseleave="tagNum=''">
       <nuxt-link :to="`/hashtags/1?name=${tag.name}`">
        #{{ tag.name }}
      </nuxt-link>
      <!-- 삭제 버튼 -->
       <!-- 내책이고, 마우스를 올린 대상에서만 엑스버튼이 보이도록 구현 -->
      <span v-if="ismybook && bookId && index === tagNum " @click.prevent="onRemoveHashtag(tag.id)"><i class="fas fa-plus-circle"></i></span>
    </li>
  </ul>
</template>
```
<br>

data
```js
// ~/components/Hashtag/List.vue
  data () {
    return {
      tagNum: ''
    }
  }
 ```
|data|설명|
|:---|:---|
|tagNum|내가 삭제할 해시태그의 번호(index)|

<br>

methods
 ```js
  methods: {
    onChangeState(tag) {
      const tagNum = this.hashtags.findIndex(hashtag => hashtag.id === tag.id)
      this.tagNum = tagNum
    }
  }
```
|data|설명|
|:---|:---|
|onChangeState|마우스를 올리면, 마우스를 올린 태그의 `id`값과 `책 조회 API`를 호출하여 저장한 `hashtags`배열에서 `id` 값이 같은 것을 찾아  `엑스 버튼`을 보여줍니다.|

<br>

computed
```js
  computed: {
    ...mapState('user', ['user']),
    ismybook () {
      return this.user.id === this.userId
    }
  },
```

|computed|설명|
|:---|:---|
|ismybook|로그인할 때 저장된 사용자의 `id`와 `props`로 내려준 `userId`를 비교해 내 책인지 다른 사용자의 책인지 비교합니다.|

<br>

<b>2. 해시태그 삭제 API</b>

2-1. 해시태그 삭제 버튼 클릭
> `store`의 `actions`함수 `deleteHashtag` 를 호출합니다.
```js
// ~/components/Hashtag/List.vue
 methods: {
   ...mapActions('books', ['deleteHashtag']),
   onRemoveHashtag(id) {
     this.deleteHashtag({
       bookId: this.bookId,
       hashtagId: id
     })
   }
 }
```

<br>

2-2. store
|<div>actions</div>|
|---|
|deleteHashtag|

```js
//store/book.js actions
 async deleteHashtag ({ commit }, { bookId, hashtagId }) {
    try {
      await this.$axios.delete(`hashtags/${bookId}/tag/${hashtagId}`)
      commit('removeHashtag', hashtagId)
    } catch (error) {
      console.error(error)
    }
  }
```
> `axios`를 이용해 해시태그 삭제 API를 호출합니다.

`commit`를 이용해 `mutatonis`을 호출합니다.

<br>

|mutations|
|---|
|removeHashtag|
```js
//store/book.js  mutations
  removeHashtag (state, hashtagId) {
    state.book.Hashtags = state.book.Hashtags.filter(tag => tag.id !== hashtagId)
  },
```
> `state`의 `book`객체의 `Hashtags`배열에 `id`를 비교하여 삭제합니다.

<br>


***

<br>


### <div id="sum"><b>13. 통계</b></div>
|컴포넌트|라우터|
|---|---|
|components/book/form/Hashtag.vue|books/b/_id|
|components/hashtag/List.vue|books/_id|


<b>1. 차트 보여주기</b>
> 차트는  <a href="https://vue-chartjs.org/guide/">vue-chartjs</a> 리이브러리를 사용하였습니다.

<br>

<b>차트 종류</b>

|나의 책|나의 북마크|좋아요|나의 댓글|
|---|---|---|---|
|내가 생성한 책의 갯수|내가 북마크한 갯수|내가좋아요한 갯수/내가 좋아요 받은 갯수|내가 생성한 댓글의 갯수|
<br>

`chart-bar 컴포넌트`

```html
<template>
  <!-- ~/components/chart/Bar.vue -->
  <div>
    <bar-chart-base :data="barChartData" :options="barChartOptions" :height="400"></bar-chart-base>
  </div>
</template>
```
<br>

props
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

|props|타입|설명|
|:---|:---|:---|
|datas|Array|차트를 구성하는 배열형식의 데이터|
|titleName|String|차트의 제목|



> <a href="https://www.chartjs.org/docs/latest/charts/bar.html">chart.js 공식 문서 참고</a><br>
><a href="https://vue-chartjs.org/guide/#creating-your-first-chart">vue-chart.js 공식 문서 참고</a>

<br>


<b>2. 차트 형식 포맷</b>

```html
<!-- ~/pages/user/profile.vue -->
<template>
...
  <div class="box">
      <h2>통계박스</h2>
      <div class="charts">
        <ChartBar :datas="formatData('내가 생성한 책의 갯수',books,'#EC407A')" :title-name="`나의 책`" />
        <ChartBar :datas="formatData('내가 북마크한 갯수',bookmarks,'royalblue')" :title-name="`나의 북마크`" />
        <ChartBar :datas="[...formatData('내가 좋아요 한 갯수',likes,'#EF9A9A'),...formatData('좋아요 받은 갯수',likers,'#29B6F6')]" :title-name="`좋아요`" />
        <ChartBar :datas="formatData('내가 생성한 댓글 갯수',comments,'#26C6DA')" :title-name="`나의 댓글`" />
      </div>
    </div>
  </div>
</template>

```
<br>

methods
```js
// ~/pages/user/profile.vue
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

|methods|설명|
|:---|:---|
|formatData|`label` 차트의 소제목, `values` 갯수, `bgColor` 차트의 색을 인자로 받아 차트를 구성하도록 포맷하였습니다|


** 포맷 전 데이터
```js
`bookmarks: [{months: '5',value: '2'}, {months: '6',value: '2'}]`
```
<br>

** 포맷 후 데이터
```js
`bookmarks: [0,0,0,0,2,2,0,0,0,0,0,0]`
```
> `배열`의 `index`번호에 `월`의 숫자가 매칭되도록 포맷해주었습니다.( ["1월","2월",...."12월"])

> `5월`에는 `2`, `6월`에는 `2`,나머지 월은 `0`

<br>

<b>2. 차트 조회 API</b>

2-1. `nuxt`의 `asyncData`훅으로 차트데이터를 불러오는 api를 호출합니다.

```js
// ~/pages/user/profile.vue
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

> <div id="state_user"> 성공적으로 api 호출시, 아래 정보를 저장합니다.</div>
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

> 각각 `months` 월과 `value` 갯수로 구성된 객체형식으로 배열에 저장하도록 구현하였습니다.

<br>

2-2. store
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
> `axios`를 이용해 차트 조회 API를 호출합니다.



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



||<a href="https://github.com/aws/aws-sdk-js">aws-sdk</a>|<a href="https://github.com/expressjs/multer#readme">multer</a>|<a href="https://github.com/badunk/multer-s3#readme">multer-s3</a>|
|---|---|:---|:---:|
|버전|v2.888.0|v1.4.2|v2.9.0|v5.0.1|
|_|Node.js의 JavaScript용 AWS SDK를 지원해주는 라이브러리|파일 업로드를 위해 사용되는 multipart/form-data 를 다루기 위한 node.js 의 미들웨어|AWS S3를 위한 multer 라이브러리| 내용|비밀번호 암호화하는데 도와주는 라이브러리|
> amazon s3에 이미지를 저장할 수 있도록 도와줍니다.


||<a href="https://www.passportjs.org/">passport</a>|<a href="https://www.passportjs.org/packages/passport-google-oauth20/">passport-google-oauth20</a>|<a href="https://www.passportjs.org/packages/passport-kakao/">passport-kakao</a>|<a href="https://www.passportjs.org/packages/passport-local/">passport-local</a>|
|:---:|:---:|:---:|:---:|:---:|
|버전|v0.4.1|v2.0.0|v1.0.1|v1.0.0|
|-|전략 개념을 사용하여 사용자 정보를 인증하는 것을   도와주는 Node.js를 위한 미들웨어|구글 인증을 위한 passport 미들웨어|카카오 인증을 위한 passport 미들웨어|이메일 사용 인증을 위한 passport 미들웨어|

||<a href="https://github.com/brianc/node-postgres">pg</a>|<a href="https://sequelize.org/master/">sequdlize</a>|<a href="https://github.com/sequelize/cli">sequelize-cli</a>|
|:---:|:---:|:---:|:---:|
|버전|v8.5.1|v6.6.2|v6.2.0|
|-|Node.js 용 PostgreSQL|Postgres , MySQL , MariaDB , SQLite 및 Microsoft SQL Server를 위한  Node.js ORM|sequelize 용 cli|

>  postgresql에 연결할 수 있도록 도와줍니다.
>  sequdlize를 사용해 db 작성하였습니다.

|<a href="https://www.passportjs.org/">passport</a>|<a href="https://www.passportjs.org/packages/passport-google-oauth20/">passport-google-oauth20</a>|<a href="https://www.passportjs.org/packages/passport-kakao/">passport-kakao</a>|<a href="https://www.passportjs.org/packages/passport-local/">passport-local</a>|<a href="https://github.com/kelektiv/node.bcrypt.js#readme">bcrypt</a>|
|:---:|:---:|:---:|:---:|:---:|
|전략 개념을 사용하여 사용자 정보를 인증하는 것을   도와주는 Node.js를 위한 미들웨어|구글 인증을 위한 passport 미들웨어|카카오 인증을 위한 passport 미들웨어|이메일 사용 인증을 위한 passport 미들웨어|비밀번호 암호화하는데 도와주는 라이브러리|

> passport는 로그인시 사용하였습니다.


### 2. 사용한 DB
- <a href="https://www.postgresql.org/">postgreSQL</a>
-  <a href="https://sequelize.org/master/">sequlize</a> 사용하여 DB 작성

- 관계도
![db 관계도](https://myimageslist.s3.us-west-1.amazonaws.com/1632321493780)




## 배포
amazon EC2 에 배포

## https 적용
nginx 와 <a href="https://letsencrypt.org/">Lets' Encrypt</a>로 무료 SSL 인증서 발급하여 https 적용

```js
// /etc/nginx/nginx.conf
// 인증서 적용
server {
        listen 443 ssl;
        server_name api.rone.pe.kr;
        ssl_certificate /etc/letsencrypt/live/api.rone.pe.kr-0002/fullchain.pem; # managed by Certbot
        ssl_certificate_key /etc/letsencrypt/live/api.rone.pe.kr-0002/privkey.pem; # managed by Certbot
        include /etc/letsencrypt/options-ssl-nginx.conf; # managed by Certbot
        ssl_dhparam /etc/letsencrypt/ssl-dhparams.pem;
        location / {
                ....
       }
}
```



- `date_trunc` 함수를 이용해 `월`을 추출하여,
월별로 데이터를 가져올 수 있도록 하였습니다.
(<a href="https://www.postgresqltutorial.com/postgresql-date_trunc/">postgreSQL `date_trunc` 관련 문서 바로가기</a>)







