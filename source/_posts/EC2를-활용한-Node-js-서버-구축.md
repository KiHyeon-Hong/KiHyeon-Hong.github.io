---
title: EC2를 활용한 Node.js 서버 구축
date: 2020-11-30 23:20:24
tags:
    - EC22
categories:
    - AWS
    - AWS Guide
thumbnail: /images/AWS/EC2Project/Setting/index.svg
---

![EC2](/images/AWS/EC2Project/Setting/index.svg)

실습 요약
1. EC2, Ubuntu를 이용하여 인스턴스를 생성한다
2. Node.js를 설치하며, MariaDB를 설치한다.
3. 간단한 예제를 통하여 환경을 테스트 한다.

---------------------------------------

![EC2](/images/AWS/EC2Project/Setting/EC2Setting.jpg)

- EC2의 새 인스턴스 생성을 통하여 다음과 같은 화면이 나온다.
- Ubuntu Server 20.04 LTS를 선택한다.


![EC2](/images/AWS/EC2Project/Setting/EC2Setting1.jpg)

- 인스턴스 유형은 프리티어이므로 t2.micro를 선택한다.


![EC2](/images/AWS/EC2Project/Setting/EC2Setting2.jpg)

- 인스턴스 세부 정보 구성은 건드리지 않는다.


![EC2](/images/AWS/EC2Project/Setting/EC2Setting3.jpg)

- 스토리지 추가에서도 아무것도 선택하지 않는다.


![EC2](/images/AWS/EC2Project/Setting/EC2Setting4.jpg)

- 태그 추가에서는 키에 Name을 입력하고 값에 TestServer를 입력한다.
- 이는 추후 인스턴스의 이름이 된다.


![EC2](/images/AWS/EC2Project/Setting/EC2Setting5.jpg)

- 보안 그룹 구성에서는 기본으로 SSH가 있을 것이다.
- HTTP와 HTTPS를 추가하며 소스는 모두가 접근할 수 있도록 0.0.0.0/0을 선택한다.


![EC2](/images/AWS/EC2Project/Setting/EC2Setting6.jpg)

- 인스턴스 시작 검토는 현재까지 설정한 정보를 볼 수 있다.
- 문제가 없다면 시작하기를 선택한다.


![EC2](/images/AWS/EC2Project/Setting/EC2Setting7.jpg)

- 키를 발급 받는다.
- 기존에 가지고 있는 키를 선택해도 되며, 새로운 키를 발급 받아도 된다. 해당 키를 재발급이 불가능 하므로 반드시 저장을 해야한다.


---------------------------------------

![EC2](/images/AWS/EC2Project/Setting/EC2Setting8.jpg)

- 인스턴스가 실행되면 연결 버튼을 통하여 다음의 정보를 얻을 수 있다.
- 이때 ec2-3-87-0-78.compute-1.amazonaws.com는 우리의 인스턴스의 퍼블릭 Ip의 역할을 한다.


![EC2](/images/AWS/EC2Project/Setting/EC2Setting9.jpg)

- 인스턴스 요약을 통하여 5.87.0.78의 ip도 얻을 수 있는데, 이는 위의 ec2-3-87-0-78.compute-1.amazonaws.com와 같다.


---------------------------------------

![EC2](/images/AWS/EC2Project/Setting/EC2Setting10.jpg)

- Putty를 통해 접속을 시도한다.
- 이때 Connection -> SSH -> Auth에 방금 발급 받은 키를 넣어줘야 한다.
- 주의할 점은 putty에서는 발급받은 pem이 아니라 ppk로 변환을 해줘야 하는데, 이 부분은 추후에 포스팅 한다.


![EC2](/images/AWS/EC2Project/Setting/EC2Setting11.jpg)

- Putty의 Session에 Host Name에 방금 얻은 ec2-3-87-0-78.compute-1.amazonaws.com를 입력한다.
- 또한, ubuntu 계정으로 접근을 위해 앞에 ubuntu@를 붙인다.

``` text
ubuntu@ec2-3-87-0-78.compute-1.amazonaws.com
``` 


---------------------------------------

![EC2](/images/AWS/EC2Project/Setting/EC2Setting12.jpg)

- 연결을 누르게 되면 ubuntu 계정으로 로그인된 것을 볼 수 있다.
- Node.js 설치와 MariaDB는 ec2의 ubuntu에서 좀 다르게 설치된다.


---------------------------------------

Node.js 설치법

![EC2](/images/AWS/EC2Project/Setting/EC2Setting13.jpg)

