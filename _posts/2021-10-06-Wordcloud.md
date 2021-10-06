---
title:  "[공빅데 프로젝트] 워드클라우드"
excerpt: "R을 이용하여 워드클라우드를 표출해보자."

categories:
  - Project
  - R
tags:
  - WordCloud

toc: true
toc_sticky: true

date: 2021-10-06
last_modified_at: 2021-10-06
---
# WordCloud 만들기
지난번에 Crawling 했던 파일들을 모아서(물론 google뿐만 아니라 네이버 파일도 추가) 단어 빈도수에 따라 WordCloud를 표출해보자

## txt 파일 읽어들이기
R에서는 파일을 읽는것이 더 쉽다. '스마트횡단보도통합.txt'라는 파일을 읽어서 wc2라는 변수에 저장한다.  
```r
wc2 <- readLines("스마트횡단보도통합.txt")
```
## 사전에 필요한 단어 등재
우리는 문장들이 있는 파일로부터 명사를 받아와서 워드클라우드를 표출하려고한다. 그 가운데 사전에없는 단어들이 분명있을것이다. 예를들어 신조어라던지.. 노잼,, 이런거 ㅋㅋㅋ 그렇기때문에 명사를 뽑아오기전에 사전에 단어를 올려줘야한다. 우리가 필요했던 단어 중 사전에 없을 단어는 '지능형 CCTV'였다. 아래는 이를 명사로 처리하라는 명령이다.  
```r
mergeUserDic(data.frame(c("지능형 CCTV"),"ncn"))
```

## 특수문자 및 불용어 제거
사실 불용어 제거하는 과정은 대체로 따로 파일을 만들어서 반복문을 돌려서 제거하는것이 일반적이다. 하지만 뭐 몇개 안돼서 그냥 찾아적었다. 당신들은 이러지 마시길 ㅎ
```r
wc2 <- str_replace_all(wc2, "문서 저장하기  Keep에 저장 Keep 바로가기", "") %>%
  str_replace_all("\\W", " ") %>%
  str_replace_all("뉴스", "") %>%
  str_replace_all("네이버", "") %>%
  str_replace_all("기사", "") %>%
  str_replace_all("신문", "") %>%
  str_replace_all("개소", "") %>%
  str_replace_all("확보", "") %>%
  str_replace_all("일보", "") %>%
  str_replace_all("제공", "") %>%
  str_replace_all("설치", "") %>%
  str_replace_all("이번", "") %>%
  str_replace_all("매일", "") %>%
  str_replace_all("하기", "") %>%
  str_replace_all("들이", "") %>%
  str_replace_all("아시아경제", "") %>%
  str_replace_all("연합", "") %>%
  str_replace_all("한국", "") %>%
  str_replace_all("뉴시스", "") %>%
  str_replace_all("정원오", "") %>%
  str_replace_all("TOP", "") %>%
  str_replace_all("ITS", "") %>%
  str_replace_all("하면", "") %>%
  str_replace_all("이투데이", "") %>%
  str_replace_all("타임즈", "") %>%
  str_replace_all("총력", "") %>%
  str_replace_all("원도심", "") %>%
  str_replace_all("로봇", "") %>%
  str_replace_all("파이낸셜", "") %>%
  str_replace_all("살수차", "") %>%
  str_replace_all("언론사", "") %>%
  str_replace_all("오늘", "") %>%
  str_replace_all("아시아투데이", "") %>%
  str_replace_all("지구", "") %>%
  str_replace_all("SK", "") %>%
  str_replace_all("이날", "") %>%
  str_replace_all("만원", "") %>%
  str_replace_all("억만", "") %>%
  str_replace_all("때문", "") %>%
  str_replace_all("아주경제", "") %>%
  str_replace_all("타운", "") %>%
  str_replace_all("구성", "") %>%
  str_replace_all("비롯", "") %>%
  str_replace_all("에서", "") %>%
  str_replace_all("신아", "") %>%
  str_replace_all("제회", "") %>%
  str_replace_all("해당", "") %>%
  str_replace_all("고원", "") %>%
  str_replace_all("수상", "") %>%
  str_replace_all("하지", "") %>%
  str_replace_all("내외통신", "") %>%
  str_replace_all("전동", "") %>%
  str_replace_all("각종", "") %>%
  str_replace_all("곳곳", "") %>%
  str_replace_all("김수영", "") %>%
  str_replace_all("지역", "") %>%
  str_replace_all("계획", "") %>%
  str_replace_all("올해", "") %>%
  str_replace_all("지원", "") %>%
  str_replace_all("년간", "") %>%
  str_replace_all("이데일리", "") %>%
  str_replace_all("헤럴드경제", "") %>%
  str_replace_all("합성어", "") %>%
  str_replace_all("NSP", "") %>%
  str_replace_all("등장", "") %>%
  str_replace_all("물고", "") %>%
  str_replace_all("위클리", "") %>%
  str_replace_all("프리", "") %>%
  str_replace_all("지난해", "") %>%
  str_replace_all("하게", "") %>%
  str_replace_all("성동구청장", "") %>%
  str_replace_all("무더위", "") %>%
  str_replace_all("성동", "") %>%
  str_replace_all("센터", "") %>%
  str_replace_all("사업", "") %>%
  str_replace_all("경우", "") %>%
  str_replace_all("[0-9]{4}[:space:][0-9]{2}[:space:][0-9]{2}", "") %>%
  str_replace_all("[0-9]+", "")
  ```
