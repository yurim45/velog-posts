<h2 id="들어가며">들어가며,</h2>
<p>지난해 말~올해 초까지 <code>Nextjs14</code>로 <strong>반려동물 AI 프로필</strong> 사이트를 개발했었는데, 
(참고: 해당 사이트의 <a href="https://velog.io/@april_5/Nextjs14-%EB%8B%A4%EA%B5%AD%EC%96%B4-%EC%A7%80%EC%9B%90%ED%95%98%EA%B8%B0feat.next-international">다국어 작업</a>)
이번에 서버 다운 대응을 위한 페이지 작업을 하다보니 <code>notFound</code> 페이지가 제대로 작동하지 않는 것을 발견.
이번에 작업하며 생각보다 레퍼런스 찾기가 쉽지(?) 않았기에 <code>notFound</code> 처리에 대해 정리해 본다.</p>
<br />

<hr />
<h2 id="공식문서에서-말하는-notfound"><a href="https://nextjs.org/docs/app/api-reference/file-conventions/not-found">공식문서</a>에서 말하는 notFound</h2>
<blockquote>
<p><code>notFound</code> 함수가 route segment에서 throw됐을 때 또는 URL에 일치하는 route가 존재하지 않을 때 표시한다.</p>
</blockquote>
<p><img alt="" src="https://velog.velcdn.com/images/april_5/post/2f00bbb5-c9a5-43d6-ad9d-8bb6a7004a1f/image.png" /></p>
<br />

<h2 id="사용법">사용법</h2>
<h3 id="▪︎-ssr">▪︎ SSR</h3>
<pre><code class="language-tsx">import { notFound, redirect } from 'next/navigation';

export default async function Home() {
  const user = await fetchUser();

  if (!user) {
    notFound();
  }

    return (
    &lt;&gt;
      {/* ... */}
    &lt;/&gt;
    )
}</code></pre>
<h3 id="▪︎-csr">▪︎ CSR</h3>
<pre><code class="language-tsx">'use client';

import React, { useEffect, useState } from 'react';
import { notFound, redirect } from 'next/navigation';

export default async function Home() {
  const [isError, setIsError] = useState(false);

  useEffect(() =&gt; {
    isError &amp;&amp; notFound();
  }, [isError]);

  return (
    &lt;&gt;
      {/* ... */}
    &lt;/&gt;
  )
}</code></pre>
<h3 id="▪︎-폴더-route">▪︎ 폴더 route</h3>
<blockquote>
<p>위의 <code>SSR</code>과 <code>CSR</code> 처리까지는 잘 되어 있는 상태였는데, 
이번에 서버 다운 대응하면서 존재하지 않는 url로 접속 시 
만들어 놓은 <code>not-found.tsx</code> 페이지가 아닌, 기본 제공해주는 404 페이지로 이동하는 것을 발견했다. 
<code>app</code> 폴더 하위에 <code>[...not_found]</code> 폴더를 만들고 <code>page.tsx</code>를 만들어 해결했다.</p>
</blockquote>
<p>확인해보니 공식문서에서 <code>참고: not-found.js는 현재 notFound 함수에 의해 트리거될 때만 렌더링됩니다. 우리는 일치하지 않는 경로를 포착하기 위한 지원을 위해 노력하고 있습니다.</code> 라고 한다</p>
<ul>
<li><p>app/[...not_found]/page.tsx</p>
<p><img alt="" src="https://velog.velcdn.com/images/april_5/post/874bfde2-cbf8-4498-a31f-1f5a686038bd/image.png" /></p>
<pre><code class="language-tsx">import {notFound} from &quot;next/navigation&quot;

export default function NotFoundCatchAll() {
  notFound()
}</code></pre>
</li>
</ul>
<br />

<hr />
<h3 id="참고">참고</h3>
<p><a href="https://stackoverflow.com/questions/75302340/not-found-page-does-not-work-in-next-js-13">스택오버플로우</a></p>
<p><br /><br /><br /></p>