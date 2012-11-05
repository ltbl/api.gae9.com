======================
GAE9.COM API 스팩 문서
======================

소개
====

GAE9.COM API(이하 API)는 GAE9.COM 기능 대부분을 지원하며, 
별도의 API 도메인에서 `REST` 형식으로 지원합니다.최신 문서 내용은
항상 `API Github`__. 에서 확인하실 수 있습니다.

.. __: https://github.com/ltbl/api.gae9.com

문서의 약속
===========

* 본 문서에서 `<내용>` 형태료 표시된 부분은, 적당히 알맞은 값으로 변경해 넣어야 하는 인자를 의미 합니다.


요청
====

인증
----

API는 인증을 필요로 하는 API와 인증없이 작동되는 API로 구별되며,
모든 쓰기/업데이트/삭제등의 요청에는 인증이 필요합니다.

기본적으로 인증은 다음과 같은 방식을 지원하고 있습니다.

1. `OAuth 2.0 Draft 16`__ 이상
#. 브라우저 세션을 이용한 방법

..

    **주의**: 브라우저 세션을 이용한 방법의 경우, GAE9팀과 별도의 협의가 필요합니다.
    자세한 내용은 `support@gae9.com` 으로 메일 주세요.

__ http://tools.ietf.org/html/draft-ietf-oauth-v2-31

OAuth
-----

`OAuth` 인증은 `OAuth 2.0 Draft 16` 이상의 표준을 준수하는 선에서 지원하며,
HTTP `Authorization` 해더를 통해, `HMAC-SHA1` 으로 서명된 서명만 수락하고 있습니다.

=============  =======================================
Endpoint       URL
=============  =======================================
Token Request  http://api.gae9.com/oauth/request_token
Authorize      http://api.gae9.com/oauth/authorize
Access Token   http://api.gae9.com/oauth/access_token
=============  =======================================

`OAuth` 인증을 사용하려면 `GAE9 개발자 페이지`__ 에서 Application 등록 과정을
수행해야 합니다. 


__ http://api.gae9.com/developer

주소
----

모든 요청은 `http://api.gae9.com` 호스트에서 처리 됩니다.

======  ==================  =================================
METHOD  URL                 DESCRIPTION
======  ==================  =================================
GET     /channel            채널 목록
GET     /channel/<channel>  <channel>에 속한 개그, 사진 목록
GET     /tag                태그 목록
GET     /gag                개그, 사진 목록
GET     /gag/<gag id>       특정 <gag id>에 대한 정보
POST    /gag                사진 업로드
GET     /search             검색
======  ==================  =================================

응답
====

모든 요청에 대한 응답은 `application/json` 형태로 제공되며 `JSONP` 형태 등의 사용 편의를 위해 
모든 응답 코드는 `200` 으로 제공됩니다.

::

    HTTP/1.1 200 OK
    Server: nginx
    Content-Type: application/json
    
    {}

이에 따라 모든 응답은, 다음과 같은 형태를 취합니다.

::

    {
        "result": {
            "status": 200,
            "message": "OK"
        },
        "response": { ... }
    }

또한 `JSONP` 지원을 위해, `jsonp=` 혹은 `callback=` 형식의 인자를 지원합니다. 
API호출시 해당 인자를 포함하여 호출할 경우, 응답은 해당 인자로 전달된 함수를 호출하는
형식으로 변경됩니다.


기능별 상세
===========

/channel
--------

/channel/<channel>
------------------

/tag
----

/gag
----

/gag/<gag id>
-------------

/search
-------


