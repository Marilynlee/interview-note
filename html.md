# html面试题

1. W3C标准是什么？主要包括什么？
万维网联盟（外语缩写：W3C）标准不是某一个标准，而是一系列标准的集合。主要包含三部分：结构（Structure）、表现（Presentation）和行为（Behavior）三部分的标准
主要有：
 - 需要声明（DOCTYPE），告诉浏览器用什么标准来解析网页
 - 需要定义语言编码， 避免页面乱码
 - Js必须要用<script language="javascript" type="text/javascript">来开头定义，以保证在不支持js的浏览器上直接显示出来。
 - CSS必须要用<style type=“text/css”>开头来定义，为保证各浏览器的兼容性，在写CSS时请都写上数量单位
 - 使用注释时正确的用等号或者空格替换内部的虚线
 - 所有标签的元素和属性名字都必须使用小写，属性必须有属性值，属性值必须用引号括起来（”” ”）双引号或单引号
 - 把所有特殊符号用编码表示：空格为&nbsp; 、小于号（<）&lt、大于号（>）&gt、和号（&）&amp等
 - 标签要正确嵌套和闭合，图片添加有意义的alt属性，form表单中增加label，以增加用户友好度

2. js中attribution和property的区别
   attribution是html标签上的特性，值只能为string，属于property的一个子集。而property是dom对象的属性，为js的对象属性。
   - attr设置的值会同步到property中，反之property设置的值不会同步到attr中
   - 更改attr和props上任意的值，都会反馈到html上

