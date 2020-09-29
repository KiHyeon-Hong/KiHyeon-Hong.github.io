---
title: Lightsail 시작하기
date: 2020-09-29 22:25:54
tags:
    - lightsail
categories:
    - AWS
    - AWS Guide
thumbnail: /images/AWS/AWSGuide/lightsail_시작하기/index.svg
---

![lightsail](/images/AWS/AWSGuide/lightsail_시작하기/index.svg)

 lightsail은 플랫폼과 시작 이미지를 선택하는 것만으로 인스턴스를 바로 시작할 수 있다.

 ====================

 - 웹 사이트 블로그
 - 단순 앱
 - 개발 및 테스트 환경
 - 소수의 서버로 구성된 비즈니스 소프트웨어

====================

wordpress blig를 구축한다.

====================

구축 과정
1. AWS Lightsail 접속
2. Lightsail 인스턴스 생성
3. Lightsail 인스턴스 확인
4. wordpress 사용자 설정
5. wordpress 관리자 설정
    A. 사용자명, 패스워드 설정을 위한 원격 서버 접속
    B. Bitnami(SSH) 접속을 통한 아이디 패스워드 생성/확인
    C. wordpress 관리자 페이지 접속/확인

====================

- AWS console에서 lightsail 접속

![](/images/AWS/AWSGuide/lightsail_시작하기/lightsailStart.jpg)

 인스턴스 생성을 누른다.

![](/images/AWS/AWSGuide/lightsail_시작하기/lightsailStart1.jpg)

- 첫번째는 인스턴스 이미지를 선택하는 과정이다.
- 플랫폼과 블루푸린트 메뉴가 존재한다.
- 플랫폼은 Linux/Unix를 선택하며, 블루프린트는 Wordpress를 선택한다.

![](/images/AWS/AWSGuide/lightsail_시작하기/lightsailStart2.jpg)

- 시작 스크립트는 맨 처음 서버가 구성될 때 실행되는 스크립트이며, 사용자에 따라 필수적으로 설치해야 하는 소프트웨어가 있을 경우 선택한다.
- SSH 키 페어는 원격 서버 접속을 위해 사용한다.

![](/images/AWS/AWSGuide/lightsail_시작하기/lightsailStart3.jpg)

- 인스턴스 플랜은 프리티어 가입 후 첫 달 무료 서비스를 이용하기 위해 가장 저렴한 플랜을 선택한다.
- 인스턴스 확인에서 고유한 이름을 선택해야하며, 인스턴스 수를 늘릴경우 추가 요금이 발생할 수 있다.

====================

![](/images/AWS/AWSGuide/lightsail_시작하기/lightsailStart4.jpg)

- 처음 인스턴스를 생성하면 실행 중 메시지가 뜨며, IP 주소가 할당된다.

![](/images/AWS/AWSGuide/lightsail_시작하기/lightsailStart5.jpg)

- 해당 IP 주소를 웹 브라우저에 입력하면 기본으로 생성되는 포스트와 블로그 레이아웃을 볼 수 있다.

![](/images/AWS/AWSGuide/lightsail_시작하기/lightsailStart6.jpg)

- 관리자 페이지는 퍼블릭IP/wp-damin 이다.
- 관리자 대시보드를 이용하기 위해서는 유저명과 비밀번호를 입력해야 한다.

![](/images/AWS/AWSGuide/lightsail_시작하기/lightsailStart7.jpg)

- lightsail 대시보드에 돌아가 이름 옆의 콘솔 창을 클릭한다.

![](/images/AWS/AWSGuide/lightsail_시작하기/lightsailStart8.jpg)

- CLI console창이 뜬다.

``` bash
$ cat bitnami_credentials
```

- 명령어를 입력하면 default userName과 password를 출력한다.
- bitnami 관리자 정보는 잘 보관되어야 한다.

![](/images/AWS/AWSGuide/lightsail_시작하기/lightsailStart9.jpg)

- 관리자 정보를 통하여 관리자 대시보드로 접속 가능하다.

![](/images/AWS/AWSGuide/lightsail_시작하기/lightsailStart10.jpg)

- Setting의 General에서 Site Language를 한국어로 바꾸고 저장한다.

====================

- wordpress와 polly
1. wordpress 플러그인 설정
2. lightsail 기반 wordpress 사용을 위한 IAM 설정
    A. 정책 설정
    B. 정책 샐성 및 검토
    C. 사용자 설정
    D. 사용자 생성
    E. 기존 정책과 사용자 연결
    F. 사용자 키 보관
3. wordpress 플러그인 설정 및 사용

====================

![](/images/AWS/AWSGuide/lightsail_시작하기/lightsailStart11.jpg)

