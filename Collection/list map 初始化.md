

### List

```java
@Accessors(chain = true)
@Data
public class UserTest {
    private String id;
    private String name;
    public static void main(String[] args) {
        UserTest userTest = new UserTest();
        userTest.setId("A");
        userTest.setName("张三");
        UserTest userTest1 = new UserTest();
        userTest1.setId("A");
        userTest1.setName("李四");
        UserTest userTest2 = new UserTest();
        userTest2.setId("C");
        userTest2.setName("王五");
        List<UserTest> userTestList = new ArrayList<>();
        userTestList.add(userTest);
        userTestList.add(userTest1);
        userTestList.add(userTest2);
        System.out.println("userTestList1-->" + userTestList);
        List<UserTest> userTestList1 = new ArrayList<UserTest>() {
            {
                add(userTest);
                add(userTest1);
                add(userTest2);
            }
        };
        System.out.println("userTestList1-->" + userTestList1);
        List<UserTest> userTestList2 = Lists.newArrayList(
                new UserTest().setId("A").setName("张三"),
                new UserTest().setId("A").setName("李四"),
                new UserTest().setId("C").setName("王五")
        );
        System.out.println("userTestList2-->" + userTestList2);
    }
```

![image-20211019165418186](https://raw.githubusercontent.com/Lgccrush/uppic/master/uPic/2021/10/19/16:54:18_image-20211019165418186.png)

### Map

```java 
    HashMap hashMap = new HashMap() {
            {
                put("a", "张三");
                put("b", "李四");
                put("c", "王五");
            }
        };
     System.out.println(hashMap);
```

![image-20211019172548095](https://raw.githubusercontent.com/Lgccrush/uppic/master/uPic/2021/10/19/17:25:48_image-20211019172548095.png)

