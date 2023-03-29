# solidity基础

## solidity源文件布局

### SPDX许可证标识符

如果智能合约的源代码可用，则可以更好地建立对智能合约的信任。由于源代码的提供总是涉及到版权方面的法律问题，所以Solidity编译器鼓励使用机器可读的 \[SPDX license identifiers] 每个源文件都应以注释开头，说明其许可证
如果不想指定许可证或源代码不是开源的，请使用特殊值 UNLICENSED

## 编译指示

这个 pragma 关键字用于启用某些编译器功能或检查。杂注指令始终是源文件的本地指令，因此如果要在整个项目中启用杂注，则必须将杂注添加到所有文件。如果你import另一个文件中，该文件中的杂注执行以下操作 not 自动应用于导入文件。

### 版本pragma

源文件可以（也应该）使用版本杂注进行注释，以拒绝使用将来可能引入不兼容更改的编译器版本进行编译。我们试图将这些限制保持在绝对的最低限度，并以一种方式引入它们，即语义的改变也需要语法的改变，但这并不总是可能的。
例如：pragma版本的用法如下： pragma solidity ^0.5.2;
具有上述行的源文件不使用0.5.2版之前的编译器编译，也不适用于从0.6.0版开始的编译器（第二个条件是通过使用 ^ ). 因为在版本之前不会有突破性的变化 0.6.0 ，可以确保代码按预期方式编译。

## 导入其他源文件

### 语法和语义

Solidity支持导入语句，以帮助模块化与JavaScript中类似的代码（从ES6开始）。但是，solidity不支持 \[default export]

*   1.通过文件夹相对位置进行导入
*   `import "./xxx.sol"`
*   2.通过源文件网址导入网上的合约
*   `import "@chainlink/contracts/src/v0.8/interfaces/AggregatorV3Interface.sol";`
*   3.通过npm的目录导入
*   `import '@openzeppelin/contracts/access/xxx.sol'`
*   4.通过全局符号导入特定的合约
*   `import {xx}"./xxx.sol"   //也可对合约进行重命名和导入多个合约如： import {xx as xxx, xx}"./xxx.sol"`

# &#x20;数据类型

## 基本类型

Solidity支持的基本类型有整型，布尔型，字符串型和字节型，这里有必要强调一下Solidity在设计数据类型时精确到每一个字节，就像int8，int16等等，int等价于int256。

**地址类型addrrss**是Solidity特有的数据类型，它对应了以太坊的账户地址，地址具有balance属性可以返回账户余额。也可以通过transfer和send来交易以太币，send比transfer多返回一个布尔值表示转账是否成功。
**枚举enum**枚举是包含一个预定义的常量值列表的值类型
`enum name {常量1，常量2}`
name.常量1的值为0，后面的值从0开始自增和go的iota类似，上面的方式是默认的，你也可以自行定义，数据类型是uint8。

### 变量

按照其存储的位置，可以分为状态变量和局部变量，（状态变量也叫全局变量）。

*   状态变量是指存储在以太坊节点中的变量，这类数据的存储需要支付费用,也是合约的成员变量。

*   局部变量和其他语言的局部变量差不多，状态和局部变量的定义方式差不多。

`Type [permission identifier;  //状态变量定义`
其中permission用来修饰变量的访问权限，可以使用public，private和internal，如果不写默认为private。

*   public：可以直接从外部调用访问这些状态变量。编译器隐式生成getter两数以读取公共状态变量的值。

*   internal：无法 直接从外部调用访问这些状态变量。可以从当前合约中的函数和此合约的子合约访问它们。

*   private：无法直接从外部调用访间这些状态变量，子合约也无法访问它们，只能通过当前合约中的函数访问。

局部变量的修饰符主要强调是该值的值传递还是引用传递。有时候编译器要求我们显式地声明到底是值传递（memory）还是引用传递（storage）。函数的参数及返回值都是memory类型传递，函数体内部根据需要可以使用storage传递或memory传递

