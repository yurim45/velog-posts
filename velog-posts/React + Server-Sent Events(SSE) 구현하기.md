<h2 id="■-들어가며">■ 들어가며,</h2>
<p>그 동안 사용해왔던 api 호출은 요청/응답 형식 중심으로 진행되었는데,
이번에 구현해야 할 로직은 실시간으로 데이터를 받아오는 형식으로 해야했다.</p>
<ul>
<li>즉, 서버에서 클라이언트로 업데이트를 스트리밍 한다는 것. </li>
</ul>
<p>이런 경우, 적용 가능한 것을 찾아보니 <code>Server-Sent Events(SSE)</code> 라는 것을 알게 되었다. </p>
<p>개발 전 웹소켓(<code>WebSocket</code>)과 <code>SSE</code> 중 어느 것을 적용할 지 논의되었는데
웹소켓은 인프라쪽도 변경(프로토콜을 처리하기 위해 전이중 연결과 새로운 웹 소켓 서버가 필요)이 되어야 해서 </p>
<ul>
<li>브라우저에서 직접 처리되는, </li>
<li>웹소켓보다 간단히 구현할 수 있는, </li>
<li>즉 특별한 프로토콜이나 서버 구현이 필요하지 않는, </li>
<li>웹소켓보다 가볍고 서버 → 클라이언트 방향을 지원하는</li>
</ul>
<p>SSE를 적용하기로 결졍되어 개발하면서 알게 된 SSE를 기록해본다</p>
<hr />
<h1 id="server-sent-eventssse-란"><code>Server-Sent Events(SSE)</code> 란?</h1>
<p><a href="https://developer.mozilla.org/ko/docs/Web/API/EventSource">EventSource - Web API | MDN</a>
<a href="https://ko.javascript.info/server-sent-events">Server Sent Events</a></p>
<blockquote>
<p>💡 <code>Server-Sent Events(SSE)</code> 란, 서버의 데이터를 실시간으로, 지속적으로 streaming 하는 기술.</p>
</blockquote>
<br />

<h3 id="✓-실시간-통신-기술-비교">✓ 실시간 통신 기술 비교</h3>
<table>
<thead>
<tr>
<th>polling(client pull)</th>
<th>webSocket(server push)</th>
<th>sse(server push)</th>
</tr>
</thead>
<tbody><tr>
<td>클라이언트가 일정한 주기로 서버에 업데이트 요청을 보내는 방법. 지속적인 HTTP 요청이 발생하기 때문에 리소스 낭비가 발생한다</td>
<td>실시간 양방향 데이터 통신을 위한 스펙으로 서버와 브라우저가 지속적으로 연결된 TCP라인을 통해 실시간 데이터를 주고받을 수 있도록 하는 HTML5 사양이다. 연결지향 양방향 통신이 가능하며 채팅, 게임, 주식 차트 등에 사용된다. polling은 주기적으로 HTTP 요청을 수행하지만 webSocket은 연결을 유지하여 서버와 클라이언트 간 양방향 통신이 가능하다.</td>
<td>이벤트가 서버→ 클라이언트 방향으로만 흐르는 단방향 통신 채널이다. SSE는 클라이언트가 polling과 같이 주기적으로 HTTP 요청을 보낼 일 없이 HTTP 연결을 통해 서버에서 클라이언트로 데이터를 보낼 수 있다</td>
</tr>
<tr>
<td><img alt="" src="https://velog.velcdn.com/images/april_5/post/3c9cbb1e-951d-46b7-8d11-2fa637c3c69c/image.png" /></td>
<td><img alt="" src="https://velog.velcdn.com/images/april_5/post/bc3d8915-5a6b-48e5-a38a-20feb26d2089/image.png" /></td>
<td><img alt="" src="https://velog.velcdn.com/images/april_5/post/f28a2d67-5e44-40dd-a165-1a119f839595/image.png" /></td>
</tr>
</tbody></table>
<br />

<h3 id="✓-sse-특징-정리">✓ SSE 특징 정리</h3>
<ul>
<li>JavaScript는 SSE를 쉽게 사용할 수 있도록 <a href="https://developer.mozilla.org/ko/docs/Web/API/EventSource">EventSource API</a>를 제공한다</li>
<li>webSocket과 달리 별도의 프로토콜을 사용하지 않고 HTTP 프로토콜만으로 사용이 가능하며 훨씬 가볍다.</li>
<li>접속에 문제가 있으면 자동으로 재연결을 시도한다.</li>
<li>IE를 제외한 브라우저에서 지원된다.(Polyfills을 사용하면 가능하다고 한다.)</li>
<li>이벤트 데이터는 UTF-8 인코딩된 문자열만 지원한다. 일반적으로 JSON으로 전송한다.</li>
<li>클라이언트에서 페이지를 닫아도 서버에서 감지하기가 어렵다.</li>
</ul>
<br />

