# App-router

### 제 2장 CSS 스타일링

app-router에서 말하는 css 사용법은 3가지가 있다
1. TailwindCSS - 유틸리티 클래스(미리 만들어진 css 클래스를 html에 붙임)
2. CSS module - 고유한 클래스 이름을 생성하여 특정한 곳에서 갖다 쓸수 있게됨. {style.shape}
3. clsx 라이브러리- 조건부로 css를 적용해야할때 주로 사용

### 제 3장 font와 image 최적화

### Font

일반적인 폰트 적용:
사용자 접속 → 내 서버 → (별도로) 구글 서버에 폰트 요청 → 폰트 다운로드

next/font 방식:
빌드할 때 → 폰트 파일을 미리 내 서버에 다운로드해서 저장
사용자 접속 → 내 서버에서 폰트 바로 제공 (외부 요청 없음)

빌드 시 다른 정적에셋들과 함께 호스팅하기 떄문에 추가 네트워크 요청이 없다.

### Image

주로 최상위 폴더 /public 에 있다. 보통 html의 경우 아래와 같이 쓴다

```jsx
<img
src="/hero.png"
alt="Screenshots of the dashboard project showing desktop version"
/>
```

이렇게 되면 수동으로 다음과 같은 작업들을 해야하는데:
- 이미지가 다양한 화면 크기에 맞춰 반응형 제작
- 기기별 이미지 크기
- 이미지가 로드될 때 레이아웃 바뀌는 것 방지
- lazy loading
next/image를 사용할 경우 이미지를 자동으로 최적화 해준다.
**<Image> 컴포넌트는** <img> 태그의 확장 기능이며 다음과 같은 이미지 최적화를 제공:
- 이미지 로딩 시 레이아웃 자동 변경 방지
> CLS(Cumulative Layout Shift) 해결, 주로 미리 크기를 잡아놓음(img는 수동 설정)
- 이미지 크기 조정
> 자동으로 기기 크기를 보고 줄여서 전송
- lazy loading(이미지가 뷰포트에 들어 올때 로드됨)
- webp같은 최신 형식으로 이미지 제공

이미지 크기 조정에 대해
일반적으로 브라우저에서 이미지에 요청을 받으면 서버에서 이미지를 넘겨준다. 근데 next.js는 흐름이 이렇게 된다.

```jsx
모바일 브라우저가 이미지 요청
        ↓
Next.js 서버가 요청 받음
"아 모바일이네, 화면 작네"
        ↓
원본 이미지 가져와서 서버에서 리사이징
        ↓
작은 이미지를 브라우저에 전송
```

중간에서 next.js 서버가 이미지를 리사이징 해주고 캐싱을 해준다. 그리고 캐시가 아래 폴더에 쌓인다.

```jsx
/next/cache/images/
  photo-400px.jpg
  photo-800px.jpg
```

단 이렇게 되면 트래픽과 next.js 서버에 부담이 가기 때문에 CDN을 이용한다.
이미지 저장은 > AWS S3
리사이징/캐싱 > cloudflare로 넘길수 있다.

```tsx
          <Image
            src="/hero-desktop.png"
            width={1000}
            height={760}
            className="hidden md:block"
            alt="Screenshots of the dashboard project showing desktop version"
					 />
```

width와height는 픽셀이 아니라 비율이다. 위에서 말했듯 미리 공간을 잡는데 쓰인다.
display: none (<> block) (Tailwind hidden)
공간이 없음
visibility: hidden(<>visible)
공간이 있음
**fill 속성 (boolean)**
부모 크기에 맞게 꽉 채우는 것
**objectFit  :**
contain : 비율 유지하면서 컨테이너에 맞춤- 여백 생김
cover: 비율 유지하면서 컨테이너 꽉 채움 - 넘치는 부분은 잘림
fill: 비율 무시하면서 컨테이너에 맞게 채움
none: 암것도 안함
scale-down: contain과 none중에 더 작은 쪽으로 표시
**blurDataURL
임시 이미지** placeholder:"blur"와 사용

**src와 srcset**
소스는 이미지 경로를 넣는 곳 srcset을 못읽는 구형 브라우저 대비용
srcset는 화면 크기별로 다른 이미지 지정하는 html 속성

그밖에 다양한 설정을 보기 위해선: https://nextjs.org/docs/app/api-reference/components/image#alt

### 제 4장 레이아웃 및 페이지 생성

레이아웃 시 모든걸 다 그리는게 아니라 콘텐츠만 바뀌기 때문에 깜빡임이 없습니다.

### 부분 렌더링

