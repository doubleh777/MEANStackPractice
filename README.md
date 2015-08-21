# MEANStackPractice

"중급 개발자를 위한 MEAN 스택 공략" 실습 및 요약 정리.

NPM를 이용해 모듈을 설치 할 때, -g로 전역으로 설치를 하게 되면 /usr/local/lib/node_modules에 설치된다.

NPM모듈 삭제 : npm uninstall <패키지명>  (전역 모듈의 경우 -g옵션)
Package.json : 노드 어플리케이션의 메타데이터를 가지고 있는 파일. 여기서 dependencies를 정의한다.
		      버젼에 "latest"를 쓰면 최신버전을 설치하도록 요청하게 되는데, 패키지가 하위 호환성을 
		      유지하지 못할 가능성이 있으므로 특정 버전이나 범위를 사용해 개발하는 것이 좋다.
		       npm install명령을 통해서 필요한 모듈들을 설치 할 수도 있고, npm update명령을 쓰면
		    빠진 패키지를 설치하고, 모든 기존 모듈들을 특정 버전으로 갱신해 준다.
		
자바스크립트의 클로져는 함수가 생성되는 순간의 환경변수들을 저장하고있는 객체인데, 이 덕분에 자바스크립트에서는 자유자제로 함수를 반환하는 방식을 사용 할 수 있다.

Node.js는 단일 전역공간을 이용하게 되는 불편함을 해소하기 위해 CommonJS표준을 만들었다.
//hello.js
var message = 'hello';

exports.sayHello = function(){
	Console.log(message);
}

//server.js
var hello = require('./hello');
hello.sayHello();
이처럼 exports객체를 통해 메소드를 외부에 공개하는 방식으로 각자의 Name Space를 유지하는 방법을 사용.

또한 module.exports를 이용한 모듈 생성 방식도 있다.
//hello.js
var message = 'hello';

module.exports = function(){
	Console.log(message);
}

//server.js
var hello = require('./hello');
hello();

이러한 방식으로 사용 가능.

미들웨어는 어떤 http요청이 왔을 때, 그 요청을 처리하도록 하는 로직 함수를 말한다.
만약 어떠한 미들웨어도 등록되어 있지 않다면 Cannot GET/ 과 같은 문구가 브라우져에 뜰것이다.
connect 모듈의 미들웨어 등록 방식은 express에도 내장되어 있으므로 잘 이해해야함.

connect모듈의 미들웨어 동작 방식.
미들웨어는 http요청이 들어왔을 경우 항상 동작 되는 함수이다.
여러개의 미들웨어를 등록 해 놓으면 이 모든 함수들이 실행이 되는데,  미들웨어들은 req,res,next파라미터를 가진다. 이때 next는 미들웨어로 그 다음 등록된 함수를 가리키는 함수이다. 따라서 다음 미들웨어가 실행 되도록 하기 위해서는 next();를 써서 실행 될 수 있도록 해야한다. 특히 res.end()가 없는 미들웨어의 경우는 응답이 끝나지 않기 때문에 계속해서 멈춰있을 수 밖에 없게 된다.

한편 모든 http요청에 대해 모든 미들웨어가 동작한다면 필요없는 미들웨어까지 동작할 수도 있다. 이를 해결하기 위해 connect 미들웨어 아키텍쳐에서는 app.use('/helloWorld', 'helloWorld')와 같은 방식으로 /helloWorld로 리퀘스트가 들어오면 helloWorld미들웨어를 실행시켜 주도록 하는 방식을 제공한다.

connect 모듈에서는 res.setHeader();로 헤더를 설정하고, res.end();로 응답을 보냈다.
express에서는 res.send()함수를 이용해 res의 객체 타입에 대응하는 Content-Type을 설정하고 응답을 돌려보내는 함수인데, connect의 res.setHeader()와 res.end()를 합쳐서 랩핑해놓은 함수라고 보면 된다. Content-Type 셋팅은 res.set()함수로 할 수 있다.


-express가 제공하는 객체들의 메소드 정리

app.set(name, value) : 익스프레스가 사용할 환경변수 설정.
app.get(name) : 익스프레스가 구성에서 사용할 환경변수를 얻음.(app.get(path, callback)과 다름. 반드시 구분)

