---
tags: docker, dev
---

- docker repository에 공유된 이미지로 컨테이너 실행
    
    ```jsx
    docker run -d -p 80:80 docker/getting-started
    ```
    
    -d: detach mode (background)
    
    -p: port mapping (host : container)
    
    -docker: repository name
    
    -getting-started: image name
    
- docker 이미지 생성 (Dockerfile 필요)
    
    ```jsx
    docker build -t getting-started .
    ```
    
    -t: flag tags image(image name)
    
    -.: 빌드시 사용할 Dockerfile 참조 경로
    
- docker 정보 확인
    
    ```jsx
    docker ps
    ```
    
- docker 종료 (새로운 컨테이너를 실행하려면 기존 컨테이너 종료(삭제) 후 실행)
    
    ```jsx
    docker stop <the-container-id>
    docker rm <the-container-id>
    또는
    docker rm -f <the-container-id>
    ```
    
- docker image 공유
    
    ```jsx
    docker push woo972/getting-started
    ```
    
    -woo972: 생성한 개인 레포지토리
    
- image 태깅 (repository에 올리기 전에 해야함)
    
    ```jsx
    docker login -u woo972
    docker tag getting-started woo972/getting-started
    ```
    
- named volume(간단히 사용할 때) 생성 및 매핑
    
    ```jsx
    docker volume create todo-db
    docker run -d -p 3000:3000 -v todo-db:/etc/todos getting-started
    ```
    
    -todo-db: persistency를 위해 생성할 volume명
    
    -v: volume 마운트를 위한 flag
    
    -/etc/todos: todo-db를 마운트할 container 내 경로 
    
- bind mount(특정 디렉토리의 변경을 감지해서 업데이트하거나 할때)
    
    ```jsx
    docker run -d -p 3000:3000 \
    -w /app \
    -v "$(pwd):/app" \
    node:12-alpine \
    sh -c "yarn install && yarn run dev"
    ```
    
    -w /app: working directory(커맨드를 실행할)를 /app으로 설정한다
    
    -v "$(pwd):/app": host의 현재 directory를 컨테이너내의 /app 디렉토리로 bind mount한다
    
    -node:12-alpine: 이미지
    
    -sh -c "yarn install && yarn run dev": 실행할 커맨드 
    
- docker 내에서 실행하기
    
    ```jsx
    docker exec <container-id> 명령어 
    (ex docker exec <container-id> cat /data.txt) 
    ```
    
- container networking
    
    ```jsx
    **docker network create todo-app**
    docker run -d \
        **--network todo-app --network-alias mysql \**
        -v todo-mysql-data:/var/lib/mysql \
        -e MYSQL_ROOT_PASSWORD=secret \
        -e MYSQL_DATABASE=todos \
        mysql:5.7
    docker run -dp 3000:3000 \
      -w /app -v "$(pwd):/app" \
      **--network todo-app \**
      -e MYSQL_HOST=mysql \
      -e MYSQL_USER=root \
      -e MYSQL_PASSWORD=secret \
      -e MYSQL_DB=todos \
      node:12-alpine \
      sh -c "yarn install && yarn run dev"
    ```
    
    -network create: 네트크워크 생성
    
    —network todo-app: todo-app 네트워크에 연결한다
    
    —network-alias mysql: ip주소 대신 mysql이란 hostname으로 컨테이너를 식별할 수 있게 한다
    
- docker-compose.yml 이용
    
    ```jsx
    version: "3.7"
    
    services:
      app:
        image: node:12-alpine
        command: sh -c "yarn install && yarn run dev"
        ports:
          - 3000:3000
        working_dir: /app
        volumes:
          - ./:/app
        environment:
          MYSQL_HOST: mysql
          MYSQL_USER: root
          MYSQL_PASSWORD: secret
          MYSQL_DB: todos
    
      mysql:
        image: mysql:5.7
        volumes:
          - todo-mysql-data:/var/lib/mysql
        environment: 
          MYSQL_ROOT_PASSWORD: secret
          MYSQL_DATABASE: todos
    
    volumes:
      todo-mysql-data:
    ```
    
    -services: 여러 컨테이너를 띄울 수 있음
    
    -app: 컨테이너 이름
    
    -volumes:` ./:/app: docker-compose를 사용하면 volume에서 상대경로 사용이 가능
    
    -mysql: mysql이라는 이름으로 서비스 정의 및 자동으로 network alias 부여
    
    -volumes todo-mysql-data: todo-mysql-data라는 이름으로 volume 생성
    
- docker-compose로 실행 및 중지
    
    ```jsx
    docker-compose up -d
    docker-compose logs -f
    docker-compose down
    ```