1. **서버 렌더링:** 페이지 이동 시 RCS페이로드를 생성해서 클라이언트로 전송
RCS 페이로드: 브라우저에서 요청이 오면 서버에서 RSC를 렌더링 한 다음에 페이로드에 포장해서 브라우저로 전송. html로 보내면 페이지 이동 시 전체 새로고침.
RCS 페이로드에 경우 부분렌더링/ 상태 유지 가능
2. **프리페칭:** 사용자가 링크를 클릭하기 전에 백그라운드에서 미리 로딩. <LInk>컴포넌트가 뷰포트에 진입 시 프리페칭
정적 라우트: 전체 미리 로딩
동적 라우트: loading.tsx가 있으면 부분적으로만 미리로딩
3. **스트리밍:** 서버가 전체 렌더링을 기다리는 것이 아닌 준비된 부분부터 클라이언트에 전송

느려지는 이유: 느린 네트워크, 하이드레이션 미완, 프리페칭 비활성화(무한 스크롤 같은 부분에서 소스 낭비 방지 prefetch={false}), loading.tsx 없음, 동적 세그먼트 없음.

### 제 5장 페이지 간 이동

페이지 간 이동신 <a>태그의 링크를 사용하게 된다면 이동 시 전체 페이지가 새로고침 된다.

<LInk> 컴포넌트를 사용해 애플리케이션 내 페이지 간 링크를 만들 수 있다.

**최적화는 단순 기능 최적 수치 최적이 아니라 네이티브 앱처럼 사용자가 보기에 부드럽고 불쾌함을 주지 않기 위한(깜빡임) 것을 알았다.**

### 제 7장 데이터 페칭

데이터를 가져오는 방법은 여러가지가 있는데

**1. API 레이어**
- 타사 api 제공 서비스를 이용하는 경우
- 클라이언트단에 정보 노출을 피하기 위해
라우트 핸들러를 사용하여 api 엔드포인트를 생성할 수 있다.

**2. 데이터베이스 쿼리**
- 풀스택 기반으로 사용
- api 레이어를 이용하지 않고 서버에서 데이터를 직접 가져옴

서버 컴포넌트에서는 `async/await`을 바로 사용할 수 있어 별도의 상태관리 라이브러리 없이 데이터를 가져올 수 있다.

```tsx
// app/lib/data.ts
import { sql } from '@vercel/postgres';

export async function fetchRevenue() {
  const data = await sql<Revenue>`SELECT * FROM revenue`;
  return data.rows;
}
```

```tsx
// app/dashboard/page.tsx (서버 컴포넌트)
import { fetchRevenue } from '@/app/lib/data';

export default async function Page() {
  const revenue = await fetchRevenue(); // 바로 await 사용 가능
  return <RevenueChart revenue={revenue} />;
}
```

#### 요청 워터폴 (Request Waterfalls)

데이터 요청이 순차적으로 실행되는 현상. 앞의 요청이 완료되어야 다음 요청이 시작됨.

```
fetchRevenue()    --|>
fetchInvoices()        --|>
fetchCustomers()              --|>
                  시간 →
```

의도적으로 쓰는 경우: 이전 요청 결과에 따라 다음 요청을 결정할 때 (예: 사용자 ID를 먼저 가져온 후 그 ID로 프로필 조회)

#### 병렬 데이터 페칭 (Parallel Data Fetching)

`Promise.all()` 또는 `Promise.allSettled()`을 사용해 여러 요청을 동시에 실행.

```tsx
export async function fetchCardData() {
  const [
    invoiceCountData,
    customerCountData,
    invoiceStatusData,
  ] = await Promise.all([
    sql`SELECT COUNT(*) FROM invoices`,
    sql`SELECT COUNT(*) FROM customers`,
    sql`SELECT
      SUM(CASE WHEN status = 'paid' THEN amount ELSE 0 END) AS paid,
      SUM(CASE WHEN status = 'pending' THEN amount ELSE 0 END) AS pending
      FROM invoices`,
  ]);
  // ...
}
```

```
fetchRevenue()    --|>
fetchInvoices()   --|>      ← 동시에 실행됨
fetchCustomers()  --|>
                  시간 →
```

단점: 하나의 요청이 다른 요청보다 훨씬 느리면 결국 가장 느린 요청을 기다려야 함.

### 제 8장 정적 렌더링 및 동적 렌더링

정적 렌더링
Next.js는 빌드 시 페이지를 미리 html로 만들어놓는다. 그래서 유저가 접속하면 html을 그대로 전달하게 된다. (캐시된 결과를 제공한다)
1. 웹사이트 속도 향상(사전 렌더링)
2. 서버 부하 감소(그때 그때 만들어지는게 아니기 때문에)
3. seo 용이

블로그같은 글이나 제품 페이지에는 용이하나 실시간 데이터를 보기엔 적합하지 않다.

동적 렌더링
사용자가 페이지를 방문하는 시점에 서버가 각 사용자에 맞게 콘텐츠를 제공한다
1. 실시간 데이터 주기 가능
2. 맞춤형 컨텐츠 제작 가능
3. 요청이 오는 그시점에 생기는 정보 가능 (쿠키, url 서치 파라미터 같은 정보가 1,2번을 위한것)

### 제 9장 스트리밍

