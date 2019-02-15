## 코드리뷰-5

### Connecting To A Real API - (2)

지난번 **코드리뷰-3**에서 react로 만든 웹 페이지에서 실제 서버의 데이터베이스를 이용하여 웹 페이지에 게시물을 출력하는 기능을 구현하는 것이 목표입니다.

기본적으로 `react-admin`에서 제공하는 `jsonServerProvider`를 이용하여 연결하기 위해서 아래와 같이 코드를 작성합니다.
```
const dataProvider = jsonServerProvider('http://django-env.h9tj6a2pet.ap-northeast-2.elasticbeanstalk.com/api/v1/main');
```
위에 코드 변경사항을 저장하고, 웹 페이지에 접속하면 아래와 같은 에러가 발생합니다.
![img](https://blogfiles.pstatic.net/MjAxOTAyMTNfMTY0/MDAxNTUwMDQzOTQwMjA1.9d4VFvKzkvontoBUHXJxakvVakUWp_-V8QnwLoe3SU8g.zLOX2haS6LFzBu47CgHk7ITJwzeFyeNnUBgD8Ra8KAEg.PNG.jjscan/fetch_error.PNG)

jsonServerProvider가 해당 주소의 json 파일을 fetch 하지 못했다는 내용입니다.
해당 에러의 원인을 분석하기 위해서 request url을 찾아보니, `http://django-env.h9tj6a2pet.ap-northeast-2.elasticbeanstalk.com/api/v1/main/boards/?_end=10&_order=DESC&_sort=id&_start=0`으로 확인됩니다.

현재 회사의 서버는 Django 기반으로 제작했습니다. Django에서는 server.urls을 이용하여 URLconf 에 대하여 규칙을 명세하고 있습니다. 현재 서버에 설정된 URLconf은 아래와 같습니다.

```
1. admin/
2. api/v1/main/ boards/ [name='boards_list']
3. api/v1/main/ boards<drf_format_suffix:format> [name='boards_list']
4. api/v1/main/ boards/<int:pk>/ [name='board_detail']
5. api/v1/main/ boards/<int:pk><drf_format_suffix:format> [name='board_detail']
6. api/v1/doc/
7. __debug__/
```

따라서 `http://django-env.h9tj6a2pet.ap-northeast-2.elasticbeanstalk.com/api/v1/main/boards/?_end=10&_order=DESC&_sort=id&_start=0`으로 요청을 하면 서버는 URLconf 에 따라서 3번으로 응답하고, `boards_list`를 돌려주게 됩니다.

따라서, `_end=10&_order=DESC&_sort=id&_start=0`을 요청했던 `dataProvider`의 기대와는 다른 결과 값이 들어오기 때문에 `dataProvider`는 해당 결과 값을 올바르게 처리하지 못하고 fetch 에러를 출력하게 됩니다.

해당 에러를 해결하기 위해 생각한 방법은 아래와 같습니다.
1. `dataProvider`의 데이터 요청 및 처리 부분을 적절히 수정한다.
2. django로 개발된 서버의 URLconf를 비롯한 나머지 부분을 `jsonServerProvider`에 알맞게 수정한다.

여기서는 `dataProvider`를 알맞게 수정하는 방법을 선택해서 `react`에 대해서 더 공부하고 연습할 수 있도록 할 예정입니다.

### dataProvider 수정하기

`dataProvider`는 `C:\Users\ADMIN\react_worksapce\React-admin-test\node_modules\ra-data-json-server\` 경로에 저장된 상태입니다. 경로를 보면 알 수 있듯이 해당 패키지는 패키지매니저인 `yarn`을 통해서 자동 설치됐습니다.
해당 폴더 아래에는 `esm/index.js`, `lib/index.js`, `src/index.js`이렇게 중요한 세 개의 파일이 있고,  나머지 파일들 중에서 `README.md` 파일을 참고하면 어떻게 수정하는지 도움을 받을 수 있다.

우선, `jsonServerProvider`는 [JSONPlaceholder](http://jsonplaceholder.typicode.com/)이라고 불리는 [JSON Server](https://github.com/typicode/json-server)에 최적화된 `dataProvider`이다.

해당 `dataProvider`는 아래와 같은 메서드 매핑 규칙을 사용한다.

| REST verb            | API calls                                                    |
| -------------------- | ------------------------------------------------------------ |
| `GET_LIST`           | `GET http://my.api.url/posts?_sort=title&_order=ASC&_start=0&_end=24&title=bar` |
| `GET_ONE`            | `GET http://my.api.url/posts/123`                            |
| `CREATE`             | `POST http://my.api.url/posts/123`                           |
| `UPDATE`             | `PUT http://my.api.url/posts/123`                            |
| `DELETE`             | `DELETE http://my.api.url/posts/123`                         |
| `GET_MANY`           | `GET http://my.api.url/posts/123, GET http://my.api.url/posts/456, GET http://my.api.url/posts/789` |
| `GET_MANY_REFERENCE` | `GET http://my.api.url/posts?author_id=345`                  |

**주의할 점 1** : JSON Server REST Data Provider는 `GET_LIST` 호출에 대한 응답(response)의 헤더가 `X-Total_Count`를 포함하고, 이 값을 사용하여 `전체 게시물의 개수`를 알 수 있고 `react-admin`이 얼마나 많은 `page`를 생성해야하는지 계산합니다.

**주의할 점 2** : If your API is on another domain as the JS code, you'll need to whitelist this header with an `Access-Control-Expose-Headers` [CORS](https://developer.mozilla.org/en-US/docs/Web/HTTP/Access_control_CORS) header.

서버에 요청(request)할 때, 헤더(Header)를 추가하고 싶으면 `App.js` 파일에 아래 코드를 추가한다.

```jsx
import { fetchUtils, Admin, Resource } from 'react-admin';
import jsonServerProvider from 'ra-data-json-server';

const httpClient = (url, options = {}) => {
    if (!options.headers) {
        options.headers = new Headers({ Accept: 'application/json' });
    }
    // add your own headers under here
    options.headers.set('X-Custom-Header', 'foobar');
    return fetchUtils.fetchJson(url, options);
}
const dataProvider = jsonServerProvider('http://jsonplaceholder.typicode.com', httpClient);

render(
    <Admin dataProvider={dataProvider} title="Example Admin">
       ...
    </Admin>,
    document.getElementById('root')
);
```
위에 코드를 추가하면 모든 요청(request)는 `X-Custom-Header: foobar` 헤더를 가지게 됩니다.

**Tip** : 커스텀 헤더를 추가하는 방법은 아래와 같이 인증 절차를 추가할 때 사용합니다. `fetchJson` has built-on support for the `Authorization` token header:

```jsx
const httpClient = (url, options = {}) => {
    options.user = {
        authenticated: true,
        token: 'SRTRDFVESGNJYTUKTYTHRG'
    }
    return fetchUtils.fetchJson(url, options);
}
```

----

우선, fetch 주소를 회사 서버에 맞게 수정하기 위해서 `jsonServerProvider` 아래 있는 세 개의 index.js 파일을 수정해야 합니다. 세 개의 파일이 비슷한 코드로 이뤄져 있어서 대표적으로 하나의 코드만 아래에 설명하겠습니다.

우선 `App.js` 파일에서 `const dataProvider = jsonServerProvider('http://django-env.h9tj6a2pet.ap-northeast-2.elasticbeanstalk.com/api/v1/main');` 을 추가하여 api 서버의 주소를 `jsonServerProvider`에 넘겨줍니다.

세 개의 index.js 파일을 열어 `convertDataRequestToHTTP` 부분에서 `GET_LIST, GET_MANY_REFERENCE` 부분에 `url` 완성 패턴을 수정합니다.
```jsx
case GET_LIST: {
                const { page, perPage } = params.pagination;
                const { field, order } = params.sort;
                const query = {
                    ...fetchUtils.flattenObject(params.filter),
                    format: "json",
                };
                url = `${apiUrl}/${resource}/?${stringify(query)}`;
                break;
            }
```

이렇게 수정 후, 저장하고 React를 다시 재시작하면, 의도했던 `http://django-env.h9tj6a2pet.ap-northeast-2.elasticbeanstalk.com/api/v1/main/boards/?format=json` 주소로부터 fetch를 시도하는 모습을 확인할 수 있었습니다.

하지만, 아래처럼 CORS 정책에 의해서 막히는 에러가 발생하면서 더 이상 진행하지 못했습니다.

```
Access to fetch at 'http://django-env.h9tj6a2pet.ap-northeast-2.elasticbeanstalk.com/api/v1/main/boards/?format=json' from origin 'http://localhost:3000' has been blocked by CORS policy: Response to preflight request doesn't pass access control check: No 'Access-Control-Allow-Origin' header is present on the requested resource. If an opaque response serves your needs, set the request's mode to 'no-cors' to fetch the resource with CORS disabled.
```

![img](https://mdn.mozillademos.org/files/14295/CORS_principle.png)

[CORS](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS)는 Cross-Origin Resource Sharing의 약자로 서로 다른 도메인 주소 간에 자원을 공유하기 위한 정책을 의미합니다. CORS의 종류는 `1.  Simple Request / 2. Preflight Request / 3. Credentialed Request / 4. Non-Credentialed Request`으로 구성됩니다.

```
Simple Request : 
- 가장 기본적인 요청 종류
- GET/HEAD/POST 중 한가지 메소드 사용
- POST일 경우, Content-type이 application/x-www-form-urlencoded or multipart/form-data or text/plain 중 하나를 만족
- 커스텀 헤더 사용 X

Preflight Request :
- 예비 요청을 먼저 보내 서버에 응답이 가능한지 확인
- GET/HEAD/POST 이외의 메소드 사용
- 본 요청이 POST일 경우, Content-type이 simple Request 조건 3가지와 다르거나 커스텀 헤더를 사용해야함
- 예비요청-예비응답, 본 요청-본 응답 총 2번의 round-trip으로 구성

Credentialed Request : 
- HTTP cookie와 HTTP Authentication 정보를 인식할 수 있게 해주는 요청
- xhr.withCredentials = true로 지정

Non-Credentialed Request :
- xhr.withCredentials = false
```

회사에서 만든 서버는 Django를 기반으로 만들었고,  Django에서는  아래처럼 `django-cors-headers package`를 설치하여 `WhiteList`로 CORS를 추가할 수도 있습니다.
```jsx
CORS_ORIGIN_WHITELIST = (
    'google.com',
    'hostname.example.com',
    'localhost:8000',
    '127.0.0.1:9000'
)
```



----


<< 이어서 계속..... >>