- https://docs.aws.amazon.com/ko_kr/sdk-for-javascript/v2/developer-guide/setting-up-node-on-ec2-instance.html
- 노드의 설치 방법은 위 사이트에 나와있다.
- 아래의 코드를 한줄씩 입력한다.

``` bash
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.34.0/install.sh | bash

. ~/.nvm/nvm.sh

nvm install node

node -e "console.log('Running Node.js ' + process.version)"
``` 

- 위의 명령어를 통해 Node.js가 설치되고, 버전을 확인할 수 있다.


![EC2](/images/AWS/EC2Project/Setting/EC2Setting14.jpg)

- Node.js가 설치된 후 다음의 명령어를 통해 예제 코드 실행을 위한 모듈을 설치한다.

``` bash
npm install ejs
npm install jade
np install express
npm install cookie-parser
npm install body-parser
npm install express-session
npm install mysql
npm install sync-mysql
``` 

- 한줄씩 입력하여 모듈을 설치한다.


MariaDB 설치
![EC2](/images/AWS/EC2Project/Setting/EC2Setting15.jpg)

- https://downloads.mariadb.org/mariadb/repositories
- MariaDB를 설치하는 코드는 다음과 같다.

``` bash
sudo apt-get install software-properties-common

sudo apt-key adv --fetch-keys 'https://mariadb.org/mariadb_release_signing_key.asc'

sudo add-apt-repository 'deb [arch=amd64,arm64,ppc64el] https://ftp.harukasan.org/mariadb/repo/10.5/ubuntu focal main'

sudo apt update

sudo apt install mariadb-server

mysql -V
``` 

- 위 코드를 통해 MariaDB가 설치되고 버전을 확인할 수 있다.


![EC2](/images/AWS/EC2Project/Setting/EC2Setting16.jpg)

- 접속을 위해 MariaDB의 환경 설정을 해야한다.

``` bash
cd /etc/mysql/mariadb.conf.d

sudo cp 50-server.cnf server.cnf.backup

sudo vi 50-server.cnf
``` 


![EC2](/images/AWS/EC2Project/Setting/EC2Setting17.jpg)

- bind-addess = 127.0.0.1을 주석 처리한다.
- bind-addess = 127.0.0.1  ->  #bind-addess = 127.0.0.1


![EC2](/images/AWS/EC2Project/Setting/EC2Setting18.jpg)

- mariaDB를 재시작 시키며, 접속을 위한 비밀번호를 설정

``` bash
sudo systemctl restart mariadb.service

sudo mysqladmin -u root password 'gachon654321'
``` 


![EC2](/images/AWS/EC2Project/Setting/DBSetting.jpg)

``` text
sudo mysql -u root -p
Enter password: *
set password for root@localhost = password('gachon654321');
use mysql;
flush privileges;
exit

mysql -u root -p
gachon654321
``` 

- 예제 페이지를 위해 테이블을 하나 만든다
``` SQL
create database mydb;
create table member ( name varchar(10), uid varchar(10), pass varchar(10));
``` 


---------------------------------------

![EC2](/images/AWS/EC2Project/Setting/EC2Setting19.jpg)

- Atom으로 접속을 한다.
- 이때 자세한 환경 세팅은 다음 포스팅에서 실시한다.
- ftp-remote을 통하여 .ftpconfig 파일을 다음과 같이 작성한다.

``` JSON
{
    "protocol": "sftp",
    "host": "ec2-3-87-0-78.compute-1.amazonaws.com",
    "port": 22,
    "user": "ubuntu",
    "promptForPass": false,
    "remote": "/home/ubuntu",
    "local": "",
    "agent": "",
    "privatekey": "C:/users/ghdrl/Desktop/MyFiles/AwsKey/aws_password.pem",
    "passphrase": "",
    "hosthash": "",
    "ignorehost": true,
    "connTimeout": 10000,
    "keepalive": 10000,
    "keyboardInteractive": false,
    "keyboardInteractiveForPass": false,
    "remoteCommand": "",
    "remoteShell": "",
    "watch": [],
    "watchTimeout": 500
}
``` 


---------------------------------------

소스 코드 작성
- 예제를 돌려보기 위해 소스 코드를 다음과 같이 작성한다.

