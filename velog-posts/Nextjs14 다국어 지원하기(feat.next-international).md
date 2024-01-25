<h2 id="들어가며">들어가며,</h2>
<p>이번에 회사에서 서비스 외에 새로운 프로젝트를 진행하게 되었는데
레포부터 새로 생성하는 거라 새로운, 안써본, 트렌디한 스택으로 진행하기로 했다
그 중 <code>next14</code>로 시작하면서, 결제, 다국어 지원을 내가 맡아 적용하기로 했다</p>
<p>결제는 경험이 있어 크게 어렵지 않았는데,
다국어 지원은 생각보다 레퍼런스가 별로 없어 공식문서에 의지하며 적용한 내용을 기록해본다!!</p>
<p>(진짜.. 3일만에 만들어서 QA 하고 운영배포 하고..😩 그 다음주인 2주차에 글로벌 작업 2일만에 하고.. QA 준비를 하고 있다..😫 빡신 일정이었지만.. 계획대로 진행되어 다행.. 과정에 많은 빡침🤬이 있었지만.. 뭐.. 그래..)</p>
<br />

<hr />
<h2 id="폴더구조">폴더구조</h2>
<pre><code class="language-tsx">├── locales
│       ├── client.ts // 클라이언트에서 동작하도록 셋팅
│       ├── server.ts // 서버에서 동작하도록 셋팅
│       ├── en.ts
│       └── ko.ts
├── middleware.ts // defaultLocale, locales 설정 등
└── app
    └── [locale] 
        ├── layout.tsx
        └── page.tsx
</code></pre>
<h2 id="다국어-설정">다국어 설정</h2>
<ul>
<li><p><code>middleware.ts</code></p>
<pre><code class="language-tsx">  import { createI18nMiddleware } from 'next-international/middleware';
  import type { NextRequest } from 'next/server';

  const I18nMiddleware = createI18nMiddleware({
    locales: ['ko', 'en'],
    defaultLocale: 'ko',
  });

  export function middleware(request: NextRequest) {
    return I18nMiddleware(request);
  }

  export const config = {
    matcher: ['/((?!api|static|.*\\..*|_next|favicon.ico|robots.txt).*)'],
  };</code></pre>
</li>
</ul>
<p><img alt="" src="https://velog.velcdn.com/images/april_5/post/8cedc7d2-76d6-4b99-a412-898a64c31601/image.png" /></p>
<ul>
<li><p><code>locales/server.ts</code></p>
<pre><code class="language-tsx">  import { createI18nServer } from 'next-international/server';

  export const { getI18n, getScopedI18n, getCurrentLocale, getStaticParams } =
    createI18nServer(
      {
        ko: () =&gt; import('./ko'),
        en: () =&gt; import('./en'),
      },
      {
        // Uncomment to use custom segment name
        // segmentName: 'locale',
        // Uncomment to set fallback locale
        // fallbackLocale: en,
      },
    );</code></pre>
</li>
<li><p><code>locales/client.ts</code></p>
<pre><code class="language-tsx">  import { createI18nClient } from 'next-international/client';

  export const {
    useI18n,
    useScopedI18n,
    I18nProviderClient,
    useChangeLocale,
    defineLocale,
    useCurrentLocale,
  } = createI18nClient(
    {
        ko: () =&gt; import('./ko'),
        en: () =&gt; import('./en'),
    },
    {
      // Uncomment to set base path
      // basePath: '/base',
      // Uncomment to use custom segment name
      // segmentName: 'locale',
      // Uncomment to set fallback locale
      // fallbackLocale: en,
    },
  );</code></pre>
</li>
</ul>
<ul>
<li><p><code>locales/ko.ts</code></p>
<pre><code class="language-tsx">  console.log('Loaded KO');

  export default {
    hello: '안녕',
    welcome: '안녕 {name}!',
    'detail.title': '헬로우봇 AI 프로필 생성기',
  } as const;</code></pre>
</li>
</ul>
<ul>
<li><p><code>locales/en.ts</code></p>
<pre><code class="language-tsx">  console.log('Loaded EN');

  export default {
    hello: 'Hello',
    welcome: 'Hello {name}!',
    'detail.title': 'hello ai profile',
  } as const;</code></pre>
</li>
<li><p><code>app/[locale]/provider.ts</code></p>
<pre><code class="language-tsx">  'use client';

  import type { ReactNode } from 'react';
  import { I18nProviderClient } from '../../locales/client';

  type ProviderProps = {
    locale: string;
    children: ReactNode;
  };

  export function Provider({ locale, children }: ProviderProps) {
    return (
      &lt;I18nProviderClient locale={locale} fallback={&lt;p&gt;Loading...&lt;/p&gt;}&gt;
        {children}
      &lt;/I18nProviderClient&gt;
    );
  }</code></pre>
</li>
</ul>
<h2 id="사용법">사용법</h2>
<pre><code class="language-tsx">import { getI18n, getScopedI18n, getCurrentLocale } from '../../../../locales/server';
import { useChangeLocale, useCurrentLocale } from '../../../../locales/client';  // 'use client'; 에서 적용
import { Provider } from '@/app/[locale]/provider.ts';

export default function MyComponent () {
  const t = await getI18n(); // 단일 사용
  const t2 = await getScopedI18n('detail'); // 묶음으로 사용
  const currentLocale = getCurrentLocale(); // 현재 값 가져오기
  const locale = useCurrentLocale() // 현재 값 가져오기, 'use client'; 에서 적용

  const changeLocale = useChangeLocale(); // 언어 변경하기

  const onClick = () =&gt; {
  // 'use client'; 에서 적용
    changeLocale('en');
  };

 return (
      &lt;&gt;
        &lt;h1&gt;{t('title')}&lt;/h1&gt;
        &lt;p&gt;{t2('title')}&lt;/p&gt; {/* detail.title 값이 적용 됨 */} 
        &lt;p&gt;{t('welcome', { name: 'yurim' })}&lt;/p&gt;  {/* 변수 사용 가능 */} 
        &lt;button type=&quot;button&quot; onClick={onClick}&gt;언어 변경&lt;/button&gt;

        &lt;Provider locale={locale}&gt; {/* 가장 하위의 컴포넌트에 적용할 것! */} 
            &lt;ClientComponent /&gt; {/* client component인 경우는 Provider로 감싸야 적용 됨 */} 
        &lt;/Provider&gt;
      &lt;/&gt;
  );
}
</code></pre>
<br />

<hr />
<h2 id="관련-링크">관련 링크</h2>
<ul>
<li><a href="https://nextjs.org/docs/app/building-your-application/routing/internationalization">nextjs 공식문서</a></li>
<li><a href="https://github.com/QuiiBz/next-international/tree/main/examples/next-app">next-international 깃허브</a></li>
</ul>
<p><br /><br /><br /></p>