- 플러그인을 보면 AWS for WordPress가 보인다.
- 활성화를 클릭한다.

![](/images/AWS/AWSGuide/lightsail_시작하기/lightsailStart12.jpg)

- 활성화를 하면 AWS라는 매뉴가 나타난다.
- 클릭하면 AWS access key와 AWS secret key를 입력하라는 메시지가 출력된다.
- 이를 이용하기 위해서는 IAM이 필요하다.

====================

![](/images/AWS/AWSGuide/lightsail_시작하기/lightsailStart13.jpg)

- IAM의 대시보드를 들어가면 액세스 관리에 사용자와 정책이 있다.

![](/images/AWS/AWSGuide/lightsail_시작하기/lightsailStart14.jpg)

- 정책 생성을 클릭한 후 시각적 편집기가 아닌 JSON을 클릭한다.

``` json
{
	"Version": "2012-10-17",
	"Statement": [
		{
			"Sid": "Permissions1",
			"Effect": "Allow",
			"Action": [
				"s3:HeadBucket",
				"polly:SynthesizeSpeech",
				"polly:DescribeVoices",
				"translate:TranslateText"
			],
			"Resource": "*"
		},
		{
			"Sid": "Permissions2",
			"Effect": "Allow",
			"Action": [
				"s3:ListBucket",
				"s3:GetBucketAcl",
				"s3:GetBucketPolicy",
				"s3:PutObject",
				"s3:DeleteObject",
				"s3:CreateBucket",
				"s3:PutObjectAcl"
			],
			"Resource": [
				"arn:aws:s3:::audio_for_wordpress*",
				"arn:aws:s3:::audio-for-wordpress*"
			]
		}
	]
}
```

- 해당 json 파일을 복사 & 붙여넣기 한다.

![](/images/AWS/AWSGuide/lightsail_시작하기/lightsailStart15.jpg)

- 정책 검토를 하면 다음과 같은 화면이 나온다.
- 정책 명을 policy_for_wordpress_polly으로 입력한 후 정책 생성을 클릭한다.

====================

![](/images/AWS/AWSGuide/lightsail_시작하기/lightsailStart16.jpg)

- 정책이 생성되었으면, 사용자에서 사용자 추가를 클릭한다.
- 사용자 이름은 userForWordpress로 하며, 액세스 유형은 일반 사용자가 아닌 워드프레스라는 애플리케이션을 통해 AWS에 서비스 접근하기 때문에 '프로그래밍 방식 액세스'를 선택한다.

![](/images/AWS/AWSGuide/lightsail_시작하기/lightsailStart17.jpg)

- 기존 정책 직접 연결을 통하여 방금 생성한 정책을 선택한다.

![](/images/AWS/AWSGuide/lightsail_시작하기/lightsailStart18.jpg)

- 태그 화면은 별도의 설정을 하지 않고 넘어간다.
- 이상이 없다면 사용자 만들기를 클릭한다.

![](/images/AWS/AWSGuide/lightsail_시작하기/lightsailStart19.jpg)

- 프로그래밍 액세스 방식은 액세스 키와 비밀 액세스 키가 발급된다.
- 키는 .csv 파일로 보관할 수 있다.

====================

![](/images/AWS/AWSGuide/lightsail_시작하기/lightsailStart20.jpg)

- 앞서 생성한 키를 입력한다.
- 변경 사항 저장을 클릭한다.

![](/images/AWS/AWSGuide/lightsail_시작하기/lightsailStart21.jpg)

- Text to Speech에서 source language를 한국어로 선택하며, Enable text-to-speech support를 체크한다.

====================

![](/images/AWS/AWSGuide/lightsail_시작하기/lightsailStart22.jpg)

- 글을 클릭한 후 새로 추가를 선택한다.

![](/images/AWS/AWSGuide/lightsail_시작하기/lightsailStart23.jpg)

- 제목과 내용을 입력한 후 밑에 있는 enable Text-to-speech에 체크 박스를 선택한다.
- 우측 상단의 공개 버튼을 클릭한다.

![](/images/AWS/AWSGuide/lightsail_시작하기/lightsailStart24.jpg)

- 다음과 같이 포스트된 글을 확인할 수 있으며, 재생 버튼을 통하여 polly 서비스를 확인할 수 있다.

![](/images/AWS/AWSGuide/lightsail_시작하기/lightsailStart25.jpg)

- 다운로드도 가능하다.

====================

![](/images/AWS/AWSGuide/lightsail_시작하기/lightsailStart26.jpg)

- 첫 한달 간은 무료로 사용이 가능하지만, 그 이후로는 매달 $3.5의 금액이 나가므로, 실습 후 인스턴스를 삭제한다.

====================
