---
layout: post
title: Java操作Excel For Poi
category: Java
duoshuo: true
date: 2015-01-25
tags : [excel]

---

利用poi Api操作excel,excel的版本包括：2003-2007和2010两个版本， 即excel的后缀名为：xls和xlsx。

* maven 依赖

{% highlight xml %}
<!-- excel -->
  <dependency>
      <groupId>org.apache.poi</groupId>
      <artifactId>poi</artifactId>
      <version>3.10-FINAL</version>
  </dependency>
<dependency>
  <groupId>org.apache.poi</groupId>
  <artifactId>poi-ooxml</artifactId>
  <version>3.10-FINAL</version>
</dependency>
{% endhighlight %}
<!-- more -->

* java测试类  读取excel文件

{% highlight java %}

package com.my.excel;

import org.apache.poi.hssf.usermodel.HSSFWorkbook;
import org.apache.poi.ss.usermodel.*;
import org.apache.poi.xssf.usermodel.XSSFWorkbook;

import java.io.*;
import java.math.BigDecimal;

/**
 * Created with IntelliJ IDEA
 * Author: songrenfei
 * Date: 15/1/24
 * Time: 下午1:08
 */
public class ReadExcelFile2 {
    /**
     * @param args
     */
    public static void main(String[] args) {

        File file = new File("/Users/songrenfei/Downloads/test.xlsx");
        if(!file.exists()){
            System.out.println("文件不存在");
            return;
        }
        ReadExcelFile2 rf = new ReadExcelFile2();
        rf.readExcel(file);
    }

    /**
     * 读取Excel数据
     * @param file
     */
    public void readExcel(File file){
        try {
            InputStream inputStream = new FileInputStream(file);
            String fileName = file.getName();
            Workbook workBook = null;
            if(fileName.endsWith("xls")){
                workBook = new HSSFWorkbook(inputStream);//解析xls格式
            }else if(fileName.endsWith("xlsx")) {
                workBook = new XSSFWorkbook(inputStream);//解析xlsx格式
            }
                // 获得Excel中工作表个数
                System.out.println("工作表个数 :" + workBook.getNumberOfSheets());
                //循环每个工作表
                for (int i = 0; i < workBook.getNumberOfSheets(); i++) {
                    // 创建工作表
                    Sheet sheet = workBook.getSheetAt(i);
                    System.out.println("第"+i+"工作表名称："+sheet.getSheetName());
                    int rows = sheet.getPhysicalNumberOfRows(); // 获得行数
                    if (rows > 0) {
                        sheet.getMargin(Sheet.TopMargin);
                        for (int r = 0; r < rows; r++) { // 行循环
                            Row row = sheet.getRow(r);
                            if (row != null) {
                                int cells = row.getLastCellNum();// 获得列数
                                for (short c = 0; c < cells; c++) { // 列循环
                                    Cell cell = row.getCell(c);
                                    if (cell != null) {
                                        String value = getValue(cell);
                                        System.out.println("第"+i+"工作表，第" + r + "行 " + "第" + c+ "列：" + value);
                                    }
                                }
                            }
                        }
                    }
                    // 查询合并的单元格
                    /*for (i = 0; i < sheet.getNumMergedRegions(); i++) {
                        System.out.println("第" + i + "个合并单元格");
                        CellRangeAddress region = sheet.getMergedRegion(i);
                        int row = region.getLastRow() - region.getFirstRow() + 1;
                        int col = region.getLastColumn() - region.getFirstColumn() + 1;
                        System.out.println("起始行:" + region.getFirstRow());
                        System.out.println("起始列:" + region.getFirstColumn());
                        System.out.println("所占行:" + row);
                        System.out.println("所占列:" + col);
                    }*/
                }
        } catch (FileNotFoundException e) {
            // TODO 自动生成 catch 块
            e.printStackTrace();
        } catch (IOException e) {
            // TODO 自动生成 catch 块
            e.printStackTrace();
        }
    }