스트리밍
데이터 전송 기술로, 경로를 더 작은 "청크"로 나누고 서버에서 클라이언트로 준비되는 대로 보여주는 기술 > 이렇게 되면 페이지 전체가 차단되는것을 막고 일부를 보고 상호작용을 할수 있다.

1. 페이지 수준에선 loading.tsx(시스템이 <Suspense> 자동으로 생성하는)파일을 사용합니다.
2. 컴포넌트 수준에선 <Suspense>로 더욱 세밀한 제어가 된다.

**페이지 스트리밍 - Loading.tsx**

1. loading.tsx 이 파일은 React Suspense위에 구축된 특별한 next.js 파일이다. Next.js가 loading.tsx를 자동으로 <Suspense>로 감싸준다.

```jsx
개발자가 쓰는 것:
loading.tsx → 스켈레톤 UI
page.tsx → 실제 콘텐츠

Next.js가 내부적으로 하는 것:
<Suspense fallback={<Loading />}>  ← loading.tsx
  <Page />                          ← page.tsx
</Suspense>
```

1. 정적 컨텐츠기 때문에 <sideNav>는 그대로 표시가 된다 layout.tsx는 빌드 타임에 한번 렌더링 되고 페이지 이동해도 리렌더링이 안되기 때문에 정적으로 즉시 보인다.
2. 이제 로딩 중간에 다른 페이지로 넘어갈수있다. 원래는 화면 전체가 전부 로딩 될때까지 기다렸는데 그동안엔 클릭해도 무시되었었다. 이제는 근데 넘어가기 가능.

**<Suspense>**
리액트18이후 데이터 페칭에도 사용가능하게 된 컴포넌트. 데이터 로딩같은 비동기 작업 중에 fallback UI보여주다가 완료되면 실제 컴포넌트로 전환해주는 선언적 로딩처리 방식입니다.
선언적: 어떻게 보여줄지만 말하는것
명령적: 일일히 다 제시 하는 것

/dashboard/**(overview)**/page.tsx 이경우 ()폴더는 url 경로에 포함이 되지 않는다.

바깥에 한다면 인보이스나 커스텀스 페이지에도 적용이 됨.

**컴포넌트 스트리밍**

페이지레벨에서 컴포넌트 레벨로 해볼 수 있다. 마찬가지로 <Suspense>를 사용하며 동적 렌더링에 사용하여 데이터 로딩이 될때까지 대체 컴포넌트를 보여줄수 있다.

**그룹핑 컴포넌트**

### 제 10장 검색 및 페이지네이션

Next.js API(useSearchParams, usePathname, useRouter) 사용방법에 대해 알아보기

**url Search Params**
state대신 이용할수 있는 방법
1. 새로고침해도 결과가 그대로 있다.
2. 서버 렌더링이 된다.> 서버가 query=name을 읽고 이 검색 결과를 바로 html에 생성해서 보내줌
3. 서버로그가 남기 때문에 분석 추적이 가능하다.

state로 할경우에 클라이언트 단에만 작동을 하므로 새로고침해도 날라가고 일단 빈페이지 로딩 후에 js실행시 보여준다.

공유받은 링크 클릭했을 때
state 방식 → 빈 화면 → 로딩 → 결과
URL 방식  → 바로 결과

검색 기능을 위해선 다음과 같은 클라이언트 훅이 필요하다.

**useSearchParams**
현재 URL의 매개변수에 접근 할수 있다. {} 형식

**usePathname**
현재 url의 경로를 읽을 수 있다 `'/dashboard/invoices'`.

**useRouter**
클라이언트 컴포넌트 내에 경로간 이동을 프로그래밍 방식으로 가능하게 한다

```tsx
onChange={(e) => {
          handleSearch(e.target.value);
        }}
```

브라우저에서 input에 뭔가 입력하면, 브라우저가 자동으로 **이벤트 객체(e)** 를 만들어서 onChange 함수에 넘겨줌. 그럼 이안의 속성 e.target.value를 handleSearch에 넘겨줌.

useSearchParams()를 searchParams에 넣는다.

그리고 URLSearchParams로 새 인스턴스 만들기

**URLSearchParams**
url search params를 가져와 조작하게 해주는 유틸리티 web API.(`?page=1&query=a`. 이런식으로 붙여줌) useSearchParam은 읽기전용이다. set은 문자열 생성 delete는 삭제

현재 url을 읽어와서> 객체를 만들고(복사)> 그리고 그위에 조작 query를 붙인다.

**디바운싱**
함수 실행 빈도를 제한하는 프로그래밍 기법 사용자가 멈췄을때만 데이터 베이스를 조회하게 한다.

작동 방식:
1. 트리거 이벤트: 디바운싱이 필요한 이벤트가 발생하면 타이머 시작
2. 대기: 타이머가 만료되기 전에 새로운 이벤트 발생하면 타이머 재설정
3. 실행: 카운트다운 종료 시점에 도달하면 함수 실행
