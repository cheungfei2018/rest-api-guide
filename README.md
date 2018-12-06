## 参考
- http://www.ruanyifeng.com/blog/2018/10/restful-api-best-practices.html
- http://www.ruanyifeng.com/blog/2014/05/restful_api.html  
- http://www.ruanyifeng.com/blog/2014/05/oauth_2_0.html

## HTTP 动词
对于资源的具体操作类型，由 HTTP 动词表示（括号里是对应的 SQL 命令）。
```
GET（SELECT）：从服务器取出资源（一项或多项）。
POST（CREATE）：在服务器新建一个资源。
PUT（UPDATE）：在服务器更新资源（客户端提供改变后的完整资源）。
PATCH（UPDATE）：在服务器更新资源（客户端提供改变的属性）。
DELETE（DELETE）：从服务器删除资源。
HEAD：获取资源的元数据。
OPTIONS：获取信息，关于资源的哪些属性是客户端可以改变的。
```

例子
```
GET /zoos：列出所有动物园
POST /zoos：新建一个动物园
GET /zoos/ID：获取某个指定动物园的信息
PUT /zoos/ID：更新某个指定动物园的信息（提供该动物园的全部信息）
PATCH /zoos/ID：更新某个指定动物园的信息（提供该动物园的部分信息）
DELETE /zoos/ID：删除某个动物园
GET /zoos/ID/animals：列出某个指定动物园的所有动物
DELETE /zoos/ID/animals/ID：删除某个指定动物园的指定动物
```

具体例子
```
GET https://api.liruan.cn/v1/articles
GET https://api.liruan.cn/v1/articles/1
POST https://api.liruan.cn/v1/articles
PATCH https://api.liruan.cn/v1/articles
DELETE https://api.liruan.cn/v1/articles/1
DELETE https://api.liruan.cn/v1/articles?id=1,2,3
GET https://api.liruan.cn/v1/articles/1/authors/2
POST https://api.liruan.cn/v1/actions/login
```

条件查询例子
```
?$filter=title like 文章标题,created_at gte 1475855919&$offset=0&$limit=10&$order_by=views
```

## 状态码
服务器向用户返回的状态码和提示信息，常见的有以下一些（方括号中是该状态码对应的 HTTP 动词）。
```
200 OK - [GET]：服务器成功返回用户请求的数据，该操作是幂等的（Idempotent）。
201 CREATED - [POST/PUT/PATCH]：用户新建或修改数据成功。
202 Accepted - [*]：表示一个请求已经进入后台排队（异步任务）
204 NO CONTENT - [DELETE]：用户删除数据成功。
400 INVALID REQUEST - [POST/PUT/PATCH]：用户发出的请求有错误，服务器没有进行新建或修改数据的操作，该操作是幂等的。
401 Unauthorized - [*]：表示用户没有权限（令牌、用户名、密码错误）。
403 Forbidden - [*] 表示用户得到授权（与401错误相对），但是访问是被禁止的。
404 NOT FOUND - [*]：用户发出的请求针对的是不存在的记录，服务器没有进行操作，该操作是幂等的。
406 Not Acceptable - [GET]：用户请求的格式不可得（比如用户请求JSON格式，但是只有XML格式）。
410 Gone -[GET]：用户请求的资源被永久删除，且不会再得到的。
422 Unprocesable entity - [POST/PUT/PATCH] 当创建一个对象时，发生一个验证错误。
500 INTERNAL SERVER ERROR - [*]：服务器发生错误，用户将无法判断发出的请求是否成功。
```

## 状态码归类
```
API 正常工作 (200, 201)
客户端错误 (400, 401, 403, 404)
服务端错误 (500, 503)
```

## 常用客户端错误类型
#### 400 Bad Request
由于包含语法错误，当前请求无法被服务器理解。除非进行修改，否则客户端不应该重复提交这个请求。
通常在请求参数不合法或格式错误的时候可以返回这个状态码。

#### 401 Unauthorized
当前请求需要用户验证。
通常在没有登录的状态下访问一些受保护的 API 时会用到这个状态码。

#### 403 Forbidden
服务器已经理解请求，但是拒绝执行它。与401响应不同的是，身份验证并不能提供任何帮助。
通常在没有权限操作资源时(如修改/删除一个不属于该用户的资源时)会用到这个状态码。

#### 404 Not Found
请求失败，请求所希望得到的资源未被在服务器上发现。
通常在找不到资源时返回这个状态码。

## 错误代码
400
```
FORMAT_INVALID    格式错误
DATA_EXISTED      数据已存在
DATA_INVALID      数据无效
```
401
```
LOGIN_REQUIRED    登录错误
```
403
```
PERMISSION_DENIED 权限不足
```
404
```
DATA_NOT_FOUND    数据不存在
```
500
```
INTERNAL_SERVER_ERROR 服务器内部错误
```

## 返回结果
针对不同操作，服务器向用户返回的结果应该符合以下规范。
```
GET /collection：返回资源对象的列表（数组）
GET /collection/resource：返回单个资源对象
POST /collection：返回新生成的资源对象
PUT /collection/resource：返回完整的资源对象
PATCH /collection/resource：返回完整的资源对象
DELETE /collection/resource：返回一个空文档
```

## 返回 JSON 格式数据
返回错误
```js
{
  "error": {
    "code": "ARTICLES/NOT_FOUND",
    "message": "文章不存在"
  },
  "data": null
}
```

返回成功
```js
{
  "error": null,
  "data": {
    "id": 11,
    "title": "文章标题",
    "content": "文章内容",
    "author": "作者",
    "created_at": 1475855919,
    "updated_at": 1475855919
  }
}
```

返回分页数据
```js
{
  "error": null,
  "data": {
    "total": 120,
    "items": [
      {
        "title": "标题一",
        "content": "内容一"
      },
      {
        "title": "标题二",
        "content": "内容二"
      }
    ]
  }
}
```
