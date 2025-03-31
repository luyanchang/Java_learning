# Day03：File和I/O
## 1. File类
### File类的构造方法
```java
File(String pathname)
File(String parent,String child)
File(File parent,String child)
```
### File类的常用方法
```java
public String[] list()//返回当前目录下的所有一级文件名称到数组
public File[] listFiles()//返回当前文件夹下的所有一级文件对象
public String getAbsolutePath()//获取绝对路径
public String getPath()//获取路径
public String getName()//获取名称
public String getParent()//获取上层文件目录路径。若无，返回null
```
### 使用listFiles方法时的注意事项
```java
1.当主调是文件，或者路径不存在时，返回 null  
2.当主调是空文件夹时，返回一个长度为 0 的数组
3.当主调是一个有内容的文件夹时，将里面所有一级文件和文件夹的路径放在 File 数组中返回
4.当主调是一个文件夹，且里面有隐藏文件时，将里面所有文件和文件夹的路径放在 File 数组中返回，包含隐藏文件
5.当主调是一个文件夹，但是没有权限访问该文件夹时，返回 null
```
### 使用遍历进行文件的搜索
```java
    /**
     * 搜索文件
     * @param dir 搜索的目录
     * @param fileName 搜索的文件名称
     */
    public static void searchFile(File dir, String fileName) throws Exception {
        // 1、判断极端情况
        if(dir == null || !dir.exists() || dir.isFile()){
            return; // 不搜索
        }

        // 2、获取目录下的所有一级文件或者文件夹对象
        File[] files = dir.listFiles();

        // 3、判断当前目录下是否存在一级文件对象，存在才可以遍历
        if(files != null && files.length > 0){
            // 4、遍历一级文件对象
            for (File file : files) {
                // 5、判断当前一级文件对象是否是文件
                if(file.isFile()){
                    // 6、判断文件名称是否和目标文件名称一致
                    if(file.getName().contains(fileName)){
                        System.out.println("找到目标文件：" + file.getAbsolutePath());
                        Runtime r = Runtime.getRuntime();
                        r.exec(file.getAbsolutePath());
                    }
                }else{
                    // 7、如果当前一级文件对象是文件夹，则继续递归调用
                    searchFile(file, fileName);
                }
            }
        }
    }
```
## 2. I/O
### 1. Unicode 
```java
1.是Unicode字符集的一种编码方案，采取可变长编码方案，共分四个长度区：1个字节，2个字节，3个字节，4个字节
2.英文字符、数字等只占1个字节（兼容标准ASCII编码），汉字字符占用3个字节。
```
![Unicode字符集](./image/unicode.png)
```java
1.ASCII字符集：只有英文、数字、符号等，占1个字节。
2.GBK字符集：汉字占2个字节，英文、数字占1个字节。
3.UTF-8字符集：汉字占3个字节，英文、数字占1个字节。
```
### 2.使用程序对字符进行编码和解码操作 
```java
1.对字符的编码
byte[] getBytes​() // 默认编码
byte[] getBytes​(String charsetName) // 指定字符集

2.对字符的解码
String​(byte[] bytes) // 默认编码
String​(byte[] bytes, String charsetName) // 指定字符集
```

I/O流的体系：  
---
抽象类：
- InputStream（字节输入流）
- OutputStream（字节输出流）
- Reader（字符输入流）
- Writer（字符输出流）

