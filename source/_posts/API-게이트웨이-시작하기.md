---
title: API 게이트웨이 시작하기
date: 2020-11-03 21:04:21
tags:
    - API Gateway
categories:
    - AWS
    - AWS Guide
thumbnail: /images/AWS/AWSGuide/API게이트웨이_시작하기/index.svg
---

![API Gateway](/images/AWS/AWSGuide/API게이트웨이_시작하기/index.svg)

- API 게이트웨이는 HTTP 프로토콜을 이용하여 API를 개발자가 손수비게 구축할 수 있는 완전 관리형 서비스

- 실습 요약
1. API 게이트웨이용 람다 함수 생성
    A. 람다 함수 생성(람다 함수 이름 : lambda_for_apigateway_get)
    B. 람다 함수 실행 역할 생성(람다 함수 역할 이름 : role_for_apigateway)
2. API 게이트웨이용 람다 이벤트 구성
3. 람다 함수 소스코드 작성
4. 다이나모 DB 서비스 실행 권한을 위한 IAM 생성
    A. 정책 생성 및 검토(정책 이름 : policy_dynamodb_crud)
    B. 역할 생성(역할 이름 : role_for_apigateway_get)
5. 다이나모 DB 생성
6. 람다 함수 수정
7. API Gateway 테스트 및 다이나모 DB GET 확인

---------------------------------------

![API Gateway](/images/AWS/AWSGuide/API게이트웨이_시작하기/apigatewayStart.jpg)

- AWS 콘솔에서 람다 함수를 선택하고, 함수 생성을 선택
- 함수 이름을 lambda_for_apigateway_get로 지정
- 권한은 AWS 정책 템플릿에서 새 역할 생성을 선택
- 역할 이름은 role_for_apigateway
- 정책 템플릿은 Lambda@Edge 선택


![API Gateway](/images/AWS/AWSGuide/API게이트웨이_시작하기/apigatewayStart1.jpg)

- 람다 함수 에디터 부분의 코드를 다음과 같이 입력

``` javascript
exports.handler = async (event) => {  
    const response =  {
        statusCode:200,
        body:JSON.stringify(event.queryStringParameters)

    }
    return response
};
```


![API Gateway](/images/AWS/AWSGuide/API게이트웨이_시작하기/apigatewayStart2.jpg)
![API Gateway](/images/AWS/AWSGuide/API게이트웨이_시작하기/apigatewayStart3.jpg)

- 추가 트리거를 선택한 후 API 게이트웨이를 선택
- API 생성을 선택한 후 보안을 열기로 선택


![API Gateway](/images/AWS/AWSGuide/API게이트웨이_시작하기/apigatewayStart4.jpg)

- 추기를 선택하면 다음과 같이 화면이 뜸


![API Gateway](/images/AWS/AWSGuide/API게이트웨이_시작하기/apigatewayStart5.jpg)

- API 게이트웨이가 추가되면 기존에는 없던 리소스 경로가 뜸
- API 엔드포인트라는 항목과 URL이 나타나는데 함수를 실행시키기 위해 접속해야 하는 주소


![API Gateway](/images/AWS/AWSGuide/API게이트웨이_시작하기/apigatewayStart7.jpg)

- ?"key"="value"&"key"="value" 형태로 URL 뒤에 붙혀서 URI를 만들어 준다
- 화면에 GET으로 보내준 파라매터가 뜨는 것을 확인할 수 있음

---------------------------------------

![API Gateway](/images/AWS/AWSGuide/API게이트웨이_시작하기/apigatewayStart8.jpg)

- 다이나모DB에 대한 권한을 주기 위해 권한에 들어감


![API Gateway](/images/AWS/AWSGuide/API게이트웨이_시작하기/apigatewayStart9.jpg)

- role_for_apigateway_get을 선택하면 다음과 같은 화면이 뜸
- 정책 연결을 선택


