```java
@GetMapping(value = "spider/label/{param}")
public void getSpiderLabel(@PathVariable("param") String param, HttpServletResponse response) {
    //解密请求参数
    try {
        byte[] decode = Base64.decode(param);
        String referenceNo = new String(decode);
        byte[] bytes = kecExportFacade.getSpiderLabel(referenceNo);
        response.setContentType("application/pdf");
        response.setHeader("Content-Disposition", "attachment;filename=" + referenceNo + ".pdf");
        OutputStream os = response.getOutputStream();
        os.write(bytes);
    } catch (Exception e) {
        throw new KecBusinessException("下载pdf失败");
    }
}
```