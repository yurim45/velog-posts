<h2 id="☐-들어가며">☐ 들어가며</h2>
<p>1분기 개발팀 이니셔티브 중 하나인 <code>형상 관리 표준화 및 전파</code> 작업! 주요 내용은 </p>
<ul>
<li>개발 본부의 표준 깃전략 확정</li>
<li>개발 본부의 표준 커밋 컨벤션 확정</li>
<li>개발 본부의 표준 PR 규칙 확정</li>
</ul>
<p>이다. </p>
<p>깃 전략 및 깃허브 워크플로우 전략 등이 </p>
<ul>
<li>각 직무별(<em>서버, 웹 프론트엔드, 안드로이드, iOS</em>)로 다르고</li>
<li>해당 직무의 레포지토리(<em>스플, 헬봇 등</em>)별로 또 달라서</li>
<li>같은 웹 프론트엔드 개발자라 하더라도 어떤 서비스를 담당하느냐에 따라 깃, 배포 전략 등을 새로 익혀야 하고</li>
<li>새로 입사한 개발자의 온보딩 시간을 고려하고</li>
<li>개발팀 역량강화 차원에서</li>
</ul>
<p>해당 이니셔티브를 진행하게 되었다.</p>
<p>그 중 Web Workflow 표준화 작업을 담당하게 되어 작업 히스토리와 결과 산출물을 기록해본다</p>
<p><br /><br /></p>
<hr />
<h2 id="☐-깃헙-워크플로우-동작">☐ 깃헙 워크플로우 동작</h2>
<p><img alt="" src="https://velog.velcdn.com/images/april_5/post/30ad64cc-0b04-487c-a346-71a59b4a9cb5/image.png" /></p>
<h3 id="▪︎-main">▪︎ main</h3>
<pre><code class="language-yaml">name: (서비스명) deploy-prod
on:
    push:
    branches:
        - main # 배포 브랜치

    jobs:
      deploy:
        runs-on: ubuntu-latest

        steps:
          - name: Checkout
            uses: actions/checkout@v2

          - name: Setup Environment
            uses: pnpm/action-setup@v3
            with:
              version: 8 
          - name: Install Dependencies
            uses: actions/setup-node@v3
            with:
              node-version: '18.x' # node-version-file: '.nvmrc'
          - name: Build
            run: |
              pnpm i
              pnpm build


      # 슬랙 알림
      slack_notifications:
        runs-on: ubuntu-latest
        # 배포 결과 슬랙 알림
        steps:
          - name: Notify Deployment Failure
            if: ${{ failure() }}
            uses: slackapi/slack-github-action@v1.23.0
            with:
              payload: |
                {
                  &quot;attachments&quot;: [
                      {
                        &quot;title&quot;: &quot;❌ 배포 실패했습니다 🥲&quot;,
                        &quot;author_name&quot;: &quot;${{github.actor}} 님의 배포 에러 😱&quot;,
                        &quot;color&quot;: &quot;danger&quot;,
                        &quot;fields&quot;: [
                          {
                            &quot;title&quot;: &quot;Branch&quot;,
                            &quot;value&quot;: &quot;${{github.ref}}&quot;,
                            &quot;short&quot;: true
                          },
                          {
                            &quot;title&quot;: &quot;Event&quot;,
                            &quot;value&quot;: &quot;${{github.event_name}}&quot;,
                            &quot;short&quot;: true
                          },
                          {
                            &quot;title&quot;: &quot;Actions URL&quot;,
                            &quot;value&quot;: &quot;&lt;${{github.server_url}}/${{github.repository}}/commit/${{github.sha}}/checks|${{github.workflow}}&gt;&quot;,
                            &quot;short&quot;: true
                          },
                          {
                            &quot;title&quot;: &quot;Commit&quot;,
                            &quot;value&quot;: &quot;&lt;${{github.server_url}}/${{github.repository}}/commit/${{github.sha}}|Commit Link&gt;&quot;,
                            &quot;short&quot;: true
                          },
                          {
                            &quot;title&quot;: &quot;Message&quot;,
                            &quot;value&quot;: &quot;${{github.event.head_commit.message}}&quot;,
                            &quot;short&quot;: false
                          }
                        ]
                      }
                    ]
                }
            env:
              SLACK_WEBHOOK_URL: ${{ secrets.SLACK_WEBHOOK_URL }}
              SLACK_WEBHOOK_TYPE: INCOMING_WEBHOOK

          - name: Notify Deployment Success
            uses: slackapi/slack-github-action@v1.23.0
            with:
              payload: |
                {
                  &quot;attachments&quot;: [
                      {
                        &quot;title&quot;: &quot;✅ 배포 완료되었습니다! 👍&quot;,
                        &quot;author_name&quot;: &quot;${{github.actor}} 님! 성공적으로 배포되었습니다.🎉&quot;,
                        &quot;color&quot;: &quot;adff2f&quot;,
                        &quot;fields&quot;: [
                          {
                            &quot;title&quot;: &quot;Branch&quot;,
                            &quot;value&quot;: &quot;${{github.ref}}&quot;,
                            &quot;short&quot;: true
                          },
                          {
                            &quot;title&quot;: &quot;Event&quot;,
                            &quot;value&quot;: &quot;${{github.event_name}}&quot;,
                            &quot;short&quot;: true
                          },
                          {
                            &quot;title&quot;: &quot;Actions URL&quot;,
                            &quot;value&quot;: &quot;&lt;${{github.server_url}}/${{github.repository}}/commit/${{github.sha}}/checks|${{github.workflow}}&gt;&quot;,
                            &quot;short&quot;: true
                          },
                          {
                            &quot;title&quot;: &quot;Commit&quot;,
                            &quot;value&quot;: &quot;&lt;${{github.server_url}}/${{github.repository}}/commit/${{github.sha}}|Commit Link&gt;&quot;,
                            &quot;short&quot;: true
                          },
                          {
                            &quot;title&quot;: &quot;Message&quot;,
                            &quot;value&quot;: &quot;${{github.event.head_commit.message}}&quot;,
                            &quot;short&quot;: false
                          }
                        ]
                      }
                    ]
                }
            env:
              SLACK_WEBHOOK_URL: ${{ secrets.SLACK_WEBHOOK_URL }}
              SLACK_WEBHOOK_TYPE: INCOMING_WEBHOOK

      # 배포 후 릴리즈 태그 생성
      release:
        needs: deploy # deploy 성공하면 실행해라
        runs-on: ubuntu-latest

        steps:
          - uses: actions/checkout@v2

          # package.json에서 버전 정보 추출
          - name: Get Package.json Info
            id: info
            uses: jaywcjlove/github-action-package@main
          - run: echo &quot;version - ${{ steps.info.outputs.version }}&quot;

          - name: Get Latest Release Tag
            uses: actions-ecosystem/action-get-latest-tag@v1
            id: get-latest-tag
            with:
              semver_only: true

          - name: Create Release
            uses: actions/create-release@v1
            env:
              GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
            with:
              tag_name: ${{  steps.info.outputs.version }}
              release_name: ${{  steps.info.outputs.version }}
              body: |
                ${{ github.event.head_commit.message }}

                **Full Changelog**: https://github.com/${{ github.repository }}/compare/${{ steps.get-latest-tag.outputs.tag }}...${{ steps.info.outputs.version }}

      # release, hotfix 배포 후 개발 배포도 동시에 되게끔 develop 브랜치에 머지
      back-merge-to-develop:
        needs: deploy # deploy 성공하면 실행해라
        runs-on: ubuntu-latest
        steps:
          - uses: actions/checkout@v2

          - name: Merge main → develop
            uses: devmasx/merge-branch@master
            with:
              type: now
              from_branch: main
              target_branch: develop # 개발 배포 브랜치
              github_token: ${{ secrets.GITHUB_TOKEN }}
