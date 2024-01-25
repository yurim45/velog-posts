<p>결과만 이야기하면,</p>
<table>
<thead>
<tr>
<th>전</th>
<th>후</th>
</tr>
</thead>
<tbody><tr>
<td><img alt="" src="https://velog.velcdn.com/images/april_5/post/851007ee-3759-4c64-abee-18dbc37dfa1f/image.png" /></td>
<td><img alt="" src="https://velog.velcdn.com/images/april_5/post/12e59a27-4aa2-4cb1-bdaf-d5a8e9b804e9/image.png" /></td>
</tr>
</tbody></table>
<br />

<hr />
<h2 id="들어가며">들어가며,</h2>
<p>웹 성능 최적화에 관심을 가지게 된 것은</p>
<ul>
<li>최근 이직 준비를 하며 신입의 단계(?)를 지나면 어떤 것을 할 줄 알아야 하는가?🤔 (또는 어떤 쪽으로 관심을 가져야 하는가?🤔)</li>
<li>이직에 성공하고 내가 담당하게 될 서비스를 보니.. 너무 느리다.. 느려.. (게다가 글로벌 서비스인데 인터넷이 느린 어떤 나라에서는 로딩되다가 다운된 적도 있다고?😱)</li>
</ul>
<p>라는 고민? 생각?을 하던 시기에 접했던 강의에서 배운대로 내가 담당하게 된 서비스를 <code>Light House</code>로 점검해보았다! 그런데 왠걸.. </p>
<ul>
<li><code>Performance</code> 점수도 안좋고, </li>
<li>네트워크 탭에서 분석해봐도 최초 로딩시에 불필요한 동영상, 이미지 등이 한 번에 다운되는 것이 확인되어서 </li>
</ul>
<p>틈틈히 리펙토링을 해보기로 했다.</p>
<br />

<hr />
<h1 id="nextjs-프로젝트-성능-개선하기"><code>Next.js</code> 프로젝트 성능 개선하기</h1>
<h2 id="✅-현재-상태-점검">✅ 현재 상태 점검</h2>
<h3 id="1-네트워크-탭">1. 네트워크 탭</h3>
<h4 id="최초-로딩-속도">최초 로딩 속도</h4>
<p>최초 로딩 속도가 얼마나 느릴까? 확인해보고 싶었다. 그런데.. 생각보다 너무 느리더라..</p>
<p><img alt="" src="https://velog.velcdn.com/images/april_5/post/2969db27-1dce-4984-b2b0-d2e8916aae56/image.gif" /></p>
<p>자세히 살펴보니 당장 필요하지 않은 동영상 파일과 이미지 파일들이 너무 많다.. 좀 나눠서 필요한 시점에 로딩시켜볼 수 있겠다 싶었다</p>
<p><img alt="" src="https://velog.velcdn.com/images/april_5/post/47a5c962-2cf6-4ab0-a9bc-25f301697503/image.gif" /></p>
<p>그리고 assets 폴더의 거의 200개의 이미지, SVG 파일들을 한번에 묶어서 사용하도록 되어있었는데, 이 부분에서 배포하려고 빌드할 때도 워닝으로 알려주었다. 너무 크다고..😰</p>
<p><img alt="" src="https://velog.velcdn.com/images/april_5/post/dfe1e7d3-e65a-4c9c-bde4-9e01e8e8ce44/image.png" /></p>
<p>일단, 네트워크 탭에서의 서비스 상태를 확인하고 <code>Light House</code>도 검사했다.</p>
<br />