app.engine(ext, callback) : 특정 파일 타입을 출력하기 위해 필요한 템플릿 엔진을 정의
Ex) app.engine('html', require('ejs').renderFile) : html파일 템플릿으로 사용하도록 EJS템플릿 엔진에게 알림

app.locals  : 애플리케이션 수준의 변수를 출력하기 위해 모든 템플릿에 전송?????
app.use([path], callback) 미들웨어 등록. 옵션으로 특정 path에 미들웨어를 마운트 할 수 있다.
app.VERB(path, callback) : get,post,delete,put과 같은 VERB가 특정 path로 들어오면 그 VERB와 path에 해당하는 미들웨어를 할당할 수 있다.
app.route(path).VERB([callback], callback) : 여러 HTTP VERB가 특정 경로와 연결된 HTTP요청에 응답하기 위한 미들웨어 함수를 하나 이상 등록 할 수 있다.
Ex) app.route(path).get(callback).post(callback) : 특정 path로 들어오는 get, post 리퀘스트에 관해 각각 callback 함수를 등록 함.

app.param([name], callback) : 특정 라우팅 매개변수를 포함한 경로로 들어오는 요청에 특정 기능을 붙인다 ????

req.query : 해석된 query-string매개변수를 포함
req.params : 해석된 라우팅 매개변수 포함
req.body : 해석된 요청 내용을 인출하기 위해 사용되는 객체. 이 속성은 bodyParser()미들웨어에 포함.
req.param(name) : 요청 매개변수 값을 인출함. 매개변수는 quert-string매개변수, 라우팅 매개변수 JSON요청에서 얻은 속성이 될 수 있다.
req.path, req.host, req.ip : 현재 요청 경로, 호스트 이름, 원격 IP를 인출함.
req.cookies : user-agent가 전송한 쿠키를 인출하기 위해 cookieParser()미들웨어와 함께 사용.

res.status(code) : 응답 HTTP 상태 코드를 설정.
res.set(field, [value]) : 응답 HTTP 헤더를 설정.
res.cookie(name, value, [option]) : 응답 쿠키를 설정한다. option인수는 maxAge속성과 같은 일반적인 쿠키 구성을 정의하는 객체를 전달하기 위해 사용된다.?????

res.redirect([status], url) : 주어진 URL로 요청을 리다이렉트 한다. status의 default값은 302 Found이다.
res.json([status | body], [body]) : JSON으로 된 객체나 배열을 전송할 때 사용한다. res.send()메소드로도 전송 할 수 있지만.... 물론, 만약 null이나 undefiled와 같은 비객체를 JSON으로 응답을 강제할 경우에는 사용할 필요가 있을 수 있다.
res.render(view, [locals], callback) : 뷰를 생성하고 HTML응답을 전송한다.

잘쓰는 외부 미들웨어 정리
morgan : HTTP 요청 로거 미들웨어
body-parser : 요청 내용을 해석하는 미들웨어
compression : gzip/deflate를 사용해 응답 데이터를 압축하는 미들웨어
express.static : 정적 파일을 서비스하는 미들웨어
cookie-parser : req.cookies 객체를 채우기 위한 쿠키 해석용 미들웨어
session : 영속적인 세션을 지원하기 위해서 사용하는 미들웨어


디렉토리 구조
express는 디렉토리 구조에 중립적이다.
따라서 간단한 프로젝트의 경우는 수평적인 간단한 구조를 사용하면 좋고, 복잡한 프로젝트는 수직적인 구조를 사용하는것이 좋을 수 있다.
한편, MEAN스택은 front-end 와 back-end에서 모두 자바 스크립트를 사용하므로, 이름이 겹칠 가능성이 높다. 따라서 파일 네이밍이 중요한데, feature.server.controller.js와 같이 전체 구조에서의 해당 모듈의 기능, 실행 목적지, 개별 파일의 기능 등을 상세하게 붙여 만드는 방식을 권한다.


유효성 검증 패턴

var express = require('express');
var hasName = function(req,res,next){
    if(req.param('name')){
	 next();
    } else {
	 res.send('What is your name?');
    }
};
var sayHello = function(req,res,next){
    res.send('Hello ' + req.param('name'));
};
var app = express();
app.get('/', hasName, sayHello);
app.listen(3000);
console.log('Server runnint at http://localhost:3000/');

