
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


### primitive types

rust中的`new`方法是开辟堆内存的规范方法接口。
`let a: slice = .......`的切片需要对其引用进行`let b = &a[.........]`，因为slice内部可能可变，所以我们用引用。

且rust直接*解压*

#### 动态数组
我们这样实现一个广播，如果是用`map`，我们再这个闭包里面得到的`item`并不是引用，可以发现其类型就是`i32`.如果熟悉rs的所有权就很清楚了，这里肯定是直接返回`item * 2`. 不能是`item *= 2`.
rs问map的描述:
> If you have an iterator that gives you elements of some type A, and you want an iterator of some other type B, you can use map(), passing a closure that takes an A and returns a B.
```rust
fn boardcast_map(v: &Vec<i32>) -> Vec<i32> {

    v.iter().map(|item| {
        item * 2
    }).collect::<Vec<i32>>()

}

```
并且得到`struct map`后还要通过`collect::<Vec<i32>>`这个`collect` + turbofish expr来转化。

而如果是迭代地:
```rust
for item: &mut i32 in v.iter_mut(){
	item *= 2;
}
```
这样就可以，且是对原`v`直接修改

下标索引两种方法：
* `v.get(index)`
* `&v[index]`
前者性能会由轻微所耗但是会做边界检查。返回的是`Option`.

多借用。







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


### MODULES
E0603:
> import const if private

我们需要将`const`也添加`pub`关键词
```rust
mod delicious_snacks {
    // TODO: Fix these use statements
    use self::fruits::PEAR as fruit;
    use self::veggies::CUCUMBER as veggie;

    pub mod fruits {
        pub const PEAR: &'static str = "Pear";
        pub const APPLE: &'static str = "Apple";
    }

    pub mod veggies {
        pub const CUCUMBER: &'static str = "Cucumber";
        pub const CARROT: &'static str = "Carrot";
    }
}
```
```rust
        delicious_snacks::fruits::PEAR,
        delicious_snacks::veggies::CUCUMBER
```
这样可以
但是如果
```rust
        delicious_snacks::fruit,        delicious_snacks::veggie
```
则出现"private constant import"

同样我们只要在`use`前加上`pub`即可






