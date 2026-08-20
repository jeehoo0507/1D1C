#### 1. GitHub에 이미 레포가 있고, 로컬로 받아올 때
```bash
git clone https://github.com/user/repo.git
```

복제 폴더 생김
이 경우 init 설정까지 다 됨

#### 2. 로컬에 이미 폴더/파일이 있고, GitHub에 새로 올릴 때
```bash
cd 폴더명 # 폴더 내부 터미널에서 해야함
git init # git 저장소 설정
git add . 
git commit . 
git remote add origin https://github.com/user/repo.git # 깃헙 주소 연결
git branch -M main # 선택 git 버전마다 master가 main인 경우가 있어서 이걸 main으로
git push -u origin main # -u는 어떤 브랜치랑 연결할지 알려줌
```

새로운 branch 연결 떄만 -u origin 브랜치명 해주면 됨
origin은 관례일 뿐 그냥 깃헙 저장소 url 줄이기 용도임

```bash
git push # 레포를 깃헙 저장소에 push
git pull # 깃헙 저장소에서 로컬로 가져오기
```

```bash
git remote add origin1 https://github.com/user/repo1.git
git remote add origin2 https://github.com/user/repo2.git
```
이런식으로 여러개 저장소에 연결 가능