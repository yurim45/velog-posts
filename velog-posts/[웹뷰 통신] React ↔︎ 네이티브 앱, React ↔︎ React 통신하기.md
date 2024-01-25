<h3 id="들어가기-전에">들어가기 전에,</h3>
<p>이번 스프린트는 웹소설 기능 구현이었다. 
<code>epubjs</code> 라이브러리를 사용해서 구현한 <code>react</code> 앱을 ios, android, web(Nextjs) 안에 띄워야 했고, 
그 중 <strong><code>epubjs</code> 라이브러리를 사용해서 <code>react</code> 앱을 구현하는 것</strong>이 내 목표였다.
<em>(<code>epubjs</code> 라이브러리.. 제공하는 기능이 에러가 많아 힘든 라이브러리..😱😱)</em></p>
<p>해당 기능을 구현하는 과정에서 <code>epubjs</code> 라이브러리를 처음 다뤄보기도 했지만 웹뷰 통신도 처음 경험해보는 기능이었기에 
2주 안에, 정확히는 공휴일 제외하고 영업일 7일 동안!! 
<code>QA</code> 및 <code>버그 수정</code>까지 마쳐야 하는 일정을 소화할 수 있을까 걱정도 많았지만 결국은 잘 끝냈다..💪💪👍😂</p>
<p>그 과정에서 처음 경험해본 웹뷰 통신을 간단히 기록해본다!</p>
<br />

<hr />
<h2 id="요구사항">요구사항</h2>
<p>ios, android, web에 웹뷰 형태로 띄워질 <code>epub 뷰어 웹 어플리케이션</code>을 구현한다.
네이티브(또는 Nextjs앱)와 통신하여 뷰어가 보여줄 epubUrl, 뷰어 스타일 등의 데이터를 주고받는다.</p>
<p><img alt="" src="https://velog.velcdn.com/images/april_5/post/4e21e9b0-082f-4eb3-8154-9669566932e0/image.png" /></p>
<br />