</code></pre>
<br />


<h3 id="▪︎-release--push---개발-테스트-운영-테스트-배포">▪︎ release : Push - 개발 테스트, 운영 테스트 배포</h3>
<pre><code class="language-yaml"># Github Flow 전략에선 release 브랜치가 불필요한 경우가 많다.
# 따라서 아예 분기하지 않는 경우가 일반적이다.

# ...

# develop 브랜치에 머지
 back-merge-to-develop:
    needs: deploy # deploy 성공하면 실행해라
     runs-on: ubuntu-latest
     steps:
        - uses: actions/checkout@v2

        - name: Merge main → develop
           uses: devmasx/merge-branch@master
          with:
            type: now
            from_branch: main # merge 시작할 브랜치
            target_branch: develop # 개발 배포 브랜치
            github_token: ${{ secrets.GITHUB_TOKEN }}
</code></pre>
<br />

<h3 id="▪︎-hotfix-push---운영-테스트-배포">▪︎ hotfix: Push - 운영 테스트 배포</h3>
<pre><code class="language-yaml"># hotfix PR merge 후 배포 브랜치 merge 후 배포 
# Github Flow 전략에선 hotfix를 사소한 기능 변화로 바라본다.
# 마찬가지로 아예 분기하지 않는 경우가 많다.

