# Dockerfile 만들기

docker는 이미지가 있어야지 컨테이너를 생성할 수 있습니다. Dockerfile은 이런 이미지를 만들 수 있게 해주는 일종의 명세서입니다.
Dockerfile을 빌드해주면 도커 이미지가 나옵니다. 

<img width="577" alt="스크린샷 2021-01-19 오후 11 52 15" src="https://user-images.githubusercontent.com/61309514/105135025-a46c8580-5b32-11eb-8c3a-c6db1ca182cb.png">
<br>
<br>
자 그럼 Dockerfile이 어떻게 생겼는지 직접 확인하면서 체크해 봅시다.

<img width="337" alt="스크린샷 2021-01-19 오후 11 55 28" src="https://user-images.githubusercontent.com/61309514/105135063-bcdca000-5b32-11eb-8742-78e1efce2bc6.png">

<span style="color:purple; font-size:2em">FROM</span>부분은 베이스 이미지를 명시해 줍니다.
베이스 이미지는 도커 이미지에 기본적으로 깔리는 인스톨로 도커 엔진 바로 위 레이어에 존재하는 이미지입니다. 보통 리눅스 운영체제, 언어 패키지가 주를 이룹니다.

<span style="color:purple; font-size:2em">WORKDIR</span>는 이미지 안에서 어플리케이션 소스코드를 가지고 있을 디렉토리를 생성하는 것입니다. 이 설정은 안해주면 도커 이미지를 이용해 만든 컨테이너의 root directory에 로컬 파일에서 복사한 파일들이 널부러져 있습니다. 만약 이름이 같을 경우 덮어씌여지기 때문에 안전하고, 관리하기 쉽게 WORKDIR설정을 해주도록 합시다.
여기서는 /usr/src/app 폴더에 소스코드들을 넣어놓도록 설정해 놓았습니다.
그리고 도커 컨테이너를 실행할 때 WORKDIR에서 지정한 디렉토리에서 부터 시작하게 됩니다.

<img width="518" alt="스크린샷 2021-01-20 오후 3 30 22" src="https://user-images.githubusercontent.com/61309514/105136147-8bfd6a80-5b34-11eb-8c33-ad639e9f68a9.png">

이렇게 shell로 들어가서 pwd 하면 WORKDIR에서 시작합니다.


<span style="color:purple; font-size:2em">COPY</span>는 도커 이미지를 만드려고 하는 기존 로컬 폴더의 파일을 이미지에 복사하는 것입니다.
COPY [로컬파일 이름 ] [저장될 디렉토리] 로 구성되어 있고 공백 맞춰서 적으셔야합니다. 위 사진의 경우 ./ 로 설정했을 경우 WORKDIR로 저장됩니다. 여기서 COPY가 RUN의 npm install보다 앞에 나온 이유는 npm install이 시행되기 위해서는 package.json 파일이 필요하기 때문입니다.
여기서 COPY 명세가 두번 나온 이유는 Docker cache를 이용하고자해서 입니다.
+ 토막 상식! 도커는 이미지 생성 시간을 줄이기 위해서 Dockerfile의 각 과정을 캐시합니다.

만약 package.json에 선언되어 있는 dependencies는 바뀌지 않고 소스코드만 변경 되었을 경우에 이러한 방법은 이미지 빌드를 빠르게 도와줍니다.

사진 위쪽에 있는 COPY package.json ./ 를 먼저 카피하는 절차가 docker cache에 저장되어 있기 때문에 dependencies의 변화가 없을 경우 이러한 캐시를 사용해서 빌드 속도를 빠르게 할 수 있습니다.


<span style="color:purple; font-size:2em">CMD</span>는 '[]' 기호 안에 컨테이너 시작시 실행할 명령어를 명시해 줍니다. docker run으로 컨테이너가 실행 되었을 때 대괄호 안에 있는 명령어가 시행됩니다. 도커 엔진이 CMD안에 인자들을 json 형식으로 인식하기 때문에 큰따옴표를 이용하고 공백이 있을 경우에 콤마를 넣어서 구별해 줍니다. RUN과의 가장 큰 차이점은 RUN은 이미지를 만들때 사용되는 반면 CMD는 이미지에 저장되지 않고 이미지가 컨테이너로 실행될때 실행되는 명령어입니다.
CMD 명령어는 도커파일 전체에 단 하나만 존재 가능하다.
위 방식은 CMD["executable","param1"] 방식으로 만들어 졌으며 "executable"에는 "node" "param1"에는 "server.js"가 들어가 있다. 파라미터는 더 많이 추가가 가능하다. 하지만 "executable" 명령어는 하나만 가능하다.

