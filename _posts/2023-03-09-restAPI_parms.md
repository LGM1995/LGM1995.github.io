---
title: RestAPI와 Parameter의 종류에 대해서
categories: [Learn, Spring, RestAPI]
tags: [api, params, restapi]		# TAG는 반드시 소문자로 이루어져야함!
lastmod : 2023-03-09 22:00:00 # 페이지의 마지막 수정일
sitemap :
changefreq : daily # 페이지의 변경 빈도 always/hourly/daily/weekly/monthly/yearly/never
priority : 1.0 # 페이지의 검색순위로 검색엔진에게 우선순위를 알려줌 0.0-1.0 (defult 0.5)
---


# RestAPI와 Parameter의 종류에 대해서

이번 포스팅에서는 요즘 흔하게 쓰이는 API통신중 RestAPI의 Parameter로 올 수 있는 종류에 대해 알아본다.

# REST란

Representational State Transfer의 줄임말로 모든 자원(URI)에 대해 행위(HTTP메서드)를 통해 원하는 정보가 표현(xml,json)되는 것이다.

## RestController

이전 프론트와 백엔드가 분리되기 전에는 Controller는 모델과 뷰를 명시했으나 요즘의 트랜드는 백은 Json타입의 데이터만 나머지는 vue나 react와 같이 프론드에서 백엔드로 부터 받아온 데이터를 가공하는 일이 많다.
그래서 요즘엔 ResponseEntity에 HttpStatus의 상태값과 필요한 데이터를 body(ResponseBody)에 담아서 보내는 경우가 많은데 이때 사용하는 것이 RestController다.

### RestAPI 파라미터 종류

RestAPI에선 4가지 타입의 파라미터를 받을 수 있는데 그 종류는 다음과 같다.

* header 파라미터
* path 파라미터
* query string 파라미터
* request body 파라미터

### header 파라미터

header 파라미터는 흔히 인증이나 권한 부여의 목적에서 사용되는 파라미터다.
주로 암호화된 키를 사용하여 사용자의 권한에 따른 인증 절차를 가지거나 로그인에 따른 회원의 사용권한을 부여한다.
보통 JWT(Json Web Token)이나 다른 인증 서버를 통한 OAuth 2.0 에 많이 사용된다.

### path 파라미터

보통 대부분의 설명은 엔드포인트의 일부라고 설명하는데 쉽게 말하면 자원URI에 포함되는 파라미터이다.

```
@RestController
@RequestMapping("/api")
public class CreamApiController {
    private final CreamService creamService;
    private final UserService userService;

    public CreamApiController(CreamService creamService, UserService userService) {
        this.creamService = creamService;
        this.userService = userService;
    }

    @GetMapping("/{username}")
    @PreAuthorize("hasAnyRole('USER')")
    public ResponseEntity<List<CreamDto>> read(@PathVariable String username) {
        List<CreamDto> creamDtos = creamService.creamList(username);
        return ResponseEntity.status(HttpStatus.OK).body(creamDtos);
    }
    .....
```

위 코드는 실제 나의 프로젝트에서 사용되는 코드로 회원 정보를 찾기위한 자원(URI)에 회원이름을 받아서 사용하는 컨트롤러다.
만약 admin이라는 회원의 데이터가 필요하면 /api/admin으로 Get요청이 실행되고 인증절차를 거쳐 HttpStatus.OK 싸인과 함께 데이터가 전송된다.


### query string 파라미터

엔드포인트에서 물음표 뒤에 등장하는 qurey 파라미터로 정렬이나 특정 필터를 하기 위해 적절하다.

```
@RestController
@RequestMapping("/api")
public class CreamApiController {
    private final CreamService creamService;
    private final UserService userService;

    public CreamApiController(CreamService creamService, UserService userService) {
        this.creamService = creamService;
        this.userService = userService;
    }

    @GetMapping("/user")
    @PreAuthorize("hasAnyRole('USER')")
    public ResponseEntity<List<CreamDto>> read(@RequestParam String username) {
        List<CreamDto> creamDtos = creamService.creamList(username);
        return ResponseEntity.status(HttpStatus.OK).body(creamDtos);
    }
    .....
```

@PathVariable에서 @RequestParam으로 설정한 후 똑같이 admin이란 회원의 정보를 요청하면 해당 URI자원은
/api/user?username=admin으로 호출된다.
만약 조건이 많을경우 객체를 바인딩해서 @ModelAttribute를 사용하여 객체의 모든 값을 query string으로 사용할 수 있으며 @RequestParam(required = true)를 설정하여 필수 조건을 결정할 수 있다.

### request body 파라미터

requestBody에 데이터(주로 json)를 담아 요청하는 방식으로 주로 Post방식에 사용되는 파라미터이다.

```
@RestController
@RequestMapping("/api")
public class CreamApiController {
    private final CreamService creamService;
    private final UserService userService;

    public CreamApiController(CreamService creamService, UserService userService) {
        this.creamService = creamService;
        this.userService = userService;
    }

    ....

    @PostMapping("/{username}/scoop")
    @PreAuthorize("hasAnyRole('USER')")
    public ResponseEntity<CreamDto> scoop(@PathVariable String username, @RequestBody CreamDto creamDto) {
        CreamDto cream = creamService.create(username, creamDto);
        return ResponseEntity.status(HttpStatus.OK).body(cream);
    }
    ....
```

위의 코드로 admin이라는 회원의 cream 데이터를 추가한다면 /api/admin 이라는 URI 자원에 Http메서드는 Post 방식으로 json데이터가 같이 넘어온다.

```
public class CreamDto {
    private Long id;
    private String menu;
    private Timestamp date;
    private Long state;
    private Long temperature;
```
CreamDto는 다음 형태와 같으며 json으로
```
{
  "id": 3,
  "menu": "순대국",
  "date": "2023-03-02T10:03:30.000Z",
  "state": 1,
  "temperature": 36
  }
```
등의 형태로 값이 넘어와 객체와 매핑되어 사용된다.

### 마무리

요약 하자면 RestAPI에서 중요한 점은 모든 요청에 대한 자원(URI), HTTP 메서드 명시, URI에 동사 표현하지 않기, path파라미터와 query string 파라미터의 적재적소 배치가 중요한 것 같다.


>#### 개인적인 이해를 바탕으로 작성한 글 입니다. 피드백 언제든 환영합니다!