# ...

# develop 브랜치에 머지
back-merge-to-develop:
  needs: deploy # deploy 성공하면 실행해라
  runs-on: ubuntu-latest
  steps:
    - uses: actions/checkout@v2

    - name: Merge main → develop
      uses: devmasx/merge-branch@master
      with:
        type: now
        from_branch: main # merge 시작할 브랜치
        target_branch: develop # 개발 배포 브랜치
        github_token: ${{ secrets.GITHUB_TOKEN }}
</code></pre>
<br />

<h3 id="▪︎-pr-시-공통-style-unittest-각-단계마다-범위가-강화되는">▪︎ PR 시 공통: Style, UnitTest (각 단계마다 범위가 강화되는)</h3>
<pre><code class="language-yaml"># 모든 PR 체크
name: Check PR
on: [pull_request]

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout
        uses: actions/checkout@v2

      - name: Setup Environment
        uses: pnpm/action-setup@v3
        with:
          version: 8
      - name: Install Dependencies
        uses: actions/setup-node@v3
        with:
          node-version: '18.x' # node-version-file: '.nvmrc'

      - name: Check Lint
        run: |
          pnpm i
          pnpm lint
        env:
          CI: true

      # 테스트 코드 실행 결과 보여주기       
      - name: Run Tests and Create Comment         
        uses: actions/github-script@v6
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
        with:
          github-token: ${{ secrets.GITHUB_TOKEN }}
          script: |
            const script = require('./test-result-commit.js')
            console.log(script({github, context}))

      # 결과 슬랙 알림
      - name: Notify PR🎁 Check Failure
        if: ${{ failure() }}
        uses: slackapi/slack-github-action@v1.23.0
        with:
          payload: |
                 {
                  &quot;attachments&quot;: [
                      {
                        &quot;title&quot;: &quot;❌ ${{ github.event.pull_request.title }} &lt;&lt; 다시 확인해주세요! 🥲&quot;,
                        &quot;author_name&quot;: &quot;${{github.actor}} 님의 PR 에러 발생 😱&quot;,
                        &quot;color&quot;: &quot;danger&quot;,
                        &quot;fields&quot;: [
                          {
                            &quot;title&quot;: &quot;Title&quot;,
                            &quot;value&quot;: &quot;${{ github.event.pull_request.title }}&quot;,
                            &quot;short&quot;: true
                          },
                          {
                            &quot;title&quot;: &quot;Actions URL&quot;,
                            &quot;value&quot;: &quot;&lt;${{github.server_url}}/${{github.repository}}/commit/${{github.sha}}/checks|${{github.workflow}}&gt;&quot;,
                            &quot;short&quot;: true
                          },
                          {
                            &quot;title&quot;: &quot;Commit&quot;,
                            &quot;value&quot;: &quot;&lt;${{github.server_url}}/${{github.repository}}/commit/${{github.sha}}|Commit Link&gt;&quot;,
                            &quot;short&quot;: true
                          },
                          {
                            &quot;title&quot;: &quot;Message&quot;,
                            &quot;value&quot;: &quot;${{github.event.pull_request.html_url}}&quot;,
                            &quot;short&quot;: false
                          }
                        ]
                      }
                    ]
                }
        env:
          SLACK_WEBHOOK_URL: ${{ secrets.SLACK_WEBHOOK_URL }}
          SLACK_WEBHOOK_TYPE: INCOMING_WEBHOOK

      - name: Notify PR🎁 Check Success
        uses: slackapi/slack-github-action@v1.23.0
          with:
              payload: |
                {
                  &quot;attachments&quot;: [
                      {
                        &quot;title&quot;:  &quot;PR🎁: ${{ github.event.pull_request.title }}&quot;,
                        &quot;author_name&quot;: &quot;${{github.actor}} 님의 PR🎉&quot;,
                        &quot;color&quot;: &quot;adff2f&quot;,
                        &quot;fields&quot;: [
                          {
                            &quot;title&quot;: &quot;Commit&quot;,
                            &quot;value&quot;: &quot;&lt;${{github.server_url}}/${{github.repository}}/commit/${{github.sha}}|Commit Link&gt;&quot;,
                            &quot;short&quot;: true
                          },
                          {
                            &quot;title&quot;: &quot;Message&quot;,
                            &quot;value&quot;: &quot;${{github.event.pull_request.html_url }}&quot;,
                            &quot;short&quot;: false
                          }
                        ]
                      }
                    ]
                }
        env:
          SLACK_WEBHOOK_URL: ${{ secrets.SLACK_WEBHOOK_URL }}
          SLACK_WEBHOOK_TYPE: INCOMING_WEBHOOK
