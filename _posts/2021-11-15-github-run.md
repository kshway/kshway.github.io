---
title:  "[ATC] 아나콘다로 깃허브 파일 실행하기"
excerpt: "깃허브 파일을 아나콘다 프롬프트로 열기"

categories:
  - ATC

tags:
  - python
  - anaconda

toc: true
toc_sticky: true
use_math: true

date: 2021-11-15
last_modified_at: 2021-11-15
---

# 가상환경 만들기

## 아나콘다 프롬프트에서 가상환경 만들기

아나콘다가 노트북에 설치되어있다면, 아나콘다 프롬프트를 실행시켜 아래 코드를 입력해준다.  
그러면 laneDetect라는 이름으로 가상환경이 만들어 질것이다!

```py
conda create -n laneDetect python=3.7
```

## 가상환경 실행

가상환경을 만들었다면 활성화 시켜줘야한다!  
아래 코드를 입력해주자

```py
conda activate laneDetect
```

## 파일 받아오기

git을 설치한뒤 'git clone ~~ github주소' 형식으로 입력해준다
```
git clone https://github.com/windowsub0406/Advanced-lane-finding.git
```

## 패키지 설치
pip install 명령어로 패키지를 설치한다

```py
# openCV 4.3.0.36 버전으로 다운
pip install opencv-python
pip install matplotlib
pip install scikit-image
```



## 폴더옮기기
cd 명령어를 이용해서 폴더로 이동해준다

```
cd Advanced-lane-finding
```

## 실행하기

폴더에있는 실행파일을 실행해준다
```
python main.py
```

## 실행결과
![image](https://user-images.githubusercontent.com/91586956/141775316-e18d2b79-872e-4e5d-9fa2-72aecde20f92.png)
