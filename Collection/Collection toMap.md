### toMap

#### key相同的时候报错

```java
@Accessors(chain = true)
@Data
public class UserTest {
    private String id;
    private String name;
    public static void main(String[] args) {
             List<UserTest> userTestList2 = Lists.newArrayList(
                new UserTest().setId("A").setName("张三"),
                new UserTest().setId("A").setName("李四"),
                new UserTest().setId("C").setName("王五")
        );
        System.out.println("userTestList2-->" + userTestList2);
        Map<String, List<UserTest>> map = userTestList2.stream().collect(
                Collectors.toMap(
                        UserTest::getId,//key
                        item -> Lists.newArrayList(item)//value
                )
        );
        System.out.println(map);
      }
}
```



![image-20211019170348283](https://raw.githubusercontent.com/Lgccrush/uppic/master/uPic/2021/10/19/17:03:48_image-20211019170348283.png)

解决 使用第三个参数

```java
@Accessors(chain = true)
@Data
public class UserTest {
    private String id;
    private String name;
    public static void main(String[] args) {
            List<UserTest> userTestList2 = Lists.newArrayList(
                new UserTest().setId("A").setName("张三"),
                new UserTest().setId("A").setName("李四"),
                new UserTest().setId("C").setName("王五")
        );
        System.out.println("userTestList2-->" + userTestList2);
        Map<String, List<UserTest>> map = userTestList2.stream().collect(
                Collectors.toMap(
                        UserTest::getId,//key
                        item -> Lists.newArrayList(item),//value
                        (n1, n2) -> (List) CollectionUtils.union(n1, n2)//当key重复时执行
                )
        );
        System.out.println(map);
         }
}
```

![image-20211019172145457](https://raw.githubusercontent.com/Lgccrush/uppic/master/uPic/2021/10/19/17:21:45_image-20211019172145457.png)