## Bomb frontend

Micro-Frontend-Architecture 적용기
(Bomb Platform by next.js 14 & turborepo)

### 참조

1. https://turbo.build/repo/docs
2. https://github.com/vercel/turbo/tree/main/examples
3. https://d2.naver.com/helloworld/7553804
4. https://www.kimcoder.io/blog/micro-frontend-module-federation
5. https://engineering.linecorp.com/ko/blog/monorepo-with-turborepo

### 프로젝트 생성하기

```bash
pnpm init
```

```json
{
  "name": "aion-portal-monorepo",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "keywords": [],
  "author": "",
  "license": "ISC"
}
```

pnpm-workspace.yaml 파일 생성하기

```yaml
packages:
  - "apps/*"
  - "packages/*"
```

```bash
corepack prepare pnpm@8.10.0 --activate
corepack enable
```

Shell은 application의 main 역할을 하며 Host의 역할을 갖는 Shell project를 next.js 14 기반으로 생성한다.

```bash
mkdir apps
cd apps
pnpm create next-app shell
```

모노레포(monorepo) 관리 전략을 위해 아래와 같이 Naming Convention을 적용한다.

frontend 폴더 root 경로에 있는 package.json 파일의 name 필드를 수정한다.

```json
{
  "name": "@frontend/monorepo",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "keywords": [],
  "author": "",
  "license": "ISC"
}
```

frontend/apps/shell 경로에 있는 package.json 파일의 name 필드도 아래와 같이 수정한다.

```json
{
  "name": "@frontend/shell"
}
```

변경된 사항을 확인한다.

```bash
pnpm i
```

다음으로 pnpm 패키지 매니저를 사용하여 turbo를 개발 의존성으로 모노레포 전체(workspace)에 추가한다.

```bash
pnpm -w add turbo -D
```

다음으로 aion-portal 폴더 root 경로에 turbo.json 파일을 생성 후, 파이프라인(pipeline)을 작성한다.
파이프라인의 각 명령어들은 하나의 태스크 단위이며 이 단위가 '병렬 처리 및 의존성의 범위'가 된다.

```json
{
  "$schema": "https://turbo.build/schema.json",
  "pipeline": {
    "build": {
      "dependsOn": ["^build"],
      "outputs": [".next/**", "!.next/cache/**", "dist/**"]
    },
    "lint": {
      "outputs": []
    },
    "prettier-fix": {
      "outputs": []
    },
    "dev": {
      "cache": false
    },
    "start": {
      "cache": false
    }
  }
}
```

pakages.json 파일을 위에서 작성한 파이프라인을 참조하여 작성한다.

```json
  "scripts": {
    "dev": "turbo dev",
    "build": "turbo build",
    "start": "turbo run start",
    "prettier-fix": "prettier --write ."
  },
```

frontend 상단에 packages 폴더를 생성 후, pnpm 패키지 매니저를 사용해 common-ui 이름으로 React, SWC, TypeScript를 포함하는 Vite 프로젝트를 생성한다.

```bash
cd..
mkdir packages
cd packages
pnpm create vite common-ui --template react-swc-ts
```

_to be continued..._
