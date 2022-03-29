---
title: java 读写文件

tags: 
- java
- io
---

**字符流读写文件**
```java
    public class A{
        /**
         * 向文件写数据
         * @param fileName
         * @param content
         */
        private void wite2file(String fileName,String content){
            new Thread(()->{
                String filePath = "D:/a/b";
                File dir = new File(filePath);
                // 一、检查放置文件的文件夹路径是否存在，不存在则创建
                if (!dir.exists()) {
                    dir.mkdirs();// mkdirs创建多级目录
                }
                File checkFile = new File(filePath + File.separator+fileName);
                FileWriter writer = null;
                try {
                    // 二、检查目标文件是否存在，不存在则创建
                    if (!checkFile.exists()) {
                        checkFile.createNewFile();// 创建目标文件
                    }
                    // 三、向目标文件中写入内容
                    // FileWriter(File file, boolean append)，append为true时为追加模式，false或缺省则为覆盖模式
                    writer = new FileWriter(checkFile, true);
                    writer.append(content);
                    writer.flush();
                    fileMap.put(fileName,checkFile);
                } catch (IOException e) {
                    e.printStackTrace();
                } finally {
                    if (null != writer) {
                        try {
                            writer.close();
                        } catch (IOException e) {
                            e.printStackTrace();
                        }
                    }
                }
            }).start();
    
        }
    
        /**
         * 从文件读取数据
         * @param file
         * @return
         */
        public StringBuffer readFromFile(File file){
            FileReader fre = null;
            BufferedReader bre = null;
            StringBuffer sb = new StringBuffer();
            try {
                fre=new FileReader(file);
                bre=new BufferedReader(fre);
                String str="";
                while((str=bre.readLine())!=null)	//●判断最后一行不存在，为空
                {
                    //System.out.println(str);
                    sb.append(str);
                }
                bre.close();
                fre.close();
            } catch (IOException e) {
                e.printStackTrace();
            }finally {
                if (bre!=null){
                    try {
                        bre.close();
                    } catch (IOException e) {
                        e.printStackTrace();
                    }
                }
                if (fre!=null){
                    try {
                        fre.close();
                    } catch (IOException e) {
                        e.printStackTrace();
                    }
                }
            }
            return sb;
        }
    }
```
