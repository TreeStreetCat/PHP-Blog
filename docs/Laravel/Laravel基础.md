# 一、Laravel中访问器&修改器



访问器和修改器允许你在获取模型属性或设置其值时格式化 Eloquent 属性。例如，你可能想要使用 Laravel 加密器对存储在数据库中的数据进行加密，并且在 Eloquent 模型中访问时自动进行解密。

**访问器**

举个例子，有下面这么一个成绩表 `grade`

| id   | name | math_grade | eng_grade |
| ---- | ---- | ---------- | --------- |
| 1    | 小王 | 78         | 88        |
| 2    | 小红 | 99         | 100       |

我们想计算小王和小红的总成绩(即数学成绩+英语成绩)，这个时候如果在 `grade` 里添加一个 `total_score` 的字段，可能会让数据表显得十分冗余。因此，我们需要一个`访问器`。



1.在Model里做以下修改：

- 要定义一个访问器，修改器的格式必须为 `getXXXAttribut()` 且需要访问的字段使用驼峰命名，我们在model里新增一个 `getTotalScoreAttribute` 方法，

```php
public function getTotalScoreAttribute()
{
   return $this->math_grade + $this->eng_grade;
}
```



2.新增两条数据

```shell
INSERT INTO `grade`(`id`, `name`, `math_grade`, `eng_grade`) VALUES (1, '小王', '78', '88');
INSERT INTO `grade`(`id`, `name`, `math_grade`, `eng_grade`) VALUES (2, '小红', '99', '100');
```



3.实现一个简单的控制器

```php
class GradeController extends Controller
{
    public function index(){
        $grade_list =  Grade::all();
        foreach ($grade_list as $value){
            $value->total_score = $value->getTotalScoreAttribute();
        }
        return $grade_list;
        return response()->json($grade);
    }
}
```

查询结果如下：

```json
[
  {
    "id": 1,
    "name": "小王",
    "math_grade": 78,
    "eng_grade": 88,
    "total_score": 166
  },
  {
    "id": 2,
    "name": "小红",
    "math_grade": 99,
    "eng_grade": 100,
    "total_score": 199
  }
]
```



4.当然，我们也可以结合`序列化`来使用，在数组或 JSON 中添加一些数据库中不存在字段的对应属性。


- 通过序列化追加JSON值，

```php
protected $appends = ['total_score'];
```

- 修改控制器如下：

```php
class GradeController extends Controller
{
    public function index(){
        $grade_list =  Grade::all();
        return $grade_list;
    }
}
```

能够得到跟上面一样的返回结果。

<br>

**修改器**

与访问器相反，修改器用于字段值保存到数据库之前进行一些相关处理，如密码加密，数据格式化等等。

要定义一个修改器，则需在模型上定义一个 `setFooAtrribute` 方法，要访问的Foo字段需使用驼峰式命名，命名规则与访问器一致。

1.定义一个password属性的修改器 `setPasswordAttribute` ，当我们在模型上设置 password 的值时会自动触发修改器，自动调用 `setPasswordAttribute` 方法，将 password 进行加密

```php
public function setPasswordAttribute($value){
      $this->attributes['password'] = encrypt($value);
}
```

2.设置用户密码，自动调用修改器

```php
public function store(){
      $user = User::find(1);
      $user->password = 12345;
      $user->save();
}
```









参考链接：https://learnku.com/docs/laravel/8.x/eloquent-mutators/9409

https://learnku.com/laravel/t/38986

<br>

# 处理类

<br>

## 日期处理类 Carbon

Carbon 是 php 的日期处理类库

