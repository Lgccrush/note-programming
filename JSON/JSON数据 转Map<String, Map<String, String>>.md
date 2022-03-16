### json数据转换

#### json数据 转Map<String, Map<String, String>>

```json


{
  "dhlexpress": {
    "name": "DHL Express"
  },
  "skynet": {
    "name": "Skynet"
  },
  "sf": {
    "name": "SF"
  }
}
```

```java
import com.alibaba.fastjson.JSONObject;

Map<String, Map<String, String>> json = JSONObject.parseObject(res, Map.class);
```