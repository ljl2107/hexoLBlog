---
abbrlink: 修改目录下的文件名，组合成html格式，便于使用。
categories:
- - 工具
date: '2023-12-23T22:34:30.719776+08:00'
tags:
- 小工具
- 代码段
title: 修改目录下的文件名，组合成html格式，便于使用。
updated: 2023-12-23T22:34:35.421+8:0
---
小代码:

用于修改目录下的文件名，组合成html格式，便于使用。

```java
package tool;

import java.io.File;
import java.io.FilenameFilter;
import java.util.ArrayList;
import java.util.List;

/**
 * Created with IntelliJ IDEA.
 *
 * @Author: name
 * @Date: 2023/12/23
 * @Description: tool
 */
public class ImgPath2MD {
    public static void main(String[] args) {
        String folderPath = "C:\\Users\\name\\Desktop\\实践\\redwork\\rednetwork\\rednetwork\\source\\img\\英雄纪念公园";
        File folers = new File(folderPath);
        String[] imageFiles = folers.list(new FilenameFilter() {
            @Override
            public boolean accept(File dir, String name) {
//                return name.endsWith(".jpg"); 如果有过滤条件，请写在这里
                return true;
            }
        });
        ArrayList<String> MDimageFiles = new ArrayList<String>();
        for (String imageFile : imageFiles) {
            MDimageFiles.add("<img src=\"../img/英雄纪念公园/"+imageFile+"\" alt=\"Image\" width=\"500\" title=\"Hover text\">");
        }
        for (String file : MDimageFiles) {
            System.out.println(file);
        }
    }
}

```
