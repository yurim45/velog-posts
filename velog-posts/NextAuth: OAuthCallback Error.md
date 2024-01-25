<h2 id="들어가며">들어가며,</h2>
<p><span style="color: lightcoral;"><strong><a href="https://next-auth.js.org/getting-started/example">NextAuth</a></strong></span>는 <code>Next.Js</code>에서 제공하는 <span style="color: dodgerblue;"><strong>Social로그인을 쉽게 관리할 수 있는 라이브러리</strong></span>다</p>
<p>내가 운영하는 서비스에도 NextAuth를 이용해서 애플, 구글, 페이스북 회원가입/로그인이 구현되어 있는데 최근 이슈가 제보되어서 조치했던 적이 있다. 
이슈의 내용은 소셜 로그인을 진행했을 때</p>
<ul>
<li>회원가입 과정이 매끄럽지 못하고<ul>
<li>두 번 회원가입 버튼을 클릭하거나</li>
<li>회원가입을 해도 다시 회원가입 절차가 진행되거나(두 번 회원가입 후 정상 로그인 가능)</li>
</ul>
</li>
<li>또는 그 과정에서 예기치 못한 에러(<code>OAuthCallback Error</code>)가 발생해서 정상 회원가입이 되지 않는 등</li>
</ul>
<p>의 현상이다. </p>
<p>결과적으로 진입 url의 문제로 파악되었는데, 케이스1의 경우 위의 제보와 동일한 현상이 발생하고 케이스2는 회원가입/로그인이 정상 동작한다.</p>
<ul>
<li>케이스1: <a href="http://www.example.com">www.example.com</a></li>
<li>케이스2: example.com</li>
</ul>
<p>제보 내용을 조치하려 에러 핸들링을 위한 코드가 추가되었다가 원복했고 결과적으로 코드상 별다른 조치를 하지 않았음에도 종결된 이슈..
(결과가 도출되기까지의 심리적으로나 원인 파악의 과정이 고달팠다..🥲 
 회원가입/로그인은 서비스의 수익 창출에 매우 중요한 부분이므로.. 당연하지만 즉각 대처를 해야하는데 그 과정에서 심리적인 압박이 매우 있는 편이다..🫠🫠)</p>
<p>운영할 때 이런 이슈가 발생할 수 있구나 싶어 잊지 않으려 기록해본다</p>


<hr />
<h2 id="원인-파악-및-조치-과정">원인 파악 및 조치 과정</h2>
<p>케이스 1로 진입하여 회원가입을 시도하면 버튼을 두 번 클릭해야만 회원가입이 진행되었고,</p>
<p><img alt="" src="https://velog.velcdn.com/images/april_5/post/4526b325-f5fd-4e1d-86ff-2fbfa58dab1e/image.png" /></p>
<p>callback url에 <code>error=OAuthCallback</code>와 같이 에러가 전달되어왔다.</p>
<ul>
<li><code>https://example.com/auth?callbackUrl=https%3A%2F%2Fexample.com&amp;error=OAuthCallback</code> </li>
</ul>
<p>구글링해보니 아래의 사례를 확인할 수 있었지만 특별히 원인 파악이 되거나, 나의 상황에 적용할만한 코드가 있었던 것은 아니었다.</p>
<ul>
<li><a href="https://atrix-developer.tistory.com/3">[Next.js] 내가 겪은 next-auth Error들</a></li>
<li><a href="https://github.com/nextauthjs/next-auth/issues/4190">checks.state argument is missing</a></li>
<li><a href="https://github.com/nextauthjs/next-auth/discussions/7491">OAUTH_CALLBACK_ERROR State cookie was missing</a></li>
</ul>
<p>결국 근본적인 원인은 파악하지 못하고 <code>OAuthCallback</code>의 에러에 대한 핸들링 코드만 추가한채 핫픽스 배포가 되었고.. 다시 이슈 제보가 되었다.🥲</p>
<h3 id="재현해보기">재현해보기!</h3>
<p>핫픽스 배포를 했을 당시(그전 배포에서도..) 내가 테스트했을 땐 크롬, 사파리, 인스타그램 인앱 브라우저에서 <span style="color: dodgerblue;"><strong>정상 동작</strong></span>을 하는데 제보와 함께 첨부된 영상에서는 그렇지 못했다. 이 쯤에서 멘탈이 슬슬 흔들리기 시작.. <code>나는 되는데 제보자는 왜 안되는 걸까..</code>라는 물음표와 함께 다시 원인 파악에 나섰다..💪💪</p>


