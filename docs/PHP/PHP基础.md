# 1.const和define区别

const和define都可以用于定义常量，常量一旦定义就不能重新定义或取消定义

1、const可用于类成员变量的定义（在PHP5.3以前，const只能在类中使用，而PHP5.3开始在类外也可以使用），一经定义，不可修改，是个语言结构。define不可以用于类成员变量的定义，可用于全局常量，是个函数。 

```php
class A{
    const TEXT = 'helloworld';    // 有效的valid
    define('TEXT', 'helloworld'); // 无效的invalid
}
```



2、const不能在条件语句中定义常量

```php
if (...){
    const TEXT = 'helloworld';    // 无效的invalid
}
if (...) {
    define('TEXT', 'helloworld'); // 有效的valid
}
```



3、const在编译时要比define快很多。



4、const采用普通的常量名称（从PHP5.6开始可以使用表达式），define可以采用表达式作为名称。

```php
const NUM=1<<5;		// 有效的valid
define('NUM', 1<<5);// 有效的valid
```



5、const定义的常量时大小写敏感，而define可以通过第三个参数（为true表示大小写不敏感）来指定大小写是否敏感，但是在7.3.0废弃了表示大小写是否敏感的功能case_insensitive，并将在 8.0.0 版中移除。

```php
define('TEXT', 'helloworld', true);   // 有效的valid
echo TEXT;
echo text;
```



6、编译器处理方式不同
define宏是在预处理阶段展开。
const常量是编译运行阶段使用。

```php
$hello = 'hello';
$world = 'world';
const TEXT = $hello.$world;     // 无效的invalid
const TEXT = 'helloworld';      // 有效的valid
define('TEXT', $hello.$world);  // 有效的valid
define('TEXT', 'helloworld');   // 有效的valid
```



常量可以通过常量名直接访问，也可以通过`constant()`函数访问，或者通过`get_defined_constants()`可以获得所有已定义的常量。

```php
const TEXT = 'helloworld';      // 有效的valid
echo constant('TEXT');
print_r(get_defined_constants()); 
```



参考博客：https://juejin.cn/post/6844903849459712013