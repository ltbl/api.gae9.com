======================
개9 오픈API 사용설명서
======================

소개
====

개9 API(이하 API)는 개9 기능 대부분을 지원하며, 
별도의 API 도메인에서 `REST` 형식으로 지원합니다.최신 문서 내용은
항상 `API Github`__ 에서 확인하실 수 있습니다.

.. __: https://github.com/ltbl/api.gae9.com


워터마크
========

개9에서 실제로 제공하는 모든 이미지에는, 워터마크가 포함되어 있습니다.
API를 사용하는 사용자는, 이 워터마크를 임의로 제거할 수 없으며 제거하여 이용할 경우
API 사용이 차단 되는 등의 불이익을 받으실 수 있습니다.

이와 관련된 자세한 문의는 `support@gae9.com` 으로 부탁드립니다.

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

======  =======================  =================================  ======
METHOD  URL                      DESCRIPTION                        인증
======  =======================  =================================  ======
GET     /channel                 채널 목록                          N
GET     /channel/<channel>       <channel>에 속한 개그, 사진 목록   N
GET     /tag                     태그 목록                          N
GET     /gag                     개그, 사진 목록                    N
GET     /gag/<gag id>            특정 <gag id>에 대한 정보          N
GET     /gag/<gag id>/image      가장 큰 사이즈 이미지              N
POST    /gag/<gag id>/tag/<tag>  개그에 태그 추가                   Y
DELETE  /gag/<gag id>/tag/<tag>  개그에 태그 삭제                   Y
POST    /gag                     사진 업로드                        Y
DELETE  /gag/<gag id>            사진 삭제                          Y
GET     /search                  검색                               N
======  =======================  =================================  ======

응답
====

모든 요청에 대한 응답은 `application/json` 형태로 제공되며 `JSONP` 형태 등의 사용 편의를 위해 
모든 HTTP 응답 코드는 `200` 으로 제공됩니다.

.. code-block:: http

    HTTP/1.1 200 OK
    Server: nginx
    Content-Type: application/json

    {}

이에 따라 모든 응답은, 다음과 같은 형태를 취합니다.

.. code-block:: json

    {
        "meta": {
            "status": 200,
            "message": "OK"
        },
        "response": { ... }
    }

Callback
--------

`JSONP` 지원을 위해, `jsonp=` 혹은 `callback=` 형식의 인자를 지원합니다. 
API호출시 해당 인자를 포함하여 호출할 경우, 응답은 해당 인자로 전달된 함수를 호출하는
형식으로 변경됩니다.

페이징
------

요청이 일정 갯수를 넘어갈 경우, `meta` 응답에 페이징 정보가 포함됩니다. 
따라서 다음과 같은 형태로 페이지 요청을 할 수 있습니다.

 * http://api.gae9.com/gag
 * http://api.gae9.com/gag?page=2

`meta` 응답에 포함되는 페이징 정보는 아래와 같은 형식 입니다.

.. code-block:: json

    {
        "meta": {
            "paging": {
                "next": 2,
                "cur": 1,
                "prev": null
            }
        }
    }


/channel
========

`개9` 전체 채널 목록을 반환 합니다.

요청
----

* GET http://api.gae9.com/channel

응답
----

=================  ======  ========   ==================================================================
필드명             Type    기능       설명
=================  ======  ========   ==================================================================
name               String  채널명     고유한 구분자(Uniqye Key)입니다.
url                String  주소       웹에서 채널에 접근할 수 있는 고유링크(Permalink)입니다.
feed               String  피드주소   채널의 업데이트를 받아볼 수 있는 `RSS` 주소 입니다.
last_published_at  String  최근항목   채널에 가장 마지막에 업로드된 개그의 시간으로 ISO8601 형식 입니다.
count              Number  항목갯수   채널에 업로드 되어 있는 항목의 총 수 입니다.
latest_gag         GAG     최근항목   채널에 가장 마지막에 업로드된 항목의 데이터 입니다.
=================  ======  ========   ==================================================================

예제
----

.. code-block:: json

    {
        "meta": {
            "status": 200,
            "message": "OK"
        },
        "response": [
            {
                "name": "GAE9",
                "url": "http://gae9.com/channel/1",
                "feed": "http://gae9.com/channel/1/feed",
                "last_published_at": "2012-11-05T11:11:11Z",
                "count": 9292,
                "latest_gag": { ... }
            },
        ]
    }

/channel/<channel>
==================

지정한 `<channel>` 정보를 반환 합니다.

요청
----

* GET http://api.gae9.com/channel/1

응답
----

=================  ======  ========   ==================================================================
필드명             Type    기능       설명
=================  ======  ========   ==================================================================
name               String  채널명     고유한 구분자(Uniqye Key)입니다.
url                String  주소       웹에서 채널에 접근할 수 있는 고유링크(Permalink)입니다.
feed               String  피드주소   채널의 업데이트를 받아볼 수 있는 `RSS` 주소 입니다.
last_published_at  String  최근항목   채널에 가장 마지막에 업로드된 개그의 시간으로 ISO8601 형식 입니다.
count              Number  항목갯수   채널에 업로드 되어 있는 항목의 총 수 입니다.
latest_gag         GAG     최근항목   채널에 가장 마지막에 업로드된 항목의 데이터 입니다.
=================  ======  ========   ==================================================================

