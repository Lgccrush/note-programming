#### Http 请求

#### hutool

- GET

```java
//没有请求头
String string = HttpUtil.get("https://spider.tec-api.com/provider");//调用spider接口获取末端供应商

//有请求头
String result = HttpUtil.createGet("url")
                        .header("Authorization", "Bearer test123token")
                        .execute()
                        .body();
```



- POST

```java
  String result = HttpRequest.post(url_).//url
                    body(json, ContentType.JSON.toString()).//json格式
                    header(key, token).//请求头
                    timeout(timeOut).//超时时间
                    execute().
                    body();
```