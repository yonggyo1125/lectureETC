## git - 형상관리 툴(버전관리)

- <a href='https://drive.google.com/drive/folders/1CM7aI02a7t7qCc-roYohi3YAxKu6Ngkv?usp=share_link'>동영상 강의</a>

###  git 설치
- git-scm.com

### 로컬 레포지토리(로컬 저장소)
- git init

### 계정 연결
- 원격 레포지토리인 깃허브 계정과 동일하게 입력 

```
git config --global  user.name "yonggyo1125"
git config --global user.email "yonggyo00@kakao.com"
```

## 형상관리

### 스테이지 단계 
- git add 파일명, 파일경로 
- git add . (모든 변경된 파일)


### 스냅샷 단계 
- git commit -m "메모"

### 커밋 로그 
- git log
- git log --oneline : 한줄로 짧게 로그 확인

### 버전 관리 
- git checkout 커밋 ID
- git checkout -  (가장 최근 커밋 단계로 복구)
        
### 브랜치 관리
- master - 기본 브랜치

- git branch 브랜치명   :  현재 브랜치의 파일 -> 생성된 브랜치로 복사 

- git branch  -> 브랜치 목록 확인
- git branch -d 브랜치명 
- git branch -D 브랜치명(강제 삭제)

### 브랜치 변경 
- git checkout 브랜치명


### 소스 합치기
- git merge 브랜치명
- 현재 브랜치에 브랜치명에 있는 소스가 합쳐진다.