예제
----

.. code-block:: json

    {
        "meta": {
            "status": 200,
            "message": "OK"
        },
        "response": {
            "name": "GAE9",
            "url": "http://gae9.com/channel/1",
            "feed": "http://gae9.com/channel/1/feed",
            "last_published_at": "2012-11-05T11:11:11Z",
            "count": 9292,
            "latest_gag": { ... }
        }
    }

/tag
====

등록된 모든 태그의 목록을 반환합니다.

요청
----

* GET http://api.gae9.com/tag

응답
----

=========  ======  ========  ==============================
필드명     Type    기능      설명
=========  ======  ========  ==============================
name       String  이름      태그명
permalink  String  고유주소  그의 고유주소(URL)
count      Number  총갯수    해당 태그로 태깅된 컨텐츠의 수
=========  ======  ========  ==============================

예제
----

.. code-block:: json

    {
        "meta": {
            "status": 200,
            "message": "OK"
        },
        "response": [
            {
                "name": "\\uace0\\uc591\\uc774",
                "permalink": "http://gae9.com/search?tags=\uace0\uc591\uc774",
                "count": 100
            }
        ]
    }

/gag
====

전체 개그 목록을 반환합니다.

요청
----

* GET http://api.gae9.com/gag

응답
----

`/gag/<gag id>`_ 항목을 참고하세요.


/gag/<gag id>
=============

`<gag id>` 에 해당하는 내용을 반환합니다.

요청
----

* GET http://api.gae9.com/gag/16232

응답
----

=================  ======  ========   ==================================================================
필드명             Type    기능       설명
=================  ======  ========   ==================================================================
id                 String  고유ID     해당 개그의 고유 구분자(Unique Key) 입니다.
permalink          String  고유주소   해당 개그를 고유하게 표현하는 URL 입니다.
author             Hash    작성자
author.id          String  고유ID     작성자의 고유 구분자(Unique Key) 입니다.
author.name        String  작성자명   작성자의 표시명(Screen Name) 입니다.
title              String  제목       해당 개그의 제목
published_at       String  작성일     `ISO8601` 형식의 개그 작성일 입니다.
score              Number  점수       해당 개그가 획득한 점수 입니다.
share              Number  공유       해당 개그가 SNS에서 공유된 수를 나타냅니다.
likes              Number  좋아요     해당 개그가 사이트에서 획득한 좋아요의 수를 나타냅니다.
tags               Array   태그
tags.name          String  태그명     해당 개그에 태그된 태그의 이름입니다.
tags.permalink     String  태그주소   해당 태그에 대한 고유 URL 입니다.
images             Array   이미지     크기별 이미지 종류를 나타냅니다.
images.name        String  종류       이미지의 종류를 나타냅니다.
images.width       Number  가로크기   이미지의 가로크기(단위: px)를 나타냅니다.
images.height      Number  세로크기   이미지의 세로크기(단위: px)를 나타냅니다.
images.url         String  주소       이미지의 URL 입니다.
source             String  출처       해당 컨텐츠의 출처를 나타냅니다.
type               String  종류       해당 컨텐츠의 종류를 나타냅니다.(image|video)
type_features      Hash    부가정보   종류별로 추가적인 정보가 담깁니다.
=================  ======  ========   ==================================================================

컨텐츠 부가정보
---------------

`개9` 에서 제공하는 모든 컨텐츠는 `image` 혹은 `video` 중 하나의 종류입니다.
안타깝게도, 컨텐츠중 일부는 외부사업자의 컨텐츠에 의존해야 하므로 API 에서 표준화된
응답을 제공하기 어려운 면이 있습니다. 

따라서, `개9` 에서는 `type_features` 라는 부가정보를 각 컨텐츠 종류에 맞게 추가적인
응답으로 제공합니다. 

image
-----

.. code-block:: json

    "type-features": {
        "content-type": "image/gif"
        "animated": true
    }

video
-----

`type_features["provider"]` 정보에 따라서 각 다른 형태의 부가정보가 포함됩니다.

youtube
-------

.. code-block:: json

    "type_features": {
        "permalink": "http://youtu.be/lhe7IiQ_4xA",
        "provider": "youtube", 
        "thumbnails": {
            "default": "http://img.youtube.com/vi/lhe7IiQ_4xA/default.jpg",
            "hqdefault": "http://img.youtube.com/vi/lhe7IiQ_4xA/hqdefault.jpg"
        }
    }

vimeo
-----

.. code-block:: json

    "type_features": {
        "permalink": "http://vimeo.com/52942657",
        "provider": "vimeo",
        "oembed": "http://vimeo.com/api/oembed.json?url=http%3A//vimeo.com/52942657"
    }