    public String getValue(Cell cell) {
        String value = "";
        switch (cell.getCellType()) {
            case Cell.CELL_TYPE_NUMERIC: // 数值型
                if (DateUtil.isCellDateFormatted(cell)) {
                    // 如果是date类型则 ，获取该cell的date值
                    value = DateUtil.getJavaDate(cell.getNumericCellValue()).toString();
                } else {// 纯数字
                    //value = String.valueOf(cell.getNumericCellValue());
                    value = new BigDecimal(cell.getNumericCellValue()).toString();//读取手机号
                }
                break;
                /* 此行表示单元格的内容为string类型 */
            case Cell.CELL_TYPE_STRING: // 字符串型
                value = cell.getRichStringCellValue().toString();
                break;
            case Cell.CELL_TYPE_FORMULA:// 公式型
                // 读公式计算值
                value = String.valueOf(cell.getNumericCellValue());
                if (value.equals("NaN")) {// 如果获取的数据值为非法值,则转换为获取字符串
                    value = cell.getRichStringCellValue().toString();
                }
                // cell.getCellFormula();读公式
                break;
            case Cell.CELL_TYPE_BOOLEAN:// 布尔
                value = " "+ cell.getBooleanCellValue();
                break;
            /* 此行表示该单元格值为空 */
            case Cell.CELL_TYPE_BLANK: // 空值
                value = "";
                break;
            case Cell.CELL_TYPE_ERROR: // 故障
                value = "";
                break;
            default:
                value = cell.getRichStringCellValue().toString();
        }
        return value;
    }
}

{% endhighlight %}

* java测试类 写入excel文件

{% highlight java %}
package com.my.excel;

import org.apache.poi.hssf.usermodel.HSSFWorkbook;
import org.apache.poi.ss.usermodel.*;
import org.apache.poi.xssf.usermodel.XSSFWorkbook;

import java.io.FileOutputStream;

/**
 * Created with IntelliJ IDEA
 * Author: songrenfei
 * Date: 15/1/24
 * Time: 下午1:38
 */
public class CreateExcelFile {

    public static void main(String[] args) throws Exception {
        Workbook[] wbs = new Workbook[]{new HSSFWorkbook(),
                new XSSFWorkbook()};
        for (int i = 0; i < wbs.length; i++) {
            Workbook wb = wbs[i];
            CreationHelper createHelper = wb.getCreationHelper();
            // create a new sheet 创建一个新表
            Sheet s = wb.createSheet();
            // declare a row object reference 声明一个对象的引用行
            Row r = null;
            // declare a cell object reference 声明对象的引用单元格
            Cell c = null;
            // create 2 cell styles 创建2单元格样式
            CellStyle cs = wb.createCellStyle();
            CellStyle cs2 = wb.createCellStyle();
            DataFormat df = wb.createDataFormat();
            // create 2 fonts objects 创建2字体对象
            Font f = wb.createFont();
            Font f2 = wb.createFont();
            // Set font 1 to 12 point type, blue and bold 设置字体1至12点类型，蓝色和粗体
            f.setFontHeightInPoints((short) 12);
            f.setColor(IndexedColors.RED.getIndex());
            f.setBoldweight(Font.BOLDWEIGHT_BOLD);
            // Set font 2 to 10 point type, red and bold 设置字体2至10点类型，红色和粗体
            f2.setFontHeightInPoints((short) 10);
            f2.setColor(IndexedColors.RED.getIndex());
            f2.setBoldweight(Font.BOLDWEIGHT_BOLD);
            // Set cell style and formatting 设置单元格样式和格式
            cs.setFont(f);
            cs.setDataFormat(df.getFormat("#,##0.0"));
            // Set the other cell style and formatting 设置其他单元格样式和格式
            cs2.setBorderBottom(cs2.BORDER_THIN);
            cs2.setDataFormat(df.getFormat("text"));
            cs2.setFont(f2);
            // Define a few rows 定义几行
            for (int rownum = 0; rownum < 30; rownum++) {
                r = s.createRow(rownum);
                for (int cellnum = 0; cellnum < 10; cellnum += 2) {
                    c = r.createCell(cellnum);
                    Cell c2 = r.createCell(cellnum + 1);
                    c.setCellValue((double) rownum + (cellnum / 10));
                    c2.setCellValue(createHelper.createRichTextString("Hello! "
                            + cellnum));
                }
            }
            // Save 保存
            String filename = "/Users/songrenfei/workbook.xls";
            if (wb instanceof XSSFWorkbook) {
                filename = filename + "x";
            }
            FileOutputStream out = new FileOutputStream(filename);
            wb.write(out);
            out.close();
        }
    }
}

{% endhighlight %}