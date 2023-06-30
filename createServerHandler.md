# createServerHandler

packages/next/src/server/lib/render-server-standalone.ts

1. render-server.ts를 워커로 생성
    - render-server.ts
        
        ```tsx
        const app = next({
            ...opts,
            _routerWorker: opts.workerType === 'router',
            _renderWorker: opts.workerType === 'render',
            hostname,
            customServer: false,
            httpServer: server,
            port: opts.port,
            isNodeDebugging: opts.isNodeDebugging,
          })
        
          requestHandler = app.getRequestHandler()
          upgradeHandler = app.getUpgradeHandler()
          await app.prepare()
          resolve(result)
        ```
        
    - http 서버
    - setup 서버와 거의 동일
2. proxy서버
    1. const targetUrl = `http://${hostname}:${routerPort}${pathname}`
    2. 프록시 서버 만듬.