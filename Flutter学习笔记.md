# Flutter学习笔记

[TOC]



## 相关网址

- flutter官网：https://flutter.dev
- flutter api: <https://api.flutter.dev/index.html>

## 开发相关

- **url_launcher** 用于ios/andriod 电话、邮件、打开app、打开webview

- **pubspec.yaml** 项目依赖文件


## 布局方法

- **Row**  <widget >列布局
- **Column** <widget >行布局
- **ListView** <widget >列表布局
- **GridView** <widget >grid布局
- **PageViwe** <widget>滑动页面布局
- **SingleChildScrollView**<widget> 滑动页面布局
- **Container** <widget >容器（可以设置border、margin、padding 、backgroundImage）
- **Stack** <widget >堆容器（如果您想以简单的方式重叠多个子级，例如具有一些文本和图像，并用渐变和一个附加在底部的按钮覆盖）
- **GestureDetector**<widget> 手势检测容器 (eg: onTap onLongPress etx..)


## 笔记

1. **有状态的组件 _MyApp createState() => new _MyApp();**

2. **PageView + bottomNavigationBar 构建可滑动的首页**

3. **如何让子组件保持状态？**

   把该组件改成StatefulWidget，则可采用AutomaticKeepAliveClientMixin保持页面状态。此方法需要重写wantKeepAlive，代码如下。

   ```dart
   class _MyWidget extends State<MyWidget> with AutomaticKeepAliveClientMixin {
       @override
   	bool get wantKeepAlive => true;
   }
   ```

   

4. **flutter dp值与设备关系** 

   dp值 = physicalSize/devicePixelRatio

   physicalSize 设备物理大小单位是px（像素）

   devicePixelRatio 设备像素比

   1080\*10920像素devicePixelRatio等于3.0转换为dp值为360\*640

5. **如何获取容器的宽高度？**

   不多逼逼见代码

   ```dart
   MediaQuery.of(context).size
   ```

6. **解决flutter pub get速度慢**

   Configuring Flutter to use a mirror site

   ```powershell
   export PUB_HOSTED_URL=https://pub.flutter-io.cn
   export FLUTTER_STORAGE_BASE_URL=https://storage.flutter-io.cn
   ```

   参考网址 <https://flutter.dev/community/china>  

7. **next**

