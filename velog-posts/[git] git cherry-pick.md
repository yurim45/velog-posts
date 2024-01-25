<h2 id="cherry-pick">Cherry Pick</h2>
<blockquote>
<p>다른 브랜치의 일부 커밋만 반영하고 싶을 때 사용하는 깃 명령어</p>
</blockquote>
<ul>
<li>히스토리를 갖고 있는 해당 줄기(브랜치)를 전부 다 가져오고 싶은 게 아니라 특정 커밋만 골라내서 가져올 수 있는 깃 명령어, 체리픽을 사용</li>
</ul>
<h3 id="git-명령어로-사용하기">git 명령어로 사용하기</h3>
<ul>
<li>master와 hotfix 두 개의 브랜치가 있다고 가정하고</li>
<li>hotfix의 특정 커밋을 master에 적용하고 싶다면</li>
</ul>
<pre><code class="language-bash"># hotfix 브랜치로 이동해서 git log 에서 가져오고 싶은 커밋 id 확인
git checkout hotfix
git log </code></pre>
<pre><code class="language-bash"># master 브랜치로 이동해서 git log 에서 가져오고 싶은 커밋 id 확인
git checkout master
git cherry-pick 커밋 id</code></pre>
<h3 id="체리픽-충돌-해결-방법">체리픽 충돌 해결 방법</h3>
<pre><code class="language-bash"># 충돌난 코드 수정
git add .
git cherry-pick --continue</code></pre>
<h3 id="체리픽-중단">체리픽 중단</h3>
<pre><code class="language-bash"># cherry-pick을 중단하고 이전 상태로 돌아감
git cherry-pick --abort</code></pre>