<h3 id="2-light-house-결과">2. <code>Light House</code> 결과</h3>
<p><img alt="" src="https://velog.velcdn.com/images/april_5/post/821a69b6-35ff-43ae-bc87-cdf107535bbe/image.png" /></p>
<ul>
<li><strong>Defer offscreen images</strong><ul>
<li>Lazy loading을 사용하라는 것이다. 
Lazy loading은 이미지 영역이 화면에 보이지 않을 때는 이미지를 불러오지 않고, 화면에 보이게 되었을 때 이미지를 불러오는 방식이다.</li>
</ul>
</li>
<li><strong>Use video formats for animated content</strong><ul>
<li>대형 GIF는 애니메이션 컨텐츠 전달에 비효율적이라는 내용이다. 네트워크 바이트를 저장하려면 GIF 대신 MPEG4/WebM 비디오와 정적 이미지에 PNG/WebP를 사용하는 것이 좋다고 한다.<ul>
<li><em>해당 이미지는 서버사이드로 받아오고</em> 🤔, <em>GIF 제작자의 도움 필요하므로 일단 패스하기로 했다..</em></li>
</ul>
</li>
</ul>
</li>
<li><strong>Serve images in next-gen formats</strong>: 이미지 포맷 문제<ul>
<li>이미지 형식을 업로드된 그대로 사용하고 있었는데, WebP 등 새로운 이미지 파일 형식을 사용하는 것이 좋다고 한다. WebP는 구글이 제공하는 웹을 위한 새로운 이미지 파일 형식으로, JPEG의 손실 있는 압축과 PNG의 투명도 기능을 제공하면서도 더 나은 압축을 제공한다.</li>
</ul>
</li>
<li><strong>Reduce unused Javascript</strong>: 사용하지 않는 자바스크립트 코드 문제</li>
</ul>
<p><img alt="" src="https://velog.velcdn.com/images/april_5/post/248ac946-c8b7-4059-8314-ba919e584abd/image.png" /></p>
<ul>
<li><strong>Avoid enormous network payloads</strong>: 너무 큰 네트워크 페이로드 문제<ul>
<li>5000KiB를 초과하는 페이지에 나타난다. 압축, 최신포멧 사용, 코드분할 등을 하라는 내용이다</li>
</ul>
</li>
<li><strong>Serve static assets with an efficient cache policy</strong><ul>
<li>가져온 이미지에 캐시 TTL이 설정되어 있지 않았다는 내용이다
이걸 해결하려면 하나하나 이미지 리소스에 캐시 설정을 달아주거나, cloudfront를 CDN으로 연결해서 구성해 주어야 한다고 한다.<ul>
<li>속성으로 <code>loading=&quot;lazy&quot;</code> 값을 주면 Lazy loading이 적용된다. 
<code>next.config.js</code> 파일에서 이미지의 캐시 TTL을 지정해 줄 수 있다. 외부 도메인에서 이미지를 가져올 경우, <code>next.config.js</code> 파일에 도메인 정보를 추가해 주어야 한다.</li>
</ul>
</li>
</ul>
</li>
</ul>
<pre><code class="language-js">// next.config.js
module.exports = {
  images: {
    domains: [...],
    minimumCacheTTL: 60,
  }
};

// next/image
&lt;Image
  // ...    
  loading=&quot;lazy&quot;
/&gt;</code></pre>
<ul>
<li><strong><a href="https://www.commercegurus.com/dom-size/">Avoid an excessive DOM size</a></strong><ul>
<li>DOM size가 너무 크단다.. 웹 페이지에 DOM노드(또는 HTML 태그)가 너무 많거나 너무 깊게 중첩된 경우 DOM 크기가 커진다고 한다.<ul>
<li>총 1,500개가 넘는 DOM 노드가 있을 때</li>
<li>32개 노드보다 큰 최대 노드 깊이에 도달했을 때</li>
<li>하위 노드가 60개보다 많은 상위 노드가 있을 때</li>
</ul>
</li>
<li>해결 방법은 body의 노드 수를 줄이거나 필요할 때만 DOM 노드를 만들고 더 이상 필요하지 않을 때 노드를 파괴하도록 하면 된다고 한다 </li>
</ul>
</li>
</ul>
<br />

<p>음.. <code>Performance</code> 점수가 매우 안좋군! 하나씩 고쳐가보자! 💪</p>
<br />

