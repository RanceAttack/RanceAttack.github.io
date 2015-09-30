---
layout: post
title: Assets and Saveing files private
categories:
- Programming
tags:
- Android
- storage
---

## Assets
Assets 与raw 的区别就在于 
>*raw会生成资源id
>*assets 则需要使用AssetsManager进行Stream的获取
list(String namepath）方法可以方便的获取assets目录下的文件夹名和文件名
之后就可以通过open方法获得输入流了

####一部分网上找到的assets转存的代码
```java
private static void copyFilesFassets(Context context, String oldPath, String newPath){
try {
String fileNames[] = context.getAssets().list(oldPath);//获取assets目录下的所有文件及目录名
if (fileNames.length > 0) {//如果是目录
File file = new File(newPath);
file.mkdirs();//如果文件夹不存在，则递归
for (String fileName : fileNames) {
copyFilesFassets(context, oldPath + "/" + fileName, newPath + "/" + fileName);
}
} else {//如果是文件
InputStream is = context.getAssets().open(oldPath);
FileOutputStream fos = new FileOutputStream(new File(newPath));
byte[] buffer = new byte[1024];
int byteCount = 0;
while ((byteCount = is.read(buffer)) != -1) {//循环从输入流读取 buffer字节
fos.write(buffer, 0, byteCount);//将读取的输入流写入到输出流
}
fos.flush();//刷新缓冲区
is.close();
fos.close();
}
} catch (Exception e) {
// TODO Auto-generated catch block
e.printStackTrace();
}
}
```
---
continue...
