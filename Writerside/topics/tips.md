# Tips

## PHP命名空间
在编译时，所有的类都会被转换为携带命名空间的全名，命名空间也就从程序中消失了。  
而变量由于在程序运行时才会被确定，所以使用变量作为类名无法享受命名空间的便利，也就必须为类全名。
```PHP
<?php
// test1.php
namespace a;
class A
{
    public function __construct()
    {
        echo 'a';
    }
}

// test2.php
namespace b;
use a\A;

$a1 = 'A';
$a2 = 'a\A';

new $a1();   // 报错
new $a2();   // 输出 'a'
new A();     // 输出 'a'
?>
```

## mysql datetime运算
两个datetime类型的变量直接相减会把年月日的数字拼起来相减  
正确做法：
```SQL
timestampdiff(second, update_time, now())
-- or
unix_timestamp(now()) - unix_timestamp(update_time)
```