<h3 id="3-nextbundle-analyzer-로-분석해보기">3. <code>@next/bundle-analyzer</code> 로 분석해보기</h3>
<blockquote>
<p>Nextjs Project는 <a href="https://www.npmjs.com/package/@next/bundle-analyzer">@next/bundle-analyzer</a>으로 분석할 수 있다</p>
</blockquote>
<pre><code class="language-bath">yarn add @next/bundle-analyzer
# 설치 후 `next.config.js` 설정하기</code></pre>
<br />

<table>
<thead>
<tr>
<th>package.json</th>
<th>빌드된 후</th>
</tr>
</thead>
<tbody><tr>
<td><img alt="" src="https://velog.velcdn.com/images/april_5/post/9fab6dac-6198-407b-a62f-faa4ff52b457/image.png" /></td>
<td><img alt="" src="https://velog.velcdn.com/images/april_5/post/19a44e52-a588-4bf1-9685-8423c02a2cf2/image.png" /></td>
</tr>
</tbody></table>
<h4 id="결과">결과</h4>
<table>
<thead>
<tr>
<th>storyplay-server/web/.next/analyze/nodejs.html</th>
<th>storyplay-server/web/.next/analyze/client.html</th>
</tr>
</thead>
<tbody><tr>
<td><img alt="" src="https://velog.velcdn.com/images/april_5/post/494a83f6-9a92-4c4f-a4e1-20eb460ec6e7/image.png" /></td>
<td><img alt="" src="https://velog.velcdn.com/images/april_5/post/9755f561-c988-457e-96a6-6bdc65041dbb/image.png" /></td>
</tr>
</tbody></table>
<br />

<hr />
<h2 id="✅-리펙토링">✅ 리펙토링!</h2>
<h3 id="1-assets-폴더--파일-분리">1. assets 폴더 &gt; 파일 분리</h3>
<blockquote>
<p>간단해보였기에 가장 먼저 시작한 작업. 그러나 사용하는 파일을 모두 수정하는 노가다 작업이었다..ㄷㄷ 🫠</p>
</blockquote>
<p><img alt="" src="https://velog.velcdn.com/images/april_5/post/f9fc2dd2-8d65-4aee-a0a8-e49bd23c076a/image.png" /></p>
<p>사용하는 시점에 모두 import 하지 않고 사용할 파일만 import 되도록 분리했다.
그리고 미사용중인 파일 45개 모두 삭제했다</p>
<table>
<thead>
<tr>
<th>전</th>
<th>후</th>
</tr>
</thead>
<tbody><tr>
<td><img alt="" src="https://velog.velcdn.com/images/april_5/post/1080a0bf-00b2-49c2-96f9-27952f8bfd42/image.png" /></td>
<td><img alt="" src="https://velog.velcdn.com/images/april_5/post/a69a2526-46e5-4b31-940c-f112c5dc6f32/image.png" /></td>
</tr>
</tbody></table>
<br />

<h3 id="2-lazy-loadingcode-spltting">2. <code>Lazy Loading(Code Spltting)</code></h3>
<p>담당하는 서비스의 홈 가장 하단에 모든 컨텐츠들을 보여주는 컴포넌트가 있는데 이 부분을 분리시켰다.</p>
<p>즉, 필요한 시점에 렌더링을 시켰다</p>
<h4 id="적용-코드">적용 코드</h4>
<p><a href="https://velog.io/@april_5/Intersection-Observer-API%EB%A1%9C-infinite-scroll-%EA%B5%AC%ED%98%84%ED%95%98%EA%B8%B0">useIntersection</a> hook을 만들어서 보여주는 시점에 렌더링 시켰다</p>
<p><img alt="" src="https://velog.velcdn.com/images/april_5/post/6562e926-6c92-4c30-8aae-1e36e5e565f2/image.png" /></p>
<h4 id="결과-1">결과</h4>
<p>필요한 시점에 렌더링 시키고, JS 파일도 분리된 것을 확인할 수 있다.</p>
<table>
<thead>
<tr>
<th>적용</th>
</tr>
</thead>
<tbody><tr>
<td>분리된 JS 파일 <br /> <img alt="" src="https://velog.velcdn.com/images/april_5/post/19dc3cb1-873d-4790-ac9f-a66824343067/image.png" /></td>
</tr>
<tr>
<td>필요한 시점에 이미지 파일 불러오기 <br /> <img alt="" src="https://velog.velcdn.com/images/april_5/post/7792235e-46dc-4356-896a-42a24a9545c4/image.png" /></td>
</tr>
<tr>
<td>필요한 시점에 렌더링 시키기 <br /> <img alt="" src="https://velog.velcdn.com/images/april_5/post/5230d032-e3a1-4f4d-8eff-3d08ce2cd440/image.gif" /></td>
</tr>
</tbody></table>
<p>테스트로 가장 큰 컴포넌트만 적용시켰는데 나머지 컴포넌트도 적용시켜야겠다!</p>
<ul>
<li>Lazy Loading을 적용하니 동영상 파일도 해당 시점에 불러오는 것을 확인!</li>
</ul>
<br />