Carbon 继承了 Datetime 类，也就是说 Carbon 是一个关于 DateTime 的 PHP拓展，DateTime 里已经实现的方法，Carbon 都能使用。Carbon 具有从基本 DateTime 类继承的所有功能。这种方法使您可以访问基本功能，例如 [Modify](http://php.net/manual/en/datetime.modify.php)，  [Format](http://php.net/manual/en/datetime.format.php) 或 [diff](http://php.net/manual/en/datetime.diff.php) 。

```php
class Carbon extends DateTime implements JsonSerializable
```

<br>

**安装：**

```shell
composer require nesbot/carbon
```

如果是使用 Laravel 框架，从 Laravel 5.8 版开始，Laravel 正式支持 Carbon 2，也就是开箱即用 Carbon，无需使用 composer 安装 Carbon 依赖。

<br>

**使用：**

> 引入 Carbon

```php
use Carbon\Carbon;
```

<br>

- 可以使用 `now()` 获取当前时间，如果你想指定其他时区，也可以给 `now()` 方法传入一个参数指定。

```php
echo Carbon::now();    // 2021-02-19 15:38:59⏎

echo Carbon::now('America/Los_Angeles');    // 2021-02-18 23:38:41
```

<br>

- 除了 `now()` 方法外，Carbon 还提供了 `today()`， `tomorrow()`，`yesterday()` 等静态方法，但是它们的时间都是 `00:00:00`。

```php
echo Carbon::today();     // 2021-02-19 00:00:00

echo Carbon::tomorrow();     // 2021-02-20 00:00:00

echo Carbon::yesterday();    // 2021-02-18 00:00:00
```

- 日期类型转换为字符串
  默认情况下，Carbon 的方法返回的是一个日期时间对象。如果需要的是字符串，可以使用如下方法转换：

```php
echo Carbon::now()->toDateString();         // 2021-02-19

echo Carbon::now()->toDateTimeString();     // 2021-02-19 09:41:42

echo Carbon::now()->toTimeString();         // 09:43:42
```

- 时间加减

​       默认的DateTime提供了几种不同的方法来轻松地增加和减少时间。还有 `modify()`，`add()`和`sub()`

```php
$dt = Carbon::create(2012, 1, 31, 0);

echo $dt->toDateTimeString();            // 2012-01-31 00:00:00

// 加减百年
echo $dt->addCenturies(5);               // 2512-01-31 00:00:00
echo $dt->addCentury();                  // 2612-01-31 00:00:00
echo $dt->subCentury();                  // 2512-01-31 00:00:00
echo $dt->subCenturies(5);               // 2012-01-31 00:00:00

// 加减年
echo $dt->addYears(5);                   // 2017-01-31 00:00:00
echo $dt->addYear();                     // 2018-01-31 00:00:00
echo $dt->subYear();                     // 2017-01-31 00:00:00
echo $dt->subYears(5);                   // 2012-01-31 00:00:00

// 加减季度
echo $dt->addQuarters(2);                // 2012-07-31 00:00:00
echo $dt->addQuarter();                  // 2012-10-31 00:00:00
echo $dt->subQuarter();                  // 2012-07-31 00:00:00
echo $dt->subQuarters(2);                // 2012-01-31 00:00:00

// 加减月
echo $dt->addMonths(60);                 // 2017-01-31 00:00:00
echo $dt->addMonth();                    // 2017-03-03 00:00:00 equivalent of $dt->month($dt->month + 1); so it wraps
echo $dt->subMonth();                    // 2017-02-03 00:00:00
echo $dt->subMonths(60);                 // 2012-02-03 00:00:00

// 加减天
echo $dt->addDays(29);                   // 2012-03-03 00:00:00
echo $dt->addDay();                      // 2012-03-04 00:00:00
echo $dt->subDay();                      // 2012-03-03 00:00:00
echo $dt->subDays(29);                   // 2012-02-03 00:00:00

// 加减工作日
echo $dt->addWeekdays(4);                // 2012-02-09 00:00:00
echo $dt->addWeekday();                  // 2012-02-10 00:00:00
echo $dt->subWeekday();                  // 2012-02-09 00:00:00
echo $dt->subWeekdays(4);                // 2012-02-03 00:00:00

// 加减周
echo $dt->addWeeks(3);                   // 2012-02-24 00:00:00
echo $dt->addWeek();                     // 2012-03-02 00:00:00
echo $dt->subWeek();                     // 2012-02-24 00:00:00
echo $dt->subWeeks(3);                   // 2012-02-03 00:00:00

// 加减小时
echo $dt->addHours(24);                  // 2012-02-04 00:00:00
echo $dt->addHour();                     // 2012-02-04 01:00:00
echo $dt->subHour();                     // 2012-02-04 00:00:00
echo $dt->subHours(24);                  // 2012-02-03 00:00:00

// 加减分钟
echo $dt->addMinutes(61);                // 2012-02-03 01:01:00
echo $dt->addMinute();                   // 2012-02-03 01:02:00
echo $dt->subMinute();                   // 2012-02-03 01:01:00
echo $dt->subMinutes(61);                // 2012-02-03 00:00:00

// 加减秒
echo $dt->addSeconds(61);                // 2012-02-03 00:01:01
echo $dt->addSecond();                   // 2012-02-03 00:01:02
echo $dt->subSecond();                   // 2012-02-03 00:01:01
echo $dt->subSeconds(61);                // 2012-02-03 00:00:00

// 加减毫秒
echo $dt->addMilliseconds(61);           // 2012-02-03 00:00:00
echo $dt->addMillisecond();              // 2012-02-03 00:00:00
echo $dt->subMillisecond();              // 2012-02-03 00:00:00
echo $dt->subMillisecond(61);            // 2012-02-03 00:00:00

// 加减微秒
echo $dt->addMicroseconds(61);           // 2012-02-03 00:00:00
echo $dt->addMicrosecond();              // 2012-02-03 00:00:00
echo $dt->subMicrosecond();              // 2012-02-03 00:00:00
echo $dt->subMicroseconds(61);           // 2012-02-03 00:00:00

echo $dt->add(61, 'seconds');                      // 2012-02-03 00:01:01
echo $dt->sub('1 day');                            // 2012-02-02 00:01:01
echo $dt->add(CarbonInterval::months(2));          // 2012-04-02 00:01:01
echo $dt->subtract(new DateInterval('PT1H'));      // 2012-04-01 23:01:01
```

- 两个时间比较

```php
/**
  * min –返回最小日期。
  * max – 返回最大日期。
  * eq – 判断两个日期是否相等。
  * gt – 判断第一个日期是否比第二个日期大。
  * lt – 判断第一个日期是否比第二个日期小。
  * gte – 判断第一个日期是否大于等于第二个日期。
  * lte – 判断第一个日期是否小于等于第二个日期。
*/
$first = Carbon::create(2019, 1, 20, 12, 30, 30);
echo $first->toDateTimeString();                   // 2019-01-20 12:30:30

$second = Carbon::create(2019, 1, 22, 9, 30, 30);
echo $second->toDateTimeString();                  // 2019-01-22 09:30:30

// 是否相等
var_dump($first->equalTo($second));                // bool(false)
var_dump($first->eq($second));                     // bool(false)
var_dump($first == $second);                       // bool(false)

// 是否不相等
var_dump($first->notEqualTo($second));             // bool(true)
var_dump($first->ne($second));                     // bool(true)
var_dump($first != $second);                       // bool(true)

// $first 是否大于 $second
var_dump($first->gt($second));                     // bool(false)
var_dump($first->greaterThan($second));            // bool(false)
var_dump($first->isAfter($second));                // bool(false)
var_dump($first > $second);                        // bool(false)

// $first 是否大于或等于 $second
var_dump($first->gte($second));                    // bool(false)
var_dump($first->greaterThanOrEqualTo($second));   // bool(false)
var_dump($first >= $second);                       // bool(false)

// $first 是否小于 $second
var_dump($first->lt($second));                     // bool(true)
var_dump($first->lessThan($second));               // bool(true)
var_dump($first->isBefore($second));               // bool(true)
var_dump($first < $second);                        // bool(true)

// $first 是否小于或等于 $second
var_dump($first->lte($second));                    // bool(true)
var_dump($first->lessThanOrEqualTo($second));      // bool(true)
var_dump($first <= $second);                       // bool(true)
```

<br>

- 格式化处理

```php
Carbon::now()->format('Y/m/d H:i:s');			// 2021/02/19 17:23:27
Carbon::now ()->format ('Y 年 m 月 d 日');		  // 2021 年 02 月 19 日
```



<br>

如需更加详细的使用方法，可以参考[官方文档](https://carbon.nesbot.com/docs/#api-introduction)。

在线操作请看：http://try-carbon-package.herokuapp.com/