</code></pre>
<p><img alt="" src="https://velog.velcdn.com/images/april_5/post/2eac17de-13f8-42bc-a606-35fe05489461/image.png" /></p>
<br />

<hr />
<h2 id="추가">:추가</h2>
<blockquote>
<p>💡 테스트 코드가 통과해야 merge 가능하도록 Github repository 설정하기</p>
</blockquote>
<br />

<h3 id="▪︎-설정하기">▪︎ 설정하기</h3>
<ol>
<li><p>Repository <code>Settings</code> → <code>Branches</code> → <code>Add rule</code> 을 선택 </p>
<p><img alt="" src="https://velog.velcdn.com/images/april_5/post/d43c8ae5-7a2a-4351-b9cd-cb1cb9855a4e/image.png" /></p>
</li>
</ol>
<ol start="2">
<li><p><code>Branch name pattern</code> 을 설정하고 <code>Require status checks to pass before merging</code> 설정을 통해 merge를 위해 통과해야할 Action들을 선택할 수 있다</p>
<p><img alt="" src="https://velog.velcdn.com/images/april_5/post/e9b6613f-65bd-465d-8c4d-0de085d7a95c/image.png" /></p>
</li>
</ol>
<p>  <img alt="" src="https://velog.velcdn.com/images/april_5/post/a05e19dc-aeb3-4ac2-9995-4c1b5003d5c9/image.png" /></p>
<hr />
<h2 id="☐-적용-과정">☐ 적용 과정</h2>
<h3 id="▪︎-자동화하고-싶은-것">▪︎ 자동화하고 싶은 것</h3>
<ul>
<li>일단 tag</li>
<li>pr 요청 시 lint 체크</li>
<li>테스트 결과도 확인해야 하고</li>
<li>배포 완료/실패시 슬랙 알림<ul>
<li>내용도 있음 좋겠지?</li>
</ul>
</li>
<li>release, hotfix 후 개발 배포도 자동화</li>
</ul>
<h3 id="▪︎-자동화하고-싶은-시점">▪︎ 자동화하고 싶은 시점?</h3>
<ul>
<li><p>각 브랜치에 푸시 시 동작</p>
<ul>
<li><p>배포시 Git Tag, Release Note 생성</p>
<ol>
<li><p>Git Tag 생성하기</p>
<ul>
<li><p>방법1: Release branch가 master/main으로 merge 될때</p>
<ul>
<li><p><code>release/1.0.0</code>생성</p>
</li>
<li><p>merge commit 으로부터 버전 정보 추출</p>
<pre><code class="language-yaml">  # 브랜치로 태그 생성
  name: Release Tag
  on:
    push:
      branches:
        - main
  jobs:
    build:
      runs-on: ubuntu-latest
      steps:
        - uses: actions/checkout@v2
        - name: 버전 정보 추출
          run: echo &quot;##[set-output name=version;]$(echo '${{ github.event.head_commit.message }}' | egrep -o '[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}')&quot;
          id: extract_version_name

        - name: Release 생성
          uses: actions/create-release@v1
          env:
            GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
          with:
            tag_name: ${{ steps.extract_version_name.outputs.version }}
            release_name: ${{ steps.extract_version_name.outputs.version }}</code></pre>
