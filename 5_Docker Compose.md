# Docker Compose

도커 컴포즈는 무엇인가?
도커 컴포즈는 다중 컨테이너 도커 어플리케이션을 정의,실행하기 위한 도구이다.
그렇다면 이게 왜 필요할까?
일반 시스템은 단일 어플리케이션으로 구동하지 않는다. 여러개의 어플리케이션들이 서로 의존하는 구조이다. 따라서 각 어플리케이션마다 각자 다른 컨테이너에 존재하면 컨테이너 개수는 늘어날 것이다. 이 때 필요한 기술이 도커 컴포즈로 여러 컨테이너를 한번에 관리하는데 굉장히 유용한 기술이다.

그리고 여러 컨테이너가 동시에 작동할 때 두 컨테이너는 독립적으로 작동하기 때문에 두 컨테이너가 서로 다른 컨테이너에 접속하는게 네트워크 포트로만 가능하다. 서로 독립된 컴퓨터라고 보면 됨. 이때 도커 컴포즈를 사용하면 이러한 문제들을 해결 할 수 있다.
<br>
<br>

# Docker-compose.yml 파일 만들기 

들어가기에 앞서..
yaml 파일이란?

.yml 확장자를 가지고 있는 yaml 파일은 yaml ain't markup language의 약자로 일반적으로 구성 파일 및 데이터가 저장되거나 전송되는 응용프로그램에서 사용된다. (이전 xml, json 파일로 저장된것 대체함.)

<img width="315" alt="스크린샷 2021-01-20 오후 5 12 49" src="https://user-images.githubusercontent.com/61309514/105145993-c79f3100-5b42-11eb-9238-8dddc33f032f.png">

<span style="color:purple; font-size:2em">version</span>은 3으로 지정한다. 도커 컴포즈 버전은 3이다.

<span style="color:purple; font-size:2em">services</span>는 컨테이너들을 묶는 곳이다. services에 속성들로 컨테이너 이름을 넣어준다. 여기서는 redis-server, node-app 이라는 두 컨테이너를 설정해 놓고 있다.

<span style="color:purple; font-size:2em">image</span>는 해당 컨테이너에서 사용하는 이미지이다.

<span style="color:purple; font-size:2em">build</span> 설정된 디렉토리에 있는 도커 파일을 사용해서 빌드 한 후 이미지를 만든다는 것이다. 여기서는 . 을 통해서 현재 디렉토리에 있는 도커파일을 사용한다는 것이다.

<span style="color:purple; font-size:2em">ports</span>는 해당 컨테이너 포트매핑을 해주는 곳이다.

<br>
<br>

# Docker Compose 실행 (docker-compose up)

들어가기에 앞서.. docker compose 실행은 docker-compose.yml파일이 있는 디렉토리에서 실행해줘야한다.
<br>

+ <span style="color:green; font-size:1.5em">docer-compose build</span>를 하면 이미지를 빌드하기만 하며 컨테이너 시작 즉 run을 하지는 않는다.
+ <span style="color:green; font-size:1.5em">docer-compose up</span>을 하면 이미지가 존재하지 않을 경우에만 빌드하며 이미지가 존재하면 빌드를 생략하고 바로 컨테이너를 시작한다.
+ <span style="color:green; font-size:1.5em">docer-compose up --build</span>는 필요하지 않을때도 강제로 이미지를 빌드하며, 컨테이너를 시작한다.
+ <span style="color:green; font-size:1.5em">docer-compose up --no-build</span>는 이미지 빌드 없이, 컨테이너를 시작한다. 이미지가 없을 경우 실행 실패가 뜬다.


<br>
정리 하자면 소스코드가 변경되어서 다시 빌드가 필요하면 뒤에 --build 옵션을 넣어주면 된다.

# Docker Compose down

Docker compose로 실행된 컨테이너를 모두 한꺼번에 종료 하는 방법은
```linux
docker-compose down
```
이다.
근데 ctrl+c 를 사용해서 실행하고 있는 터미널에서 종료하는 방법도 존재한다.

정확히 말하면 위 방법은 -d 옵션을 사용해서 백그라운드에서 docker-compose가 작동하고 있을 때 docker-compose를 중단하는 방법이다.

<br>
<br>

출처<br>
[따라하며 배우는 도커와 ci환경](https://www.inflearn.com/course/%EB%94%B0%EB%9D%BC%ED%95%98%EB%A9%B0-%EB%B0%B0%EC%9A%B0%EB%8A%94-%EB%8F%84%EC%BB%A4-ci#)

[안지훈 블로그 docker compose](https://namsick96.github.io/docker/docker-compose/)