위 코드에서 app.get()을 통해 hasName과 sayHello 미들웨어를 등록하고있다. 그리고 hasName에서 param('name') 값이 있는지를 확인하고 있으면 다음 미들웨어로 넘기고 그렇지 않으면 넘기지 않는 방식으로 유효성을 검증하는 패턴.


98~101페이지(04번 폴더에 실습한 예제) : 디렉토리 구조와 server.js, expess.js를 추가로 구현하여 설정하는 방식(env폴더에 express.js에 routing부분을 추가 구현한 뒤 server.js에서 ./env/express.js를  require하는 방식으로 구현한 것 참고하여 공부하기)

process.env속성은 전역 변수이며, 미리 정의된 환경 변수에 대한 접근을 하기 위해 사용한다. 그 중 NODE_ENV환경 변수는 환경 구성을 설정하기 위해 사용된다. 예를 들면 개발환경에서는 로거를 사용하기를 원하지만 상용 환경에서는 원하지 않는다거나에 관한 설정을 할 수 있다.

morgan모듈은 단순한 로거 미들웨어를 제공하며, compression모듈은 응답 압축을 지원하고, body-parser모듈은 요청 데이터를 처리하며, method-override모듈은 DELETE나PUT과 같은 HTTP 메소드 기본 지원 기능을 제공한다.

1 var express = require('express'),
2     morgan = require('morgan'),
3     compress = require('compression'),
4     bodyParser = require('body-parser'),
5     methodOverride = require('method-override');
6 
7 module.exports = function(){
8     var app = express();
9     
10     if(process.env.NODE_ENV === 'development'){
11         app.use(morgan('dev'));
12     }else if(process.env.NODE_ENV === 'production'){
13         app.use(compress());
14     }
15     
16     app.use(bodyParser.urlencoded({
17         extended: true
18     }));
19     app.use(bodyParser.json());
20     
21     require('../app/routes/index.server.routes.js')(app); // route.js에서 app.get('/',~~~)방식으로 쓰기 위해서는
22     return app;						     //파라미터로 express를 담고있는 app을 전달해줘야함
23 };
 
위 코드를 보면 process.env.NODE_ENV를 체크하여 app의 환경에 따라 미들웨어를 적절히 마운트 할 수 있다.

NODE_ENV는 유닉스 기반의 운영체제에서는 터미널에 
$ export NODE_ENV=development
와 같은 방식으로 환경변수를 설정 할 수 있다.

process.env.NODE_ENV = process.env.NODE_ENV || 'development'; 처럼 환경변수가 설정되어 있지 않을 경우 'development'로 설정되도록 처리 해주기.

단순히 NODE_ENV를 읽어 적절한 환경에 맞는 파일을 불러주는 config.js를 만드는 방식도 참고 할것.(마찬가지로 예제 04번에 실습하였음)


뷰 생성
익스프레스는 뷰를 생성하는 메소드가 2개 있다.
app.render()는 뷰를 생성하기 위해 사용되며, 콜백함수에 HTML을 전달한다. 더 일반적인 res.render()는 뷰를 지역적으로 생성하며, 응답으로 HTML을 전송한다. res.render()를 더 자주 사용하는 이유는 흔히 응답으로 HTML 출력을 원하기 때문이다. 하지만 애플리케이션이 HTML 이메일을 전송하기를 원한다면, app.render()를 사용해야한다.

 app.set('view','../app/views'); //views폴더 내부에서 render()할 view를 찾도록 views폴더를 설정해줌.
 app.set('view engine', 'ejs'); //템플릿 변수를 설정하는 것이다. 이 변수를 이용해 뷰 엔진을 써서 뷰를 생성할 것이다.
위와 같은 방식으로 view폴더를 설정하고 view engine을 등록 할 수 있다.


정적 파일  서비스
app.use(express.static('./public'))과 같이 미들웨어를 등록한다. 
이후 ejs파일에서 그냥 img/logo.png를 src로 써주기만하면 public폴더에서 logo.png를 찾아 넣어준다.


session이용하는 방법(p113~114) 참고

