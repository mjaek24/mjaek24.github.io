---
layout: post
comments: true
author: MinjaeGhim
title: "Mac Terminal에서 사용할 Custom 명령어 만들기"
description: 자동화를 좋아하는 사람들을 위한 Custom 명령어 만드는 방법
image: "https://user-images.githubusercontent.com/48783078/65959069-f7c54b00-e48b-11e9-9a09-76d493b1731a.png"
categories:
- programming
date: 2021-04-04 12:40:00
tags:
- schell script
introduction: 자동화를 좋아하는 사람들을 위한 Custom 명령어 만드는 방법
---

## Mac Terminal 에서 Custom 명령어 만들기
  
우리가 컴퓨터를 켜면 항상 실행하는 프로그램들이 있습니다.
그 프로그램들을 여러 번 클릭하며 하나씩 실행하지 않고 명령어 한 번에 내가 원하는 프로그램들이 실행되는 shell script 파일을 만들어 보았습니다.
  
  
  
### 목표
>- 키보드로 프로그램 실행하기
>- Terminal 평소 자주 사용하는 여러 프로그램 한 번에 실행하기
  

#### Terminal 에서 IntelliJ 실행할 수 있게 준비하기

1.  IntelliJ 상단 Tools > Create Command-line Launcher...  클릭

![스크린샷 2021-04-03 오후 8 42 59](https://user-images.githubusercontent.com/39821089/113484151-bfb82180-94e1-11eb-8f85-8ee26d070e67.png)

2.  Toolbox 앱에서 설정하라고 안내창이 뜬다.

3.  Terminal 에서 환경변수 추가하기

```
vi ~/.zshrc
``` 

<img width="282" alt="스크린샷 2021-04-03 오후 3 40 07" src="https://user-images.githubusercontent.com/39821089/113483985-183aef00-94e1-11eb-976a-d0a334ea156c.png">

```  
export PATH=~/jetbrains:$PATH
 ```  

 4.  Toolbox 앱 Settings > Shell Scripts
	Generate Shell Script 를 켜고 아까 설정한 환경변수에 추가한 Shell scripts location을 잡아준다. 
	
![스크린샷 2021-04-03 오후 8 44 11](https://user-images.githubusercontent.com/39821089/113484015-37d21780-94e1-11eb-86a7-4d197c8309c9.png)

### 실행 파일 만들기

이제 모든 명령을 보관할 새로운 bash script 파일을 만들면 됩니다.
Terminal에서만 사용되며 실수로 인한 삭제를 방지하기 위해 파일 이름 앞에  
점 ( `.`) 을 추가하여 숨겨진 파일로 만듭니다.
1.  **파일 생성 및 저장하기**

.custom_command.sh 파일 편집모드

```  
vi .custom_command.sh
```   

태스트 함수 작성

```
#!/bin/bash  
function test_func() {  
  echo 'test_func'
}
```
저장 `:wq` 해줍니다.
 
2. **permission 설정**

기본적으로 새로 생성된 파일에는 읽기 권한만 있기 때문에 바로 실행하면 권한이 거부되었다고 뜰 것입니다. 그래서 파일에 실행 권한을 따로 설정해 줍니다.

```  
chmod +x .custom_command.sh
```  

파일이 잘 생성됐는지 확인하고 싶다면... `ls -al`

파일을 실행하는 명령어로 `source ~/.custom_command.sh` 하면 아까 파일에 입력한 Test가 잘 나올 것입니다. 하지만 이렇게까지만 하면 현재 터미널 세션에서는 사용 가능하지만 새로운 탭을 열면 합니다. 그래서 모든 터미널 세션에서 명령을 로드할 수 있게 설정해 줘야 합니다.

3.   **`rc`  파일에 명령을 추가**

- 각 터미널 세션에서 스크립트의 내용을 로드하도록 shell 의 `rc`  파일을 연다.

```
vi ~/.zshrc
```

- **`~/.bashrc` 혹은 `~/.zshrc` 파일에 실행 명령 `source ~/.custom_command.sh` 추가**
- 저장 `:wq`
- 이제 다른 터미널 탭에서도 test_func 명령이 사용 가능합니다.

4. ***.custom_command.sh* 파일 편집**

커스텀한 명령어를 작성해 줍니다.

```  
#!/bin/bash

# command :  open frequenly used apps
function omu() {
    URL_LIST=( "https://www.google.com/" "https://www.youtube.com/" )
    
    for URL in ${URL_LIST[@]}; do
            crm ${URL}
    done
    
    itj 'demo-project'
    open -a 'Notion'
}
# command : open url on 'Chrome' app
function crm() {
	open -a "Google Chrome" ${1:="https://www.google.com/"}
}
# command : open 'Chrome' app and searh google
function crm-s() {
        open -a "Google Chrome" "https://www.google.com/search?q=${@}"
}
# command : open 'IntelliJ' with project under folder name 'Project'
function itj() {
	idea ~/Project/${1:='demo-project'}
}
```  
#### 각 명령어 설명

|명령어| 목적 |  실행 프로그램 | 파라미터  | default | 예시
|--|--|--|--|--|--|
| omu | 자주 사용하는 앱 한번에 실행 | Chrome, Notion, IntelliJ ||| `omu` 
| crm | Terminal 사용 중 크롬 열기 | Chrome | url | Google | `crm https://mjaek24.github.io/` 혹은 `crm`
| crm-s | Terminal 사용 중 구글 검색 | Chrome| 검색할 단어 | Google | `crm-s seoul`
| itj | Finder에서 프로젝트 위치 찾아가지 않고 명령어로 바로 프로젝트 실행하기 | IntelliJ | 프로젝트 | 지정한 default 프로젝트 | `itj demo-project` 혹은 `itj`

* 파라미터 값을 받아 사용하는 함수는 default 값을 넣어 오류를 방지

이렇게 실행 파일까지 다 만들었으면
이제 터미널에서 간단하게 omu만 쳐도 한 번에 자주 사용하는 프로그램들이 실행됩니다!


### 성과
>- 시간절약으로 생산성 올리기
>- 작업 자동화
>- 마우스 사용 줄이고 손목 보호
  
  


