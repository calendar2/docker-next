# Next.js 프로젝트를 Docker로 실행시키기

## 지식 정리

### 원리

1. Next.js 프로젝트를 통째로 도커 이미지에 복사한다.
2. Next.js 버전에 따라 node 버전을 결정해주고 이미지 생성과정에서 RUN 명령어를 이용해 npm install과 build를 진행해준다.
3. ENTRYPOINT를 설정하여 빌드된 파일을 실행해준다.

### Dockerfile 명령어

```dockerfile
# 프로젝트를 동작시키는 node 버전
# Next.js 버전에 맞춰서 결정해준다.
FROM node:20-alpine

# Next.js 프로젝트가 위치할 디렉토리 위치
# WORKDIR 는 특정 폴더를 만들어서 관리하므로 컨테이너 내부의 가독성을 높여줄 수 있다
WORKDIR /app

# Next.js 프로젝트 통째로 복사
# 그리고 npm 설치와 빌드를 진행해준다.
COPY . .

RUN npm install

RUN npm run build

# 해당 프로젝트가 실행될 포트 번호
# 문서화 용도의 명령어이다. 실제 동작하지 않음
EXPOSE 3000

# 도커 컨테이너가 실행되면 Next 프로젝트 실행
ENTRYPOINT [ "npm", "run", "start" ]
```

### docker cli

```bash
# Spring Boot 프로젝트 도커 이미지로 구축하기
docker build -t "[이미지 이름]" .

# 현재 가지고 있는 도커 이미지 리스트 조회
docker image ls

# 내가 만든 Spring Boot 도커 이미지를 도커 컨테이너로 실행시키기
# -d: 컨테이너를 백그라운드에서 실행하기
# -p: 호스트와 컨테이너의 포트 설정(FE 프로젝트니까 호스트는 80으로 컨테이너는 3000으로)
docker run -d -p 80:3000 "[이미지 이름]"
```

### 강의 주소

[비전공자도 이해할 수 있는 Docker 입문/실전](https://inf.run/GvHgg)

### 어떤 방식이 더 나을까?

- 현재 강의에서 사용한 방식은 도커 이미지에 Next.js 프로젝트를 통째로 복사해서 이미지 내부에서 빌드하는 "내부 빌드 방식"

  - 내부 빌드 방식의 경우 이미지 용량도 1.33GB였고 시간도 꽤 걸렸다(2분 40초 정도?)
  - 이전에 진행한 Spring Boot의 경우 용량이 819MB로 상대적으로 낮음

- Next.js도 같은 방식으로 사전 빌드를 진행한 후 이미지는 빌드된 파일만 복사할 수 있을거다.

  - 문제는 이럴경우 프로젝트를 실행하기 위한 node_modules의 복사도 같이 필요하다.(node_modules이 프로젝트 내에서 차지하는 용량이 꽤 큰 편이다)

- 결론은 나중에 두 가지 방식 중 어떤 방식이 더 적합할지는 한번 시도해봐야할 것 같다
