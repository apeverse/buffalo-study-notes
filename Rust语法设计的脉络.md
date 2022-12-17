# Rust语法设计的脉络

现代的任何一门编程语言，其设计都可分为三个方面：

- 1）核心语法设计
- 2）并发编程模型
- 3）项目组织管理

Rust作为一门功能齐全，性能强悍的系统编程语言，自然也不例外，在这三方面，Rust做了足够多的思考提炼。我自觉掌握程度不够精细，简明、扼要、完整地总结这几方面，难度艰巨，因此这里只简单总结其一面：核心语法设计。

刚接触Rust的程序员，估计会有一种感觉：语法花哨繁杂，知识点多，只比C++简单一点，学习很吃力，需长时间的琢磨、足够多的练习，才能逐渐熟悉起来。

因此，找出Rust语法设计的脉络，并顺着脉络主线向前走，而不用强记零碎繁多的语法点，就显得尤为重要，这可节约许多精力与学习成本，并让我们尽快掌握它。

任何编程语言的设计特征，必然会反应其设计者的世界观，以及如何审视现实世界的底层构造。所以在学习和理解Rust时，可将自己想象成一个编程语言设计者，站在设计者的角度去思考，更有代入感、更容易总结出语法设计的脉络主线：

> Process（Runtime of Program），进程是程序的运行时，涉及环境变量、动态库装载、内存布局、信号处理、网络通信、进程间通信、生成子进程、与OS交互等多方面内容。代码、数据、通信、环境，四个要素，定义了一个进程。

> 或许可以这么看：无极（程序）=> 太极（进程）=> 两仪（代码、数据）=> 四象（代码、数据、通信、环境）。

> 程序由什么构成？（代码Code）与（数据Data），Code是Data，而Data也可以是Code，比如Closure有时候也被认为是Data，可以很方便地传递。

> Data通常又被称为（对象Object）或（值Value），不同语境下会有不同的称呼。

> Code与Data，都有(类型Type)。

> Code的Type，语言自带的，就是（运算符Operator），比如：(加+)、(减-)、(乘*)、(除/)、(余%)；可自定义的，主要是(函数Function)与(闭包Closure)。

> Data的Type，语言自带的，就是基础性的、原子性的数据类型(Primitive)，比如(u8、i64、bool)；可自定义的，主要是(struct、enum、tuple)，可称之为复合数据类型(Complex)，Rust标准库自带的(array、vector、string、slice、str)等类型，是通过struct实现的。

> Function用于操作数据，当用（impl关键词）与（Complex类型）建立关联时，就是面向对象编程思想的自然表达。将Complex Data的（self指针）设定为Function的第一个参数，则将Data与function绑定在一起，以此完成对象与方法的结合调用。

> 面向对象编程的四大核心概念是：抽象、封装、继承、多态，struct、enum实现了简单而富有表达力的抽象、封装，impl、trait则实现了自由灵活的继承、多态。

> Function的集合（一组接口定义）被称为（品格trait），同样采用impl和Primitive、Complex 类型挂接起来。

> 当我们创建（trait object）时，实际上是创建了（一对指针），一个指向数据对象，另一个指向函数列表，（trait object）像胶水一样把一组Function和data粘合绑定在一起。这是一种非常松散的耦合关系，为编程带来了高度的灵活性，可很容易针对Primitive类型（而不仅仅是Complex类型）设计和使用trait。

> trait可以和任意Type的Data绑定，为了让trait所包含的Function接口定义更灵活，又引入了Self关键词，用于指定Function的返回值Type，比如：fn funcx() -> Self，则foo.funcx()的返回值类型就会与foo的类型保持一致。

> 用最少的源代码表达完整的意图、做尽可能多的事，把这种思想继续发挥下去，即为（泛型Generic）。

> Complex Type、Function、trait在定义的时候，都可额外引入（类型参数），这就形成了较为通用的数据模版、函数模版、品格模版，以实现用一套（描述代码）按需编译生成多套（目标代码）。

> struct的功能是组合性的多个、多种数据的集结，而enum的功能是排他性的、唯一性相关的数据的抉择。Rust的enum，像是借鉴并揉合了C语言的union和enum，但远远扩展了它们的功能，到令人惊叹的地步，不得不佩服其设计者满地流淌的横溢才华。

> 举例：存在与虚无、正确与错误，是构成现实世界的基本逻辑要素，编程无非就是对现实世界的抽象模拟，程序员总会思考诸类问题：foo列表中有元素吗？bar函数返回一个正确结果呢，还是报错呢？如何处理这两类问题，往往决定了能否写出简洁优美的代码。我还从未见过其他语言（虽然我接触的语言并不多）能够像Rust这样用如此优雅的方式表达出来，这就得靠enum、generic、tuple、（variant with no data）的组合应用。

> 以上内容已从侧面反映出为什么Rust难以上手：简简单单的几行语句，却包含着太多语法点，而每个语法点，却都蕴含着独特的哲学观点，需长期琢磨才能想明白为何要这么设计。

> Option和Result，都是用enum定义的Complex Type，且使用了Generic泛型定义，T和E是类型参数。由于enum的成员互相排斥，同一时刻只能有一个成员存在，这就恰当地表达了（有与无）、（对与错）（只能二选一）的情景。

```rust
enum Option<T> {
    None,
    Some(T)
}

enum Result<T, E> {
    Ok(T),
    Err(E)
}
```

