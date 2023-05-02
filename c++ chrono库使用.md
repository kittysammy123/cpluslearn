## 										c++ chrono库使用

chrono是纳秒级别的，ctime库里面的time等函数返回的是秒

#### 1.头文件

```
#include <chrono>
```

#### 2.使用

`chrono` 库中有三种时间点类型：`system_clock`、`steady_clock` 和 `high_resolution_clock`。`system_clock` 提供了当前时间的系统级表示，`steady_clock` 提供了一个单调递增的时间点，`high_resolution_clock` 提供了一个高分辨率的时间点。我们可以使用这些时间点类型来表示时间。

```
using namespace std::chrono;

// 获取当前时间点
auto now = system_clock::now();
auto start = steady_clock::now();
auto high_res = high_resolution_clock::now();

//平常使用system_clock就行了
```

#### 3.system_clock使用

systemc_clock是结构体，

```
// 计算时间间隔，now()方法返回std::chrono::time_point
std::chrono::time_point<std::chrono::system_clock> now = std::chrono::system_clock::now();
auto duration = system_clock::now() - now;

// 转换为毫秒
//
auto millisec = duration_cast<milliseconds>(duration);

// 转换为微秒
auto microsec = duration_cast<microseconds>(duration);

// 转换为纳秒
auto nanosec = duration_cast<nanoseconds>(duration);

std::chrono::microseconds us(1234567);
std::chrono::milliseconds ms = std::chrono::duration_cast<std::chrono::milliseconds>(us);

```

3.2获取当前时间点的时间戳（就是相对0点的时间间隔）

```
//这是一个duration
auto timestamp = std::chrono::system_clock::now().time_since_epoch();
//duration转换为longlong的计数值
auto timestamp = std::chrono::system_clock::now().time_since_epoch().count();

```

3.3计算时间间隔

```
auto start = std::chrono::system_clock::now();
// 执行一些操作
auto end = std::chrono::system_clock::now();
//两个time_point相减，转换为duration，再转换为计数值
auto elapsed = std::chrono::duration_cast<std::chrono::milliseconds>(end - start).count();

```

3.4格式化时间点

```
auto now = std::chrono::system_clock::now();
auto t = std::chrono::system_clock::to_time_t(now);  to_time_t是将time_point转换为time_t ，time_t实际上就是long类型
//local_time将time_t转换为本地的tm结构体，包含时分秒等信息
//put_time将tm结构体转换为指定的格式格式化为字符串
std::cout << std::put_time(std::localtime(&t), "%Y-%m-%d %H:%M:%S") << std::endl;

```

```
put_time 函数的格式化字符串可以使用以下占位符：

占位符	含义
%Y	年（四位数字）
%m	月（两位数字）
%d	日（两位数字）
%H	小时（24 小时制）
%M	分钟（两位数字）
%S	秒（两位数字）
%j	年内天数（三位数字）
%U	年内周数（两位数字，以周日为一周的第一天）
%W	年内周数（两位数字，以周一为一周的第一天）
%Z	时区（例如 CST）
```