<h3 id="3-ts-파일-분리">3. ts 파일 분리</h3>
<p><img alt="" src="https://velog.velcdn.com/images/april_5/post/6cd827c2-60b8-4f54-b862-dbce0bcd3905/image.png" /></p>
<ul>
<li><code>Reduce unused Javascript</code>에 해당하는 상황이 어느 곳인지 해결하고자 <code>coverage</code> 탭도 확인해보았으나 눈에 띄는 부분은 찾지 못했고..😞
어떤 파일에서 이런 현상이 나타나는 걸까..🤔 고민 하던 중!!! 코드 수정을 하며 눈에 띈 파일이 있었으니!!!</li>
<li>1400줄이 넘는 파일 안에 있는 함수를 호출 할 때, 한 번에 <code>import</code> 하고 있는 상황을 발견했다!!!<ul>
<li>assets과 비슷한 상황..</li>
</ul>
</li>
<li>결과만 이야기하면, 이 작업으로 퍼포먼스 점수가 10점 넘게 올랐다는 점!! 🥹 흐흐!!! </li>
</ul>
<br />

<h3 id="4-무거운-svg-파일--png-로-변경">4. 무거운 svg 파일 &gt; png 로 변경</h3>
<p><img alt="" src="https://velog.velcdn.com/images/april_5/post/43e452f9-aed6-4aa4-b7f0-de5b169f253f/image.png" /></p>
<p>문득 터미널에서 빌드하면서 발견한 내용으로 파일 변경 시도!</p>
<table>
<thead>
<tr>
<th>전</th>
<th>후</th>
</tr>
</thead>
<tbody><tr>
<td><img alt="image" src="https://user-images.githubusercontent.com/63839302/232678034-22927b3f-f6a2-467e-8053-dd995a68b6e5.png" width="361" /></td>
<td><img alt="image" src="https://user-images.githubusercontent.com/63839302/232677990-9e60959b-4cb0-4f85-b9fe-9703021d52c8.png" width="375" /></td>
</tr>
</tbody></table>
<br />



