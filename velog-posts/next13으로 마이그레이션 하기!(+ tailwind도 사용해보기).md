<h2 id="들어가며">들어가며,</h2>
<p>기존에 next12로 만들었던 <a href="https://www.april5.dev/">포트폴리오</a> 사이트를 next13으로 마이그레이션 해봤다</p>
<p>마이그레이션 하게 된 계기는,</p>
<ul>
<li>회사의 직무미팅 시간에 <span style="color: dodgerblue;"><strong>개인 역량 발전을 위해 각자 공부하고 싶은 분야를 진행</strong></span>하기로 했었는데,<ul>
<li>회사에서 next12를 사용하기 때문에 언젠가 next13으로 마이그레이션을 해야할텐데, 그때를 대비해서 미리 경험해보고 싶은 이유로,</li>
</ul>
</li>
<li>엘리님의 next13 강의를 듣고 <span style="color: dodgerblue;"><strong>next13을 사용해보고 싶은 마음</strong></span>에<ul>
<li>강의를 들은건 올해.. 상반기??였는데 강의를 듣고 아무것도 안하는건 남는게 없다고 생각한다. 그래서 실전이 필요하다고 느끼던 참에!</li>
</ul>
</li>
</ul>
<p>연습삼아 개인 포트폴리오 프로젝트를 next13으로 마이그레이션 해보았다!</p>


<hr />
<h2 id="마이그레이션-후기🔥">마이그레이션 후기🔥</h2>
<p><img alt="" src="https://velog.velcdn.com/images/april_5/post/708b2637-a163-4cc0-bb9f-514e74131a88/image.png" /></p>
<blockquote>
<p>pages 디렉토리 제거하고 app 디렉토리 적용하기!</p>
</blockquote>
<h3 id="좋았던-점👍">좋았던 점👍</h3>
<ul>
<li><a href="https://nextjs.org/docs/app/building-your-application/optimizing/metadata">metadata</a> 설정이 쉽다</li>
<li>ssr 적용이 쉽다<ul>
<li>기존 page 단위로 ssr이 적용되던 것에 비해 요소 단위로 ssr이 적용 가능하다</li>
</ul>
</li>
<li>tailwind 사용해보기?!<ul>
<li>기존 프로젝트는 <code>styled-components</code>였는데, app폴더로 마이그레이션 하니 스타일이 깨졌다.. 그래서 강제로 사용해본..</li>
</ul>
</li>
</ul>


<h3 id="아쉬운-점💪"><a href="https://nextjs.org/docs/getting-started/react-essentials">아쉬운 점</a>💪</h3>
<p><img alt="" src="https://velog.velcdn.com/images/april_5/post/d0d96480-ee52-4680-b339-295b8124d828/image.png" /></p>
<ul>
<li><p>컴포넌트를 더 쪼개자!</p>
<ul>
<li>아직 완벽하게 next13을 사용한건 아니라고 생각한다. 컴포넌트 단위로 ssr을 적용할 수 있기에 컴포넌트를 더 쪼개보려 한다!</li>
</ul>
</li>
<li><p><code>Best Practices</code> 성능 개선 필요?!
<img alt="" src="https://velog.velcdn.com/images/april_5/post/7ca94d9a-4f07-4fe8-a3b7-27b5c3e423fe/image.png" /></p>
<p>요렇게 이미지가 길쭉하게 보여지면서 발생하는 이슈..
<img alt="" src="https://velog.velcdn.com/images/april_5/post/6e9b62e2-e31c-485d-802b-e999133f7880/image.png" /></p>
<ul>
<li><a href="https://developer.chrome.com/ko/docs/lighthouse/best-practices/image-aspect-ratio/">lighthouse best-practices</a> 에 따르면 이미지가 잘못된 종횡비로 나타난다는 이슈이다<ul>
<li>렌더링된 이미지의 종횡비가 원본 파일의 종횡비(자연스러운 종회비)와 크게 다른 경우, 렌더링된 이미지가 왜곡되어 사용자에게 불쾌감을 줄 수 있어서 점수가 이렇게 나왔는데..</li>
<li>이 때 고민한건 스타일이냐! 자연스런 이미지냐! 인데, 일단은 스타일을 택..🫠🫠</li>
</ul>
</li>
</ul>
</li>
</ul>