<br>
<br>

# docker run 명령어 작성시 포트매핑 하는 방법과 그 이유
도커 파일을 빌드해서 이미지를 만들고 이미지를 run 해서 컨테이너까지 만들어 보자.
엥? 그런데 도커로 실행한 앱에 접속이 되지 않는다... 왜 이렇지?
PORT 설정도 제대로 했는데 되지 않는다. 왜그럴까?

<span style="color:RED; font-size:3em">그건 바로! </span>
<br>
<br>
당신이 포트 매핑을 해주지 않았기 때문이다.
도커 컨테이너 안에서 열린 포트와 로컬에서 열린 포트가 다르기 때문에 docker run을 할때 포트 매핑을 꼭 해줘야한다.
즉 로컬 네트웤과 컨테이너 네트웤을 mapping해주는 작업을 해야한다.

방법은 다음과 같다.
```linux
 docker run -p [로컬 호스트 네트워크 포트]:[컨테이너 네트워크 포트] [이미지 이름] 
```

이렇게 run 명령어를 작성해 주면 된다.

끝!

<span style="color:green; font-size:1.5em"> 추가 꿀팁 -d 옵션이란? </span>

-d 는 detach 옵션의 약어이다.

일반적인 도커 run의 경우 컨테이너를 실행 한 후 컨테이너 실행 화면을 터미널에 계속 보여준다. 하지만 -d를 넣을 경우 컨테이너 실행 화면은 백그라운드로 넘어가서 실행되고 터미널 화면은 처음 도커 run을 한 디렉토리로 돌아오게 된다.

<br>
<br>

# Docker Volume

도커 볼륨은 도커 컨테이너에서 특정 파일들을 로컬 파일에 참조하도록 설정하는 방법이다.

도커 볼륨을 COPY와 비교해 보자.

<img width="536" alt="스크린샷 2021-01-20 오후 4 22 12" src="https://user-images.githubusercontent.com/61309514/105141234-31680c80-5b3c-11eb-9d74-0bf4ee55b3f0.png">


Dockerfile에 COPY로 설정을 해줄 경우 이렇게 파일들을 컨테이너에 복사해 놓는다.
이렇게 하면 문제점이 소소스코드를 변경할 때마다 변경 된 것을 빌드해줘야지 된다. 그래야 컨테이너에 적용이 된다.

이런 COPY를 대체하는게 Volume 설정이다.

<img width="534" alt="스크린샷 2021-01-20 오후 4 22 25" src="https://user-images.githubusercontent.com/61309514/105141263-3af17480-5b3c-11eb-8541-2fc725ed7615.png">


이렇게 컨테이너에 파일을 매핑을 해주면 소스코드가 변경 될 때 마다 빌드를 할 필요가 없다. 로컬 파일에 매핑이 되어있기 때문에 변경 사항이 바로바로 반영 된다.

# 도커 Volume 설정 

도커 볼륨 설정하는 법은 다음과 같다.

```linux
docker run [포트 설정] [-v [로컬 파일중 컨테이너에 매핑하지 않을 파일 ex)/usr/src/app/node_module]] [-v [컨테이너에 매핑할 파일 경로]:[컨테이너 디렉토리 경로 ]] [이미지 아이디]
```
이렇게 설정하면 된다.
즉 매핑할거면 로컬 경로:컨테이너 경로  이런 식으로 적어 주고 아니면 컨테이너 경로만 적어주고 끝내면 된다. 컨테이너 경로만 적어주면 해당 파일은 매핑을 하지 않는다.

이런 Volume 설정은 나중에 docker compose에서 docker-compose.yml에 Volume 설정을 통해서 docker compose에서도 사용할 수 있다.


출처<br> 
[따라하며 배우는 도커와 ci환경](https://www.inflearn.com/course/%EB%94%B0%EB%9D%BC%ED%95%98%EB%A9%B0-%EB%B0%B0%EC%9A%B0%EB%8A%94-%EB%8F%84%EC%BB%A4-ci#)

[안지훈 블로그 Dockerfile만들고빌드하기](https://namsick96.github.io/docker/Dockerfile만들고빌드/)

[안지훈 블로그 Docker container port mapping](https://namsick96.github.io/docker/docker-container-port-mapping/)

[안지훈 블로그 docker volume](https://namsick96.github.io/docker/docker-volume/)