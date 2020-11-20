---
title: Lambda 시작하기
date: 2020-10-27 20:17:10
tags:
    - Lambda
categories:
    - AWS
    - AWS Guide
thumbnail: /images/AWS/AWSGuide/Lambda_시작하기/messageService/index.svg
---

![lightsail](/images/AWS/AWSGuide/Lambda_시작하기/messageService/index.svg)

AWS Lambda 함수 기반 문자 알림 서비스를 구축해본다.

구축 과정
1. SNS 서비스 실행 권한을 위한 IAM 정책 설정
    A. 정책 생성 및 검토(정책 이름 policy_for_publising_SNS)
    B. 역할 생성(역할 이름 role_for_sns_sending)
2. SNS 람다 함수 만들기
    A. 람다 함수 생성(람다 함수 이름 : lambda_for_sns)
    B. 람다 함수 실행 역할 생성(람다 역할 이름 : role_for_sns_sending)
3. SNS 람다 이벤트 구성
    A. 람다 함수 이벤트 이름(eventForSendingsSNS)
    B. 람다 함수 소스코드 작성
4. SNS 람다 함수 테스트

---------------------------------------

![lightsail](/images/AWS/AWSGuide/Lambda_시작하기/messageService/lambdaStart.jpg)

- AIM 대시 보드에서 죄측 정책 메뉴를 클릭한 후 정책 생성을 선탣한다.
- 서비스 선택에서 SNS 입력 후 필터링 된 SNS를 선택한다.
- 문자 보내기는 사용자에게 알림 서비스를 생성하는 것이므로 액세스 레벨 "쓰기"에 해당한다.


![lightsail](/images/AWS/AWSGuide/Lambda_시작하기/messageService/lambdaStart1.jpg)

- 리소스는 모든 리소스를 선택한다.
- 요청 조건은 기본 설정으로 하고 정책 검토를 클릭한다.


![lightsail](/images/AWS/AWSGuide/Lambda_시작하기/messageService/lambdaStart2.jpg)

- 정책 이름은 policy_for_publising_SNS로 한 후 정책 생성을 클릭한다.


---------------------------------------

![lightsail](/images/AWS/AWSGuide/Lambda_시작하기/messageService/lambdaStart3.jpg)

- 정책과 연결해 줄 역할을 만든다.
- 역할 만들기를 선택한다.
- 신뢰할 수 있는 유형의 개체로 AWS 서비스를 선택한다.
- 사용 사례 선택은 Lambda를 선택한다.


![lightsail](/images/AWS/AWSGuide/Lambda_시작하기/messageService/lambdaStart4.jpg)

- 권한 정책 연결에서 policy_for_publising_SNS를 선택한다.
- 태그는 없으므로 넘어간다.


![lightsail](/images/AWS/AWSGuide/Lambda_시작하기/messageService/lambdaStart5.jpg)

- 역할 이름에 role_for_sns_sending를 입력하고 역할 만들기를 선택한다.

---------------------------------------

![lightsail](/images/AWS/AWSGuide/Lambda_시작하기/messageService/lambdaStart6.jpg)

- AWS console에서 Lambda를 클릭한다.
- 람다 함수 대시보드에서 함수 생성을 선택한다.


![lightsail](/images/AWS/AWSGuide/Lambda_시작하기/messageService/lambdaStart7.jpg)

- 함수 이름은 lambda_for_sns로 입력한다.
- 권한은 기존 역할 사용을 선택하며, role_for_sns_sending을 선택한다.
- 함수 행성을 선택한다.

---------------------------------------

![lightsail](/images/AWS/AWSGuide/Lambda_시작하기/messageService/lambdaStart8.jpg)

- 성공적으로 함수가 만들어진 후 이벤트 선택에서 테스트 이벤트 구성을 선택한다.
- 이벤트 이름은 eventForSendingsSns로 입력한 후, 코드는 다음과 같이 입력한다.

``` json
{
  "text": "hello world",
  "number": "+821012345678"
}
```


![lightsail](/images/AWS/AWSGuide/Lambda_시작하기/messageService/lambdaStart9.jpg)

- 함수 코드에는 다음과 같이 입력한다.

``` javascript
//AWS를 실행시키기위한 라이브러리를 가져옵니다.
const AWS = require('aws-sdk');

//이전과 다른부분이 있다면 context와 callback을 파라미터로 받습니다.
//context에서는 현재 실행중인 람다의 메타정보를 받고
//callback은 람다가 끝나는 시점 호출합니다.
exports.handler = (event, context, callback) => {

	  //위에 입력했던 json값이 event 즉 input으로 들어옵니다.
	  //params에 Message와 PhonNumber 변수를 선언합니다.
    const params = {
        Message: event.text,
        PhoneNumber: event.number
    };

    // SNS SDK를 가져옵니다.
    // SNS서비스에서 메세지를 보내는것은 한정된 리전에서만 사용할 수 있기때문에
    // region을 도쿄리전으로 설정해주어야합니다. 이를 위해 인자값으로
    // region에 도쿄리전의 식별자인 'ap-northeast-1'을 입력합니다.
    const publishTextPromise = new AWS.SNS({ apiVersion: '2010-03-31',region: 'ap-northeast-1'}).publish(params).promise();

    // SDK를 실행합니다.
    publishTextPromise.then(
        function(data) {
            //메세지가 있다면 첫번째에 null, 두번째에 메세지를 리턴합니다.
            callback(null,"MessageID is " + data.MessageId);
        }).catch(
        function(err) {
			  //에러가 있다면 err를 리턴합니다.
            callback(err);
    });
};
``` 

---------------------------------------

![lightsail](/images/AWS/AWSGuide/Lambda_시작하기/messageService/lambdaStart10.jpg)

- 오른쪽 상단의 테스트 버튼을 클릭하면 함수가 실행된다.


![lightsail](/images/AWS/AWSGuide/Lambda_시작하기/messageService/lambdaStart11.jpg)

- 입력한 텍스트가 문자로 온 것을 확인할 수 있다

---------------------------------------
