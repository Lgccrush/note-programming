读取src/resource/templates 文件

![image-20220813164638987](https://raw.githubusercontent.com/Lgccrush/uppic/master/uPic/2022/08/13/16:46:39_image-20220813164638987.png)

```java
import org.apache.poi.util.IOUtils;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.core.io.Resource;
import org.springframework.core.io.ResourceLoader;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

import javax.servlet.http.HttpServletResponse;
import java.io.InputStream;
import java.io.OutputStream;
import java.util.Base64;

/**
 * resource test
 *
 * @Author liguangcheng
 * @Date 2022/8/13 16:44
 * @Vision 1.0
 **/
@RestController
public class ResourceController {
    @Autowired
    private ResourceLoader resourceLoader;

    @GetMapping("resourceTest/toBase")
    public ResponseEntity<String> resourceTestToBase64() throws Exception {
        Resource resource = resourceLoader.getResource("classpath:templates/test.pdf");
        InputStream inputStream = resource.getInputStream();
        byte[] bytes = IOUtils.toByteArray(inputStream);
        String encode = Base64.getEncoder().encodeToString(bytes);
        ResponseEntity<String> response = ResponseEntity.status(HttpStatus.CREATED).body(encode);
        return response;
    }

    @GetMapping("resourceTest")
    public void resourceTest(HttpServletResponse response) throws Exception {
        Resource resource = resourceLoader.getResource("classpath:templates/test.pdf");
        InputStream inputStream = resource.getInputStream();
        byte[] bytes = IOUtils.toByteArray(inputStream);
        response.setContentType("application/pdf");
        response.setHeader("Content-Disposition", "attachment;filename=test.pdf");
        OutputStream os = response.getOutputStream();
        os.write(bytes);
    }

}
```

