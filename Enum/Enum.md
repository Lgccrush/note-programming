####  遍历 匹配code得到value

```java

@Getter
@AllArgsConstructor
public enum SFEnum {

    PU("origin_sc_inbound_success", "208");

    private String scanCode;
    private String opCode;

    public String getScanCode() {
        return scanCode;
    }

    public String getOpCode() {
        return opCode;
    }

    public static String getOpCode(String code) {
        if (StrUtil.isBlank(code)) {
            return null;
        }
        for (SFEnum sfEnum : values()) {
            if (code.equals(sfEnum.getScanCode())) {
                return sfEnum.getOpCode();
            }
        }
        return null;
    }
}
```



​	