<h3 id="✓-적용코드">✓ 적용코드</h3>
<ul>
<li>적용하면서 연결이 끊어지는 시점에 <code>eventSource.close()</code>적용해야 했는데<ul>
<li>안 해주면 계속 데이터를 받아오더라.. 내 경우는 꼭 종료를 시켜줘야 했다;;</li>
</ul>
</li>
<li><code>onerror</code> 이벤트가 에러 뿐만 아니라 종료 시점도 된다는 것을 나중에 알게 돼서 이때 좀 어려움을 겪었다..😰</li>
</ul>
<pre><code class="language-tsx">const fetchSSE = () =&gt; {
    const eventSource = new EventSource(url);

    eventSource.onopen = () =&gt; {
      // 연결 시 할 일
    };

    eventSource.onmessage = async (e) =&gt; {
      const res = await e.data;
      const parsedData = JSON.parse(res);

      // 받아오는 data로 할 일
    };

    eventSource.onerror = (e: any) =&gt; {
      // 종료 또는 에러 발생 시 할 일
      eventSource.close();

      if (e.error) {
        // 에러 발생 시 할 일
      }

      if (e.target.readyState === EventSource.CLOSED) {
        // 종료 시 할 일
      }
    };
 };</code></pre>
<br />

<hr />
<h2 id="■-헤더-추가하기">■ 헤더 추가하기</h2>
<ul>
<li>작업 하다보니 헤더에 <code>token</code>을 보내야 했다. 그런데 <code>EventSource</code> 는 지원하지 않는다..😰</li>
<li>그래서 찾아보니 <a href="https://yarnpkg.com/package/event-source-polyfill"><code>EventSourcePolyfill</code></a> 라는 것을 알게 되었다.</li>
</ul>
<h3 id="✓-설치">✓ 설치</h3>
<pre><code class="language-bash">npm install event-source-polyfill
// 또는
yarn add event-source-polyfill</code></pre>
<h3 id="✓-적용코드-1">✓ 적용코드</h3>
<pre><code class="language-tsx">import { EventSourcePolyfill } from 'event-source-polyfill';

const fetchSSE = () =&gt; {
    const eventSource = new EventSourcePolyfill(url, {
            headers: {
        token: authToken,
      },
    });

    // 생략.. 동일..
};
</code></pre>
<br />

<hr />
<h2 id="■-post-로-변경하기">■ POST 로 변경하기..</h2>
<p><a href="https://yogae.github.io/etc/2019/06/11/node_client_stream.html">POST 로 변경</a></p>
<p>위의 방법은 <code>GET</code> 메서드만 지원하는데, 요청 작업에서 <code>POST</code> 메서드로 변경해야 하는 일이 생겼다</p>
<ul>
<li>실시간 요청을 하는 서버측에 필요한 params 는 url에 query 로 보내고 있었기에 <code>GET</code> 으로 처리가 가능했는데</li>
<li>알고보니 일부 params에 글자 제한이 있어 <code>POST</code> 로 변경해서 body에 보내야 했다.</li>
</ul>
<p>서버는 node.js 환경이라 <code>axios</code>로 가능했는데 브라우저에서 불가능해서 <code>fetch</code> 를 사용해서 구현했다</p>
<pre><code class="language-tsx">const fetchSSE = () =&gt; {
    fetch(url, {
      method: 'POST',
      headers: {
        token: authToken,
        'Content-Type': 'application/json; charset=utf-8',
      },
      body: {
         // 생략
      },
     })
      .then((response) =&gt; {
        const reader = response.body!.getReader();
        const decoder = new TextDecoder();

        const readChunk = () =&gt; {
          return reader.read().then(appendChunks);
        };

        const appendChunks = (result) =&gt; {
          const chunk = decoder.decode(result.value || new Uint8Array(), {
            stream: !result.done,
          });
          const parseData = JSON.parse(chunk);
          // 받아오는 data로 할 일

          if (!result.done) {
            return readChunk();
          }
        };

        return readChunk();
      })
      .then(() =&gt; {
        // 종료 시 할 일
      })
      .catch((e) =&gt; {
        // 에러 처리
      });
};</code></pre>