实现类：
- FileInputStream（文件字节输入流）
- FileOutputStream（文件字节输出流）
- FileReader（文件字符输入流）
- FileWriter（文件字符输出流）
---
### 3.FileInputStream（文件字节输入流）
```java
1.构造方法
public FileInputStream​(File file) // 创建字节输入流对象，并关联文件
public FileInputStream​(String name) // 创建字节输入流对象，并关联文件，本质上是对上一个方法做了包装

2.常用方法
public int read() // 每次读取一个字节返回，如果发现没有数据可读会返回-1.
public int read(byte[] buffer) // 每次用一个字节数组去读取数据，返回字节数组读取了多少个字节，如果发现没有数据可读会返回-1.
```
**单个字节进行读取**
```java
// 1、创建文件字节输入流管道于源文件接通
InputStream is = new FileInputStream("day03-file-io\\src\\dlei02.txt"); 

// 2、开始读取文件中的字节并输出： 每次读取一个字节
// 定义一个变量记住每次读取的一个字节
int b;
while ((b = is.read()) != -1) {
    System.out.print((char) b);
}
// 每次读取一个字节的问题：性能较差,读取汉字输出一定会乱码！

is.close();
```
**使用字节数组进行读取**
```java
// 1、创建文件字节输入流管道于源文件接通
InputStream is = new FileInputStream("day03-file-io\\src\\dlei02.txt"); 

// 2、开始读取文件中的字节并输出： 每次用一个字节数组去读取数据，返回字节数组读取了多少个字节，如果发现没有数据可读会返回-1.
byte[] buffer = new byte[1024]; // 创建一个字节数组，大小为1024字节
int len;
// 1、创建文件字节输入流管道于源文件接通
InputStream is = new FileInputStream("day03-file-io\\src\\dlei03.txt"); // 简化写法

// 2、开始读取文件中的字节并输出： 每次读取多个字节
// 定义一个字节数组用于每次读取字节
byte[] buffer = new byte[3];
// 定义一个变量记住每次读取了多少个字节。
int len;
while ((len = is.read(buffer)) != -1) {
    // 3、把读取到的字节数组转换成字符串输出,读取多少倒出多少
    String str = new String(buffer,0, len);
    System.out.print(str);
}
is.close();

// 拓展：每次读取多个字节，性能得到提升，因为每次读取多个字节，可以减少硬盘和内存的交互次数，从而提升性能。
// 依然无法避免读取汉字输出乱码的问题：存在截断汉字字节的可能性！
```
**一次读取完全部字节**  
Java官方为InputStream提供了如下方法：
```java
public byte[] readAllBytes() throws IOException // 一次性读取文件的全部字节，返回字节数组
```
```java
// 1、创建文件字节输入流管道于源文件接通
InputStream is = new FileInputStream("day03-file-io\\src\\dlei04.txt"); // 简化写法

// 2、一次性读完文件的全部字节:可以避免读取汉字输出乱码的问题。
byte[] bytes = is.readAllBytes();

String rs = new String(bytes);
System.out.println(rs);

is.close();
```
### 4.FileOutputStream（文件字节输出流）
```java
1.构造方法
public FileOutputStream​(File file) // 创建字节输出流对象，并关联文件
public FileOutputStream​(String name) // 创建字节输出流对象，并关联文件，本质上是对上一个方法做了包装
public FileOutputStream​(File file, boolean append) // 创建字节输出流对象，并关联文件，第二个参数表示是否追加数据
public FileOutputStream​(String name, boolean append) // 创建字节输出流对象，并关联文件，第二个参数表示是否追加数据，本质上是对上一个方法做了包装

2.常用方法
public void write​(int b) // 写入一个字节
public void write​(byte[] b) // 写入一个字节数组
public void write​(byte[] b, int off, int len) // 写入一个字节数组的一部分
public void close() throws IOException // 关闭流对象，释放资源
```
**具体的例子**
```java
// OutputStream os = new FileOutputStream("day03-file-io/src/dlei05-out.txt"); // 覆盖管道
OutputStream os = new FileOutputStream("day03-file-io/src/dlei05-out.txt", true); // 追加数据

// 2、写入数据
//  public void write(int b)
os.write(97); // 写入一个字节数据
os.write('b'); // 写入一个字符数据
// os.write('徐'); // 写入一个字符数据 会乱码
os.write("\r\n".getBytes()); // 换行

// 3、写一个字节数组出去
// public void write(byte[] b)
byte[] bytes = "我爱你中国666".getBytes();
os.write(bytes);
os.write("\r\n".getBytes()); // 换行

// 4、写一个字节数组的一部分出去
// public void write(byte[] b, int pos, int len)
os.write(bytes, 0, 3);
os.write("\r\n".getBytes()); // 换行

os.close(); // 关闭管道 释放资源
```
### 5.文件复制
- 任何文件的底层都是字节，字节流做复制，是一字不漏的转移完全部字节，只要复制后的文件格式一致就没问题!
```java
// 1、创建输入输出管道
InputStream is = new FileInputStream("day03-file-io\\src\\dlei06.txt");
OutputStream os = new FileOutputStream("day03-file-io\\src\\dlei07.txt");

// 2、开始复制
byte[] buffer = new byte[1024];
int len;
while ((len = is.read(buffer)) != -1) {
    os.write(buffer, 0, len);
}

// 3、关闭管道
System.out.println("复制成功！");
is.close();
os.close();
```
- 自动释放资源 try-with-resources
```java
try (InputStream is = new FileInputStream("day03-file-io\\src\\dlei06.txt");
     OutputStream os = new FileOutputStream("day03-file-io\\src\\dlei07.txt")) {
    // 2、开始复制
    byte[] buffer = new byte[1024];
    int len;
    while ((len = is.read(buffer)) != -1) {
        os.write(buffer, 0, len);
    }
    System.out.println("复制成功！");
} catch (IOException e) {
    e.printStackTrace();
}
```
### 6.FileReader（文件字符输入流）
```java
1.构造方法
public FileReader​(File file) // 创建字符输入流对象，并关联文件
public FileReader​(String name) // 创建字符输入流对象，并关联文件，本质上是对上一个方法做了包装

2.常用方法
public int read() // 每次读取一个字符返回，如果发现没有数据可读会返回-1.
public int read(char[] buffer) // 每次用一个字符数组去读取数据，返回字符数组读取了多少个字符，如果发现没有数据可读会返回-1.
```
**使用字符数组读取字符的内容，并使用 try-with-resources**
```java
try (Reader fr = new FileReader("day03-file-io\\src\\dlei01.txt")) {
    // 2、开始读取文件中的字符并输出： 每次用一个字符数组去读取数据，返回字符数组读取了多少个字符，如果发现没有数据可读会返回-1.
    char[] buffer = new char[1024];
    int len;
    while ((len = fr.read(buffer)) != -1) {
        // 3、把读取到的字符数组转换成字符串输出,读取多少倒出多少
        String str = new String(buffer, 0, len);
        System.out.print(str);
    }
} catch (IOException e) {
    e.printStackTrace();
}
```
### 7.FileWriter（文件字符输出流）
```java
1.构造方法
public FileWriter​(File file) // 创建字符输出流对象，并关联文件
public FileWriter​(String name) // 创建字符输出流对象，并关联文件，本质上是对上一个方法做了包装
public FileWriter​(File file, boolean append) // 创建字符输出流对象，并关联文件，第二个参数表示是否追加数据
public FileWriter​(String name, boolean append) // 创建字符输出流对象，并关联文件，第二个参数表示是否追加数据，本质上是对上一个方法做了包装

2.常用方法
public void write​(int c) // 写入一个字符
public void write​(String str) // 写入一个字符串
public void write​(String str, int off, int len) // 写入一个字符串的一部分
public void write​(char[] cbuf) // 写入一个字符数组
public void write​(char[] cbuf, int off, int len) // 写入一个字符数组的一部分
```
**使用字符数组写入数据**
```java
try (
        // 1. 创建一个字符输出流对象，指定写出的目的地。
        //  Writer fw = new FileWriter("day03-file-io/src/dlei07-out.txt"); // 覆盖管道
        Writer fw = new FileWriter("day03-file-io/src/dlei07-out.txt", true); // 追加数据
        ){

    // 2. 写一个字符出去：  public void write(int c)
    fw.write('a');
    fw.write(98);
    fw.write('磊');
    fw.write("\r\n"); // 换行

    // 3、写一个字符串出去：  public void write(String str)
    fw.write("java");
    fw.write("我爱Java，虽然Java不是最好的编程之一,但是可以挣钱");
    fw.write("\r\n"); // 换行

    // 4、写字符串的一部分出去：  public void write(String str, int off, int len)
    fw.write("java", 1, 2);
    fw.write("\r\n"); // 换行

    // 5、写一个字符数组出去：  public void write(char[] cbuf)
    char[] chars = "java".toCharArray();
    fw.write(chars);
    fw.write("\r\n"); // 换行

    // 6、写字符数组的一部分出去：  public void write(char[] cbuf, int off, int len)
    fw.write(chars, 1, 2);
    fw.write("\r\n"); // 换行

    // fw.flush(); // 刷新缓冲区，将缓冲区中的数据全部写出去。
    // 刷新后，流可以继续使用。
    // fw.close(); // 关闭资源，关闭包含了刷新！关闭后流不能继续使用！

} catch (Exception e) {
    e.printStackTrace();
}
```
### 8.缓冲字节流
- 作用：可以提高字节输入流读取数据的性能
- 原理：缓冲字节输入流自带了8KB缓冲池；缓冲字节输出流也自带了8KB缓冲池。

