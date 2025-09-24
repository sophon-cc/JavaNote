# Apache POI 介绍
Apache POI 是一个处理Miscrosoft Office各种文件格式的开源项目。简单来说就是，我们可以使用 POI 在 Java 程序中对Miscrosoft Office各种文件进行读写操作。
一般情况下，POI 都是用于操作 Excel 文件。

![](./pictures/ApachePOI/POI.png)

Apache POI 的应用场景：
- 银行网银系统导出交易明细；
- 各种业务系统导出Excel报表；
- 批量导入业务数据。

# 快速入门
## 依赖导入
```xml
<!-- poi -->
<dependency>
    <groupId>org.apache.poi</groupId>
    <artifactId>poi</artifactId>
</dependency>
<dependency>
    <groupId>org.apache.poi</groupId>
    <artifactId>poi-ooxml</artifactId>
</dependency>
```

## 将数据写入Excel文件
```java
@Test
public void testWrite() throws Exception {
    //在内存中创建一个Excel文件对象
    try (XSSFWorkbook excel = new XSSFWorkbook();
            FileOutputStream out = new FileOutputStream("D:\\test.xlsx")) {
        // 创建Sheet页
        XSSFSheet sheet = excel.createSheet("sheet1");

        // 在Sheet页中创建行，0表示第1行
        XSSFRow row1 = sheet.createRow(0);
        //创建单元格并在单元格中设置值，单元格编号也是从0开始，1表示第2个单元格
        row1.createCell(1).setCellValue("姓名");
        row1.createCell(2).setCellValue("城市");

        XSSFRow row2 = sheet.createRow(1);
        row2.createCell(1).setCellValue("张三");
        row2.createCell(2).setCellValue("北京");

        XSSFRow row3 = sheet.createRow(2);
        row3.createCell(1).setCellValue("李四");
        row3.createCell(2).setCellValue("上海");

        // 通过输出流将内存中的Excel文件写入到磁盘上
        excel.write(out);

        // 刷新输出流，将数据写入到磁盘上
        out.flush();
    }
}
```

## 从Excel文件中读取文件
```java
@Test
public void testRead() throws Exception {
    //通过输入流读取指定的Excel文件
    try (FileInputStream in = new FileInputStream("D:\\test.xlsx");
            XSSFWorkbook excel = new XSSFWorkbook(in)) {
        //获取Excel文件的第1个Sheet页
        XSSFSheet sheet = excel.getSheetAt(0);

        //获取Sheet页中的最后一行的行号
        int lastRowNum = sheet.getLastRowNum();

        for (int i = 0; i <= lastRowNum; i++) {
            //获取Sheet页中的行
            XSSFRow titleRow = sheet.getRow(i);
            //获取行的第2个单元格
            XSSFCell cell1 = titleRow.getCell(1);
            //获取单元格中的文本内容
            String cellValue1 = cell1.getStringCellValue();
            //获取行的第3个单元格
            XSSFCell cell2 = titleRow.getCell(2);
            //获取单元格中的文本内容
            String cellValue2 = cell2.getStringCellValue();
            System.out.println(cellValue1 + " " + cellValue2);
        }
    }
}
```
