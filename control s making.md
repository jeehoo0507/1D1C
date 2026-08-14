```
pyproject.toml
│
├── [project]
│   ├── 프로젝트 이름
│   ├── 버전
│   ├── Python 버전
│   └── 필요한 라이브러리
│
├── [project.scripts]
│   └── 터미널 명령어 설정
│
└── [build-system]
    └── Python 패키지를 어떻게 빌드할지 설정
```

우선 이런 플젝을 위해서는 이러한 pyproject.toml이 필요하다고 함
pyproject.toml은 python 프로젝트 설명서임
프로젝트 명 필요한 파이썬 버전 필요한 라이브러리 어떤 명령을 사용 가능한지 등을 적어두는 파일


[project.scripts]

```
[project.scripts]
lab = "lab.cli:app"
```

이런식으로 작성하면 터미널에서 lab 명령어를 사용가능하다

```
lab
 ↓
lab/cli.py
 ↓
app
```

cli.py
```python
import typer

app = typer.Typer()

@app.command()
def hello():
    print("hello")
```

우리는 단순히 hello() 를 실행하는게 아니라 터미널에서 hello() 함수를 실행할 명령어를 만드는 거다

그래서 app 이라는 명령어 관리자를 선언하고 @app.command 데코레이터를 통해 hello() 함수 를 hello 명령어 목록에 등록하다

위에서 lab = lab.cli:app 으로 선언했기에
```
lab hello
```
를 실행하면

운영체제는 lab이라는 프로그램을 실행 근데 선언 되어있으니까 확인하면
lab 폴더 안에 cli.py 안에 app 을 찾아감 그 후에 뒤에 명령어 이름과 
app이 라우팅하는? 명령어들과 대조해서 찾는다

프로젝트 폴더를 설치하면서 toml 파일을 읽는 과정에서 명령어 lab이 등록된다.

```bash
cd ~                    # 홈에 바로 만들어도 됨
uv init --package ctrls
cd ctls
```

이걸 통해 기본적인 project.toml이나 이런 파일들이 존재하는 패키지를 생성가능하다.


```bash
uv add typer rich tomlkit
```

필요한 라이브러리 설치
toml 파일 dependencies에 자동 추가됨

```bash
uv tool install --editable .  
ctrls --help                    
```


uv tool install 를 통해 파이썬 패키지를 cli 도구로 설치하는데
--editable .  을 통해 지금 만드는 폴더를 직접 바라보게해서 바로바로 수정하면서 명령어 테스트가 가능하다. help는 어떤 명령어를 가지는지 무슨 역할을 하는지 소개한다.

