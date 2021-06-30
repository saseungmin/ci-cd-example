# 👉 CI/CD Example
- Github Action CI/CD example

## 📚 Settings
- npm install dependencies

```bash
> npm i express
> npm i -D eslint eslint-config-airbnb-base eslint-plugin-import
```

- [Install Docker Desktop on Mac](https://docs.docker.com/docker-for-mac/install/)

### 🎈 Setting for Docker
- 개발하는 환경과 실행하는 환경을 똑같이 일치시키게 하기 위해서 도커를 사용(달라질때마다 어떤 문제가 생기기 떄문에)
- 배포를 할 때 우리가 실행하는 애플리케이션 뿐만 아니라 애플리케이션을 실행하는 환경까지도 같이 배포한다.

```bash
> docker build -t cicd-example
> docker run --rm -p 3000:3000 cicd-example
```

- 실행

```bash
> curl localhost:3000/hello
Hello world!
```

## 📚 Github Action CI/CD
- Github의 Action탭에서 Node.js 선택
- `.github/workflows/ci.yml`

```yml
name: Node.js CI

on:
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]

jobs:
  build:

    runs-on: ubuntu-20.04 # latest가 문제가 생길 수 있다.

    strategy:
      matrix:
        node-version: [16.x]

    steps:
    - uses: actions/checkout@v2
    - name: Use Node.js ${{ matrix.node-version }}
      uses: actions/setup-node@v2
      with:
        node-version: ${{ matrix.node-version }}
    - run: npm ci
    - name: Docker build
      run: |
        docker build -t cicd-example .
```

- 도커 이미지를 빌드까지 한 상태
- 이제 실제로 서버에서 실행시키려면 이 코드를 받아올 수 있어야 한다. 그것을 [도커 허브](https://hub.docker.com/)에 올린다.
- [Docker hub](https://hub.docker.com/)에 새로운 Repository를 만든다.

```bash
> docker tag cicd-example seung02169/cicd-example
> docker push seung02169/cicd-example
```

- `.github/workflows/ci.yml`의 Docker build 부분을 다음과 같이 추가

```yml
# 생략...
    - name: Docker build
      run: |
        docker login -u ${{ secrets.USERNAME }} -p ${{ secrets.PASSWORD }}
        docker build -t cicd-example .
        docker tag cicd-example seung02169/cicd-example:${GITHUB_SHA::7}
        docker push seung02169/cicd-example:${GITHUB_SHA::7}
```

- 해당 Github Repository의 Settings의 Secret에 Docker 아이디 비번을 넣어준다.
