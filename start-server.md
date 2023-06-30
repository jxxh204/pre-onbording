# start-server.ts

- **sockets** = new Set<ServerResponse | Duplex>()
    - Duplex : 쓰기 읽기 전부 가능한 스트림.
- **worker :** import('next/dist/compiled/jest-worker').Worker | undefine
- **handlersReady** = () => {}
- **handlersError** = () => {}
- **handlersPromise**
    - **handlersReady 와 handlersError 에 각각 resolve, reject를 입력한다.**
- requestHandler
- upgradeHandler
- **server = http.createServer**
    - 셋업 서버 생성
    - handlersPromise 초기화
    - sockets.add(response)
    - response 초기화.
- **server.on**
    - upgrade(req, socket, head)
        
        ```tsx
        sockets.add(socket)
        socket.on('close', () => sockets.delete(socket))
        await upgradeHandler(req, socket, head)
        ```
        
    - error(err: NodeJS.ErrnoException)
    - if문으로 명확하게 port문제 말고 없는 지 확인 후 port 문제라면. 새로운 포트로 서버 리턴
    
    ```tsx
    if ...
    Log.warn(`Port ${port} is in use, trying ${port + 1} instead.`)
    port += 1
    portRetryCount += 1
    server.listen(port, hostname)
    ```
    
    - listening
    - 비동기 실행
    - server.listen(port, hostname) 실행
    - listening 이벤트 실행
    - ipv6인지 확인 후 host+port로 url 생성
    - 디버깅 할지 확인 후 레디 로그와 함께 프로미스 끝냄.
    
    ```tsx
    let targetHost = hostname
    await new Promise<void>((resolve) => {
        server.on('listening', () => {
    		// 서버 실행을 위한 정보 초기화
    			const addr = server.address()
          port = typeof addr === 'object' ? addr?.port || port : port
    
          let host = !hostname || hostname === '0.0.0.0' ? 'localhost' : hostname
    
          let normalizedHostname = hostname || '0.0.0.0'
    
    			if (isIPv6(hostname)) { // ipv6라면 주소방식 변경
            host = host === '::' ? '[::1]' : `[${host}]`
            normalizedHostname = `[${hostname}]`
          }
    			targetHost = host
    
          const appUrl = `http://${host}:${port}` // ur
    
          if (isNodeDebugging) {
            const debugPort = getDebugPort()
            Log.info(
              `the --inspect${
                isNodeDebugging === 'brk' ? '-brk' : ''
              } option was detected, the Next.js proxy server should be inspected at port ${debugPort}.`
            )
          }
    			// 서버 시작할 때 터미널에 뜨는 문구.
          Log.ready(
            `started server on ${normalizedHostname}${
              (port + '').startsWith(':') ? '' : ':'
            }${port}, url: ${appUrl}`
          )
          resolve()
        }) // on
        server.listen(port, hostname)
    })
    
    ```
    
- **useWorkers 183번 줄부터 보기.**
    - jest를 사용한다?
    - 워커 스레드로 사용하는듯.
- **워커를 사용하지 않을 경우**

```tsx
// when not using a worker start next in main process
      const next = require('../next') as typeof import('../next').default
      const addr = server.address()
      const app = next({
        dir,
        hostname,
        dev: isDev,
        isNodeDebugging,
        httpServer: server,
        customServer: false,
        port: addr && typeof addr === 'object' ? addr.port : port,
      })
      // handle in process
      requestHandler = app.getRequestHandler()
      upgradeHandler = app.getUpgradeHandler()
      await app.prepare()
      handlersReady()
```