> Option中的None表示（无），不包含任何数据的变量，很神奇吧？Some表示（有），有什么呢？有一个T类型的data。

> Result中的Ok表示（正确），Ok(T)表示正确的结果，是一个tuple，包含着T类型的Data、Value；类似的，Err表示（错误），Err(E)表示错误的结果，是一个tuple，包含着E类型的错误信息指示数据。

> 用Option<&T>可彻底消除访问空指针带来的段错误（Segment-Fault）。因为Option<&T>的值要么是None，要么是Some(r)，而在Rust中，任何（引用/指针 r）必然指向一个实际存在的value，这种语法特性强行要求程序员在编程时必须判断一个指针是否为None，访问空指针的问题根本不存在。

> C语言中的union成员共享同一份存储空间，Rust的enum也一样。但若不同成员size差别过大，系统就需要按照所有成员中的最大size分配存储空间，如何解决这类问题呢？这时就要用到（Box打包工具）。

> 举一个二叉树的例子：TreeNode<T>的size可能特别大，而Box则返回size很小的(Heap存储空间的指针)，在enum类型定义中，用Box类型代替个别成员的原初类型，则可碾平不同成员之间的size差距，这个设计技巧，也适用于其他（Container容器类型)，比如（array、vector）等容器实际包含的内容，可以是value对象的指针，而不是value对象本身。

```rust
struct TreeNode<T> {
    Node: T,
    Left: BinaryTree<T>,
    Right: BinaryTree<T>
}

enum BinaryTree<T> {
    Empty,
    Root(Box<TreeNode<T>>)
}
```

> 再举一个Box的例子：编译returns_closure1会报错，因为编译器必须知道函数返回值的固定size，但（ Fn(i32) -> i32 ）的size不确定；returns_closure2的返回值用Box打包，则可正常编译通过。

```rust
fn returns_closure1() -> Fn(i32) -> i32 {
    |x| x + 1
}

fn returns_closure2() -> Box<dyn Fn(i32) -> i32> {
    Box::new(|x| x + 1)
}
```

> 在Rust中，任何value都必须有一个容器，可称其为owner，即：任何value都有主人，这样才可有效实行生命周期管理，并及时执行垃圾回收。

> 在C、C++中，程序员需手动管理Heap内存空间的分配与释放，这绝对是一件听起来平淡无奇、做起来全是Bug的事情（Null Pointers、Dangling Pointers、Double Frees、Memory Leaks...），多线程并发模型更是特别放大了这些Bug的危害性。所以Java、Go中自带了垃圾回收器，为程序员节约大量精力，并让程序变得更加安全健壮。不过这有代价：编译后的可执行程序或执行环境必须内嵌垃圾回收线程，让程序size变大，并在运行时出现间歇性卡顿现象，因为需要间歇性地让渡一部分时空资源用于执行垃圾回收器程序。

> Rust则独辟蹊径，走出了一条与众不同的内存空间管理路线，即不需要手动分配与释放，也不需要内嵌垃圾回收器，而是在编译源代码时，透彻地分析每一个value及其owner的生命周期，且单个value只能被单个owner独自拥有，当进程控制流跳出owner的scope作用域时，owner和value就会被立即drop掉，并回收其占据的所有资源（不仅仅是存储空间）。这是一件听起来容易，做起艰难的事情，必须对语言的核心语法做全新的设计才可能做到，要不然其他编程语言早就这么干了。有些程序员（比如我）刚开始听说这一特性的时候，估计会在内心质疑一下：“扯淡，不可能”，这是典型的思维定势。Rust给我的教训就是不要轻易说不可能，自己认为不可能的事情，别人可能做得灿烂又辉煌。因为无论是一个人，还是一门编程语言，若不改变底层构造，其外在表现也很难有特别突出的地方。

> 变量variable这个词在Rust中，其含义比较模糊，因为variable实际上是owner和value的结合体，变量的名字，可以被视为owner，变量的内容，可以被视为value，而复合型value本身又可以（包含、拥有）多个其他value或value的指针，成为其他value的owner。拥有与被拥有的关系追根溯源必然是一个树状结构（Ownership-Tree），而树根则必然是某个variable。Rust的Ownership设计，比C++程序中value之间潜在的任意图状关系更简单、更清晰、更有条理。

> 由于单个value只能被单个owner独自拥有，若需和其他owner分享value，要么（move转移），要么（borrow借用）。若move，则原先的owner将被drop掉，或被标记重置为（未初始化的不可用状态），若borrow，则将value引用/指针传递给新的owner，但用完之后必须将value归还给原owner。新的owner被drop之后，Rust会自动完成value归还动作，就是说，在borrow情况下，新owner不会比原owner活得更久，value的引用/指针不会比value本身活得更久，这就是Rust的Lifetime生命周期设计，可简记为：（r <= &v <= v）。千万不要小看这个简记方式，其能够发挥的威力非常强大。

> 有两种borrow的方式：sharing/read-only只读可共享、mutable可改写。

> Rust编译器非常智能，能让编译器做的，一定不要手动去做，能在编译时确定的事情，一定不要放在运行时确定。

参考材料：
- O’Reilly螃蟹书《Programming Rust》。
- Rust官方教材《The Rust Programming Language》。
- rustup docs --book  ##it will open the book in web browser.

未完，待续。。。草稿阶段：

- 有品（trait）无类（class），而此类（class）非彼类（type）
- 关联Trait，Where，SQL
- 一切皆定语，一切皆修饰，一切皆描述





