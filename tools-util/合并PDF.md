itext依赖

```xml
     <dependency>
            <groupId>com.itextpdf</groupId>
            <artifactId>itextpdf</artifactId>
            <version>5.5.9</version>
        </dependency>
        <dependency>
            <groupId>com.itextpdf</groupId>
            <artifactId>itext-asian</artifactId>
            <version>5.2.0</version>
        </dependency>
```

```java

import com.itextpdf.text.Document;
import com.itextpdf.text.pdf.PdfImportedPage;
import com.itextpdf.text.pdf.PdfReader;
import com.itextpdf.text.pdf.PdfSmartCopy;
import lombok.extern.slf4j.Slf4j;

import java.io.ByteArrayOutputStream;
import java.io.IOException;
import java.nio.file.Files;
import java.nio.file.Path;
import java.nio.file.Paths;
import java.util.ArrayList;
import java.util.List;

/**
 * 测试合并PDF
 *
 * @Author liguangcheng
 * @Date 2022/8/6 11:03
 * @Vision 1.0
 **/
@Slf4j
public class PDFMergeTest {

    public static void main(String[] args) throws IOException {
        byte[] bFile = Files.readAllBytes(Paths.get("test-副本.pdf"));
        byte[] bFile1 = Files.readAllBytes(Paths.get("test-副本1.pdf"));
        List<byte[]> files = new ArrayList<byte[]>() {
            {
                add(bFile);
                add(bFile1);
            }
        };
        byte[] pdfFiles = mergePdfFiles(files);
        Path path = Paths.get("test-合并副本.pdf");
        Files.write(path, pdfFiles);

    }

    public static byte[] mergePdfFiles(List<byte[]> files) {
        try (ByteArrayOutputStream arrayOutputStream = new ByteArrayOutputStream()) {
            Document document = new Document(new PdfReader(files.get(0)).getPageSize(1));
            PdfSmartCopy copy = new PdfSmartCopy(document, arrayOutputStream);
            document.open();
            for (int i = 0; i < files.size(); i++) {
                PdfReader reader = new PdfReader(files.get(i));
                int n = reader.getNumberOfPages();
                for (int j = 1; j <= n; j++) {
                    document.newPage();
                    PdfImportedPage page = copy.getImportedPage(reader, j);
                    copy.addPage(page);
                }
            }
            document.close();
            return arrayOutputStream.toByteArray();
        } catch (Exception e) {
            log.info("合成PDF失败-->{}", e.getMessage());
            return null;
        }

    }
}

```

