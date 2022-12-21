
> Rust为什么会需要loop关键词？为什么不用while true代替loop？这是由Rust的设计宗旨所决定的，因为用Rust编程，本质上就是程序员和编译器好好说话、沟通交流的过程，尽可能多地告诉编译器精准的信息，编译器才能更好地辅助程序员编写出功能正确、符合预期的程序。我特意创造了两个短句，类似“CAD”，“CAM”，专门用来总结Rust的核心设计思想：Compiler Aided Programming / Compiler Aided Software Engineering

```rust
fn main() {
    let i = 1;
    bar(1);
    foo(1);
}
```

> 编译器报错，因为循环被认为可以正常终止，函数将返回i32类型的值。

```rust
fn foo(mut i: i32) -> i32 {
    while true {
       println!("Hello Foo");  
       if i == 3 {  
         std::process::exit(1);  
       }
       i += 1;
    }
}
```

> 编译器知道这个函数要么一直运行，要么异常返回，肯定不会正常返回。

```rust
fn bar(mut i: i32) -> ! {
    loop {
       println!("Hello Bar");  
       if i == 3 {
         std::process::exit(1);  
       }
       i += 1;
    }
}
```

我觉得Rust可以新增一个语法，对语句块/Scope进行命名。命名的好处在于，语句块可在源代码的其他位置被引用（相当于自动复制了一份），而不需要手动复制粘贴（又重新写了一遍，费劲，没必要）。

若将

```rust
{
    ...
}
```

改写为

```rust
{
    scope FOO;
    ...
}
```

则表示此代码块/Scope被命名为FOO

```rust
FOO {
    fn hello() {
        println!("hello");
    }
}
```

表示此处包含了整个FOO代码块的内容，并新添加了一个hello函数定义。本质上就是在一个地方给代码块起个名字，在另一个地方才能指名要copy那个代码块，并且可在copy之后，添加/删除一些变量定义、函数定义等。

或者改写为其他风格的语法

```rust
FOO::{
    fn hello() {
        println!("hello")
    }
}
```

```rust
{
    copy FOO;
    fn hello() {
        println!("hello")
    }
}
```

或者采用“#”号注解的方式

```rust
#[scope(FOO)]
{
    ...
}

#[scope_copy(FOO)]
{
    fn hello() {
        println!("hello")
    }
}
```



