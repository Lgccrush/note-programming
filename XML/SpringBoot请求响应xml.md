#### 使用xml请求 并响应xml

##### 需要使用jackson

**Maven**

```xml
    <dependency>
            <groupId>com.fasterxml.jackson.dataformat</groupId>
            <artifactId>jackson-dataformat-xml</artifactId>
            <version>2.11.4</version>
        </dependency>
```

**Gradle**

```properties
 compile group: 'com.fasterxml.jackson.dataformat', name: 'jackson-dataformat-xml', version: '2.13.1'
```

##### 简单返回

**controller**

```java
@Controller
public class XMLController {
    //consumes:指定指定处理请求的提交内容类型
    //produces:指定返回的内容类型 produces = {"text/plain;charset=UTF-8"}解决返回乱码
    @PostMapping(value = "/test/xml", consumes = {MediaType.APPLICATION_XML_VALUE, MediaType.TEXT_XML_VALUE}, produces = MediaType.APPLICATION_XML_VALUE)
    @ResponseBody
    public ResponseEntity testXML(@RequestBody String request) throws Exception {
       //解析xml请求数据
        Document requestDoc = DocumentHelper.parseText(request);
        Element requestRoot = requestDoc.getRootElement();// 指向根节点
        Iterator it = requestRoot.elementIterator();
        while (it.hasNext()) {
            Element element = (Element) it.next();// 一个Item节点
            String name = element.getName();
            if (Objects.equals("Validation", name)) {
                Element userIDElement = element.element("UserID");
                System.out.println("userId = " + userIDElement.getTextTrim());
                Element passwordElement = element.element("Password");
                System.out.println("password = " + passwordElement.getTextTrim());
            }
            if (Objects.equals("TrackingNumber", name)) {
                String trackingNumber = element.getTextTrim();
                System.out.println("TrackingNumber" + trackingNumber);
            }
        }
      	//响应
        XMLResponse xmlResponse = new XMLResponse();
        xmlResponse.setName("name");
        xmlResponse.setScore("score");
        xmlResponse.setAge(1);
        ResponseEntity stringResponseEntity = ResponseEntity.status(HttpStatus.OK).body(xmlResponse);
        return stringResponseEntity;
    }
}
```

**实体**

```Java
import com.fasterxml.jackson.annotation.JsonProperty;
import com.fasterxml.jackson.annotation.JsonRootName;
import lombok.Getter;
import lombok.Setter;

/**
 * xml响应
 *
 * @Author liguangcheng
 * @Date 2022/1/15 2:39 下午
 * @Vision 1.0
 **/
@Getter
@Setter
@JsonRootName("response")//根节点
public class XMLResponse {
    @JsonProperty(value = "name")
    private String name;
    @JsonProperty(value = "age")
    private Integer age;
    @JsonProperty(value = "score")
    private String score;
}
```

![image-20220115162525167](https://raw.githubusercontent.com/Lgccrush/uppic/master/uPic/2022/01/15/16:25:25_image-20220115162525167.png)

**响应**

![image-20220115162540516](https://raw.githubusercontent.com/Lgccrush/uppic/master/uPic/2022/01/15/16:25:40_image-20220115162540516.png)

##### 实体有集合的情况

```java
import com.fasterxml.jackson.annotation.JsonProperty;
import com.fasterxml.jackson.annotation.JsonRootName;
import lombok.Getter;
import lombok.Setter;

/**
 * xml响应
 *
 * @Author liguangcheng
 * @Date 2022/1/15 2:39 下午
 * @Vision 1.0
 **/
@Getter
@Setter
@JsonRootName("response")//根节点
public class XMLResponse {
    @JsonProperty(value = "name")
    private String name;
    @JsonProperty(value = "age")
    private Integer age;
    @JsonProperty(value = "score")
    private String score;
    @JsonProperty(value = "xmlResposeList")
    private List<XMLResponseList> xmlResponseLists;
}
```

```java
 				ArrayList<XMLResponseList> xmlResponseLists = Lists.newArrayList();
        XMLResponseList xmlResponseList = new XMLResponseList();
        xmlResponseList.setResponseAge("Age");
        xmlResponseList.setResponseName("name");
        xmlResponseLists.add(xmlResponseList);
        xmlResponseLists.add(xmlResponseList);
        xmlResponse.setXmlResponseLists(xmlResponseLists);
```

**响应**

![image-20220115163257548](https://raw.githubusercontent.com/Lgccrush/uppic/master/uPic/2022/01/15/16:32:57_image-20220115163257548.png)

**去除包装 在字段新增注解@JacksonXmlElementWrapper(useWrapping = false)**

```java
    @JsonProperty(value = "xmlResposeList")
    @JacksonXmlElementWrapper(useWrapping = false)
    private List<XMLResponseList> xmlResponseLists;
```

**响应**

![image-20220115164227204](https://raw.githubusercontent.com/Lgccrush/uppic/master/uPic/2022/01/15/16:42:27_image-20220115164227204.png)

**响应带有 declaration 和 namespace**

```java
				XmlMapper xmlMapper = new XmlMapper();
        String response = xmlMapper.writeValueAsString(xmlResponse);
        Document responseDoc = DocumentHelper.parseText(response);
        Element rootElement = responseDoc.getRootElement();
        rootElement.addNamespace("xsi", "http://www.w3.org/2001/XMLSchema-instance");
        ResponseEntity stringResponseEntity = ResponseEntity.status(HttpStatus.OK).body(responseDoc.asXML());
```

![image-20220115165640113](https://raw.githubusercontent.com/Lgccrush/uppic/master/uPic/2022/01/15/16:56:40_image-20220115165640113.png)

**空字段不返还 加注解@JsonInclude(JsonInclude.Include.NON_NULL)**

**实体**

```java
    @JsonProperty(value = "name")
    @JsonInclude(JsonInclude.Include.NON_NULL)
    private String name;
```

**controller**

```java
//xmlResponse.setName("name");
```

**响应**

![image-20220115170014542](https://raw.githubusercontent.com/Lgccrush/uppic/master/uPic/2022/01/15/17:00:14_image-20220115170014542.png)