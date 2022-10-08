OrderHive平台对接 获取签名

测试获取签名后添加订阅

```java
import lombok.AllArgsConstructor;
import lombok.Data;
import okhttp3.OkHttpClient;
import okhttp3.Request;
import okhttp3.RequestBody;
import okhttp3.Response;

import javax.validation.constraints.NotBlank;
import java.text.DateFormat;
import java.text.SimpleDateFormat;
import java.util.*;

/**
 * @Author liguangcheng
 * @Date 2022/9/12 09:48
 * @Vision 1.0
 **/
public class test {
    private static final okhttp3.MediaType JSON = okhttp3.MediaType.parse("application/json; charset=utf-8");
    private static final String orderhiveHost = "api.orderhive.com";
    private static final String orderhiveServiceName = "execute-api";
    //平台提供的 ID Token / Refresh Token /App Id
    public static final String ID_TOKEN = "idToken";
    public static final String REFRESH_TOKEN = "refreshToken";
    public static final String APP_ID = "appId";
    //平台回调接口地址
    public static final String WEBHOOKS_URL = "平台回调接口地址";

    public static void main(String[] args)throws Exception {
        //需要先Refresh Token POST https://api.orderhive.com/setup/refreshtokenviaidtoken
        //得到access_key_id  / secret_key / session_token  / region
        //需要注意Refresh Token得到的数据是有效期45分钟 且30内不能重复获取 否则或报错 Tokens data that is received by Refresh Token API is valid upto 30 minutes please try after ...
        RefreshTokenRequest refreshTokenRequest = new RefreshTokenRequest(ID_TOKEN, REFRESH_TOKEN);
        String requestJson = JsonUtils.deserializer(refreshTokenRequest); 
        String url = "https://api.orderhive.com/setup/refreshtokenviaidtoken";
        OkHttpClient okHttpClient = new OkHttpClient();
        Request tokenRequest = new Request.Builder().url(url).post(RequestBody.create(requestJson, JSON))
                .addHeader("Content-Type", "application/json")
                .build();
        Response response = okHttpClient.newCall(tokenRequest).execute();
        String responseStr = response.body().string();
        RefreshTokenResponse tokenResponse = JsonUtils.serializable(responseStr, RefreshTokenResponse.class);

        //通过得到的access_key_id  / secret_key / session_token  / region 加上请求方法 请求地址 请求参数 获取签名
        Date now = new Date();
        String method = "POST";
        String uri = "/setup/subscription/add";
        SubscriptionRequest orderTagged = new SubscriptionRequest(APP_ID, WEBHOOKS_URL, Arrays.asList("orderTagged"));
        String addSubscriptionJson = JsonUtils.deserializer(orderTagged);
        String authorization = getAuthorization(tokenResponse, now, addSubscriptionJson, uri, method, null);
        //得到签名后去请求
        String result = performOrderhiveRequest(tokenResponse, now, uri, authorization, method, requestJson);
    }
  
  //获取签名
    private static String getAuthorization(RefreshTokenResponse tokenResponse, Date now, String requestJson, String uri, String method, TreeMap<String, String> queryParameters) {

        //AWS4-HMAC-SHA256 Credential=ASIAJCOK74M5JWLWMCDQ/20171213/ap-southeast-1/execute-api/aws4_request, SignedHeaders=content-type;host;id_token;x-amz-date;x-amz-security-token, Signature=8e3dbc663f97508406c4825b74a647765022ae021fa224754701722b7bcf2288

        String accessKeyId = tokenResponse.getAccessKeyId();
        String secretKey = tokenResponse.getSecretKey();
        String sessionToken = tokenResponse.getSessionToken();
        String region = tokenResponse.getRegion();
        String idToken = tokenResponse.getIdToken();
        String xAmzDate = getTimeStamp(now);
        TreeMap<String, String> awsHeaders = new TreeMap<String, String>();
        awsHeaders.put("content-type", JSON.toString());
        awsHeaders.put("host", orderhiveHost);
        awsHeaders.put("id_token", idToken);
        awsHeaders.put("x-amz-date", xAmzDate);
        awsHeaders.put("x-amz-security-token", sessionToken);
        Map<String, String> headers = new AWSV4Auth.Builder(accessKeyId, secretKey)
                .regionName(region)
                .serviceName(orderhiveServiceName)
                .httpMethodName(method)
                .canonicalURI(uri)
                .now(now)
                .queryParametes(queryParameters)
                .awsHeaders(awsHeaders)
                .payload(requestJson)
                .debug()
                .build()
                .getHeaders();

        String authorization = headers.get("Authorization");
        return authorization;
    }
  
  //请求平台
    private static String performOrderhiveRequest(RefreshTokenResponse tokenResponse, Date now, String uri, String authorization, String method, String requestJson) throws Exception {
        OkHttpClient okHttpClient = new OkHttpClient();
        Request.Builder builder = new Request.Builder().url("https://" + orderhiveHost + uri)
                // .post(RequestBody.create(requestJson, JSON))
                .addHeader("Content-Type", JSON.toString())
                .addHeader("host", orderhiveHost)
                .addHeader("id_token", tokenResponse.getIdToken())
                .addHeader("x-amz-date", getTimeStamp(now))
                .addHeader("x-amz-security-token", tokenResponse.getSessionToken())
                .addHeader("Authorization", authorization)
                .addHeader("Accept", "application/json");
        Request build;
        switch (method) {
            case "GET":
                build = builder
                        .get()
                        .build();
                break;
            case "POST":
                build = builder
                        .post(RequestBody.create(requestJson, JSON))
                        .build();
                break;
            case "PUT":
                build = builder
                        .put(RequestBody.create(requestJson, JSON))
                        .build();
                break;
            default:
                throw new Exception("Unknown method called");
        }
        Response execute = okHttpClient.newCall(build).execute();
        String string = execute.body().string();
        return string;
    }


    private static String getTimeStamp(Date date) {
        DateFormat dateFormat = new SimpleDateFormat("yyyyMMdd'T'HHmmss'Z'");
        dateFormat.setTimeZone(TimeZone.getTimeZone("UTC"));//server timezone
        return dateFormat.format(date);
    }

    @AllArgsConstructor
    @Data
    public static class RefreshTokenRequest {
        @JsonProperty("id_token")
        private String idToken;
        @JsonProperty("refresh_token")
        private String refreshToken;
    }

    @AllArgsConstructor
    @Data
    public static class RefreshTokenResponse {
        @NotBlank
        @JsonProperty("access_key_id")
        private String accessKeyId;
        @NotBlank
        @JsonProperty("secret_key")
        private String secretKey;
        @NotBlank
        @JsonProperty("session_token")
        private String sessionToken;
        @NotBlank
        @JsonProperty("id_token")
        private String idToken;
        @NotBlank
        @JsonProperty("refresh_token")
        private String refreshToken;
        @NotBlank
        @JsonProperty("region")
        private String region;
    }
}
```

