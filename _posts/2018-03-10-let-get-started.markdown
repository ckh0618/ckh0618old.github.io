---
published: true
title: Github Pages에서 블로그를 시작합니다. 
categories: Life
tags: [BLOG. SSL, Github Pages, jekyll, CloudFlare]
layout: post
excerpt: github page 에 둥지를 틀었습니다.  
comments: yes
toc: true
last_modified_at: 2018-03-09T03:30:00+09:00
--- 

## 들아가며 

github page 에 블로그를 만들어보았는데 상당히 간단합니다. Github Page 좋아보입니다. 뭐 Step By Step은 아니지만 걍 제가 어떻게 했는지 공유하면 나중에 페이지 만드는 사람도 좋을 것 같아서 공유 해봅니다.  

## 템플릿 찾기

요즘 도커에 빠져서 인터넷에 도커관련 자료를 찾으려 돌아다니는 도중에 [subricura](http://subicura.com)라는 분이 만든 블로그 템플릿을 보고 그대로 clone 받아서 꾸며봤습니다. 화면 레이아웃이나 기능들이 잘되어있어서요. 라이센스가 ISC 로 적혀있어서 그냥 가져다썼는데 허락 안받아도 되겠죠 ? 뭐 있는지도 모르시겠지만요. 

위 블로그 추천합니다. 특히 도커 관련 포스트는 매우 좋아요. 내공이 깊으신 거 같음. 

Clone 을 로컬에 아무데나 받고, 제가 수정한 내용은 다음과 같습니다. 
 
* 일단 로컬에 클론을 하고, 
* 파일을 열고 _post 디렉토리에 가서 기존의 post 들을 모두 지웠습니다 그리고 이글을 여기에 만들어서 넣었구요.
* assets 에 보면 각 포스트에 종속된 이미지들이나 asciimena등의 자료들이 있는데 이것도 지우고
* root 에 있는 config.yaml 을 여러서 제껄로 바꾸고, facebook app id도 새로 발급 받아서 댓글 시스템도 만들고요. 잘 모르는 것은 걍 주석 처리 했습니다.  
* 그외에 대문 이미지와 뭐 기타 이미지들을 제껄로 변경했고, 
* 물론 about.md 도 바꿔야죠. 현재는 쓸말이 없네요.  
* 그외에는 잘 몰라서 걍 뒀습니다. 

## jekyll 

이거 컨셉이 좀 햇걸렸는데 걍 간단합니다. 사용자가 쓴 이런 저런 설정 및 템플릿과 마크다운 문서를 사용하여 Static 한 웹페이지 (HTML/Javascript/CSS) 로 만들어주는 겁니다. 기존의 워드프레스나 네이버블로그, 티스토리같은거랑은 좀 개념이 다르죠. 전자는 Site-generator라면 후자는 블로그 프로그램이니까요.

Github Pages는 jekyll 을 기본 사이트 생성기로 사용하기 때문에 자동으로 jekyll 형식의 웹사이트를 Static하게 바꿔서 서비스 하기는 하는데, 그냥 jekyll을 도구로 사용하고 실제 Github Page 에는 Static 한 웹페이지를 올리는게 좋습니다. 이유는 다음 절에 나와요. 

## 배포하기 

저대로 그냥 써도 되기는 합니다만, toc 등의 다른 jekyll plugin 등을 사용하기 위해서는 별도의 브랜치나 다른 Repository 에 코드를 가져다가 놓고, Page 의 master branch 로 완전한 Static HTML 형태로 배포하는 것이 좋습니다. package.json 의 저장소를 바꾸고 다음과 같이 사용하면 됩니다.

```
$ npm run deploy
```

왜 이렇게 해야하냐면 Github Page 의 정책으로 인해서 Jekyll Plugin 중 몇개만 뺴고는 모두 막아버리기 때문입니다. 저렇게 하면 master 을 다 밀어버리고 그 안의 내용을 _site 디렉토리로 바꿔버립니다. 보이기는 똑같지만 jekyll 로 사이트 생성을 우리가 하는 거니까 Github Page 에서 지원하지 않는 Jekyll Plugin 을 마음대로 사용할 수 있습니다.  

저는 jekyll_toc 가 멋져보이더군요. 그래서 한 반나절 삽질했던거 같습니다. 

굳이 블로그용으로 저장소 두개 만드는 것이 좀 그래서 걍 code branch를 만들고 거기에는 블로그를 만들어내는 코드 ( 위에서 수정한 코드 베이스) 를 두고 master 는 배포용도로 세팅 했습니다. 

## 개인 도메인 연결 

예전에 사놨지만 까먹고 있었던 mo0om.com 이라는 도메인을 github page 내용을 보고 연결해놨습니다. 

* [Using a custom domain with github-pages](https://help.github.com/articles/using-a-custom-domain-with-github-pages/) 

내용은 별게 없습니다. 도메인 네임서버를 설정하고, CNAME 파일을 page repository 에 넣으면 됩니다. 위에서 설정한건 아래 CloudFlare 설정에서 자동으로 읽어옵니다. 

## SSL 

SSL 이 뭐 그리 필요할까 싶었지만 걍 뽀대나보이기 때문에 다음 내용을 보고 따라했습니다. 

* [How to serve a custom https domain on github page with cloudflare](https://gist.github.com/cvan/8630f847f579f90e0c014dc5199c337b)

CloudFare 서비스를 이용하여 쓰는 방식인데 쉬워요. domain의 dns 를 Cloud쪽으로 바꾸고 몇가지 설정을 하면 됩니다. 위 페이지에 잘 설명되어있습니다.  
이제 끝난 거 같습니다. 글을 쓰고 publishing 합니다. 

## Google Analytics

역시 오바기는 하기만 Google Analytics 도 가입했습니다. 가입하고 나온 id 를 _config.yaml에 넣어주면 됩니다. 딱 보면 알껍니다. 이름이 워낙 직관적이라. 

## 이걸 시작하는 이유

블로그야 뭐 서비스 형도 있고, 설치형도 있고 그런데 이게 좋아보이는 이유는,

* Markdown 을 사용할 수 있는 것은 개인적으로 매우 큰 장점입니다. 
* 귀찮기는 하지만 CSS 등을 직접 바꿔서 Customizing 을 할 수 있는 것도 좋고 
* 이왕 Code Repository 로 github 을 쓰고 있으니 이걸 묶는게 좋아보이기도 하고 
* 웬지 github 에 포스팅하면 있어보이는 개발자로 보이기도 하니까요.

## 마치며 

익숙하지는 않은 환경이라 완벽하게 돌아가는 코드 베이스가 있음에도 개념등을 찾아보느라 하루 반정도 삽질했네요. 뭘 적을 수 있을지는 모르겠지만 뭐 고민해보죠. 