tvpot
-----

.. code-block:: json

    "type_features": {
        "permalink": "http://tvpot.daum.net/v/vc8abuorokllLLHxlkuTxEx",
        "provider": "tvpot",
        "thumbnails": {
            "default": "http://i1.daumcdn.net/svc/image/U03/tvpot_thumb/vc8abuorokllLLHxlkuTxEx/thumb.png",
            "thumb_1": "http://i1.daumcdn.net/svc/image/U03/tvpot_thumb/vc8abuorokllLLHxlkuTxEx/1.png",
            "thumb_2": "http://i1.daumcdn.net/svc/image/U03/tvpot_thumb/vc8abuorokllLLHxlkuTxEx/2.png",
            "thumb_3": "http://i1.daumcdn.net/svc/image/U03/tvpot_thumb/vc8abuorokllLLHxlkuTxEx/3.png",
        }

    }

이미지의 종류
-------------

개9 에서는 특정 개그의 이미지에 대해서 다양한 크기의 이미지를 생성하여 제공합니다.

=========  =============================================================================
종류       크기 규칙
=========  =============================================================================
full       원본 크기
thumbnail  가로 크기를 최대 480px 까지 (id 172까지는 640px) 허용하는 크기로 조정 됩니다.
small      80px*80px 크기의 정사각형으로 조정합니다.
=========  =============================================================================

만약, 업로드된 컨텐츠가 `Animated GIF` 라면 다음 규칙을 따릅니다.

=========  ==============================================================
full       원본의 에니메이션을 그대로 유지합니다.
thumbnail  첫 프레임만 추출하여 정적 이미지로 생성합니다.(크기 변경 없음)
small      첫 프레임만 추출하여 정적 이미지로 생성합니다.(크기 변경 없음)
=========  ==============================================================


예제
----

.. code-block:: json

    {
        "meta": {
            "status": 200,
            "message": "OK"
        },
        "response": {
            "id": "16232",
            "permalink": "http://gae9.com/gag/16232",
            "author": {
                "id": "5",
                "name": "kkungkkung"
            },
            "title": "\\uc800\\uae30.. \\ud558\\uc774\\ud30c\\uc774\\ube0c\\uc880 \\ud574\\uc8fc\\uc9c0 \\uc54a\\uc744\\ub798?",
            "published_at": "2012-10-25T02:10:00Z",
            "score": 7,
            "share": 13,
            "likes": 4,
            "tags": [
                {
                    "name": "\\uace0\\uc591\\uc774",
                    "permalink": "http://gae9.com/search?tags=\uace0\uc591\uc774"
                }
            ],
            "images": [
                {
                    "name": "full",
                    "width": 1024,
                    "height" 768,
                    "url": ""
                },
                {
                    "name": "thumbnail",
                    "width": 480,
                    "height": 480,
                    "url": ""
                },
                {
                    "name": "small",
                    "width": 80,
                    "height": 80,
                    "url": ""
                }
            ],
            "type": "image",
            "type_features": {
                "content-type": "image/jpeg"
            }
            "source": "http://imgur.com/gallery/ZoEY8",
        }
    }

/gag/<gag id>/image
===================

`<gag id>` 에 해당하는 내용의 가장 큰 이미지 주소를 `302 Found` Redirect 합니다.

..

    **주의**: 당 API는 유일하게 api.gae9.com 이 아닌 gae9.com 으로 호출 합니다.


요청
----

* GET http://gae9.com/gag/16232/image

응답
----

* `415 UNSUPPORTED MEDIA TYPE`: 동영상등 단순히 사용할 수 없는 경우 반환됩니다.
* `302 Found`: 이미지의 주소입니다.


/search
=======

다양한 방법으로 `개9` 컨텐츠를 검색할 수 있는 기능입니다.

요청
----

검색 API는 다음과 같은 종류의 인자를 지원하며, 2개의 인자를 조합하여 사용할 수 있으며, 
`sort` 를 제외한 한가지 이상의 인자가 제공되어야 합니다.

=======  ==================================================  ======
종류     설명                                                기본값
=======  ==================================================  ======
q        제목등의 검색어                                     NULL
tags     띄어쓰기로 구분하는 태그 목록으로 AND 질의 입니다.  NULL
type     컨텐츠 종류(image, animated, video)                 NULL
sort     정렬 방법을 정의합니다.                             hot
channel  특정 채널명 또는 채널의 ID로 검색합니다.            NULL
=======  ==================================================  ======

sort
----

정렬 방식은 다음과 같은 값이 지원됩니다.

=====  ===================================================
sort   설명
=====  ===================================================
hot    최신 개그들 중 인기있는 항목들
best   특정 갯수의 최신 글 중에서 가장 점수가 높은 항목
new    최신순
=====  ===================================================

응답
----

`/gag` 와 같은 형식으로 응답이 제공됩니다.

예제
----

* GET http://api.gae9.com/search?tags=아이유%20트윈테일
