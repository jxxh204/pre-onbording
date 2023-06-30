# next-start.ts

## import

```jsx
import arg from 'next/dist/compiled/arg/index.js'
```

### import { CliCommand } from '../lib/commands'

```jsx
export type CliCommand = (argv?: string[]) => void
```

- nextStart 함수의 타입이다.

### import { getPort, printAndExit } from '../server/lib/utils'

- **printAndExit**
    
    ```jsx
    export function printAndExit(message: string, code = 1) {
      if (code === 0) { // code가 0이 아니면 에러 처리.
        console.log(message)
      } else {
        console.error(message)
      }
    
      process.exit(code) // node.js 프로세스 종료
    }
    ```
    
- **getPort**
    
    ```jsx
    export function getPort(args: arg.Result<arg.Spec>): number {
      if (typeof args['--port'] === 'number') {
        return args['--port']
      }
    
      const parsed = process.env.PORT && parseInt(process.env.PORT, 10)
      if (typeof parsed === 'number' && !Number.isNaN(parsed)) {
        return parsed
      }
    
      return 3000
    }
    ```
    
    - if 1 : —port 명령어가 있을 경우 해당 포트를 적용한다.
    - if 2: —port가 없다면 process.env.PORT가 있을경우 10진수로 파싱하고 사용한다.
    - if 다 실패하면 3000리턴

### import { getProjectDir } from '../lib/get-project-dir'

- packages/next/src/lib/get-project-dir.ts
- 디렉토리 파싱해서 보내줌.

## script 명령

**validArgs :** arg.Spec

```jsx
    '--help': Boolean, // -h
    '--port': Number, // -p
    '--hostname': String, // -h
    '--keepAliveTimeout': Number,
```

**args :** arg.Result<arg.Spec>

```jsx
try {
args = arg(validArgs, { argv })
  } catch (error) { // 없는 argument 옵션일 경우.
    if (isError(error) && error.code === 'ARG_UNKNOWN_OPTION') {
      return printAndExit(error.message, 1) // 메세지 보여주고 프로세스 종료.
    }
    throw error
  }
```

- arg : argument 생성기 인가?
- [printAndExit](https://www.notion.so/next-start-ts-62bd7ef23205411cbafdd7e1d50af388?pvs=21)

### —help

```jsx
if (args['--help']) {
    console.log(`
      Description
        Starts the application in production mode.
        The application should be compiled with \`next build\` first.

      Usage
        $ next start <dir> -p <port>

      <dir> represents the directory of the Next.js application.
      If no directory is provided, the current directory will be used.

      Options
        --port, -p          A port number on which to start the application
        --hostname, -H      Hostname on which to start the application (default: 0.0.0.0)
        --keepAliveTimeout  Max milliseconds to wait before closing inactive connections
        --help, -h          Displays this message
    `)
    process.exit(0)
  }
```

## valiable

### dir : getProjectDir(args._[0])

```jsx
export function getProjectDir(dir?: string) {
  try {
    const resolvedDir = path.resolve(dir || '.')
    const realDir = realpathSync(resolvedDir) // 존재하는 주소인지 확인.

    if (
      resolvedDir !== realDir &&
      resolvedDir.toLowerCase() === realDir.toLowerCase()
    ) {
      Log.warn(
        `Invalid casing detected for project dir, received ${resolvedDir} actual path ${realDir}, see more info here https://nextjs.org/docs/messages/invalid-project-dir-casing`
      )
    }

    return realDir
  } catch (err: any) {
    if (err.code === 'ENOENT') {
      if (typeof dir === 'string') {
        const detectedTypo = detectTypo(dir, Object.keys(commands))

        if (detectedTypo) {
          Log.error(
            `"next ${dir}" does not exist. Did you mean "next ${detectedTypo}"?`
          )
          process.exit(1)// dir이 없으니 종료
        }
      }

      Log.error(
        `Invalid project directory provided, no such directory: ${path.resolve(
          dir || '.'
        )}`
      )
      process.exit(1) //찾을 수 없으니 종료
    }
    throw err
  }
}
```

- 디렉토리가 존재하는 지 확인하고 존재한다면 리턴하고 존재하지 않는다면 프로세스를 종료한다.

### host : args['--hostname']

- 

### port : getPort(args)

- 입력한 포트가 있다면 그 포트로 리턴 없다면 3000리턴

### keepAliveTimeoutArg : args['--keepAliveTimeout']

```tsx
#!/usr/bin/env node

import { startServer } from '../server/lib/start-server'
import isError from '../lib/is-error'
import { getProjectDir } from '../lib/get-project-dir'

import { resolve } from 'path'
import { PHASE_PRODUCTION_SERVER } from '../shared/lib/constants'
import loadConfig from '../server/config'

// return 없고 argv의 타입은 string[]이다.
const nextStart: CliCommand = async (argv) => {
  const validArgs: arg.Spec = {
    // Types
    '--help': Boolean,
    '--port': Number,
    '--hostname': String,
    '--keepAliveTimeout': Number,

    // Aliases
    '-h': '--help',
    '-p': '--port',
    '-H': '--hostname',
  }
  let args: arg.Result<arg.Spec>
  try {
    args = arg(validArgs, { argv })
  } catch (error) { // 없는 argument 옵션일 경우.
    if (isError(error) && error.code === 'ARG_UNKNOWN_OPTION') {
      return printAndExit(error.message, 1)
    }
    throw error
  }
  if (args['--help']) {
    console.log(`
      Description
        Starts the application in production mode.
        The application should be compiled with \`next build\` first.

      Usage
        $ next start <dir> -p <port>

      <dir> represents the directory of the Next.js application.
      If no directory is provided, the current directory will be used.

      Options
        --port, -p          A port number on which to start the application
        --hostname, -H      Hostname on which to start the application (default: 0.0.0.0)
        --keepAliveTimeout  Max milliseconds to wait before closing inactive connections
        --help, -h          Displays this message
    `)
    process.exit(0)
  }

  const dir = getProjectDir(args._[0])
  const host = args['--hostname']
  const port = getPort(args)

  const keepAliveTimeoutArg: number | undefined = args['--keepAliveTimeout']
 // undefined 아니고, nan 아니고, 0아니고, 0보다 작을 경우. 프로세스 종료
  if (
    typeof keepAliveTimeoutArg !== 'undefined' &&
    (Number.isNaN(keepAliveTimeoutArg) ||
      !Number.isFinite(keepAliveTimeoutArg) ||
      keepAliveTimeoutArg < 0)
  ) {
    printAndExit(
      `Invalid --keepAliveTimeout, expected a non negative number but received "${keepAliveTimeoutArg}"`,
      1
    )
  }

  const keepAliveTimeout = keepAliveTimeoutArg
    ? Math.ceil(keepAliveTimeoutArg) // 올림
    : undefined

  const config = await loadConfig(
    PHASE_PRODUCTION_SERVER,
    resolve(dir || '.'),
    undefined,
    undefined,
    true
  )

  await startServer({
    dir,
    isDev: false,
    hostname: host,
    port,
    keepAliveTimeout,
    useWorkers: !!config.experimental.appDir,
  })
}

export { nextStart }
```