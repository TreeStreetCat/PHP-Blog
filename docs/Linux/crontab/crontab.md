
# Linux Crontab 定时任务

### Cron 介绍

我们经常使用的是 crontab 命令是 cron table 的简写，它是 cron 的配置文件，也可以叫它作业列表。我们可以使用crontab来定时做一些事情，比如每天凌晨2点进行的定期备份。

我们可以在以下文件夹内找到相关配置文件。

- `/var/spool/cron/ ` 目录下存放的是每个用户包括 root 的 crontab 任务，每个任务以创建者的名字命名
- `/etc/crontab` 这个文件负责调度各种管理和维护任务。
- `/etc/cron.d/` 这个目录用来存放任何要执行的 crontab 文件或脚本。
- 我们还可以把脚本放在 `/etc/cron.hourly` 、`/etc/cron.daily`、`/etc/cron.weekly`、`/etc/cron.monthly` 目录中，让它每小时/天/星期、月执行一次。

<br>

### Crontab 使用

我们常用的命令如下：

```shell
crontab [-u username]　　　　//省略用户表表示操作当前用户的crontab
    -e      (编辑工作表)
    -l      (列出工作表里的命令)
    -r      (删除工作作)
```

我们用 **crontab -e** 进入当前用户的工作表编辑，是常见的 vim 界面。每行是一条命令。

我们用 **crontab -l** 列出当前用户的工作表所有命令

我们用 **crontab -r** 会从 `/var/spool/cron` 目录中删除某个用户的 crontab 文件，如果不指定用户，则默认删除当前用户的 crontab 文件，`慎重操作`。

crontab 的命令构成为 时间+动作，时间即 cron 表达式，动作即操作命令或脚本。例如：每1分钟执行一次myCommand

```shell
* * * * * myCommand
```

<br>


### Cron表达式
Cron 表达式是一个字符串，字符串以4或5个空格隔开，分为5或6个域，每一个域代表一个含义，Cron 有如下两种

#### 语法格式：

`Minutes Hours DayofMonth Month DayofWeek Year`
或
`Minutes Hours DayofMonth Month DayofWeek`


例如要表示 `每隔1分钟执行一次`，则 cron 写法如下：

```shell
1 * * * ?  
```

`注意:crontab里不支持秒，但是像Quartz  Cron是支持秒的。这点要区分好。`

<br>

下面我们来解释各个域代表的值以及允许的值：

| 域             | 允许的数值             | 允许的特殊字符        | 备注                                 |
| -------------- | ---------------------- | --------------------- | ------------------------------------ |
| 分Minutes      | 0-59                   | - * /                 |                                      |
| 小时Hours      | 0-23                   | - * /                 |                                      |
| 日期DayofMonth | 1-31                   | - * ? / L W C         |                                      |
| 月份Month      | 1-12                   | JAN-DEC - * /         |                                      |
| 星期DayofWeek  | 1-7                    | SUN-SAT - * ? / L C # | 1 表示星期天，2 表示星期一，依次类推 |
| 年（可选）Year | 留空(empty)，1970-2099 | , - * /               |                                      |

<br>

#### 允许的值解释：

**Minutes(分)：** 可以用数字0－59 表示。

**Hours(时)：**  可以用数字0 - 23表示。

**Day-of-Month(天) ：** 可以用数字1 - 31 中的任一一个值，但要注意一些特别的月份。

**Month(月)：** 可以用0 - 11 或用字符串 `JAN, FEB, MAR, APR, MAY, JUN, JUL, AUG, SEP, OCT, NOV and DEC”`表示。

**Day-of-Week(每周)：** 可以用数字1 - 7表示（1 ＝ 星期日）或用字符口串`SUN, MON, TUE, WED, THU, FRI and SAT`表示。

<br>

#### 符号解释：

**\***  表示所有值，代表整个时间段

**?**  表示未说明的值，即不关心它为何值；可以用来表示每月的某一天，或第几周的某一天

这里举个例子，如果我们指定每个月 4 号执行一次，但是每月 4 号是星期几是不确定的，我们并不知道4号是周几，我们也并不关心4号是周几，因此不能使用 **\*** 而是要使用 **?** 

