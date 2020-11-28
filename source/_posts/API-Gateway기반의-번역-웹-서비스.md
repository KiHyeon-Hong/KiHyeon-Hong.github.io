---
title: API Gateway기반의 번역 웹 서비스
date: 2020-11-10 14:53:16
tags:
    - API Gateway
categories:
    - AWS
    - AWS Guide
thumbnail: /images/AWS/AWSGuide/API게이트웨이_응용하기/index.svg
---

![API Gateway](/images/AWS/AWSGuide/API게이트웨이_응용하기/index.svg)

- 실습 요약

1. 번역 API 게이트웨이용 람다 함수 생성
    A. 람다 함수 생성(람다 함수 이름 : lambda_for_translate_Service)
    B. 람다 함수 실행 역할 생성(람다 역할 이름 : role_for_translate_Service)
2. 람다 함수 소스코드 작성
3. 람다 함수 역할 수정
    A. 정책 생성 및 검토(정책 이름 : role_for_translate_Service)
    B. 기존 관리형 정책 선택(TranslateFullAccess)
4. 람다 API 게이트웨이 설정
5. index.html 파일 수정 및 S3버킷 생성
6. S3 버킷에 수정 파일 업로드
7. 번역 서비스 정적 웹 사이트 설정 및 테스트

---------------------------------------

![API Gateway](/images/AWS/AWSGuide/API게이트웨이_응용하기/APIGatewayDetail.jpg)

- API 게이트웨이용 람다 함수를 생성한다
- 함수 이름에 lambda_for_translate_Service를 입력한다
- AWS 정책 템플릿에서 새 역할 생성 선택
- role_for_translate_Service 입력
- 정책 템플릿은 '기본 Lambda@Edge' 입력


![API Gateway](/images/AWS/AWSGuide/API게이트웨이_응용하기/APIGatewayDetail1.jpg)

- 람다 함수 코드는 다음과 같이 작성

``` javascript
/* AWS SDK 를 가져옵니다.*/
var AWS = require('aws-sdk');

AWS.config.update({region: 'us-east-1'});

var translate = new AWS.Translate();
exports.handler = function(event, context,callback){

console.log(JSON.stringify(event.body));

const response = JSON.parse(event.body)

  //event.body로 POST로 받은 데이터를 받습니다.
  try{
    const translateParams = {
    SourceLanguageCode: 'ko',
    TargetLanguageCode: 'en',
    Text: response.text
  }

  //translate SDK를 불러옵니다.
  translate.translateText(translateParams, function (err, data) {
    if (err) callback(err)
    callback(null,{
        statusCode:200,
        headers: {
        "Access-Control-Allow-Origin" : "*", //S3에서 요청을 할 수 있도록 허용해줍니다.
        "Access-Control-Allow-Credentials" : true
        },
        body:data.TranslatedText
    })
  })
  }catch(e){
    callback(null,{
      statusCode:200,
      body:JSON.stringify(e)
    })
  }


};
```


![API Gateway](/images/AWS/AWSGuide/API게이트웨이_응용하기/APIGatewayDetail2.jpg)

- 람다 함수 역할을 수정
- 권한의 실행 역할에서 role_for_translate_Service 선택


![API Gateway](/images/AWS/AWSGuide/API게이트웨이_응용하기/APIGatewayDetail3.jpg)

- 기존에 있는 권한인 TranslateFullAccess를 선택
- 새롭게 translate로의 접근 권한이 생김

---------------------------------------

![API Gateway](/images/AWS/AWSGuide/API게이트웨이_응용하기/APIGatewayDetail4.jpg)

- 추가 트리거를 통하여 API 게이트웨이를 연간함
- API 생성을 선택
- 보안은 열기로 설정


![API Gateway](/images/AWS/AWSGuide/API게이트웨이_응용하기/APIGatewayDetail5.jpg)

- API 엔드포인트를 보면 주소가 생김
- 해당 주소가 홈페이지를 통해 서비스르 만들어 요청할 주소

---------------------------------------

![API Gateway](/images/AWS/AWSGuide/API게이트웨이_응용하기/APIGatewayDetail6.jpg)

- 웹 페이지를 다음과 같이 생성

``` html
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <title>번역웹사이트</title>
    <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.2.1/css/bootstrap.min.css">
</head>
<body>

<div class="container">
    <div class="row">
        <div class="col align-self-center">
            <h5 id="resultText"></h5>
            <form action="javascript:void(0)">
                <div class="form-group">
                    <label>텍스트를 입력해주세요.</label>
                    <textarea class="form-control" id="textInput" rows="3"></textarea>
                </div>
                <button onclick="sendReqeust()" class="btn btn-primary">Submit</button>
            </form>
        </div>
    </div>
</div>

</body>
<script type="text/javascript">
    var inputSelector = document.querySelector('#textInput');
    var resultText = document.querySelector('#resultText');

    function sendReqeust() {
        resultText.innerHTML = "로딩중..."
        fetch("https://0pmjclpe95.execute-api.ap-northeast-2.amazonaws.com/default/lambda_for_translate_service", {
            method: "POST",
            body: JSON.stringify({
              text:inputSelector.value
            })
        }).then(function (response) {
          return response.text().then(function(text) {
                resultText.innerHTML = text;
          });
        })
    }
</script>
</html>
```

- 이때, fetch 안에 아까 람다 함수의 API 게이트웨이 엔드포인트를 복사한다.


![API Gateway](/images/AWS/AWSGuide/API게이트웨이_응용하기/APIGatewayDetail7.jpg)

- S3 버킷을 생성
- 버킷 이름은 전세계에서 유일한 이름으로 설정해야 함


![API Gateway](/images/AWS/AWSGuide/API게이트웨이_응용하기/APIGatewayDetail8.jpg)

- 이때, 퍼블릭 액세스 차단을 위한 버킷 설정에서 모든 퍼블릭 액세스 차단을 해제하고, 경고문을 체크


![API Gateway](/images/AWS/AWSGuide/API게이트웨이_응용하기/APIGatewayDetail9.jpg)

- 해당 버킷에 방금 생성한 index.html 파일을 업로드 한다.


![API Gateway](/images/AWS/AWSGuide/API게이트웨이_응용하기/APIGatewayDetail10.jpg)

- 이때 중요한점은 해당 index.html 파일을 퍼블릭으로 설정해야 함

---------------------------------------

![API Gateway](/images/AWS/AWSGuide/API게이트웨이_응용하기/APIGatewayDetail11.jpg)

- 정적 웹 사이트 호스팅 편집에서 활성화를 선택
- 인덱스 문서로 index.html, 파일은 없지만 오류 문서로 error.html을 입력


![API Gateway](/images/AWS/AWSGuide/API게이트웨이_응용하기/APIGatewayDetail12.jpg)

- 버킷 웹 사이트 엔드포인트가 뜨는데, 이것이 바로 번역 사이트의 엔드 포인트


![API Gateway](/images/AWS/AWSGuide/API게이트웨이_응용하기/APIGatewayDetail13.jpg)

- 사이트 접속 시 해당 화면이 나오는 것을 볼 수 있음


![API Gateway](/images/AWS/AWSGuide/API게이트웨이_응용하기/APIGatewayDetail14.jpg)

- 텍스트 입력 후 submit 선택 시 번역이 작동되는 것을 확인
---------------------------------------
