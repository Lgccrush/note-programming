#### 合并json

```java
import cn.hutool.json.JSONObject;
import cn.hutool.json.JSONUtil;

/**
 * JSON测试
 *
 * @Author liguangcheng
 * @Date 2022/1/15 5:10 下午
 * @Vision 1.0
 **/
public class JsonController {
    // 合并json
    public static void main(String[] args) {
        String jsonOne = "{ \"sys\": \"testSys\", \"testSign\": \"testSign\"}";
        String jsonTwo = "{ \"Code\": \"testCode\", \"sign\": \"sign\"}";
        JSONObject jsonObject = JSONUtil.parseObj(jsonOne);
        JSONObject extJsonObject = JSONUtil.parseObj(jsonTwo);
        JSONObject jsonThree = new JSONObject();
        jsonThree.putAll(jsonObject);
        jsonThree.putAll(extJsonObject);
        System.out.println(jsonThree);
    }
}
```

![image-20220115171905465](https://raw.githubusercontent.com/Lgccrush/uppic/master/uPic/2022/01/15/17:19:05_image-20220115171905465.png)