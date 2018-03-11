---
published: true
title: 블로그를 시작합니다. 
categories: Life
tags: [BLOG]
layout: post
excerpt: github page 에 둥지를 틀어옴.  
comments: yes
toc: true
last_modified_at: 2018-03-09T03:30:00+09:00
--- 

## 들아가며 

github page 에 블로그를 만들어보았는데 상당히 간단합니다. github 잘만들었네요. 

## 템플릿 찾기

요즘 도커에 빠져서 인터넷에 도커관련 자료를 찾으려 돌아다니는 도중에 [subricura](http://subicura.com)라는 분이 만든 블로그 템플릿을 보고 그대로 clone 받아서 꾸며봤습니다. 화면 레이아웃이나 기능들이 잘되어있어서요. (도커 입문 포스트로 최고라고 생각합니다.) 

제가 수정한 내용은 다음과 같습니다. 
 
* 일단 로컬에 클론을 하고, 
* 파일을 열고 _post 디렉토리에 가서 기존의 post 들을 모두 지웠습니다 그리고 이글을 여기에 만들어서 넣었구요.
* assets 에 보면 각 포스트에 종속된 이미지들이나 asciimena등의 자료들이 있는데 이것도 지우고
* root 에 있는 config.yaml 을 여러서 제껄로 바꾸고, facebook app id도 새로 발급 받아서 댓글 시스템도 만들고요. 잘 모르는 것은 걍 주석 처리 했습니다.  
* 그외에 대문 이미지와 뭐 기타 이미지들을 제껄로 변경했고, 
* 물론 about.md 도 바꿔야죠 
* 그외에는 잘 몰라서 걍 뒀습니다. 
 
## 개인 도메인 연결 

예전에 사놨지만 까먹고 있었던 mo0om.com 이라는 도메인을 github page 내용을 보고 연결해놨습니다. 

* [Using a custom domain with github-pages](https://help.github.com/articles/using-a-custom-domain-with-github-pages/) 

내용은 별게 없습니다. 도메인 네임서버를 설정하고, CNAME 파일을 page repository 에 넣으면 됩니다. 위에서 설정한건 아래 CloudFlare 설정에서 자동으로 읽어옵니다. 

## SSL 

SSL 이 뭐 그리 필요할까 싶었지만 걍 뽀대나보이기 때문에 다음 내용을 보고 따라했습니다. 

* [How to serve a custom https domain on github page with cloudflare](https://gist.github.com/cvan/8630f847f579f90e0c014dc5199c337b)

CloudFare 서비스를 이용하여 쓰는 방식인데 쉬워요. domain의 dns 를 Cloud쪽으로 바꾸고 몇가지 설정을 하면 됩니다. 위 페이지에 잘 설명되어있습니다.  
이제 끝난 거 같습니다. 글을 쓰고 publishing 합니다. 

## 이걸 시작하는 이유

github page 에 블로그를 만드는 이유는 다음과 같습니다. 

* Markdown : 너무 너무 좋아요. 
* 서비스형 : 뭔가 내 지적재산권을 훔쳐가는 느낌 
* 설치형 : 돈도 많이 들고 관리하기 까다로움. 
* 여러가지 플러그인과 기능이 풍부함 

또 뭐 얼마나 갈지는 모르겠지만 지금은 이게 대세인거 같네요. 