签名工具类

```java
import javax.crypto.Mac;
import javax.crypto.spec.SecretKeySpec;
import java.io.UnsupportedEncodingException;
import java.net.URLEncoder;
import java.security.MessageDigest;
import java.security.NoSuchAlgorithmException;
import java.text.DateFormat;
import java.text.SimpleDateFormat;
import java.util.*;

/**
 * AWSV4Auth
 *
 * @Author liguangcheng
 * @Date 2022/8/19 10:36
 * @Vision 1.0
 **/
public class AWSV4Auth {
    private AWSV4Auth() {
    }

    public static class Builder {
        private Date now;
        private String accessKeyID;
        private String secretAccessKey;
        private String regionName;
        private String serviceName;
        private String httpMethodName;
        private String canonicalURI;
        private TreeMap<String, String> queryParametes;
        private TreeMap<String, String> awsHeaders;
        private String payload;
        private boolean debug = false;

        public Builder(String accessKeyID, String secretAccessKey) {
            this.accessKeyID = accessKeyID;
            this.secretAccessKey = secretAccessKey;
        }

        public Builder now(Date now) {
            this.now = now;
            return this;
        }

        public Builder regionName(String regionName) {
            this.regionName = regionName;
            return this;
        }

        public Builder serviceName(String serviceName) {
            this.serviceName = serviceName;
            return this;
        }

        public Builder httpMethodName(String httpMethodName) {
            this.httpMethodName = httpMethodName;
            return this;
        }

        public Builder canonicalURI(String canonicalURI) {
            this.canonicalURI = canonicalURI;
            return this;
        }

        public Builder queryParametes(TreeMap<String, String> queryParametes) {
            this.queryParametes = queryParametes;
            return this;
        }

        public Builder awsHeaders(TreeMap<String, String> awsHeaders) {
            this.awsHeaders = awsHeaders;
            return this;
        }

        public Builder payload(String payload) {
            this.payload = payload;
            return this;
        }

        public Builder debug() {
            this.debug = false;
            return this;
        }

        public AWSV4Auth build() {
            return new AWSV4Auth(this);
        }
    }

    private Date now;
    private String accessKeyID;
    private String secretAccessKey;
    private String regionName;
    private String serviceName;
    private String httpMethodName;
    private String canonicalURI;
    private TreeMap<String, String> queryParametes;
    private TreeMap<String, String> awsHeaders;
    private String payload;
    private boolean debug = false;

    /* Other variables */
    private final String HMACAlgorithm = "AWS4-HMAC-SHA256";
    private final String aws4Request = "aws4_request";
    private String strSignedHeader;
    private String xAmzDate;
    private String currentDate;

    private AWSV4Auth(Builder builder) {
        now = builder.now;
        accessKeyID = builder.accessKeyID;
        secretAccessKey = builder.secretAccessKey;
        regionName = builder.regionName;
        serviceName = builder.serviceName;
        httpMethodName = builder.httpMethodName;
        canonicalURI = builder.canonicalURI;
        queryParametes = builder.queryParametes;
        awsHeaders = builder.awsHeaders;
        payload = builder.payload;
        debug = builder.debug;

        /* Get current timestamp value.(UTC) */
        xAmzDate = getTimeStamp();
        currentDate = getDate();
    }

    /**
     * 任务 1：针对签名版本 4 创建规范请求
     *
     * @return
     */
    private String prepareCanonicalRequest() {
        StringBuilder canonicalURL = new StringBuilder("");

        /* Step 1.1 以HTTP方法(GET, PUT, POST, etc.)开头, 然后换行. */
        canonicalURL.append(httpMethodName).append("\n");

        /* Step 1.2 添加URI参数，换行. */
        canonicalURI = canonicalURI == null || canonicalURI.trim().isEmpty() ? "/" : canonicalURI;
        if (canonicalURI.contains("?")) {
            int i = canonicalURI.indexOf("?");
            canonicalURI = canonicalURI.substring(0, i);
        }
        canonicalURL.append(canonicalURI).append("\n");

        /* Step 1.3 添加查询参数，换行. */
        StringBuilder queryString = new StringBuilder("");
        if (queryParametes != null && !queryParametes.isEmpty()) {
            for (Map.Entry<String, String> entrySet : queryParametes.entrySet()) {
                String key = entrySet.getKey();
                String value = entrySet.getValue();
                queryString.append(key).append("=").append(encodeParameter(value)).append("&");
            }

            queryString.deleteCharAt(queryString.lastIndexOf("&"));

            queryString.append("\n");
        } else {
            queryString.append("\n");
        }
        canonicalURL.append(queryString);

        /* Step 1.4 添加headers, 每个header都需要换行. */
        StringBuilder signedHeaders = new StringBuilder("");
        if (awsHeaders != null && !awsHeaders.isEmpty()) {
            for (Map.Entry<String, String> entrySet : awsHeaders.entrySet()) {
                String key = entrySet.getKey();
                String value = entrySet.getValue();
                signedHeaders.append(key).append(";");
                canonicalURL.append(key).append(":").append(value).append("\n");
            }
            canonicalURL.append("\n");
        } else {
            canonicalURL.append("\n");
        }

        /* Step 1.5 添加签名的headers并换行. */
        strSignedHeader = signedHeaders.substring(0, signedHeaders.length() - 1); // 删掉最后的 ";"
        canonicalURL.append(strSignedHeader).append("\n");

        /* Step 1.6 对HTTP或HTTPS的body进行SHA256处理. */
        payload = payload == null ? "" : payload;
        canonicalURL.append(generateHex(payload));

        if (debug) {
            System.out.println("##Canonical Request:\n" + canonicalURL.toString());
        }

        return canonicalURL.toString();
    }

    /**
     * 任务 2：创建签名版本 4 的待签字符串
     *
     * @param canonicalURL
     * @return
     */
    private String prepareStringToSign(String canonicalURL) {
        String stringToSign = "";

        /* Step 2.1 以算法名称开头，并换行. */
        stringToSign = HMACAlgorithm + "\n";

        /* Step 2.2 添加日期，并换行. */
        stringToSign += xAmzDate + "\n";

        /* Step 2.3 添加认证范围，并换行. */
        stringToSign += currentDate + "/" + regionName + "/" + serviceName + "/" + aws4Request + "\n";

        /* Step 2.4 添加任务1返回的规范URL哈希处理结果，然后换行. */
        stringToSign += generateHex(canonicalURL);

        if (debug) {
            System.out.println("##String to sign:\n" + stringToSign);
        }

        return stringToSign;
    }

    /**
     * 任务 3：为 AWS Signature 版本 4 计算签名
     *
     * @param stringToSign
     * @return
     */
    private String calculateSignature(String stringToSign) {
        try {
            /* Step 3.1 生成签名的key */
            byte[] signatureKey = getSignatureKey(secretAccessKey, currentDate, regionName, serviceName);

            /* Step 3.2 计算签名. */
            byte[] signature = HmacSHA256(signatureKey, stringToSign);

            /* Step 3.2.1 对签名编码处理 */
            String strHexSignature = bytesToHex(signature);
            return strHexSignature;
        } catch (Exception ex) {
            ex.printStackTrace();
        }
        return null;
    }

    /**
     * 任务 4：将签名信息添加到请求并返回headers
     *
     * @return
     */
    public Map<String, String> getHeaders() {
        awsHeaders.put("x-amz-date", xAmzDate);

        /* 执行任务 1: 创建aws v4签名的规范请求字符串. */
        String canonicalURL = prepareCanonicalRequest();

        /* 执行任务 2: 创建用来认证的字符串 4. */
        String stringToSign = prepareStringToSign(canonicalURL);

        /* 执行任务 3: 计算签名. */
        String signature = calculateSignature(stringToSign);

        if (signature != null) {
            Map<String, String> header = new HashMap<String, String>(0);
            header.put("x-amz-date", xAmzDate);
            header.put("Authorization", buildAuthorizationString(signature));

            if (debug) {
                System.out.println("##Signature:\n" + signature);
                System.out.println("##Header:");
                for (Map.Entry<String, String> entrySet : header.entrySet()) {
                    System.out.println(entrySet.getKey() + " = " + entrySet.getValue());
                }
                System.out.println("================================");
            }
            return header;
        } else {
            if (debug) {
                System.out.println("##Signature:\n" + signature);
            }
            return null;
        }
    }

    /**
     * 连接前几步处理的字符串生成Authorization header值.
     *
     * @param strSignature
     * @return
     */
    private String buildAuthorizationString(String strSignature) {
        return HMACAlgorithm + " "
                + "Credential=" + accessKeyID + "/" + getDate() + "/" + regionName + "/" + serviceName + "/" + aws4Request + ", "
                + "SignedHeaders=" + strSignedHeader + ", "
                + "Signature=" + strSignature;
    }

    /**
     * 将字符串16进制化.
     *
     * @param data
     * @return
     */
    private String generateHex(String data) {
        MessageDigest messageDigest;
        try {
            messageDigest = MessageDigest.getInstance("SHA-256");
            messageDigest.update(data.getBytes("UTF-8"));
            byte[] digest = messageDigest.digest();
            return String.format("%064x", new java.math.BigInteger(1, digest));
        } catch (NoSuchAlgorithmException | UnsupportedEncodingException e) {
            e.printStackTrace();
        }
        return null;
    }

    /**
     * 以给定的key应用HmacSHA256算法处理数据.
     *
     * @param data
     * @param key
     * @return
     * @throws Exception
     * @reference: http://docs.aws.amazon.com/general/latest/gr/signature-v4-examples.html#signature-v4-examples-java
     */
    private byte[] HmacSHA256(byte[] key, String data) throws Exception {
        String algorithm = "HmacSHA256";
        Mac mac = Mac.getInstance(algorithm);
        mac.init(new SecretKeySpec(key, algorithm));
        return mac.doFinal(data.getBytes("UTF8"));
    }

    /**
     * 生成AWS 签名
     *
     * @param key
     * @param date
     * @param regionName
     * @param serviceName
     * @return
     * @throws Exception
     * @reference http://docs.aws.amazon.com/general/latest/gr/signature-v4-examples.html#signature-v4-examples-java
     */
    private byte[] getSignatureKey(String key, String date, String regionName, String serviceName) throws Exception {
        byte[] kSecret = ("AWS4" + key).getBytes("UTF8");
        byte[] kDate = HmacSHA256(kSecret, date);
        byte[] kRegion = HmacSHA256(kDate, regionName);
        byte[] kService = HmacSHA256(kRegion, serviceName);
        byte[] kSigning = HmacSHA256(kService, aws4Request);
        return kSigning;
    }

    final protected static char[] hexArray = "0123456789ABCDEF".toCharArray();

    /**
     * 将字节数组转换为16进制字符串
     *
     * @param bytes
     * @return
     */
    private String bytesToHex(byte[] bytes) {
        char[] hexChars = new char[bytes.length * 2];
        for (int j = 0; j < bytes.length; j++) {
            int v = bytes[j] & 0xFF;
            hexChars[j * 2] = hexArray[v >>> 4];
            hexChars[j * 2 + 1] = hexArray[v & 0x0F];
        }
        return new String(hexChars).toLowerCase();
    }

    /**
     * 获取yyyyMMdd'T'HHmmss'Z'格式的当前时间
     *
     * @return
     */
    private String getTimeStamp() {
        DateFormat dateFormat = new SimpleDateFormat("yyyyMMdd'T'HHmmss'Z'");
        dateFormat.setTimeZone(TimeZone.getTimeZone("UTC"));//server timezone
        return dateFormat.format(now);
    }

    /**
     * 获取yyyyMMdd格式的当前日期
     *
     * @return
     */
    private String getDate() {
        DateFormat dateFormat = new SimpleDateFormat("yyyyMMdd");
        dateFormat.setTimeZone(TimeZone.getTimeZone("UTC"));//server timezone
        return dateFormat.format(now);
    }

    /**
     * UTF-8编码
     *
     * @param param
     * @return
     */
    private String encodeParameter(String param) {
        try {
            return URLEncoder.encode(param, "UTF-8");
        } catch (Exception e) {
            return URLEncoder.encode(param);
        }
    }


}


```

参考博客https://www.jianshu.com/p/66338d20b4ea
orderhive文档https://orderhive.docs.apiary.io/#reference