所以每个月 4 号执行一次可以这样来写

```shell
* * 4 * ?
```
<br>

**-**   表示一个指定的范围。

**,**   表示附加一个可能值。

**/**   符号前表示开始时间，符号后表示每次递增的值； 

**L**("last")    `L` 用在`day-of-month`字段意思是  `这个月最后一天`；用在  `day-of-week `字段, 它简单意思是 `7` or `SAT`，表示星期日。 如果在 `day-of-week` 字段里和数字联合使用，它的意思就是 `这个月的最后一个星期几`， 例如： `6L` 表示 `这个月的最后一个星期五`。

**W**("weekday") 只能用在 `day-of-month` 字段。用来描叙最接近指定天的工作日（周一到周五）。例如：在 `day-of-month ` 字段用 `15W` 指最接近这个 月第15天的工作日，即如果这个月第15天是周六，那么触发器将会在这个月第14天即周五触发；如果这个月第15天是周日，那么触发器将会在这个月第 16天即周一触发；如果这个月第15天是周二，那么就在触发器这天触发。注意一点：这个用法只会在当前月计算值，不会越过当前月。`W` 字符仅能在  `day-of-month` 指明一天，不能是一个范围或列表。也可以用 `LW` 来指定这个月的最后一个工作日。 

**\#**  只能用在 `day-of-week` 字段。用来指定这个月的第几个周几。例：在 `day-of-week` 字段用 `6#3` 指这个月第3个周五（6指周五，3指第3个）。如果指定的日期不存在，触发器就不会触发。 

**C**  指和calendar联系后计算过的值。例：在 `day-of-month` 字段用 `5C` 指在这个月第5天或之后包括 `calendar` 的第一天；在 `day-of-week` 字段用 `1C` 指在这周日或之后包括 `calendar` 的第一天。

<br>

#### 示例：

```shell
 */1 * * * ? 每隔1分钟执行一次
 0 5-15 * * ? 每天5-15点整点触发
 0/3 * * * ? 每三分钟触发一次
 0-5 14 * * ? 在每天下午2点到下午2:05期间的每1分钟触发 
 0/5 14 * * ? 在每天下午2点到下午2:55期间的每5分钟触发
 0/5 14,18 * * ? 在每天下午2点到2:55期间和下午6点到6:55期间的每5分钟触发
 0/30 9-17 * * ? 朝九晚五工作时间内每半小时
 0 10,14,16 * * ? 每天上午10点，下午2点，4点 
 
 0 12 ? * WED 表示每个星期三中午12点
 0 17 ? * TUES,THUR,SAT 每周二、四、六下午五点
 10,44 14 ? 3 WED 每年三月的星期三的下午2:10和2:44触发 
 15 10 ? * MON-FRI 周一至周五的上午10:15触发
 0 23 L * ? 每月最后一天23点执行一次
 15 10 L * ? 每月最后一日的上午10:15触发 
 15 10 ? * 6L 每月的最后一个星期五上午10:15触发 
 15 10 * * ? 2005 2005年的每天上午10:15触发 
 15 10 ? * 6L 2002-2005 2002年至2005年的每月的最后一个星期五上午10:15触发 
 15 10 ? * 6#3 每月的第三个星期五上午10:15触发


 0 12 * * ? 每天中午 12 点触发
 15 10 ? * * 每天上午 10:15 触发
 15 10 * * ? 每天上午 10:15 触发
 15 10 * * ? * 每天上午 10:15 触发
 15 10 * * ? 2005 2005 年的每天上午 10:15 触发
 * 14 * * ? 每天下午 2 点到 2:59 期间的每 1 分钟触发
 0/5 14 * * ? 每天下午 2 点到 2:55 期间的每 5 分钟触发
 0/5 14,18 * * ? 每天下午 2 点到 2:55 期间和下午 6 点到 6:55 期间的每 5 分钟触发
 0-5 14 * * ? 每天下午 2 点到 2:05 期间的每 1 分钟触发
 10,44 14 ? 3 WED 每年三月的星期三的下午 2:10 和 2:44 触发
 15 10 ? * MON-FRI 周一至周五的上午 10:15 触发
 15 10 15 * ? 每月 15 日上午 10:15 触发
 15 10 L * ? 每月最后一日的上午 10:15 触发
 15 10 ? * 6L 每月的最后一个星期五上午 10:15 触发
 15 10 ? * 6L 2002-2005 2002 年至 2005 年的每月的最后一个星期五上午 10:15 触发
 15 10 ? * 6#3 每月的第三个星期五上午 10:15 触发