<h3 id="5-정적-파일-최적화">5. 정적 파일 최적화</h3>
<ul>
<li>lightHouse의 <code>Serve static assets with an efficient cache policy</code> 에 해당하는 내용 조치<ul>
<li>이미지에 캐시 TTL 설정 필요<ul>
<li>이걸 해결하려면 일일이 s3 이미지 리소스에 캐시 설정을 달아주거나, </li>
<li><code>next.config.js</code>에 <code>minimumCacheTTL</code>를 추가<pre><code class="language-ts">const nextConfig = {
  images: {
    domains: [
      // ... 생략
    ],
    minimumCacheTTL: 60,
  },</code></pre>
</li>
</ul>
</li>
<li><code>minimumCacheTTL</code>을 60으로 설정하면, <ul>
<li>캐시에 최소 60초(1분) 동안 저장되게 되고 </li>
<li>이후에 동일한 데이터에 대한 데이터 프리패치 요청이 있을 경우, <ul>
<li>이전에 캐시에 저장된 데이터를 사용하여 </li>
<li>다시 데이터를 가져오지 않고 캐시된 데이터를 사용. </li>
</ul>
</li>
<li>이를 통해 서버와의 불필요한 요청을 줄이고, 성능을 향상시킬 수 있다고 한다!</li>
</ul>
</li>
</ul>
</li>
</ul>
<table>
<thead>
<tr>
<th>전</th>
<th>후</th>
</tr>
</thead>
<tbody><tr>
<td><img alt="스크린샷 2023-04-18 오후 4 00 14" src="https://user-images.githubusercontent.com/63839302/232698218-45251a43-73a4-4e67-af00-535ba0d25f75.png" width="463" /></td>
<td><img alt="스크린샷 2023-04-18 오후 3 59 53" src="https://user-images.githubusercontent.com/63839302/232698161-aef9ff84-5aa9-47b6-8328-e6a04261e9f4.png" width="476" /></td>
</tr>
</tbody></table>
<br />

<p>이렇게 작업하다보니, <code>Light House</code>의 점수가 어떻게 변했을지 궁금해졌다!
그래서 중간 점검!!! ⇩⇩</p>
<br />

<hr />
<h2 id="✅-리펙토링-후-light-house">✅ 리펙토링 후 <code>Light House</code></h2>
<p><img alt="" src="https://velog.velcdn.com/images/april_5/post/11d119ea-0147-41e1-ae43-6819cfefd662/image.png" /></p>
<p>오오!! <code>Performance</code> 점수가 올랐다?! 🎉🎉🎉
그리고 내용도 좀 달라졌다!</p>
<p>꾸준히 점검해가며 관리해야겠다! 흐흐</p>
<p><br /><br /></p>
<hr />
<h3 id="참고-lighthouse-항목-상세">참고: LightHouse 항목 상세</h3>
<h4 id="1-performance">1. Performance</h4>
<p>Lighthouse는 실제 속도가 어떻든 간에, 화면에 콘텐츠가 얼마나 빨리 표시되고 사용자는 얼마나 빠르게 해당 콘텐츠를 인식하는 지에 더욱 초점을 맞추고 있다.</p>
<h4 id="2-accessibility">2. Accessibility</h4>
<p>Lighthouse는 웹 애플리케이션의 접근성을 검사한다. <code>&lt;img&gt;</code> 태그에 <code>alt</code> 속성이 있는지, <code>&lt;html&gt;</code> 태그에 <code>lang</code> 속성이 있는지, 배경색과 전경색의 대비가 충분한지와 같은 항목을 확인한다.</p>
<h4 id="3-best-practices">3. Best Practices</h4>
<p>Lighthouse는 웹 페이지가 웹에 대한 표준 모범 사례를 따르고 있는지 확인한다. 웹 애플리케이션을 가동할 때 콘솔에 오류가 출력되진 않는지, 더는 사용하지 않는 API를 호출하고 있지 않은지, HTTPS를 통해 해당 페이지에 접근할 수 있는지와 같은 항목을 확인한다.</p>
<h4 id="4-seo">4. SEO</h4>
<p>Lighthouse는 웹 페이지가 검색 엔진에 대해 최적화된 순위 결과를 가지고 있는지 확인한다. 각 사용자가 자신의 디바이스를 이용하여 웹 페이지에 접근하였을 때 그들이 콘텐츠를 읽는 데에 무리가 없는 글꼴 크기를 사용하는지, 웹 페이지의 robots.txt 파일이 유효한지, 올바른 상태 코드를 사용하는 지와 같은 일부 SEO 모범 사례를 확인한다.</p>
<h4 id="5-progressive-web-app">5. Progressive Web App</h4>
<p>Lighthouse는 Progressive Web App을 정의하는 일련의 기준에 따라 웹 페이지를 확인한다. 이 검사는 해당 웹 페이지가 항목을 따르고 있는지 측정하여 점수를 부여하는 것이 아니다. 웹이 HTTP를 HTTPS로 리다이렉션하는지, 응답 코드는 명확한지, 3G 네트워크에서도 로딩이 빠르게 이루어지는 지와 같은 여부를 검사하여 합격 또는 실패를 부여한다.</p>