<hr />
<h2 id="준비">준비</h2>
<blockquote>
<p>💡 핵심은 window 객체를 이용하는 것!</p>
</blockquote>
<p><img alt="" src="https://velog.velcdn.com/images/april_5/post/4e26a78f-a0e2-4ad7-90c0-a519df44bff5/image.png" /></p>
<ul>
<li>통신을 할 때 window 객체를 이용하게 되는데 미리 타입을 지정해두지 않으면 에러 발생</li>
<li>구현하려는 함수 등을 window에 추가하기</li>
</ul>
<pre><code class="language-ts">// global.d.ts 예시
declare global {
  interface Window {
    webkit: {
      messageHandlers: {
        spWebNovel: {
          postMessage: (parameter: string) =&gt; void;
        };
      };
    };
    // 안드와 통신할 때 주고 받을 객체를 나눴다
    androidWebApi: {
      initContent: (parameter: InitContent) =&gt; void;
    androidWebViewApi: {
      spWebNovel: (parameter: string) =&gt; void;
    };

    initContent: (parameter: InitContent) =&gt; void;
  }
}

export {};</code></pre>
<br />

<hr />
<h2 id="react-↔︎-네이티브-앱-통신">React ↔︎ 네이티브 앱 통신</h2>
<h3 id="보내기">보내기</h3>
<p><img alt="" src="https://velog.velcdn.com/images/april_5/post/034163d6-5402-4635-8daa-293dadb22239/image.png" /></p>
<pre><code class="language-tsx">// react가 ios에게 보내기
// 찾아보니  window.webkit.messageHandlers 안에 등록해야 한다고 한다
 window.webkit.messageHandlers.spWebNovel.postMessage(
      // ... 전달할 데이터
 );


// react가 안드로이드에게 보내기
// 안드에서 정의한 spWebNovel를 react가 호출해서 사용하는 방식
// 안드와 androidWebViewApi 안에 추가하기도 협의함
window.androidWebViewApi.spWebNovel(
    // ... 전달할 데이터
);</code></pre>
<h3 id="받기">받기</h3>
<ul>
<li>react에서 ios 또는 안드가 사용할 함수를 정의해서 ios 또는 안드가 호출해서 사용하는 방식</li>
</ul>
<pre><code class="language-tsx">// ios가 react의 함수 사용
 window.initContent(parameter) {
   // ... 로직
 };


// 안드가 react의 함수 사용
// 안드와 androidWebApi 안에 정의하기로 협의함
 window.androidWebApi.initContent(parameter) {
   // ... 로직
 };</code></pre>
<h2 id="react-↔︎-reactnextjs-통신">React ↔︎ React(Nextjs) 통신</h2>
<p><a href="https://developer.mozilla.org/en-US/docs/Web/API/Window/postMessage">Window: postMessage() method - Web APIs | MDN</a></p>
<h3 id="보내기-1">보내기</h3>
<pre><code class="language-tsx">// 웹뷰를 품고 있는 상위에게 메세지 보내기
// iframe 입장에서 상위의 window에 메세지를 보내는 것이므로 parent애 접근!
 window.parent.postMessage(
   JSON.stringify({
     isReady: true,
   }),
   '*',
 );</code></pre>
<h3 id="받기-1">받기</h3>
<h4 id="에러나지-않게-window에-선언해주기">에러나지 않게 Window에 선언해주기</h4>
<pre><code class="language-ts">declare global {
  interface Window {
    initContent: (parameter: InitContent) =&gt; void;
    // ... 생략
  }
}</code></pre>
<h4 id="구현-코드-예시">구현 코드 예시</h4>
<pre><code class="language-tsx">const iframeRef = useRef&lt;HTMLIFrameElement | null&gt;(null);

useEffect(() =&gt; {
  iframeRef.current = document.getElementById('iframe') as HTMLIFrameElement;
}, []);

const handleButton = () =&gt; {
  // ... 생략

  // epub 뷰어에서 제공하는 함수 사용하기
  iframeRef.current?.contentWindow?.postMessage(
    {
      type: 'initContent', 
        parameter: {
             // ... 생략
        },
     },'*',
  );
}</code></pre>
<br />

<hr />
<h2 id="추가-ios에서-인스타그램-인앱-크롬-브라우저에서-오류-발생">추가: iOS에서 인스타그램 인앱, 크롬 브라우저에서 오류 발생!!</h2>
<p>해당 기능이 구현되고 정식 배포 후..
<span style="color: fuchsia;">iOS에서 인스타 인앱 브라우저와 크롬 브라우저에서 렌더링되지 않는 현상</span>이 발생한다는 제보를 받게 되었다.😱</p>
<br />

<h3 id="이슈">이슈</h3>
<p>iOS 크롬 모바일, 인앱 브라우저에서 작동하지 않는 이슈 발생</p>
<h3 id="원인-파악">원인 파악</h3>
<p>iOS 크롬 모바일, 인앱 브라우저 환경에서</p>
<pre><code class="language-js">if (window.webkit?.messageHandlers) </code></pre>
<p>이 조건문이 통과되어</p>
<pre><code>window.webkit.messageHandlers.spWebNovel.postMessage</code></pre><p>이 로직이 실행되고 있었음.</p>
<p>해당 조건문은 iOS 네이티브 앱을 위한 조건문이었는데 iOS 브라우저 환경에서 통과되면서
정의되지 않은 객체 내부에 있는 메소드를 실행하게 되면서 <code>undefined is not object</code> 에러가 발생</p>
<p><img alt="" src="https://velog.velcdn.com/images/april_5/post/47fd746b-54bb-4aea-bab2-5d39f8abf453/image.png" /></p>
<h3 id="해결-방법">해결 방법</h3>
<p>이슈가 발생했던 조건문을 수정해서 해결</p>
<pre><code class="language-js">if (window.webkit?.messageHandlers?.spWebNovel?.postMessage) </code></pre>
<h3 id="원인-파악2">원인 파악2</h3>
<blockquote>
<p>왜 크롬 모바일앱에서 웹뷰 코드가 실행되었을까..??</p>
</blockquote>
<p><span style="color: dodgerblue;"><strong>[gpt의 답변]</strong></span></p>
<p>크롬 모바일 앱에서 웹 뷰를 실행할 때, <code>window.webkit.messageHandlers</code> 객체를 통해 웹 콘텐츠와의 통신을 제공하기 위한 기능이 포함되어 있기 때문입니다.</p>
<p><code>window.webkit.messageHandlers</code> 객체는 iOS에서 사용되는 WebView의 기능 중 하나로, iOS 앱과 웹 콘텐츠 간의 메시지 전달을 위한 인터페이스를 제공합니다. 이를 통해 iOS 앱에서 웹 페이지로 메시지를 보낼 수 있고, 웹 페이지에서 iOS 앱의 기능을 호출할 수 있습니다.</p>
<p><br /><br /></p>
<br />