mydbsvr.js
``` javascript
const fs = require('fs');
const ejs = require('ejs');
const mysql = require('mysql');
const express = require('express');
const bodyParser = require('body-parser');

// MySQL DB 연결
const client = mysql.createConnection({
  host: 'localhost', // DB서버 IP주소
  port: 3306, // DB서버 Port주소
  user: 'root', // DB접속 아이디
  password: 'gachon654321', // DB암호
  database: 'mydb' //사용할 DB명
});
// 서버를 생성합니다.
const app = express();
  app.use(bodyParser.urlencoded({
    extended: false
}));
// 서버를 실행합니다.
app.listen(65001, function () {
  console.log('server running at http://127.0.0.1:65001');
});

app.get('/insert', (request, response) => {
  fs.readFile('9-insert.html', 'utf8', (error, data) => {
    //회원가입화면
    response.send(data); // 회원가입 화면전송
  });
});

app.get('/members', (request, response) => {
  fs.readFile('9-list.ejs', 'utf8', (error, data) => { // List화면
    // 데이터베이스 쿼리를 실행합니다.
    client.query('SELECT * FROM member', (error, results) => {
      // 응답합니다.
      response.send(ejs.render(data, {
        data: results // 회원조회 결과화면
      }));
    });
  });
});

app.post('/insert', function (request, response) {
  // 변수를 선언합니다.
  var body = request.body;
  console.log(body.name);
  console.log(body.uid);
  console.log(body.pass);
  // 데이터베이스 쿼리를 실행합니다.
  client.query('INSERT INTO member (name, uid, pass) VALUES (?, ?, ?)', [body.name, body.uid, body.pass], () => {
    console.log("Insertion into DB was completed !");
    response.end();
  });
});
``` 

9-insert.html
``` HTML
<!DOCTYPE html>
  <html>
    <head>
      <title>회원가입</title>
    </head>
    <body>
      <h1>회원가입</h1>
      <hr />
      <form method="post">
        <fieldset>
          <legend>회원가입</legend>
          <table>
            <tr>
              <td><label>이름</label></td>
              <td><input type="text" name="name" /></td>
            </tr>
            <tr>
              <td><label>사용자id</label></td>
              <td><input type="text" name="uid" /></td>
            </tr>
            <tr>
              <td><label>비밀번호</label></td>
              <td><input type="password" name="pass" /></td>
            </tr>
          </table>
          <input type="submit" value = "가입" />
        </fieldset>
      </form>
    </body>
</html>
``` 

9-list.ejs
``` html
<!DOCTYPE html>
  <html>
  <head>
    <title>List Page</title>
  </head>
  <body>
    <h1>List Page</h1>
    <hr />
    <table width="100%" border="1">
      <tr>
        <th>Name</th>
        <th>Model Number</th>
        <th>Series</th>
      </tr>
      <% data.forEach(function (item, index) { %>
        <tr>
          <td><%= item.name %></td>
          <td><%= item.uid %></td>
          <td><%= item.pass %></td>
        </tr>
        <% }); %>
      </table>
    </body>
</html>
``` 


---------------------------------------


![EC2](/images/AWS/EC2Project/Setting/EC2Setting20.jpg)

- Node mydbsvr.js 명령을 통하여 예제 페이지를 실행한다.


![EC2](/images/AWS/EC2Project/Setting/EC2Setting21.jpg)

- 3.87.0.78:65001/insert
- 그러나 접속을 하더라도 페이지가 제대로 뜨지 않는 것을 확인할 수 있다.
- 이는 Ec2의 보안 설정에서 포트 65001을 허용하지 않았기 때문이다.


![EC2](/images/AWS/EC2Project/Setting/EC2Setting22.jpg)

- 해당 인스턴스의 보안그룹을 들어가면 인바운드 규칙을 볼 수 있다.
- 인바운드 규칙 편집을 선택한다.


![EC2](/images/AWS/EC2Project/Setting/EC2Setting23.jpg)

- 유형을 사용자 지정 TCP로 하며 포트 범위를 65001로 한다.
- 그 후 0.0.0.0/0을 선택한 후 저장을 누른다.


---------------------------------------

![EC2](/images/AWS/EC2Project/Setting/EC2Setting24.jpg)

- 같은 URL로 접속하면 정상적으로 뜨는 것을 확인할 수 있다.


![EC2](/images/AWS/EC2Project/Setting/EC2Setting25.jpg)

- 입력 후 /members로 들어가게 되면 정상적으로 출력되는 것으 확인할 수 있다.


![EC2](/images/AWS/EC2Project/Setting/EC2Setting26.jpg)

- DB에 접속해보면 입력한 데이터가 정상적으로 저장되어 있는 것을 확인할 수 있다.