</li>
</ul>
</li>
<li><p>방법2: master/main 으로 pr 및 merge 될 때 생성 ✅</p>
<pre><code class="language-yaml">  # package.json의 version 정보로 태그 생성
  name: Create Release Tag
  on:
    push:
      branches:
        - main

  jobs:
    release:
      runs-on: ubuntu-latest
      steps:
        - uses: actions/checkout@v2

        # package.json에서 버전 정보 추출
        - name: package.json info
          id: info
          uses: jaywcjlove/github-action-package@main
        - run: echo &quot;version - ${{ steps.info.outputs.version }}&quot;

        - name: Get latest release tag
          uses: actions-ecosystem/action-get-latest-tag@v1
          id: get-latest-tag
          with:
            semver_only: true

        - name: Create Release 
          uses: actions/create-release@v1
          env:
            GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
          with:
            tag_name: ${{  steps.info.outputs.version }}
            release_name: ${{  steps.info.outputs.version }}
            body: |
              ${{ github.event.head_commit.message }} 

              **Full Changelog**: https://github.com/${{ github.repository }}/compare/${{ steps.get-latest-tag.outputs.tag }}...${{ steps.info.outputs.version }}
</code></pre>
</li>
<li><p>추출된 버전 정보를 통해 tag 생성</p>
<p>  <img alt="" src="https://velog.velcdn.com/images/april_5/post/27aec7e4-33dc-4843-aea1-257bd59bea9a/image.png" /></p>
</li>
</ul>
</li>
</ol>
</li>
</ul>
</li>
</ul>
<br />


<ul>
<li><p>PR 시 공통: Style, UnitTest (각 단계마다 범위가 강화되는)</p>
<ul>
<li>일단 lint 체크함. 통과안되면 머지 안됨
<img alt="" src="https://velog.velcdn.com/images/april_5/post/0b07f7ff-954c-4572-af8f-4a0fd440c96d/image.png" /></li>
</ul>
<pre><code class="language-yaml">              # 모든 PR lint 체크
              name: Check PR
              on: [pull_request]

              jobs:
                build:
                  runs-on: ubuntu-latest

                  steps:
                    - name: Checkout
                      uses: actions/checkout@v2

                    # pnpm 아닌 경우 제거
                    - name: Setup pnpm
                      uses: pnpm/action-setup@v3
                      with:
                        version: 8

                    - name: Node.js Setting
                      uses: actions/setup-node@v3
                      with:
                        node-version: '18.x'

                    - name: Check
                      run: |
                        pnpm i
                        pnpm lint
                      env:
                        CI: true
</code></pre>
</li>
</ul>
<br />