## 명사만 추출하기
파일은 문장으로 되어있다. 따라서 워드클라우드로 표출하기 위해 명사만 추출하는 과정이 필요하다. extractNoun()함수를 이용했다. (지금보니 이름이 완전 직관적이다.)
```r
nouns2 <- extractNoun(wc2)
```

## 데이터 완성
데이터를 워드클라우드로 표출하기 위해 몇가지 데이터 변환이 필요하다.
### 단어 리스트를 문자열 벡터로 변환
단어가 리스트 형태로 되어있으므로 nouns2를 문자열 벡터로 변환한다.
```r
wordcount2 <- table(unlist(nouns2))
```

### 데이터 프레임 변환
위에서 변환한 벡터를 데이터 프레임으로 변환한다.
```r
df_word3 <- as.data.frame(wordcount2, stringsAsFactors = F)
```
### 두 글자 이상 추출
단어 글자 수가 두글자 이상인 것만 추출하였다.
```r
df_word3 <- filter(df_word3, nchar(word) >= 2)
```

## 워드클라우드 완성
### 워드클라우드 색상 설정
워드클라우드의 색을 설정하기 위해 brewer.pal()함수를 사용했다.
```r
pal <- brewer.pal(8, "Dark2")
```

### 워드클라우드 생성
이제 다왔다 ㅜㅜ 워드클라우드를 생성해준다!!!!
```r
attach(df_word3)
df_word4 <- df_word3[c(order(-df_word3$freq)),]
df_word4 <- head(df_word4, 100)
df_word4
detach(df_word3)

wordcloud2(data=df_word4, size = 2)
```
그럼 다음과 같은 워드클라우드가 표출된다.  

![1](https://user-images.githubusercontent.com/91586956/136167696-85817cfa-2518-4407-aac9-c4597afea774.png)  

워드클라우드를 살펴보았을때 안전, 신호, 보행자, 스마트폰, 스마트 시티와 같은 키워드가 많이 나온것을 확인할 수 있었다.

## 전체코드 및 마무리
<script src="https://gist.github.com/kshway/202aec9661d95e21b80cb1767e11a51f.js"></script>  
사실 위에 패키지를 다운받고 라이브러리를 사용하는 부분은 포스팅 하지 않았다. 아마 저중에 필요없는 애들도 있을 것이다 하하.. 그러니 알아서 .. 적당히만 .. 참고하시길 ㅎ 워드클라우드를 표출하기전에 데이터프레임을 엑셀파일로 저장하면 각 단어별 빈도수가 담긴 엑셀파일을 얻을 수 있다. 실제로 그 파일을 보면서 요인을 선정하기도 했으니 파일도 함께 받아서 보는 것을 추천 ! 워드클라우드는 시각화니까 ! 그럼 오늘은 여기까지 .. (퇴근 24분전 .. 벌써 기분 좋당)