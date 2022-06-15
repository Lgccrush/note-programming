**第一种方案 使用itext填充静态PDF模板 然后生成PDF新文件**

1 使用Adobe Acrobat 编辑原PDF 添加文本域

![image-20220611104008064](https://raw.githubusercontent.com/Lgccrush/uppic/master/uPic/2022/06/11/10:40:08_image-20220611104008064.png)



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

代码实现

```java
import com.itextpdf.text.pdf.AcroFields;
import com.itextpdf.text.pdf.BaseFont;
import com.itextpdf.text.pdf.PdfReader;
import com.itextpdf.text.pdf.PdfStamper;

import java.io.FileOutputStream;
import java.util.ArrayList;

/**
 * 操作PDF
 *
 * @Author liguangcheng
 * @Date 2022/6/11 09:26
 * @Vision 1.0
 **/
public class PDFTest {
    public static void main(String[] args) throws Exception {
        String filePath = "test.pdf";
        PdfReader reader = new PdfReader(filePath);
        PdfStamper stamper = new PdfStamper(reader, new FileOutputStream("test-副本.pdf"));
        //使用中文字体
        BaseFont bf = BaseFont.createFont("STSong-Light", "UniGB-UCS2-H", BaseFont.NOT_EMBEDDED);
        ArrayList<BaseFont> fontList = new ArrayList<>();
        fontList.add(bf);

        AcroFields acroFields = stamper.getAcroFields();
        acroFields.setSubstitutionFonts(fontList);

        //设置字段
        acroFields.setField("name", "测试姓名");
        acroFields.setField("age", "18");
        stamper.setFormFlattening(true);
        stamper.close();
    }
}

```

test-副本.pdf效果

![image-20220611104319882](https://raw.githubusercontent.com/Lgccrush/uppic/master/uPic/2022/06/11/10:43:20_image-20220611104319882.png)

**第二种方案 通过坐标定位 然后添加水印**

```java

import com.itextpdf.awt.geom.Rectangle2D;
import com.itextpdf.text.BaseColor;
import com.itextpdf.text.Element;
import com.itextpdf.text.pdf.BaseFont;
import com.itextpdf.text.pdf.PdfContentByte;
import com.itextpdf.text.pdf.PdfReader;
import com.itextpdf.text.pdf.PdfStamper;
import com.itextpdf.text.pdf.parser.ImageRenderInfo;
import com.itextpdf.text.pdf.parser.PdfReaderContentParser;
import com.itextpdf.text.pdf.parser.RenderListener;
import com.itextpdf.text.pdf.parser.TextRenderInfo;
import lombok.Getter;
import lombok.Setter;

import java.io.FileOutputStream;
import java.io.IOException;
import java.util.ArrayList;
import java.util.List;

/**
 * 操作PDF
 *
 * @Author liguangcheng
 * @Date 2022/6/11 09:26
 * @Vision 1.0
 **/
public class PDFTest {
    public static void main(String[] args) throws Exception {

        List<String> keywordList = new ArrayList();
        keywordList.add("测试姓名");
        keywordList.add("18");
        List<PdfItextModel> pdfItextModels = new ArrayList<>();
        for (String keyword : keywordList) {
            float[] keyWordsByPath = getKeyWords("test-副本.pdf", keyword);
            PdfItextModel pdfItextModel = new PdfItextModel();
            pdfItextModel.setContent("水印" +keyword);
            for (int i = 0; i < keyWordsByPath.length; i++) {
                pdfItextModel.setXCoordinate(keyWordsByPath[0]);//x坐标
                pdfItextModel.setYCoordinate(keyWordsByPath[1]);//y坐标
                pdfItextModel.setPageNum((int) keyWordsByPath[2]);//页数
                System.out.println(keyword + "坐标值:" + keyWordsByPath[i]);
            }
            pdfItextModels.add(pdfItextModel);
        }
        signSinglePsw("test.pdf", pdfItextModels);
    }


    /**
     * @param filepath 文件位置
     * @param keyWords 关键字
     * @return float[]
     * @Description 获取关键字所在PDF坐标
     */
    private static float[] getKeyWords(String filepath, String keyWords) {
        float[] coordinate = null;
        int page = 0;
        try {
            PdfReader pdfReader = new PdfReader(filepath);
            int pageNum = pdfReader.getNumberOfPages();
            PdfReaderContentParser pdfReaderContentParser = new PdfReaderContentParser(pdfReader);
            CustomRenderListener renderListener = new CustomRenderListener();
            renderListener.setKeyWord(keyWords);
            for (page = 1; page <= pageNum; page++) {
                renderListener.setPage(page);
                pdfReaderContentParser.processContent(page, renderListener);
                coordinate = renderListener.getPcoordinate();
                if (coordinate != null) break;
            }
        } catch (IOException e) {
            e.printStackTrace();
        }
        return coordinate;
    }


    @Getter
    @Setter
    static class CustomRenderListener implements RenderListener {

        private float[] pcoordinate = null;

        private String keyWord;

        private int page;

        @Override
        public void beginTextBlock() {
        }

        @Override
        public void endTextBlock() {
        }

        @Override
        public void renderImage(ImageRenderInfo arg0) {
        }

        @Override
        public void renderText(TextRenderInfo textRenderInfo) {
            String text = textRenderInfo.getText();
            if (null != text && text.contains(keyWord)) {
                Rectangle2D.Float boundingRectange = textRenderInfo.getBaseline().getBoundingRectange();
                pcoordinate = new float[3];
                pcoordinate[0] = boundingRectange.x;
                pcoordinate[1] = boundingRectange.y;
                pcoordinate[2] = page;
            }
        }

    }

    /**
     * @param oldPswFilePath 原来的文件地址
     * @param list           需要添加的详细信息
     * @return
     */
    public static String signSinglePsw(String oldPswFilePath, List<PdfItextModel> list) throws Exception {

        int lastIndex = oldPswFilePath.lastIndexOf('.');
        // 获取文件后缀
        String suffix = oldPswFilePath.substring(lastIndex + 1);
        // 判断是否为pdf文件
        if (!"pdf".equalsIgnoreCase(suffix)) {
            throw new RuntimeException("Not is PDF file");
        }

        // 生成新的文件路径
        String newPswPath = oldPswFilePath.substring(0, lastIndex) + "-副本1." + suffix;
        System.out.println("单个psw文件签名生成的新路径：" + newPswPath);

        //解析文件
        PdfReader reader = new PdfReader(oldPswFilePath);
        FileOutputStream fOut = new FileOutputStream(newPswPath);
        PdfStamper stp = new PdfStamper(reader, fOut);

        // 总页数
        System.out.println("PDF总页数：" + reader.getNumberOfPages());

        for (PdfItextModel model : list) {
            Float xCoordinate = model.getXCoordinate();
            Float yCoordinate = model.getYCoordinate();
            Integer pageNum = model.getPageNum();
            String content = model.getContent();
            if (xCoordinate == null || yCoordinate == null ||
                    pageNum == null || pageNum == 0 || content == null || "".equals(content)) {
                continue;
            }
            PdfContentByte pdfContentByte = stp.getOverContent(pageNum);
            pdfContentByte.beginText();
            // 设置字体及字号
            pdfContentByte.setFontAndSize(getBaseFont(), 14);
            addDeptReview(xCoordinate, yCoordinate, pdfContentByte, content);
            pdfContentByte.endText();
        }
        stp.close();
        // 将输出流关闭
        fOut.close();
        reader.close();
        // 文件读写结束
        System.out.println("PSW文件读写完毕");

        return newPswPath;
    }

    // 获取基础文字
    public static BaseFont getBaseFont() throws Exception {
        BaseFont base = BaseFont.createFont("STSong-Light", "UniGB-UCS2-H", false);
        return base;
    }

    /**
     * @param content
     * @param keyword
     * @param x       X轴坐标
     * @param y       Y轴坐标
     * @Description 添加水印
     */
    private static void addDeptReview(float x, float y, PdfContentByte content, String keyword) {
        content.setColorFill(BaseColor.BLACK);
        // 设置水印位置和内容
        System.out.println("水印内容：" + keyword);
        System.out.println("打印位置坐标：" + x + "," + y);
        content.showTextAligned(Element.ALIGN_LEFT, keyword, x, y, 0);
    }

    @Getter
    @Setter
    static class PdfItextModel {
        //  X坐标
        private Float xCoordinate;
        //Y坐标
        private Float yCoordinate;
        //页码
        private Integer pageNum;
        //内容
        private String content;
        
        public PdfItextModel() {
        }
    }
}

```

找到test.副本.pdf中关键字坐标

![image-20220611112309476](https://raw.githubusercontent.com/Lgccrush/uppic/master/uPic/2022/06/11/11:23:09_image-20220611112309476.png)

通过test.pdf空白模板生成新的文件test.副本1.pdf并添加水印

![image-20220611112529514](https://raw.githubusercontent.com/Lgccrush/uppic/master/uPic/2022/06/11/11:25:29_image-20220611112529514.png)

参考博客[https://blog.51cto.com/u_15127609/3272726]

参考博客[https://www.cnblogs.com/alphajuns/p/12436332.html]
参考博客[https://blog.csdn.net/k_young1997/article/details/103512997]