<ul>
<li><p>테스트 결과도 확인해야 하고</p>
<p><img alt="" src="https://velog.velcdn.com/images/april_5/post/2937d2b7-25c0-45ca-b13e-82788ec7345b/image.png" /></p>
<p><img alt="" src="https://velog.velcdn.com/images/april_5/post/58fb6c82-0025-4b39-a08a-4c92163ce24d/image.png" /></p>
<pre><code class="language-yaml">      # 테스트 코드 실행
      - name: Run tests
        run: |
          pnpm test

      - name: Publish Unit Test Results
        uses: EnricoMi/publish-unit-test-result-action@v1
        if: ${{ always() }} # 테스트가 실패하여도 Report를 보기 위해 `always`로 설정
        with:
          files: build/test-results/**/*.xml

      - name: 테스트 커버리지 결과 PR에 코멘트 추가하기
        if: always()
        uses: ArtiomTr/jest-coverage-report-action@v2
        with:
          github-token: ${{ secrets.GITHUB_TOKEN }}</code></pre>
</li>
</ul>
<br />

<ul>
<li><p>배포 완료/실패시 슬랙 알림</p>
<p><img alt="" src="https://velog.velcdn.com/images/april_5/post/cda75a51-1f4d-484d-b090-7813d6477de9/image.png" /></p>
<p><img alt="" src="https://velog.velcdn.com/images/april_5/post/b07860c0-799a-4d1f-a77f-6844cd7ffce5/image.png" /></p>
</li>
</ul>
<br />


<ul>
<li>release, hotfix 후 개발 배포도 자동화</li>
</ul>
<pre><code class="language-bash">          back-merge-to-develop:
            needs: build # deploy 성공하면 실행해라
            runs-on: ubuntu-latest
            steps:
              - uses: actions/checkout@v2

              - name: Merge main → develop
                uses: devmasx/merge-branch@master
                with:
                  type: now
                  from_branch: main
                  target_branch: develop # TODO: 브랜치 전략 확인 후 수정
                  github_token: ${{ secrets.GITHUB_TOKEN }}</code></pre>
<br />

<hr />
<h2 id="☐-논의하고-싶은-것">☐ 논의하고 싶은 것</h2>
<h3 id="1-파일-분리할-것인가-한번에-작성할-것인가">1) 파일 분리할 것인가? 한번에 작성할 것인가?</h3>
<ul>
<li><p>각 역할을 하는 파일을 분리하여 관리할 것인가? 아님 한 번에 적용할 것인가?</p>
<ul>
<li><p>슬랙 알림 등</p>
</li>
<li><p>파일 분리를 했을 때 작성</p>
<pre><code class="language-yaml">  #  package.json의 version 정보로 태그 생성
  name: Create Release Tag

  on:
    workflow_run:
      workflows: ['deploy'] # deploy workflow 후 실행
      branches: [main]
      types: [completed]

  jobs:
    release:
      if: ${{ github.event.workflow_run.conclusion == 'success' }} # 성공했을 때
      runs-on: ubuntu-latest</code></pre>
</li>
<li><p>한 파일에 작성</p>
<pre><code class="language-yaml">  #...

  back-merge-to-develop:
      needs: build # deploy 성공하면 실행해라
      runs-on: ubuntu-latest</code></pre>
</li>
</ul>
</li>
</ul>
<h3 id="2-깃-전략">2) 깃 전략</h3>
<ul>
<li>배포 브랜치 등<ul>
<li>master/develop에 머지되면 배포 PR(운영, 개발 배포) 자동 생성되게끔~~</li>
<li>깃허브 전략 따르기로~~</li>
<li>운영 배포는 master에 머지되면, 개발 배포는 deploy-dev 유지<ul>
<li>단, 이름 변경하기<ul>
<li>master &gt; main 변경하기</li>
<li>deploy-dev는 고민해보기</li>
</ul>
</li>
</ul>
</li>
</ul>
</li>
</ul>
<h3 id="3-통과해야-merge-가능하도록-github-repository-설정하기를-할-것인가">3) 통과해야 merge 가능하도록 Github repository 설정하기를 할 것인가?</h3>
<ul>
<li>테스트 코드 등</li>
<li>lint 체크 등 오류 발생 했을 경우 어떻게 해결할 것인가?<ul>
<li>스프린트 중에 조치할 것인가</li>
<li>이 룰을 일시적으로 제거해서 merge 할 것인가</li>
</ul>
</li>
<li>담에 논의하기로</li>
</ul>
<p><br /><br /><br /></p>