<hr />
<h2 id="span-stylebackground-colorseashellㅤnext-13에-대해-알아보자공식문서-읽기ㅤspan"><span style="background-color: seashell;">ㅤ<a href="https://nextjs.org/docs/app/building-your-application/routing">Next 13</a>에 대해 알아보자!(공식문서 읽기)ㅤ</span></h2>
<h3 id="span-stylebackground-colorlightgoldenrodyellowㅤapp-디렉토리ㅤspan"><span style="background-color: lightgoldenrodyellow;">ㅤapp 디렉토리ㅤ</span></h3>
<p><img alt="" src="https://velog.velcdn.com/images/april_5/post/1bcba7df-fe59-46ad-b1de-22a6a5cdcceb/image.png" /></p>
<ul>
<li>Nextjs 13은 React Server Component 위에 구축된 새로운 App Router를 도입했다<ul>
<li>레이아웃, 중첩 라우팅, 로딩 상태, 에러 핸들링 등 다양한 것들을 지원한다</li>
</ul>
</li>
</ul>
<p><img alt="" src="https://velog.velcdn.com/images/april_5/post/d69d6cce-0287-4fda-aafc-71933a3e2f90/image.png" /></p>
<blockquote>
<p><code>app/page.tsx</code> 이전 버전의 <code>_app.tsx</code> 역할 <code>app/layout.tsx</code>  공통 레이아웃을 잡을 수 있으며, <code>_document.ts</code>의 역할</p>
</blockquote>
<ul>
<li>각각의 폴더 안에서 기본적으로 만들어야 하는 파일이 있다<ul>
<li><code>page.tsx</code>: 기본적인 UI 골격을 만든다<ul>
<li>version 12의 <code>index.tsx</code> 같은 너낌?!</li>
</ul>
</li>
<li><code>layout.tsx</code>: 재사용 가능한 기본적인 UI 골격을 만든다.  상태를 계속 기억하고 유지하는 특징이 있다<ul>
<li><code>template.tsx</code>: <code>layout.tsx</code> 과 비슷하지만 내부 상태를 기억하진 않는다. <code>template.tsx</code> 를 이용해서 계속 새로운 인스턴스를 만들순 있지만 각각의 인스턴스는 상태를 공유할 수 없다</li>
</ul>
</li>
</ul>
</li>
</ul>
<p><img alt="" src="https://velog.velcdn.com/images/april_5/post/8f404939-d0fa-4b9a-b118-d10fa96c59fa/image.png" /></p>
<ul>
<li><code>()</code> 로 표현한 폴더명 하위에 두 개의 경로(about, blog)가 존재하는데 이는 about, blog를 marketing과 관련된 그룹으로 묶어주는 것</li>
<li><code>()</code> 로 표현한 폴더명은 컨셉적으로 하위의 경로를 그룹핑 하기 위해 만들어진 것이고 실제로 routing에는 영향을 주지 않는다<ul>
<li><code>()</code> 로 표현한 폴더명은 url에 포함되지 않고 코드상으로 하위의 url을 정리하기 위한 용도로 사용된다</li>
</ul>
</li>
</ul>
<h4 id="span-stylebackground-colorlavenderㅤㅤdynamic-segmentsㅤㅤspan"><span style="background-color: lavender;">ㅤㅤDynamic Segmentsㅤㅤ</span></h4>
<p><img alt="" src="https://velog.velcdn.com/images/april_5/post/0cb2d67d-f4da-4a88-9fc7-87e63792f58f/image.png" /></p>
<p><img alt="" src="https://velog.velcdn.com/images/april_5/post/a8e1d02e-46ef-4e54-b436-81494c6355bf/image.png" /></p>
<p><img alt="" src="https://velog.velcdn.com/images/april_5/post/0716cec1-d68b-4bca-98a7-06efc94d8240/image.png" /></p>
<h4 id="span-stylebackground-colorlavenderㅤㅤnot-found-페이지ㅤㅤspan"><span style="background-color: lavender;">ㅤㅤNot Found 페이지ㅤㅤ</span></h4>
<p><img alt="" src="https://velog.velcdn.com/images/april_5/post/3c050b2e-edd3-4035-af4a-4d09fde0bd2d/image.png" /></p>
<ul>
<li>app 폴더 하위의 경로에서 <code>notFound</code> 라는 함수를 호출하면 그 경로안에 있는 <code>not-found.tsx</code> 의 UI를 보여준다</li>
</ul>
<p><img alt="" src="https://velog.velcdn.com/images/april_5/post/ac07bec6-e5ac-46e3-916f-5d2a8fb913fc/image.png" /></p>
<h4 id="span-stylebackground-colorlavenderㅤㅤ레이아웃으로-골격을ㅤㅤspan"><span style="background-color: lavender;">ㅤㅤ레이아웃으로 골격을!ㅤㅤ</span></h4>
<p><img alt="" src="https://velog.velcdn.com/images/april_5/post/7603df35-4b90-4eec-86b9-342348e8592e/image.png" /></p>
<ul>
<li>app 폴더 하위의 경로에서 모든 자식 요소들에게 공통으로 보여주려는, 공유 가능한 UI 골격을 만들어 준다.<ul>
<li>Header, Footer, Nav 등</li>
</ul>
</li>
</ul>


