
# Solidity智能合约安全小结

Solidity智能合约安全，大致可从以下若干方面进行思考。

- 权限检查
- 未知调⽤
- 重⼊调⽤
- 数据破坏
- 可⽤性破坏
- 边际效果
- 临界值处理
- 事件⽇志
- 特殊交互时序
- 计算精度
- 随机数
- 矿⼯优势
- 资源消耗

权限检查（Caller） 
> 谁有资格调⽤合约函数：⽩名单账⼾ 、签名验证通过的账⼾、满⾜资⾦与资产要求的账⼾、其他条件 要求。 

未知调⽤（Callee）
> 在某种情况下，合约函数调⽤了未知合约的未知函数，⽽未知函数所执⾏的操作，超出合约作者的预期，产⽣出乎意料之外的坏结果。

重⼊调⽤
> 重⼊函数调⽤是最典型的未知调⽤。

可⽤性破坏
> 合约作者未能充分考虑计算复杂度与数据规模的关系，随着合约管理的⽤⼾或数据规模的膨胀，造成某些合约函数的Gas费越来越⾼，最终导致合约不可⽤，资⾦⽆法正常取回。

数据破坏
> Delegate Call，处理Caller本⾝的数据，可对Caller⾃⾝拥有的数据，尤其是权限和额度相关数据，进⾏破坏或篡改，导致Caller本⾝完全作废，资⾦全部损失。

边际效果
> 调⽤⼀个函数，执⾏某⼀业务功能，但真正的代码，往往会执⾏ 【业务语⾔描述】 之外的操作，⽐如 【转账 NFT】，并⾮只是简单的转账，改变Owner，还需要额外清除 Approval 相关数据。 

临界值处理
> 函数输⼊参数，以及复杂结构体内部参数，都应该有适当的取值范围，输⼊临界值、边界值，是否能能够产⽣够符合预期的结果。

事件⽇志
> 合约函数所进⾏的核⼼操作，应该弹射出事件⽇志，便于链外系统，捕捉记录相关核⼼数据的变化，针对攻击事件，即时给予⼈⼯⼲涉，减少资产损失。

特殊交互时序 
> 合约函数，天⽣就⾯对并发的⽤⼾访问，虽然区块链本⾝会对交易进⾏排序，顺序执⾏，但不同的执⾏序列，会导致完全不同的结果。⽐如 Approve 的资⾦额度为 X，现在想修改为 Y，最好调⽤
IncreaseToY/DecreaseToY，⽽不是直接执⾏ Approve Y，因为在修改 X 为 Y的空隙中，可能刚好有⼈取⾛ X，导致 X -> 0 -> Y，⽽不是操作员预想的 X -> Y。 

计算精度
> 智能合约进⾏资⾦存取额度计算时，未能考虑计算精度，导致资⾦泄漏，最终造成合约不可⽤。精度计算，应该向有利于系统安全运⾏的⽅向进⾏舍⼊。 

随机数
> 随机数是否真的随机，是否存在被操纵、隐藏的可能，以此控制原本设想随机的功能，对攻击者来 说，变成确定性的功能。

矿⼯优势
> 矿⼯可以监控与任意合约紧密相关的任意交易，并操纵交易打包顺序，故意延迟合约中与时间有关的操作，造成合约未能按项⽬构想，服务好⽬标⽤⼾。

资源消耗
> 除Gas费本⾝，程序执⾏所需要的资源，还有栈空间等资源，假如调⽤栈的深度超过预期，是否会触发未知⾏为？ 

## 总结
> 假定项⽬与合约本⾝所要实现的业务逻辑，并不存在设计层⾯的漏洞，而智能合约之所以不安全，主要是因为发⽣了 【意料之外】的情景。
> 要确保安全，则需要对合约运⾏时的所有环境因⼦，进⾏综 合考虑。主要是：数据状态、交互顺序、执⾏资源、随机数、时间戳、Caller、Callee、Gas、Event。







