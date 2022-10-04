
# Rustlings 碎片记录

[toc]
## rustlings说明

* rustlings的中心思想就是修复每个练习内的 errors.

* `rustings watch`模式（推荐）下，它会自动开始第一个联系。

* 卡住了可以使用`hint`在watch mode下或者`rustlings hint exercise_name`.

* 使用RA来自动补全，可以运行`rustlings lsp`


## 记录
### Keywords

`const`和`let`是地位等同的。

### 字面量
Rust 中有两种字符串，`String` 和 `&str`，其中 `String` 可动态分配、修改，内部实现可以理解为 `Vec<u8>`，而 `&str` 是一个类型为 `&[u8]` 的切片。这两种字符串都只能保存合法的 UTF-8 字符。

而对于非肉眼可辨识的 UTF-8 字符，则可以考虑使用如下类型：

文件路径有专用的 `Path` 和 `PathBuf` 类可用。
使用 `Vec<u8>` 和 `&[u8]`
使用 `OSString` 和 `&OSStr` 和操作系统交互
使用 `CString` 和 `&CStr` 和 C 库交互(ffi)
上面的第二种方法，就是常用的处理非 UTF-8 字节流的方式，也就是使用 `Vec<u8>` 和 `&[u8]` ，其中我们也可以使用字面值来处理这两种类型的数据，我们称之为字节字符串字面值（byte string literals），其类型为 `&[u8]`。


rust字符串字面量不仅支持ASCII转义，还支持对Unicode转义.

* `\xHH`
* `\u{xxx}` 24bits long, hex, unicode
* `\n,\r,\t` => Unicode: LF, CR, HT,
* `\\`  => `\` it self
* `\0`  => Unicode: NUL

转化raw string则需要使用`r`(raw)开头，且头尾用同样多的`#`。`#`用来框定raw string的范围。







