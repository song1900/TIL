# [GitPro] Chapter 02 - Git의 기초

## 2.1 Git 저장소 만들기
### 기존 디렉토리를 Git 저장소로 만들기 
- 버전관리를 하지 아니하는 기존 프로젝트를 Git으로 관리하고 싶은 경우 우선 프로젝트의 디렉토리로 이동한다.
    
    ```
    $ cd /Users/user/my_project 
    ```
    
- 그리고 아래와 같은 명령을 실행한다.
    
    ```
    $ git init
    ```
    
    이 명령은 `.git` 이라는 하위 디렉토리를 만든다. `.git` 디렉토리는 저장소에 필요한 뼈대 파일이 들어 있다. 이 명령만으로는 아직 프로젝트의 어떤 파일도 관리하지 않는다.
    
- Git이 파일을 관리하게 하려면 저장소에 파일을 추가하고 커밋해야 한다. `git add` 명령으로 파일을 추가하고 `git commit` 명령으로 커밋한다.
    
    ```
    $ git add *.c
    $ git add LICENSE
    $ git commit -m 'inital project version'
    ```

### 기존 저장소를 Clone하기
- 다른 프로젝트에 참여하려거나 Git 저장소를 복사하고 싶을 때 `git clone` 명령을 사용한다.
- `git clone <url>` 명령으로 저장소를 Clone 한다. libgit2 라이브러리 소스코드를 Clone 하려면 아래와 같이 실행한다.
    
    ```
    & git clone https://github.com/libgit2/libgit23
    ```
    
- 이 명령은 “libgit2” 라는 디렉토리를 만들고 그 안에 `.git` 디렉토리를 만든다. 그리고 저장소의 모든 데이터를 자동으로 가장 최신 버전을 Checkout 해 놓는다.
- 아래와 같은 명령을 사용하여 저장소를 Clone하면 libgit2이 아니라 다른 디렉토리 이름으로 Clone할 수 있다.
    
    ```
    & git clone https://github.com/libgit2/libgit23 mylibgit
    ```