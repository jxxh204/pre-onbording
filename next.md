# next.ts

### getServer

```tsx
private async getServer() {
	...

        this.server = await this.createServer({ //서버 만듬.
          ...this.options,
          conf,
        })
        if (this.preparedAssetPrefix) {
          this.server.setAssetPrefix(this.preparedAssetPrefix)
        }
        return this.server
      })
    }
    return this.serverPromise
  }
```

### createServer

```tsx
if (
    options &&
    'typescript' in options &&
    'version' in (options as any).typescript
  ) {
    return require('./next-typescript').createTSPlugin(options)
  }
```

- 타입스크립트 플러그인 확인

```tsx
if (
    !('isNextDevCommand' in options) &&
    process.env.NODE_ENV &&
    !['production', 'development', 'test'].includes(process.env.NODE_ENV)
  ) {
    log.warn(NON_STANDARD_NODE_ENV)
  }
```

- 개발, 배포, 테스트 모드 확인

```tsx

return new Proxy 
switch (propKey) {
	case prepare // start-server에서 사용.
	case getRequestHandler
	case render
```

- createServer의 return 값.

**prepare**

1. dev인지 prod인지 확인하고 app 디렉토리가 제대로된 위치에 존재하는지 확인
2. app 디렉토리가 없다면 서버를 다시 준비시킨다.
3. app이 있다면 서버 핸들러 생성