<p>차근차근 제보된 <span style="color: lightcoral;"><strong><code>url</code>을 클릭</strong></span>해서 첨부된 영상과 동일하게 버튼 하나하나 클릭하는데 왠걸.. <span style="color: lightcoral;"><strong>제보된 영상과 동일한 현상이 발생</strong></span>하지 않는가..😱😱
그래서 이상하다 싶어 <span style="color: dodgerblue;"><strong>직접 <code>url</code>을 입력</strong></span>해서 영상과 동일하게 버튼 하나하나 클릭해보았더니 <span style="color: dodgerblue;"><strong>정상 동작</strong></span>했다..🫨🫨</p>
<p><code>왜지? 왜 url을 클릭해서 들어가면 안되고 직접 입력하면 되는거지?? 😫😫</code> 라고 멘탈 바사삭하며 고민하던 차에 <code>url</code>이 눈에 들어왔다!!!😳</p>
<p>전혀 의심하지 못했는데.. <code>url</code>에 <code>www</code>가 붙어있지 않은가!!!🫠
그래서 <span style="color: fuchsia;"><strong>직접 <code>url</code> 입력할 때 <code>www</code>를 붙혀 테스트했더니 제보된 영상과 동일한 현상이 발생</strong></span>했다... </p>
<h3 id="원인-파악">원인 파악!</h3>
<p>알고보니 NextAuth를 셋팅할 때 <code>NEXTAUTH_URL</code>라는 환경변수를 설정하는데
<code>NEXTAUTH_URL</code> 환경변수의 값으로 <code>www</code>가 제외된 도메인을 추가했고(서비스 랜딩 시 www 없이 랜딩한다) 
그로 인해 <code>www</code>가 추가된 도메인에서는 정상 동작하지 않았던 것이다.</p>
<p>구글링해서 확인했던 <code>OAuthCallback</code>가 발생하는 원인 중 
<u><strong>회원가입 과정에서 상태값을 잃어버려 <code>OAuthCallback</code>가 발생한다는 의미</strong></u>를 이제야 이해하게 되었다.</p>
<ul>
<li>참고: <code>NEXTAUTH_URL</code>은 next.js가 실행될 URL을 의미</li>
</ul>
<p>그러나 코드 상으로 <code>NEXTAUTH_URL</code>을 핸들링 할 수 있는 구조는 아니고
(시도해보았으나 실패.. 일단 백로그로 이동..)
배포할 때 <code>NEXTAUTH_URL</code> 환경변수가 설정되는데 이 때 NextAuth는 이미 www가 없는 주소로 초기화된 상태라
제보자쪽에서 www 없이 마케팅하는 방향으로 해결되었다..🙃</p>


<hr />
<h2 id="참고-url에-www서브-도메인가-있고-없고의-차이🙃">참고: url에 www(서브 도메인)가 있고 없고의 차이🙃</h2>
<p><code>www</code> 라는 말은 <code>world wide web</code> 이라는 문장의 줄임말인데
인터넷이 생겨난 초기에 <code>www</code>를 홈페이지를 표시하는 대표 문구로 많이들 사용하면서 케이스1과 케이스2가 같은거라고 알고 있는 사람이 많다</p>
<ul>
<li>케이스1: <a href="http://www.example.com">www.example.com</a></li>
<li>케이스2: example.com</li>
</ul>
<p>하지만 <code>www</code>라는 2차 도메인을 어느 서버로 가라고 지정하지 않는다면 케이스 1은 존재하지 않는 도메인이다</p>
<p><img alt="" src="https://velog.velcdn.com/images/april_5/post/179ab5e8-75dd-444a-87fe-f601a28d0859/image.png" /></p>
<p>예를 들어 <code>mail.naver.com</code>이라고 입력하면 네이버 메일창으로 가는 것처럼..!!
(네이버 도메인 앞에 mail이라는 단어가 붙으면 메일서버로 가라고 네이버에서 DNS에 지정을 해줬기 때문에 가능!)</p>
<p></p>