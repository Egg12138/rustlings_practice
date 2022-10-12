
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

..= 语法允许你匹配一个闭区间序列内的值
### primitive types

rust中的`new`方法是开辟堆内存的规范方法接口。
`let a: slice = .......`的切片需要对其引用进行`let b = &a[.........]`，因为slice内部可能可变，所以我们用引用。

且rust直接*解压*
#### From/Into
From 和 Into 两个 trait 是内部相关联的，实际上这是它们实现的一部分。如果我们能够从类型 A 得到类型 B，那么很容易相信我们也能把类型 B 转换为类型 A。
Into trait 就是把 From trait 倒过来而已。也就是说，如果你为你的类型实现了 From，那么同时你也就免费获得了 Into。

使用 Into trait 通常要求指明要转换到的类型，因为编译器大多数时候不能推断它。
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


### Error Handling
`Result<T,E>`具体来可以是`Result<T,T>`

**errors6.rs** 和其他的自定义错误处理系统的代码值得学一下。



### generics && traits

注意，我们是需要`impl<T,U,...> struct<T>`的。这样struct才能用到`T,U,...`等泛型。`impl`的泛型可以比后面跟的 struct自身泛型更多，因为impl的函数也可以接受其他泛型参数。
```rust
impl<T> Wrapper<T> {
    pub fn new<T>(value: T) -> Wrapper<T> {
        Wrapper { value }
    }
}
```
上面是一个有问题的泛型定义。因为rustc在这里认为`new<T>`声明了一个新的泛型签名。删去即可。
并且`Point<T>`和`Point`不是一回事了。

rust不在方法签名和类型定义上做类型推导。所以静态方法调用时，可以要在结构体后面用 Turbofish 提供具体的泛型参数。如果不添加的化要保证rustc能够推导出类型来。

```rust
trait AppendBar {
    fn append_bar(self) -> Self;
}

impl AppendBar for String {
    //Add your code here
    fn append_bar(mut self) -> Self {
        self.push_str("Bar");
        self
    }
}
```
特征声明时`mut`不应该被引入。 rust 默认实现`#[deny(patterns_in_fns_without_body)]`

> The patterns_in_fns_without_body lint detects mut identifier patterns as a parameter in functions without a body.
```rust
trait Trait {
    fn foo(mut arg: u8);
}
{{produces}}
```

> Explanation
> To fix this, remove mut from the parameter in the trait definition; it can be used in the implementation. That is, the following is OK:
```rust
trait Trait {
    fn foo(arg: u8); // Removed `mut` here
}

impl Trait for i32 {
    fn foo(mut arg: u8) { // `mut` here is OK

    }
}
```
> The parameter names in the body-less functions are only allowed to be _ or an identifier for documentation purposes (only the type is relevant).

issuse 35203:
> They are ignored and useless. If some pattern is written in the trait, or foreign fn, or fn pointer position it's either a mistake/misunderstanding (all examples found by crater) or some macro expanding into both trait definition and implementations (this wasn't possible before Accept interpolated patterns in trait method parameters #45775).
> Technical reason - we don't want to check these patterns. Patterns are checked in bodies and fn pointers etc don't have bodies and we don't want to create bodies for them only to check something as useless as patterns that are ignored otherwise.


默认 `trait` 实现后可以通过这样限定参数为都实现了同一个 trait 的类型，但不一定是要同一种类型:
```rust 
fn compare_license_types(software: impl Licensed, software_two: impl Licensed)-> bool 
```
如果是下面这样，那就是限定了参数为同一类型
```rust
fn foo<T: TraitName>(arg1: &T, arg2: &T)
```

多种 trais:
```rust
pub multitraits(arg1: impl Trait1 + Trait2, arg2: impl Trait2 + Trait3)
```
或者
```rust
pub multitraits_sametype<T: Trait1 + Trait2, U: Trait2 + Trait3>(arg1: T, arg2: U)
```

### Iterator

迭代器可以直接用`.iter()`获得, 我们可以对其使用`.next()`关联函数

String 可以通过`collect`来得到。因为她本质是`Vec<u8>`
所以对于一个字符迭代器:
```rust
let iterator_c = raw_str.iter();
let string = iterator_c.map(|&x| x as u8).collect()
```

```rust
pub fn capitalize_words_string(words: &[&str]) -> String {
    let mut news = String::new();
    let &w = words;
```

这样的写法是因为，`[&str]`仍然是一个DST，所以还需要一个引用

注意经常经常出现的`required by a bound...`这个跟泛型推导失败相关的编译器错误。比如一些`map`里，我们引入了`unwrap`那么就有可能会推断不出类型


`collect`会转化类型
```rust
// Desired output: Ok([1, 11, 1426, 3])
fn result_with_list() -> Result<Vec<i32>, DivisionError> {
    let numbers = vec![27, 297, 38502, 81];
    numbers.into_iter().map(|n| divide(n, 27)).collect()
}

// Complete the function and return a value of the correct type so the test passes.
// Desired output: [Ok(1), Ok(11), Ok(1426), Ok(3)]
fn list_of_results() -> Vec<Result<i32, DivisionError>> {
    let numbers = vec![27, 297, 38502, 81];
    numbers.into_iter().map(|n| divide(n, 27)).collect()
}
```

rust stdlib提供了`fold`方法，对于递归结构非常好用。

**对类型匹配**
```rust
fn count_iterator(map: &HashMap<String, Progress>, value: Progress) -> usize {
map.values().fold(0_usize, |acc, prog_state| {
        match prog_state {
            Progress::Complete => { acc + 1 },
            _ => { acc },
        }
    })
```
如果是对值，比如说同样是枚举类型，输入参数可以改变枚举类型，用上述方法就拉了，直接用`filter`才是更高效的:
```rust



```