<h3 id="span-stylebackground-colorlightgoldenrodyellowㅤ서버-컴포넌트가-기본값ㅤspan"><span style="background-color: lightgoldenrodyellow;">ㅤ서버 컴포넌트가 기본값ㅤ</span></h3>
<blockquote>
<p>최신 버전의 큰 차이점!!</p>
</blockquote>
<p><img alt="" src="https://velog.velcdn.com/images/april_5/post/5761075a-87e7-4f84-9e67-aa25365bd20d/image.png" /></p>
<ul>
<li>app 폴더의 component들은 React Server Component를 기본으로 한다</li>
<li>서버 컴포넌트는 정적 페이지가 되므로 JS 번들 사이즈에 영향을 주지 않는다<ul>
<li>결국 퍼포먼스에 영향을 준다. 성능이 좋아졌다</li>
</ul>
</li>
<li>서버 컴포넌트를 사용하면 초기 렌더링 속도가 빠르고</li>
<li>클라이언트 사이드에서 다운로드 받아야하는 JS 번들 사이즈가 작아진다</li>
<li>클라이언트 사이드 런타임에서는 캐시되고 예측이 가능하기 때문에<ul>
<li>즉, 작은 단위로 번들링 되기 때문에</li>
</ul>
</li>
<li>app 규모가 커지더라도 성능이 좋다</li>
<li>JS 파일은 클라이언트 컴포넌트의 인터렉트가 필요한 부분에 다운로드 된다</li>
</ul>
<h4 id="span-stylebackground-colorlavenderㅤㅤ특징ㅤㅤspan"><span style="background-color: lavender;">ㅤㅤ특징ㅤㅤ</span></h4>
<ol>
<li><code>server component</code> 는 서버에서 실행된다</li>
<li>서버에서 실행되어서 pre rendering 된 HTML 페이지로 전송되므로 console.log 는 브라우저에서 실행되지 않고 터미널에서 실행됨</li>
<li>서버에서 동작하는 컴포넌트이므로 브라우저에서 제공하는 api 사용 불가. 대신 <code>node 환경</code>에서 제공하는 <code>node api</code> 사용 가능<ol>
<li>서버 상의 파일 시스템에 접근 가능</li>
<li>서버 상의 파일을 읽고 쓸수 있다</li>
<li>서버 상의 데이터베이스에 입/출력 가능</li>
</ol>
</li>
<li><code>상태</code> 관련된 것은 사용할 수 없다<ol>
<li><code>useState()</code>, <code>useEffect()</code> 등</li>
</ol>
</li>
</ol>
<h4 id="span-stylebackground-colorlavenderㅤㅤ언제-서버-컴포넌트를-사용할까ㅤㅤspan"><span style="background-color: lavender;">ㅤㅤ언제 서버 컴포넌트를 사용할까ㅤㅤ</span></h4>
<p><img alt="" src="https://velog.velcdn.com/images/april_5/post/44cf5ca9-54a0-45bc-9b0f-53182f49c82e/image.png" /></p>
<ul>
<li>백엔드 리소스가 필요할 때</li>
<li>보안상 민감한 정보가 있을때, 이런 정보를 클라이언트가 몰라도 될 때</li>
<li>서버에 의존하는 무거운 동작을 할 때</li>
</ul>