语法不允许在合同级别进行分配。但它允许声明状态变量，并且[这些](https://ethereum.stackexchange.com/questions/1511/how-to-initialize-a-struct)变量[可以包含一个初始化程序](https://ethereum.stackexchange.com/questions/1511/how-to-initialize-a-struct)。

### 复合类型

Solidity的复合类型有数组，映射，结构体，字符串。 **数组** 数组数组分为定长数组和动态数组。

    int[5] public age; //定长数组
    int[] public age;
    int [] public age=new int[](5) //动态数组可以通过new初始化但得说明初始长度。 

动态数组可以通push（）在数组末尾添加元素和go的append类似。

**映射mapping**

```
mapping(t1=>t2) modifiers mapname;
例如 mapping(address=>string) public addr;
addr[msg.sender]="sss";//赋值

```

**字符串** 在Solidity本质上是数组，不过不能用push。 **结构体** 和go的结构体类似。

**总结**：**在Solidity中基本类型为值类型，值类型在自己的存储空间中保存数值。
复合类型为引用类型，引用类型不直接将其值存储在变量本身中，它保存的是值存储位置的地址。**

### 内建对象

在合约代码中，可以使用区块链系统本身的一些数据，如区块信息（block）及合约被调用时的交易信息（msg），对于这类对象无需声明直接使用，位于全局命名空间。

*   (1)block，区块信息。

    &#x20;区块信息是区块链系统的特点属性，具体包含内容如下。

    block.coinbase (address)：当前块的矿工地址。

    &#x20;block.dificulty (uint)： 当前块的挖矿难度系数。&#x20;

    &#x20;block.gaslimit (uint)：当前块gas 的上限。&#x20;

    block.number (uint)：当前块编号。&#x20;

    book.timestamp (uint)：当前块的时间戳，等同于 now。&#x20;

    no：时间戳，与block.timestamp 相同。 ⑧msg：合约被调用时传递过来的消息。&#x20;

*   (2）msg：在合约调用时传递了调用信息，其内容如下。&#x20;

    msg.data (bytes)：完整的 [calldata](mailto\:calldatao@msg.gas)

    [msg.gas](mailto\:calldatao@msg.gas) (uint)：剩余的gas量。&#x20;

    msg.sender (address)：消息的发送方（调用者），非常重要。

    msg.sig (bytes4)： calldata 的的四个字节（函数标识符）。&#x20;

    mse.valae (uint：所发送的消息中 wei（以大坊最小的虛拟数字货币单位）的数量。 &#x20;

*   tx：交易信息

    tx.gasprice (uint)：交易的gas 价格。

    tx.origin (address)：交易发送方（最原始调用者）。

以太坊一共有 4 个单位，从小到大分别是wel、svel、fnney 和 cther，它们之间的换算关系如下：

1 ether=1000 finney  1 finney =1000,000 gwei   1 gwei =1000,000,000 wei

# &#x20;函数

与传统编程不同的是，在智慧合约开发时没有主函数入口这样从上到下的流程逻辑。如果把智能合约理解为一个进程的话，开发的函数是为了提供与该进程交互的接口。

函数的语法：

    function func_name(paramlist..) modifiers returns (returnlist..) 

*   function: 是函数声明的关键字

    func\_name：函数名

    paramlist：参数列表

    modifiers：函数修饰符

    returns：返回值关键字，支持多个返回值，最多7个

    return list：返回值类型列表

    函数例：
    function isEqual(string memory a,string memory b) public view returns (bool){
    bytes32 hasha=keccak256(abi.encode(a));
    bytes32 hashb=keccak256(abi.encode(b));
    return hasha==hashb;
    }
    //string类型无法用“==”进行比较，但是可以比较他们的hash值进行比较，上面函数就是相关实现

函数参数中的字符串，必须明确指出是memory类型，这是在0.5.0版本以后提出的新要求。

## 函数修饰符

控制访问权限的四个修饰符：public，private，external，internal。

| 关键字      | 外部访问 | 类内访问 | 子类继承 | 子类访问 |
| :------- | :--- | :--- | :--- | :--- |
| public   | 能    | 能    | 能    | 能    |
| private  | 不能   | 能    | 不能   | 不能   |
| external | 能    | 不能   | 能    | 能    |
| internal | 不能   | 能    | 能    | 能    |

以太坊的合约部署后可以看到3种颜色，分别是蓝色，橘红色和红色，不同颜色代表不同的函数能力

*   蓝色：只读函数，使用view关键字，该类函数不允许修改状态变量，调用不消耗gas。

    橘红色：写函数，该类函数会修改状态变量的值，调用会消耗gas。

    红色：可支付函数，该类函数涉及资产转移，必须加payable关键字，可以修改状态变量的值，调用会消耗gas。

### 修饰语[](https://www.osgeo.cn/solidity/cheatsheet.html#modifiers "永久链接至标题")

*   `pure` 对于函数：不允许修改或访问状态。
*   `view` 对于函数：不允许修改状态。
*   `payable` 对于功能：允许他们在接到电话的同时接收ether。
*   `constant` 对于状态变量：不允许赋值（初始化除外），不占用存储槽。
*   `immutable` 对于状态变量：在构造时只允许一个赋值，之后是常量。存储在代码中。
*   `anonymous` 对于事件：不将事件签名存储为主题。
*   `indexed` 对于事件参数：将参数存储为主题。
*   `virtual` 对于函数和修饰符：允许在派生契约中更改函数或修饰符的行为。
*   `override` ：声明此函数、修饰符或公共状态变量更改基协定中函数或修饰符的行为。

## 断言处理与自定义修饰符

### 断言处理

为了解决合约执行到一半的时候发生错误的善后问题，Solidity推荐我们使用state-reverting（状态恢复）机制，它很像数据库的事务逻辑。而使用if检测合约运行的错误，再用revert进行回滚太麻烦，在Solidity中，我们更多的使用assert和require进行断言判断，两者为Solidity的内置函数。

    //两个函数原型
    function assert (bool condexpr);
    function require(bool condexpr,string msg);

assert和require都是断言函数，断定某件事(condexpr代表的条件)一定成立，否则智能合约就会回退。

虽然两者都会回退，但是assert的方式比较过激，它会通过扣光剩余gas的方式来惩罚调用者（gas limit是多少就会扣多少），require则会把剩余的gas返回调用者。

require比较温和，因此推荐大多时候使用require。

assert一般做内部判断，尤其是与状态变量无关的判断。或者在商用前使用assert尽可能地测试合约错误，一旦assert扣完gas代表错误发生。

### 自定义修饰符

modifier可以将一些断言判断封装，组合为我们的业务需求，方便在其他函数中使用。

语法：

    modifier modifier_name(){
    require(cond,"sth wrong");  //require回退会，相关信息会返回sth wrong
    _;//占位符号，标识modifier的结束，表示了函数内代码的位置
    }

**modifier的执行顺序**\
一个函数可能需要做多个检查，那么我们可以写多个modifier，调用时只需将每个modifier以空格隔开。而检查顺序也就是modifier们的排列顺序。

# 使用event调试

智慧合约中并没有直接打印日志的函数，但Solidity中提供了另一种机制，event函数接口可以像日志那样显示智能合约运行的数据问题。

声明语法：

    event eventname(paramlists..)
    //
     event setage(address owner, uint256 age);

    //event只是接口，不需要实现，调用与普通函数类似，只需要在前面加一个emit。
    如  emit setage(msg.sender,age) 

log中会打印出msg.sender和age。