```



通过 crontab 命令，我们可以在固定的间隔时间执行指定的系统指令或 shell script脚本。 时间间隔的单位可以是分钟、小时、日、月、周及以上的任意组合。 这个命令非常设合周期性的日志分析或数据备份等工作。

<br>

### Laravel实战定时任务

#### 创建命令
```shell
php artisan make:command Test
```
`make:command` 命令会在 app/Console/Commands 目录中创建一个新的命令类。如果command目录不存在，它会在你第一次运行 `make:command` 命令时自动创建。生成的命令将包含所有命令中默认存在的属性和方法。

```php
<?php

namespace App\Console\Commands;

use Illuminate\Console\Command;

class Test extends Command
{
	// 此处省略部分默认生成代码...

    /**
     * Execute the console command.
     *
     * @return int
     */
    public function handle()
    {
        \Log::info("Test");
        return 0;
    }
}
```

<br>

#### 定义调度任务

你可以在 `App\Console\Kernel` 类的 `schedule` 方法中定义所有调度任务。让我们从一个调度任务的例子开始，在这个例子中，我们会调用 `Test` 命令类执行，每隔1分钟打印出 `Test` 日志。

```php
<?php

namespace App\Console;

use Illuminate\Console\Scheduling\Schedule;
use Illuminate\Foundation\Console\Kernel as ConsoleKernel;

class Kernel extends ConsoleKernel
{
    /**
     * The Artisan commands provided by your application.
     *
     * @var array
     */
    protected $commands = [
        //
    ];

    /**
     * Define the application's command schedule.
     *
     * @param  \Illuminate\Console\Scheduling\Schedule  $schedule
     * @return void
     */
    protected function schedule(Schedule $schedule)
    {
         $schedule->command('command:test')->cron('*/1 * * * *');
//         $schedule->command('command:test')->everyMinute();
    }

    /**
     * Register the commands for the application.
     *
     * @return void
     */
    protected function commands()
    {
        $this->load(__DIR__.'/Commands');

        require base_path('routes/console.php');
    }
}
```

<br>

#### 启动 Schedule
在定义完以上的任务之后，可以通过 `php artisan schedule:run` 来执行这些任务，但是，这个任务执行起来后，需要不断的执行这个这个命令定时器才能不断的运行，所以就需要 linux 的系统功能的帮助，在命令行下执行下面的命令：

```shell
crontab -e
```
- 执行完以上的命令之后，会出现一个处于编辑状态的文件，在文件中填入以下内容：

```shell
* * * * * php /path/to/artisan schedule:run
```

- 如果服务器上有多个PHP或者需要指定某个PHP版本执行，可以在文件中填入以下内容：

```bash
* * * * * root /path/to/php /path/to/artisan schedule:run
```

上面一行中的 `/path/to/php`  和`/path/to/artisan` 需要改为实际的路径。



将Laravel项目部署至Linux服务器上，使用 crontab -l ，可以看到刚才编写过的命令

```shell
[wwwroot@xxx root]$ crontab -l
* * * * * /usr/local/php7/bin/php /wwwroot/cms/artisan schedule:run
```

上面命令的含义是每隔一分中就执行一下 `schedule:run` 命令。这样一来，前面定义的任务就可以不断的按照定义的时间间隔不断的执行，定时任务的功能也就实现了。

<br>

参考博客：https://learnku.com/laravel/t/1402/laravel-timing-task

https://lvwenhan.com/laravel-advanced/481.html