```java
public BufferedInputStream​(InputStream is) // 把低级的字节输入流包装成一个高级的缓冲字节输入流，从而提高读数据的性能
public BufferedOutputStream​(OutputStream os) // 把低级的字节输出流包装成一个高级的缓冲字节输出流，从而提高写数据的性能
```
```java
  try (
        InputStream fis = new FileInputStream(srcPath);
        // 把低级的字节输入流包装成高级的缓冲字节输入流
        InputStream bis = new BufferedInputStream(fis);

        OutputStream fos = new FileOutputStream(destPath);
        // 把低级的字节输出流包装成高级的缓冲字节输出流
        OutputStream bos = new BufferedOutputStream(fos);
        ){
      // 2、读取一个字节数组，写入一个字节数组  1024 + 1024 + 3
      byte[] buffer = new byte[1024];
      int len;
      while ((len = bis.read(buffer)) != -1) {
        bos.write(buffer, 0, len); // 读取多少个字节，就写入多少个字节
      }
      System.out.println("复制成功！");
  } catch (Exception e) {
      e.printStackTrace();
  }
```
### 9.缓冲字符流
- 作用：可以提高字符输入流读取数据的性能
- 原理：缓冲字符输入流自带了8KB缓冲池；缓冲字符输出流也自带了8KB缓冲池。

