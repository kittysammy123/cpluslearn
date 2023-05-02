# c++ 宽窄字符转换

## 1.char* 与wchar*之间的转换

```
头文件 #include<cstdlib>
//wchar  to multi bytes  将宽字符转换为多字节字符
wctomb

//mb to wc  多字节转换为宽字符
mbtowc

//wcs to mbs  将宽字符串转换为多字节字符串
std::size_t wcstombs( char* dst, const wchar_t* src, std::size_t len);

//mbs to wcs  将多字节字符串转换为宽字符
std::size_t mbstowcs( wchar_t* dst, const char* src, std::size_t len);
dst 为nullptr时，返回wchar*所需的宽字节数
dst 不为nullptr，则返回dst实际占用的字节数
```



## 2.string与wstring之间的转换（linux下wchar采用的时utf32，windows是utf16）

#### 2.1使用ICU库,提供统一的转换

```

```

#### 2.2使用c++  wstring_convert

```
//头文件 #include<locale>   
//转换器头文件#include<codecvt>
//使用的转换器codecvt_utf8_utf16    utf8与utf16之间的编码转换

//定义转换对象和编码类型
std::wstring_convert<std::codecvt_utf8_utf16<wchar_t>> converter;
//utf8 转utf16
wstring wide_str = converter.from_bytes("hello");
//utf16转utf8
string utf8_str = converter.to_bytes(wide_str);

```

### 3.to_string用于将各种值转换为string ,头文件#include<string>

