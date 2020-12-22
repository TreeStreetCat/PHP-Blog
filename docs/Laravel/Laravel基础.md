# 一、Laravel中访问器&修改器



访问器和修改器允许你在获取模型属性或设置其值时格式化 Eloquent 属性。例如，你可能想要使用 Laravel 加密器对存储在数据库中的数据进行加密，并且在 Eloquent 模型中访问时自动进行解密。

**访问器**

举个例子，有下面这么一个成绩表`grade`

| id   | name | math_grade | eng_grade |
| ---- | ---- | ---------- | --------- |
| 1    | 小王 | 78         | 88        |
| 2    | 小红 | 99         | 100       |

我们想计算小王和小红的总成绩(即数学成绩+英语成绩)，这个时候如果在`grade`里添加一个`total_score`的字段，可能会让数据表显得十分冗余。因此，我们需要一个`访问器`。



1.在Model里做以下修改：

- 要定义一个访问器，修改器的格式必须为`getXXXAttribut()`且需要访问的字段使用驼峰命名，我们在model里新增一个`getTotalScoreAttribute`方法，

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



**修改器**

与访问器相反，修改器用于字段值保存到数据库之前进行一些相关处理，如密码加密，数据格式化等等。

要定义一个修改器，则需在模型上定义一个`setFooAtrribute`方法，要访问的Foo字段需使用驼峰式命名，命名规则与访问器一致。

1.定义一个password属性的修改器`setPasswordAttribute`，当我们在模型上设置password的值时会自动触发修改器，自动调用`setPasswordAttribute`方法，将password进行加密

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