![API Gateway](/images/AWS/AWSGuide/API게이트웨이_시작하기/apigatewayStart10.jpg)

- 정책 생성을 선택


![API Gateway](/images/AWS/AWSGuide/API게이트웨이_시작하기/apigatewayStart11.jpg)

- 서비스는 DynamoDB, 리소스는 모든 리소스, 작업은 수동작업으로 모든 DynamoDB 작업을 선택
- 정책 검토를 선택


![API Gateway](/images/AWS/AWSGuide/API게이트웨이_시작하기/apigatewayStart12.jpg)

- 정책 이름으로 policy_dynamodb_crud를 입력
- 정책 생성을 선택


![API Gateway](/images/AWS/AWSGuide/API게이트웨이_시작하기/apigatewayStart17.jpg)

- 검색에서 이전에 만들었던 role_for_apigateway_get을 입력한 후 표시되는 역할을 선택
- 요약정보가 뜨며, 정책 연결을 선택
- 이전에 만든 정책 이름을 입력하여 검색
- 체크 박스 선택 후 하단 정책 연결 버튼을 선택
- policy_dynamodb_crud역할에 role_forapigateway 정책이 성공적으로 연결
- 이제 람다 함수가 다이나모 DB에 접근 가능함

---------------------------------------

![API Gateway](/images/AWS/AWSGuide/API게이트웨이_시작하기/apigatewayStart13.jpg)

- 다이나모DB에서 테이블 만들기를 선택하며, 테이블 이름은 dynamo_apigateway_query를 입력한 후, 기본 키는 id를 입력
- 테이블 생성

---------------------------------------

![API Gateway](/images/AWS/AWSGuide/API게이트웨이_시작하기/apigatewayStart14.jpg)

- 람다 함수로 돌아와서 함수 코드를 다음과 같이 입력

``` javascript
//aws-sdk를 불러옵니다.
const AWS = require('aws-sdk')
//다이나모디비 클라이언트를 초기화합니다.
const dynamodb = new AWS.DynamoDB.DocumentClient()

exports.handler = async (event) => {

    //리턴할 값을 선언합니다.
    let response

    //queryStringParameters즉 GET값들이 들어오는지 들어온다면 id가 있는지 체크합니다.
    if (!event.queryStringParameters || !event.queryStringParameters.id) {
        response = {
            statusCode: 400,
            body: JSON.stringify("id가 없습니다."),
        }
        return response
    } else {
        let params = {
            Item:{
                id:event.queryStringParameters.id,
                data: event.queryStringParameters
            },
            TableName: "dynamo_apigateway_query",
        }
        await dynamodb.put(params).promise().catch(e => {
            response = {
                statusCode: 500,
                body: JSON.stringify("에러가 발생하였습니다:" + e),
            }
            return response
        })

        response = {
            statusCode: 200,
            body: JSON.stringify("데이터가 성공적으로 저장되었습니다.."),
        }
        return response
    }
}
```

- queryStringParameter를 인자로 받아 다이나모 DB에 저장하고 GET에이터나 id 값이 없다면 400, 저장하는데 문제가 발생한다면 500, 성공적으로 데이터를 넣었다면 200을 반환


![API Gateway](/images/AWS/AWSGuide/API게이트웨이_시작하기/apigatewayStart15.jpg)

- 파라매터가 없는경우


![API Gateway](/images/AWS/AWSGuide/API게이트웨이_시작하기/apigatewayStart16.jpg)

- 파라매터를 정상적을 넣는 경우


![API Gateway](/images/AWS/AWSGuide/API게이트웨이_시작하기/apigatewayStart18.jpg)

- 다이나오 DB에 들어가보면 데이터가 정상적으로 들어가 있음을 확인할 수 있음

---------------------------------------





---------------------------------------





---------------------------------------





---------------------------------------





---------------------------------------




``` json
{
  "text": "hello world",
  "number": "+821012345678"
}
```