<h3 id="span-stylebackground-colorlightgoldenrodyellowㅤ클라이언트-컴포넌트ㅤspan"><span style="background-color: lightgoldenrodyellow;">ㅤ클라이언트 컴포넌트ㅤ</span></h3>
<ul>
<li>클라이언트 컴포넌트라고 무조건 CSR 되는 것은 아니고</li>
<li>pre-rendering 되어 HTML 페이지가 생성되고</li>
<li>그 후에 hydrate가 이뤄진다</li>
</ul>
<h4 id="span-stylebackground-colorlavenderㅤㅤ클라이언트-컴포넌트를-사용하려면ㅤㅤspan"><span style="background-color: lavender;">ㅤㅤ클라이언트 컴포넌트를 사용하려면?ㅤㅤ</span></h4>
<ul>
<li>app 폴터 하위는 서버 컴포넌트로 작동되는데, 이 때 <code>useEffect</code> 등을 사용하면 에러가 발생한다<ul>
<li>이 경우 파일 상단에 <code>'use client'</code>라고 명시해주면 클라이언트 컴포넌트로 작동한다</li>
<li>그래서 <code>useEffect</code> 등이 필요한 컴포넌트를 만들어 해당 부분만 클라이언트 컴포넌트로 적용시키면 된다</li>
</ul>
</li>
</ul>
<h4 id="span-stylebackground-colorlavenderㅤㅤ언제-클라이언트-컴포넌트를-사용할까ㅤㅤspan"><span style="background-color: lavender;">ㅤㅤ언제 클라이언트 컴포넌트를 사용할까ㅤㅤ</span></h4>
<p><img alt="" src="https://velog.velcdn.com/images/april_5/post/b41de9af-747c-4225-b165-2f2c0380a649/image.png" /></p>
<ul>
<li>사용자 이벤트 처리가 필요할 때</li>
<li>useState를 사용해야 할 때</li>
<li>브라우저 APIs를 사용해야 할 때</li>
<li>custom hooks을 사용해야 할 때</li>
<li>전체 페이지를 클라이언트 컴포넌트로 만들지 말고, 필요한 부분만 작은 단위로 클라이언트 컴포넌트를 사용하는 게 좋다</li>
</ul>



<hr />
<h2 id="tailwind-사용해보기"><a href="https://tailwindcss.com/docs/installation">tailwind</a> 사용해보기!</h2>
<blockquote>
<p>이전 회사에서 tailwind를 사용해 본 경험이 있는데 그 때의 기억은 부정적이었지만, 이번에 사용해보고 나서 긍정적인 경험으로 변했다! 
이유는? 두 번 사용해보니 익숙하졌달까..!</p>
</blockquote>
<ul>
<li>기존 코드는 <code>styled-components</code>로 스타일링을 했었는데, next13의 app 폴더로 마이그레이션 하면서 스타일이 깨졌다..😱</li>
<li>그래서 next13의 친화적인(??) <code>tailwind</code>로도 마이그레이션했다..<ul>
<li><code>styled-components</code>가 들어가는 모든 파일에 <code>use client</code>라고 명시하면 해결되지만
그러면 굳이 next13을 사용할 필요가 있을까? next13을 공부해야하는 입장에서 이렇게 단순하게 해결하려고 하면 안될 것 같아 tailwind를 사용하기로 했다</li>
</ul>
</li>
</ul>
<h3 id="tailwindconfigjs">tailwind.config.js</h3>
<blockquote>
<p>tailwind의 좋은 점은 기본적인 색상표, 애니메이션을 지원해준다는 것!
하지만! 커스텀이 필요할 때도 있다! 그런 경우 <code>tailwind.config.js</code> 에 적용하고 사용하면 된다!</p>
</blockquote>
<pre><code class="language-js">// tailwind.config.js 예시

/** @type {import('tailwindcss').Config} */
import defaultTheme from 'tailwindcss/defaultTheme'

module.exports = {
  mode: 'jit', // calc 사용하기 위해!
  content: [
    './app/**/*.{js,ts,jsx,tsx,mdx}',
    './pages/**/*.{js,ts,jsx,tsx,mdx}',
    './components/**/*.{js,ts,jsx,tsx,mdx}',

    // Or if using `src` directory:
    './src/**/*.{js,ts,jsx,tsx,mdx}',
  ],
  theme: {
    extend: {
      colors: { // 커스텀 색상
        common: {
          white: '#ffffff',
          black: '#000000',
          ...
        },
        compo: {
          primary: '#fbb0a3',
          'primary-press': '#0AA882',
          secondary: '#F7F9F8',
          ...
        },
        text: {
          333: '#333333',
          555: '#555555',
          ...
        },
      },
      animation: { // 커스텀 애니메이션
        trackingInExpand:
          'trackingInExpand 0.7s cubic-bezier(0.215, 0.61, 0.355, 1) 1s both',
      },
      keyframes: { // 커스텀 애니메이션을 위한 keyframe
        trackingInExpand: {
          '0%': { letterSpacing: `-0.5em`, opacity: 0 },
          '40%': { opacity: 0.6 },
          '100%': { opacity: 1 },
        },
      },
      fontFamily: { // 커스텀 폰트
        blackHan: ['Black Han Sans', ...defaultTheme.fontFamily.sans],
      },
    },
  },
  plugins: [],
}</code></pre>


<h3 id="커스텀-색상-폰트-애니메이션-적용하기">커스텀 색상, 폰트, 애니메이션 적용하기</h3>
<pre><code class="language-ts">// ... 줄임
&lt;div className=&quot;text-text-333 font-blackHan animate-trackingInExpand&quot;&gt;
  {`April's world`}
&lt;/div&gt;
// ... 줄임</code></pre>
<p></p>