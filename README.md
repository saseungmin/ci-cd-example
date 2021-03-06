# ๐ CI/CD Example
- Github Action CI/CD example
- [CodeSoom CI/CD Repo](https://github.com/CodeSoom/cicd-example)

## ๐ Settings
- npm install dependencies

```bash
> npm i express
> npm i -D eslint eslint-config-airbnb-base eslint-plugin-import
```

- [Install Docker Desktop on Mac](https://docs.docker.com/docker-for-mac/install/)

### ๐ Setting for Docker
- ๊ฐ๋ฐํ๋ ํ๊ฒฝ๊ณผ ์คํํ๋ ํ๊ฒฝ์ ๋๊ฐ์ด ์ผ์น์ํค๊ฒ ํ๊ธฐ ์ํด์ ๋์ปค๋ฅผ ์ฌ์ฉ(๋ฌ๋ผ์ง๋๋ง๋ค ์ด๋ค ๋ฌธ์ ๊ฐ ์๊ธฐ๊ธฐ ๋๋ฌธ์)
- ๋ฐฐํฌ๋ฅผ ํ  ๋ ์ฐ๋ฆฌ๊ฐ ์คํํ๋ ์ ํ๋ฆฌ์ผ์ด์ ๋ฟ๋ง ์๋๋ผ ์ ํ๋ฆฌ์ผ์ด์์ ์คํํ๋ ํ๊ฒฝ๊น์ง๋ ๊ฐ์ด ๋ฐฐํฌํ๋ค.

```bash
> docker build -t cicd-example
> docker run --rm -p 3000:3000 cicd-example
```

- ์คํ

```bash
> curl localhost:3000/hello
Hello world!
```

- ์คํ์ค์ธ docker ๋ณด๊ธฐ

```bash
> docker ps
```

- ์คํ์ค์ธ docker ์ค์งํ๊ธฐ

```bash
> docker stop <CONTAINER ID>
```

## ๐ Github Action Docker build
- Github์ Actionํญ์์ Node.js ์ ํ
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

    runs-on: ubuntu-20.04 # latest๊ฐ ๋ฌธ์ ๊ฐ ์๊ธธ ์ ์๋ค.

    strategy:
      matrix:
        node-version: [14.x]

    steps:
    - uses: actions/checkout@v2
    - name: Use Node.js ${{ matrix.node-version }}
      uses: actions/setup-node@v2
      with:
        node-version: ${{ matrix.node-version }}
    - name: Docker build
      run: |
        docker build -t cicd-example .
```

- ๋์ปค ์ด๋ฏธ์ง๋ฅผ ๋น๋๊น์ง ํ ์ํ
- ์ด์  ์ค์ ๋ก ์๋ฒ์์ ์คํ์ํค๋ ค๋ฉด ์ด ์ฝ๋๋ฅผ ๋ฐ์์ฌ ์ ์์ด์ผ ํ๋ค. ๊ทธ๊ฒ์ [๋์ปค ํ๋ธ](https://hub.docker.com/)์ ์ฌ๋ฆฐ๋ค.
- [Docker hub](https://hub.docker.com/)์ ์๋ก์ด Repository๋ฅผ ๋ง๋ ๋ค.

```bash
> docker tag cicd-example seung02169/cicd-example
> docker push seung02169/cicd-example
```

- `.github/workflows/ci.yml`์ Docker build ๋ถ๋ถ์ ๋ค์๊ณผ ๊ฐ์ด ์ถ๊ฐ

```yml
# ์๋ต...
    - name: Docker build
      run: |
        docker login -u ${{ secrets.USERNAME }} -p ${{ secrets.PASSWORD }}
        docker build -t cicd-example .
        docker tag cicd-example seung02169/cicd-example:${GITHUB_SHA::7}
        docker push seung02169/cicd-example:${GITHUB_SHA::7}
```

- ํด๋น Github Repository์ Settings์ Secret์ Docker ์์ด๋ ๋น๋ฒ์ ๋ฃ์ด์ค๋ค.
- Docker์ TAG๊ฐ์ Git์ Commit id
- Docker์ TAG๊ฐ๋ง ์์ผ๋ฉด Docker๊ฐ ์ค์น๋์ด์๋ ์ด๋ ํ๊ฒฝ์์๋  ๋ฐฐํฌํ  ์ ์๋ค.

## ๐ AWS EC2 ๋ฐฐํฌํ๊ธฐ
- [AWS](https://aws.amazon.com/)์ ์ ์ํ ๋ค ๋ก๊ทธ์ธ ํ ์ฝ์๋ก ์ด๋ ํ EC2๋ก ์ด๋
1. Amazon Machine Image(AMI)๋ฅผ Amazon Linux 2 AMI (HVM), SSD Volume Type๋ก ์ ํ

![1](images/1.png)

2. ์ธ์คํด์ค ์ ํ ์ ํ: ๊ธฐ๋ณธ ์ ํ
3. ์ธ์คํด์ค ์ธ๋ถ ์ ๋ณด ๊ตฌ์ฑ: ๊ธฐ๋ณธ ์ ํ
4. ์คํ ๋ฆฌ์ง ์ถ๊ฐ: ๊ธฐ๋ณธ ์ ํ
5. ํ๊ทธ ์ถ๊ฐ

![2](images/2.png)

6. ๋ณด์ ๊ทธ๋ฃน ๊ตฌ์ฑ

![3](images/3.png)

7. ์ธ์คํด์ค ์์ ๊ฒํ  
  - ์์ํ๊ธฐ ๋ฒํผ ํด๋ฆญ ํ ํค ์ ํ

![4](images/4.png)

8. ํด๋น ์ธ์คํด์ค ์ ํ ํ ์ฐ๊ฒฐ
  - ์ธ์คํด์ค ์ฐ๊ฒฐ์์ SSH ํด๋ผ์ด์ธํธ ํญ์์ ์ค๋ช์ ๋ฐ๋ผ SSH์ฐ๊ฒฐ

9. EC2๋ก ์ ์ ์๋ฃ

![5](images/5.png)

10. EC2 ์ปค๋งจ๋ ์ฐฝ์ ๋ค์ ๋ช๋ น์ด ์๋ ฅํ์ฌ Docker ์ค์น

```bash
sudo yum update -y
sudo yum install -y docker
sudo service docker start
sudo systemctl enable docker
sudo usermod -a -G docker ec2-user
```

11. docker hub์ ์ฌ๋ ค๋์๋ docker ์ด๋ฏธ์ง ๋ด๋ ค๋ฐ๊ธฐ

```bash
sudo docker pull seung02169/cicd-example:<ํ๊ทธ>
```

12. EC2์์ docker run

```bash
sudo docker run -d -p 80:3000 seung02169/cicd-example:<ํ๊ทธ>
```
- ์คํ์ค์ธ์ง ํ์ธ

```bash
sudo docker ps
```

13. ํด๋น ํผ๋ธ๋ฆญ ์ฃผ์๋ก ์ ์ํด ํ์ธ
- EC2 ์ฝ์์ ํด๋น ์ธ์คํด์ค๋ฅผ ํด๋ฆญํ๋ฉด ์์ฝ์ ํผ๋ธ๋ฆญ IPv4 ์ฃผ์ ํ์ธ

![6](images/6.png)

- ๋กค๋ฐฑ์ ํ๊ณ ์ถ์ ๋ ํด๋น ์ปค๋ฐ์ ID๋ก ์ด๋ํ๋ฉด ๋๋ค.

## ๐ Github Actions Deploy
- ์ด์  ์์ ๊ฐ์ด ์๋ฒ์ ์ต์  ์์์ ๋ฐ์ํด์ผ ํ๊ธฐ ์ํด์ ์คํํ๋ ์์์ ์๋ํํด์ผ ํ๋ค.

```bash
docker pull seung02169/cicd-example:${hash}
docker stop server
docker tag seung02169/cicd-example:${hash} cicd-example
docker run --rm -d -p 80:3000 --name server cicd-example
```

- Github Actions์์๋ ์ ์ํ ๋ค์ ์ ๋ช๋ น์ด๋ฅผ ์คํ์์ผ์ฃผ๋ฉด ์๋ํํ  ์ ์๋ค.
- [ssh-remote-commands](https://github.com/marketplace/actions/ssh-remote-commands)

```yml
# ์๋ต..
    - name: Docker build
      run: |
        docker login -u ${{ secrets.USERNAME }} -p ${{ secrets.PASSWORD }}
        docker build -t cicd-example .
        docker tag cicd-example seung02169/cicd-example:${GITHUB_SHA::7}
        docker push seung02169/cicd-example:${GITHUB_SHA::7}
    - name: Deploy
      uses: appleboy/ssh-action@master
      with:
        host: ec2-54-180-201-173.ap-northeast-2.compute.amazonaws.com
        username: ec2-user
        key: ${{ secrets.PRIVATE_KEY }} # ์๋ก ๋ง๋ค์ด์ค์ผํจ
        envs: GITHUB_SHA
        script: |
          sudo docker pull seung02169/cicd-example:${GITHUB_SHA::7}
          sudo docker stop server
          sudo docker tag seung02169/cicd-example:${GITHUB_SHA::7} cicd-example
          sudo docker run --rm -d -p 80:3000 --name server cicd-example
```

- `secrets.PRIVATE_KEY`๋ฅผ ์์ฑํ๊ธฐ ์ํด์ [๋งํฌ](https://github.com/appleboy/ssh-action#setting-up-a-ssh-key)์ ์ค๋ช์ ๋ฐ๋ผ ์์ฑํด์ค๋ค.

```bash
ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
```

- ์์ฑํ๋ฉด private key์ public key๊ฐ ์์ฑ๋๋๋ฐ ์ด์ค public key๋ฅผ EC2์์ ๋ฑ๋กํด์ค๋ค.
- EC2์์ ํด๋น ์์น์ ํ์ผ์ ์ฐ๋ค.

```bash
vim ~/.ssh/authorized_keys
```

- ์์ฑํ public key๋ฅผ ํด๋น ์์น์ ๋ถ์ฌ๋ฃ์ด์ค๋ค ์ ์ฅํด์ค๋ค.
- ์ด๋ ๊ฒ ํ ๋ค Github Secrets์ `PRIVATE_KEY`๋ก private key๋ฅผ ๋ฑ๋กํด์ฃผ๋ฉด ์ ์์ด ๊ฐ๋ฅํด์ง๋ค.
- ์๋์ผ๋ก ๋ณ๊ฒฝ๋ ์ฝ๋๊ฐ ๋ฐฐํฌ๋์ด ์๋ ๊ฒ์ ํ์ธํ  ์ ์๋ค.

![7](images/7.png)

- ์ฌ๊ธฐ๊น์ง๊ฐ **๋์ํ๋ ๊ณจ๊ฒฉ**์ ํฌํจ๋๋ ๊ฒ์ด๋ค.
