# 프리온보딩 사전 과제

날짜: June 29, 2023

### 원티드 프리온보딩 챌린지 - CSR / SSR with Next.js

### Assignment) 개인 블로그에 아래 질문에 대한 포스팅을 하고 링크를 제출해주세요.

- CSR(Client-side Rendering)이란 무엇이며, 그것의 장단점에 대하여 설명해주세요.
- SPA(Single Page Application)로 구성된 웹 앱에서 SSR(Server-side Rendering)이 필요한 이유에 대하여 설명해주세요.
- Next.js 프로젝트에서 `yarn start(or npm run start)` 스크립트를 실행했을 때 실행되는 코드를 Next.js Github 레포지토리에서 찾은 뒤, 해당 파일에 대한 간단한 설명을 첨부해주세요.
    - [https://nextjs.org/docs/getting-started](https://nextjs.org/docs/getting-started) (Next.js 세팅 가이드)
    - [https://github.com/vercel/next.js/](https://github.com/vercel/next.js/) (Next.js Github 레포지토리)
    - `_document.js`, `_app.js`, `getServerSideProps` 같은 요소들에 대해 설명을 요구하는 과제가 아닙니다. 오히려 Next.js 코드 베이스 내부를 살펴보라는 의미입니다.
    - 사전과제 여부나 제출된 과제 퀄리티가 수강 가능 여부 및 이후의 과정에 영향을 미치지는 않을 것이나, 3번 과제를 해보는 것이 큰 학습이 될 것이라고 확신합니다. 반드시 한번 살펴보시길 권장드립니다.
    - 제출 전 과제가 유효한 Public 한 링크인지 다시 한번 확인 부탁드립니다.

### 과제 제출

- 수행하신 과제는 챌린지 시작 후 전달될 안내에 따라 링크로 제출해주시고, 학습 수준 파악 및 강의 진행을 위해 사용될 예정입니다.

1. CSR
    1. 이란? 
        1. WebSite자체를 요청하여 유저는 HTML,JS 같은 파일을 전부 받고 유저 즉 클라이언트에서 렌더링 하는 것을 말한다.
    2. 장단점
        1. 장점
            1. 한번 다운로드 받으면 더이상 페이지를 로드할 필요가 없기 때문에 렌더링이 빠르다.
            2. 서버 요청이 적어 사용되는 자원이 적다.
        2. 단점
            1. 처음 홈페이지를 로드하는 시간이 길다.
                1. 프로젝트가 커질 수 록 더 길어진다.
            2. SEO 최적화에서 불리하다.
2. SPA로 구성된 웹 앱에서 SSR이 필요한 이유는?
    1. CSR의 단점인 처음 홈페이지의 로드시간을 단축과 SEO 최적화 문제를 해결 할 수 있다.
    2. SPA라서 배포가 간단하다.
    3. SPA는 페이지간의 이동속도가 빨라 사용자 경험에 유리하다.
3. Next.js 프로젝트에서 `yarn start(or npm run start)` 스크립트를 실행했을 때 실행되는 코드를 Next.js Github 레포지토리에서 찾은 뒤, 해당 파일에 대한 간단한 설명을 첨부해주세요.

next.js/packages/next/src/cli

[next-start.ts](https://www.notion.so/next-start-ts-62bd7ef23205411cbafdd7e1d50af388?pvs=21)

- 스크립트 명령어 폴더.

packages/next/src/server/lib/start-server.ts

[start-server.ts](https://www.notion.so/start-server-ts-92d1cbe2439c4a878d60523d71cae6fd?pvs=21)

- 서버 시작 파일

packages/next/src/server/next.ts

[next.ts](https://www.notion.so/next-ts-b939e7d731f344b1905f3927719c11c5?pvs=21)

next.js/packages/next/src/pages/_app.tsx

- 페이지 파일들 초기화

순서

1. next-start.ts
    
    ```tsx
      '--help': Boolean,
        '--port': Number,
        '--hostname': String,
        '--keepAliveTimeout': Number,
    ```
    
    - 해당 명령어들을 확인하고 처리한다.
    - 중간에 문제가 생기면 에러메세지와 함께 프로세스를 종료한다.
    - 문제가 없다면 startServer를 실행한다.
        
        ```tsx
        await startServer({
            dir,
            isDev: false,
            hostname: host,
            port,
            keepAliveTimeout,
            useWorkers: !!config.experimental.appDir,
          })
        ```
        
2. start-server.ts

1. start-server에서 next.ts 호출
- packages/next/src/server/next.ts