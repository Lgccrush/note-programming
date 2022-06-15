**目的 把list集合中对象里面的多个string类型数据放在一个集合中**

**测试类**

```java
import com.example.springbootdockertest.controller.pay.WxMssDTO;

import java.util.ArrayList;
import java.util.Collection;
import java.util.List;
import java.util.stream.Collectors;
import java.util.stream.Stream;

/**
 * strean测试
 *
 * @Author liguangcheng
 * @Date 2022/6/3 09:31
 * @Vision 1.0
 **/
public class StreamTest {
    public static void main(String[] args) {
        List<String> testList = new ArrayList();
        testList.add("d");
        testList.add("c");
        List<String> list1 = new ArrayList();
        list1.add("a");
        list1.add("b");

        WxMssDTO wxMssDTO = new WxMssDTO();
        wxMssDTO.setPage("2");
        wxMssDTO.setTouser("4");
        wxMssDTO.setTestList(testList);
        WxMssDTO wxMssDTO1 = new WxMssDTO();
        wxMssDTO1.setPage("3");
        wxMssDTO1.setTouser("5");
        wxMssDTO1.setTestList(list1);
        List<WxMssDTO> wxMssDTOList = new ArrayList();
        wxMssDTOList.add(wxMssDTO1);
        wxMssDTOList.add(wxMssDTO);

        //把wxMssDTOList中每个WxMssDTO中的test集合中的元素放在一起
        List<String> testLists = wxMssDTOList.stream().map(WxMssDTO::getTestList).flatMap(Collection::stream).collect(Collectors.toList());
        System.out.println("所以test集合中的元素==>" + testLists);

        //把wxMssDTOList中每个WxMssDTO中的Page和touser放在一起
        List<String> pageAndTouserList = wxMssDTOList.stream().flatMap(s -> {
            String page = s.getPage();
            String touser = s.getTouser();
            List<String> strings = new ArrayList();
            strings.add(page);
            strings.add(touser);
            // 将每个元素转换成一个stream
            Stream<String> stream = strings.stream();
            return stream;
        }).collect(Collectors.toList());
        System.out.println("所有的Page和touser==>" + pageAndTouserList);
    }
}

```

**打印结果**

```java
所有test集合中的元素==>[a, b, d, c]
所有的Page和touser==>[3, 5, 2, 4]
```



**测试实体**

```java

import lombok.Getter;
import lombok.Setter;

import java.util.List;
import java.util.Map;

/**
 * 小程序推送所需数据
 *
 * @Author liguangcheng
 * @Date 2022/5/25 10:59
 * @Vision 1.0
 **/
@Getter
@Setter
public class WxMssDTO {
    /**
     * 用户openid
     */
    private String touser;
    /**
     * 模版id
     */
    private String template_id;
    /**
     * 跳转到小程序页面路径
     */
    private String page;
    /**
     * 收集到的用户formid
     */
    private String form_id;
    /**
     * 放大那个推送字段
     */
    private String emphasis_keyword = "keyword1.DATA";
    private String miniprogram_state = "formal";
    private Map<String, TemplateData> data;//推送文字
    private List<String> test;

    @Getter
    @Setter
    public static class TemplateData {
        //keyword1：订单类型，keyword2：下单金额，keyword3：配送地址，keyword4：取件地址，keyword5备注
        private String value;//,,依次排下去
    }
}

```