```java
public BufferedReader​(Reader in) // 把低级的字符输入流包装成一个高级的缓冲字符输入流，从而提高读数据的性能

//字符缓冲输入流新增的功能：按照行读取字符
public String readLine() // 读取一行数据返回，如果没有数据可读了，会返回null
```
```java
  try (
        Reader fr = new FileReader("day03-file-io\\src\\dlei08.txt"); // 1、创建文件字符输入流与源文件接通
        BufferedReader br = new BufferedReader(fr); // 2、创建缓冲字符输入流包装低级的字符输入流
  ) {
      // 使用循环改进，来按照行读取数据。
      // 定义一个字符串变量用于记住每次读取的一行数据
      String line;
      while ((line = br.readLine()) != null){
        System.out.println(line);
      }
      // 目前读取文本最优雅的方案：性能好，不乱码，可以按照行读取。
  }catch (Exception e){
      e.printStackTrace();
  }
```
---
```java
public BufferedWriter​(Writer r) // 把低级的字符输出流包装成一个高级的缓冲字符输出流，从而提高写数据的性能
// 字符缓冲输出流新增的功能：换行
public void newLine() // 写一个换行符出去，这个换行符由系统决定，windows是\r\n，linux是\n
```
```java
  try (
    // 1. 创建一个字符输出流对象，指定写出的目的地。
    // Writer fw = new FileWriter("day03-file-io/src/dlei07-out.txt"); // 覆盖管道
    Writer fw = new FileWriter("day03-file-io/src/dlei07-out.txt", true); // 追加数据

    // 2. 创建一个缓冲字符输出流对象，把字符输出流对象作为构造参数传递给缓冲字符输出流对象。
    BufferedWriter bw = new BufferedWriter(fw);
  ){
    // 6、写字符数组的一部分出去：  public void write(char[] cbuf, int off, int len)
    char[] chars = "java".toCharArray();
    bw.write(chars, 1, 2);
    bw.newLine(); // 换行

  } catch (Exception e) {
      e.printStackTrace();
  }
```
- 建议使用字节缓冲输入流、字节缓冲输出流，结合字节数组的方式，目前来看是性能最优的组合。
### 9.InputStreamReader​（字符输入转换流）
- 解决思路：先获取文件的原始字节流，再将其按真实的字符集编码转成字符输入流，这样字符输入流中的字符就不乱码了
```java
public InputStreamReader​(InputStream in, String charsetName) // 把原始的字节输入流，按照指定字符集编码转成字符输入流(重点)
```
```java
try (
    // 先提取文件的原始字节流
    InputStream is = new FileInputStream("day03-file-io\\src\\dlei09.txt");
    // 指定字符集把原始字节流转换成字符输入流
    Reader isr = new InputStreamReader(is, "GBK");
    // 2、创建缓冲字符输入流包装低级的字符输入流
    BufferedReader br = new BufferedReader(isr);
) {
    // 定义一个字符串变量用于记住每次读取的一行数据
    String line;
    while ((line = br.readLine()) != null){
        System.out.println(line);
    }
}catch (Exception e){
    e.printStackTrace();
}
```
### 10.PrintStream/PrintWriter（打印流）
- 打印流：打印流可以实现更方便、更高效的打印数据出去，能实现打印啥出去就是啥出去。
```java
1.构造方法
public PrintStream​(OutputStream/File/String) // 打印流直接通向字节输出流/文件/文件路径
public PrintStream​(String fileName, Charset charset) // 可以指定写出去的字符编码
public PrintStream​(OutputStream out, boolean autoFlush) // 可以指定实现自动刷新
public PrintStream​(OutputStream out, boolean autoFlush, String encoding) // 可以指定实现自动刷新，并可指定字符的编码

2.常用方法
public void println(Xxx xx) // 打印任意类型的数据出去，并换行
public void print(Xxx xx) // 打印任意类型的数据出去，不换行
public void write(int/byte[]/byte[]一部分) // 写一个字节/字节数组/字节数组的一部分出去
```
```java
 try (
    // PrintStream ps = new PrintStream("day03-file-io/src/ps.txt");
    PrintStream ps = new PrintStream(new FileOutputStream("day03-file-io/src/ps.txt", true));
    // PrintWriter ps = new PrintWriter("day03-file-io/src/ps.txt");
         ){
   ps.println(97);
   ps.println('a');
   ps.println("黑马");
   ps.println(true);
   ps.println(99.9);
 }catch (Exception e){
   e.printStackTrace();
 }
```
### 11.DataInputStream/DataOutputStream（特殊数据流）
- 允许把数据和其类型一并写出去。
```java
1.构造方法
public DataInputStream​(InputStream in) // 把低级的字节输入流包装成一个高级的数据输入流，从而提高读数据的性能
public DataOutputStream​(OutputStream out) // 把低级的字节输出流包装成一个高级的数据输出流，从而提高写数据的性能
 
2.常用方法
public final int readInt() // 读取一个整数
public final void writeInt(int v) // 写一个整数出去
public final double readDouble() // 读取一个double
public final void writeDouble(double v) // 写一个double出去
public final String readUTF() // 读取一个字符串
public final void writeUTF(String str) // 写一个字符串出去
```
```java
try (
    DataInputStream dis = new DataInputStream(new FileInputStream("day03-file-io/src/data.dat"));
    DataOutputStream dos = new DataOutputStream(new FileOutputStream("day03-file-io/src/data.dat"));
){
    // 1、写数据出去：public final void writeInt(int v)
    dos.writeInt(100);
    dos.writeUTF("java");
    dos.writeDouble(99.9);

    // 2、读数据进来：public final int readInt()
    int i = dis.readInt();
    system.out.println(i);
    String s = dis.readUTF();
    System.out.println(s);
    double d = dis.readDouble();
    System.out.println(d);
}catch (Exception e){
    e.printStackTrace();
}
```
### 12.I/O框架
- Commons-io是apache开源基金组织提供的一组有关IO操作的小框架，目的是提高IO流的开发效率。
```java
1. FileUtils类提供的部分方法展示
public static void copyFile(File srcFile, File destFile) // 复制文件
public static void copyDirectory(File srcDir, File destDir) // 复制文件夹
public static void deleteDirectory(File directory) // 删除文件夹
public static String readFileToString(File file, String encoding) // 读数据
public static void writeStringToFile(File file, String data, String charname, boolean append) // 写数据

2. IOUtils类提供的部分方法展示
public static int copy(InputStream input, OutputStream output) // 复制文件
public static int copy(Reader reader, Writer writer) // 复制文件
public static void write(String data, OutputStream output, String charsetName) // 写数据
```
```java
package com.itheima.demo14_commonsio;
import org.apache.commons.io.FileUtils;
import java.io.File;

public class CommonsIoDemo1 {
    public static void main(String[] args) throws Exception {
        // 目标：IO框架
        FileUtils.copyFile(new File("day03-file-io\\src\\csb_out.txt"), new File("day03-file-io\\src\\csb_out2.txt"));
    // JDK 7提供的
    // Files.copy(Path.of("day03-file-io\\src\\csb_out.txt"), Path.of("day03-file-io\\src\\csb_out3.txt"));
        FileUtils.deleteDirectory(new File("E:\\resource\\图片服务器 - 副本 (2)"));
    }
}
```
