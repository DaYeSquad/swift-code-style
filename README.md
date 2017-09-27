# 分号

## 不要使用分号

Swift不需要在每句话后加分号`;`，除非你想把多个语句写在一行。
**不要在一行中用分号`;`来分开多个语句。**
DO:
```swift
let swift = "not a scripting language"
```
DON'T:
```swift
let swift = "not a scripting language";
```

# 提高 Swift 编译速度

## 少用 extension

After identifying the files slowest to compile, and conducting some additional experiments, I found that using extensions too generously -- for example, having a class extension for each protocol implementation -- increases build time. So I converted most of the extensions in Swift files to simple class methods, which resulted in a four-second median improvement (seven-second average) in compilation time. The change was simple -- I just merged all class extensions into one class:

link: https://tech.zalando.com/blog/speeding-up-xcode-builds/

# 控制流

## 使用 for-in 的形式

遍历请使用`for-in`的格式，而不是C语言风格的`for-condition-increment`的格式
*C-style for statement is deprecated and will be removed in a future version of Swift*
DO:
```swift
for _ in 0..<3 {
print("Hello three times")
}

for (index, person) in attendeeList.enumerate() {
print("\(person) is at position #\(index)")
}
```
DON'T:
```swift
for var i = 0; i < 3; i++ {
print("Hello three times")
}

for var i = 0; i < attendeeList.count; i++ {
let person = attendeeList[i]
print("\(person) is at position #\(i)")
}
```

# 空格和换行

## @objc 独立成行

使用到 @objc 的时候需要独立成行:

DO:
```Cpp
@objc
private _setupApperance() { ... }
```

DON'T
```Cpp
@objc private _setupApperance() { ... }
```

## 每行的长度不应该超过100

每行的长度如果过长了话会影响阅读代码的体验，比如有的开发者习惯左边和右边的 pannel 同时打开，那么100个字符/行的长度是最适宜的，这样可以保证在开关左右 pannel 的时候 IDE 不会出现换行，你可以在 Xcode 中设置每行的最大长度的警告线 (Preferences > Text Editing > Page guide at column: 100)。

## 如果方法名过长需要换行了话，在第二行空4格

当一个函数超过100的时候，可以在适当的地方开始换行(如某个参数的结尾)

DO:
```Swift
func reticulateSplines(spline: [Double], adjustmentFactor: Double,
translateConstant: Int, comment: String) -> Bool {
// reticulate code goes here
}
```

## README

我们同样定义了空格和换行的标准来统一代码的样式

## 不要使用 C 语言的括号形式

条件控制语句，例如 if/else/while/switch 等的左括号应当在本行，而右括号应当在新的一行。

DO:
```Swift
if user.isHappy {
// Do something
} else {
// Do something else
}
```

DON'T:
```Swift
if user.isHappy
{
// Do something
}
else {
// Do something else
}
```

## 使用空格而不使用 Tab

永远使用2个空格来表示换行，而不要使用 Tab 来换行。
你可以设置你的 IDE 自动将输入的 Tab 转换为空格。

## 在实现方法内使用空行来视觉区分

在实现方法中，你可以使用空行来区分一段逻辑，不过当你空行极其多的时候，就应该考虑是不是该在这个方法内提取出一些方法来增加可读性。

# 内存管理

## 闭包捕获，延长对象生命周期

使用`[weak self]` 和 `guard let strongSelf = self else { return }`语法组合的形式延长对象的生命周期。`[unowned self]`会在闭包结束的时候被释放掉，所以使用`[weak self]`延长声明周期。
DO:
```
resource.request().onComplete { [weak self] response in
guard let strongSelf = self else { return }
let model = strongSelf.updateModel(response)
strongSelf.updateUI(model)
}
```
DON'T:
```
// might crash if self is released before response returns
resource.request().onComplete { [unowned self] response in
let model = self.updateModel(response)
self.updateUI(model)
}

// deallocate could happen between updating the model and updating UI
resource.request().onComplete { [weak self] response in
let model = self?.updateModel(response)
self?.updateUI(model)
}
```

# 注释

## 对方法的描述以及引用

当涉及方法并包括所需的参数名称时，应该从调用者的角度命名，或使用`_`命名为缺省。

> 从你自己创建的`init`方法调用调用  `convertPointAt(column:row:)` .
>
> 如果你调用`dateFromString(_:)`要确定你提供了一个格式为"yyyy-MM-dd"的字符串.
>
> 如果你从 `viewDidLoad()`调用方法 `timedAction(afterDelay:perform:)` 记得提供一个调整延迟值和一个动作来执行此方法.
>
> 你不应该直接调用数据源方法: `tableView(_:cellForRowAtIndexPath:)` .

如果有疑问，可以参照 Xcode 在跳转栏的方法，风格和它保持一致.

## 不要在注释中嵌入代码片段

不要在注释中嵌入代码片段，因为代码要尽可能的自文档化，并且不利于自动生成文档

## 文件注释

每个文件都应该以注释开始，文件的注释包括以下内容：

- 一个许可说明的模板
- 一个基本的文件说明
- 如果你对作者的代码改动较大，可以考虑删除作者的历史版本记录，并提供更详细的说明

## 类的声明注释

每一个`class`、`category`或`protocol`都需要一个注释说明其作用及如何使用。
注释要包括概述、必要参数、版本信息、作者、更新信息，如：
```swift
/// 说明该类的作用，及如何使用
///
/// 参数: 必要的参数
///
///  @since 版本信息
/// @author 作者
/// @update by 作者二 since 版本二 更新内容
```
另外，如果是public的方法，要对每个方法做注释，包括参数、返回值以及注意情况等

## 实现中的注释

用竖线`|` 代替引号`“”`，这样能消除一些歧义，如：
```swift
// Sometimes we need |count| to be less than zero.
```
或者本身带有引号的情况，如：
```swift
// Remember to call |StringWithoutSpaces("foo bar baz")|
```

## README

首先，虽然注释很重要，但是最好的注释就是自文档化。注释是用来解释说明某段特定的代码做了些什么，或者为什么这么做。同时，注释必须能否保持更新或删除。
注释是写给下一个看这些代码的人，下一个人可能就是你自己。

# 类型

## 语法糖

使用简洁的语法糖的声明，而不是全类型
**DO**
```Swift
var deviceModels: [String]
var employees: [Int: String]
var faxNumber: Int?
```
**DON'T**
```Swift
var deviceModels: Array<String>
var employees: Dictionary<Int, String>
var faxNumber: Optional<Int>
```

## 常量

使用 `let` 定义常量， `var` 定义变量. 如果一个变量不会发生变化，尽量用 `let` 修饰而不是 `var`。
对于 Class 应当都使用 `let` 而不是 `var`，因为 Class 是引用类型，`var` 和 `let` 的修饰符并不可以阻止其属性被修改

提示：一个好的技巧是用 `let` 定义一切属性，只有当编译器提示时才改为 `var`.

## 尽量使用 Swift 形式的常量而不是 C 形式的常量

推荐使用 Swift 形式的常量: Module.Constant, 而不是 C 形式的常量: ModuleConstant:

DO:
```Swift
CGRect.infinite, CGRect.null
```

DON'T:
```Swift
CGRectInfinite, CGRectNull
```

## 可选类型

1.声明变量和方法返回类型用 `?` 表示可选类型， 表示可接受一个为 `nil` 的空值.
只有你确定变量会在使用之前被实例化，才可使用`!` 隐式类型声明一个变量.比如子视图会在 viewDidLoad 被创建
当我们访问可选类型值时，当值只被访问一次或者访问链中有多个可选类型值，那么我们使用可选型方式调用。

```Swift
self.textContainer?.textLabel?.setNeedsDisplay()
```
if let 判断可选类型一次，可以在作用域内做多个操作
```Swift
if let textContainer = self.textContainer {
// do many things with textContainer
}
```

2.当命名可选变量或属性，避免命名为比如:`optionalString` 或者 `maybeView`,因为他们的可选性已经在类型声明时明确了.
`if let` 判断语句尽量使解包前后变量名一致.
**DO**
```Swift
var subview: UIView?
var volume: Double?

// later on...
if let subview = subview, volume = volume {
// do something with unwrapped subview and volume
}
```
**DON'T**
```Swift
var optionalSubview: UIView?
var volume: Double?

if let unwrappedSubview = optionalSubview {
if let realVolume = volume {
// do something with unwrappedSubview and realVolume
}
}
```
3.不要使用强转符号(!)将一个 `nullable` 的对象转换为 `nonnull` 的对象，绝大多数的崩溃错误都是由此引起的

4.相对于`if let` 我们更偏好使用 `if guard` ，因为 `guard` 语法会强制你实现 `else` 的逻辑，让错误的 handle 更加优雅

## 使用 Swift 的原生类型而不是 Objective-C 的

尽量使用 Swift 的原生类型，Swift 可以通过桥接文件对 OC 进行调用. 对一个属性进行声明时，倾向于同时声明属性的类型.
**DO:**
```Swift
let width: Double = 120.0                                    // Double
let widthString: String = (width as NSNumber).stringValue    // String
```
**DON'T**
```Swift
let width: NSNumber = 120.0                          // NSNumber
let widthString: NSString = width.stringValue        // NSString
```

在 Sprite 工具包中，为了避免过多的转换，可以使用 CGFloat 使代码更简洁

## 类型推断

- 虽然通过编译器的类型推断使代码更加紧凑，但是，这意味着要求你的变量声明的可读性要比之前更高。
所以，**尽量给常量或变量声明带上确切的类型**。
DO:
```swift
let maximumWidth: CGFloat = 106.5
```
DON'T:
```swift
let maximumWidth = 106.5
```

- 空列表和字典的声明，使用类型注解的形式：
DO:
```swift
var names: [String] = []
var lookup: [String: Int] = [:]
```
DON'T:
```swift
var names = [String]()
var lookup = [String: Int]()
```

## Struct 的初始化尽量使用 Swift 形式的而不是 C 形式的

尽量使用新的 Swift 的 API 而不是 Objective-C 的 bridge 后的 API:

DO:
```Swift
let bounds = CGRect(x: 40, y: 20, width: 120, height: 80)
let centerPoint = CGPoint(x: 96, y: 42)
```

DON'T:
```Swift
let bounds = CGRectMake(40, 20, 120, 80)
let centerPoint = CGPointMake(96, 42)
```

# Class, Struct, Protocol

## README

Swift 2.0 开始强调值类型 (Objective-C 以及 Swift 中的 Class 是引用类型)，使用值类型可以给我们带来许多好处，如值拷贝、线程安全等 － 不要忽视他们，往往在复杂的工程中，往往就是一两处的问题 (却难以 Debug 到) 耽误了大量的时间。

## 何时选用 Class，何时选用 Struct

在 Swift 中，Class 是引用类型，Struct 是值类型，值类型有许多好处，例如当你传递一个值类型给另一个对象是，发生的是深拷贝(deep copy)操作，不会出现改了后者就改了前者的问题，还意味着线程安全，虽然很有可能有人会说值类型传递是相对耗时的(的确，相对引用类型，传递值类型是会稍微耗时一点)，但是，我们应当认为在如今的世界中，这种不是复杂度提升级别的耗时(耗时为一个常量)相对于值类型能带来的好处是非常明显的，所以我们建议在 Swift 中请优先考虑使用值类型。(关于性能问题如果你深陷网络"性能陷阱"不能自拔，可以查阅 WWDC 2015 - Swift Value Type 来看看苹果官方的回答，更可以遵循我组第二原则 : 没有真正看到性能问题时不要考虑性能问题)，下面我们就创建对象时的选用 Class 的情况做一个简单的描述:

1.当一个对象具有唯一性的时候选用 Class:
如果你要创建的对象是一个具有 identifer 的对象，例如 User, Task，哪怕两个 User 具有相同的名字，但是不同的 UID 即表明他们不是同一个人，那么这个时候 Class 更加合适，但是比如这个 User.Birthday 可能是一个 Date(你自定义的 Date)，这个 Date 就更适合用 Struct，因为两个用户如果都出生在10月1日，那么这个10月1日是对等的。

2.不要和系统的 API 对着干:
如果系统的 API 要你传一个 Class, 那么就传一个 Class，不要想方设法的对抗系统(尤其是使用只有你看得懂的黑魔法)。

## 除非语法需要，否则不要显式使用 self

在 Swift 中，默认调用方法或者 property 的时候不需要使用 self.xxx 来调用，另外，Swift 要求 Closure 中使用 self.xxx 来显式的暗示 self 已经被 retain 了，所以，在正常的方法调用以及 property 的调用中，不要使用 self.xxx 来调用以免产生歧义。

## 使用 extension 语法来分割 protocol 的实现代码

在 Swift 中，推荐使用 extension 的方式来分割实现的代码，在视觉上可以更好的加强用户的阅读体验，当然，别忘了加上 //MARK: -

DO:
```Swift
class MyViewcontroller: UIViewController {
// class stuff here
}

// 如果这个 extension 需要说明，可以这样注释在这里
extension MyViewcontroller: UITableViewDataSource {
// MARK: - UITableViewDataSource
// table view data source methods
}

extension MyViewcontroller: UIScrollViewDelegate {
// MARK: - UIScrollViewDelegate
// scroll view delegate methods
}
```

DON'T:
```Swift
class MyViewcontroller: UIViewController, UITableViewDataSource, UIScrollViewDelegate {
// all methods
}
```

## Computed Properties

如果一个 Computed Property 是只读的，则不要写 get:

DO:
```Swift
var diameter: Double {
return radius * 2
}
```

DON'T:
```Swift
var diameter: Double {
get {
return radius * 2
}
}
```

# 命名

## 枚举

Swift使用大写驼峰式命名枚举值，枚举值以小写开头：
```swift
Enum Shape {
case rectangle
case square
case rightTriangle
case equilateralTriangle
}
```
OC命名同使用大写驼峰命名法，而枚举值必须以枚举名开头，便于与 Swift 混编
```OC
typedef NS_ENUM(NSUInteger, WTKShape) {
case WTKShareRectangle
case WTKShareSquare
case WTKShareTriangle
case WTKShareCircle
}
```

## 类的前缀

Swift类型已包含它们的模块自动命名空间，你不应该添加一个类的前缀。如果来自不同模块的两个名字发生冲突，你可以通过模块名作为前缀的类型名称消除歧义

```Swift
import SomeModule

let myClass = MyModule.UsefulClass()
```

## 常量和变量

1.公开变量:用 public 修饰，如果这个变量是必传参数，应该用 ! 标明，如果是可选参数，用 ? 标明

2.私有变量:用 private 或 fileprivate 修饰，以 _ 命名.    private & fileprivate:
fileprivate： 如果这个私有变量在当前文件(包括 extension)中都允许访问，则修饰为 fileprivate
private：则是真正的私有，离开了这个类或者结构体的作用域外面就无法访问
http://www.jianshu.com/p/604305a61e57

## README

使用驼峰命名法命名类，方法，变量等等，类名以及协议名首字母应大写，然而方法名和变量名首字母小写.常量应以 k 作为首字母
**DO:**
```Swift
private let kMaximumWidgetCount = 100

class WidgetContainer {
var widgetButton: UIButton
let widgetHeightPercentage = 0.85
}
```
**DON'T:**
```Swift
let MAX_WIDGET_COUNT = 100

class app_widgetContainer {
var wBut: UIButton
let wHeightPct = 0.85
}
```
对于函数以及 init 方法，倾向于对所有的参数命名，除非上下文非常简洁清晰.如果能使函数更具可读性，我们也可以包括外部参数名称.
```Swift
func dateFromString(dateString: String) -> NSDate
func convertPointAt(column column: Int, row: Int) -> CGPoint
func timedAction(afterDelay delay: NSTimeInterval, perform action: SKAction) -> SKAction!

// 调用如下:
dateFromString("2014-03-14")
convertPointAt(column: 42, row: 13)
timedAction(afterDelay: 1.0, perform: someOtherAction)
```

对于方法，遵照标准的苹果官方约定对第一个方法参数命名：
```Swift
class Counter {
func combineWith(otherCounter: Counter, options: Dictionary?) { ... }
func incrementBy(amount: Int) { ... }
}
```

## 方法

私有方法：用 private 修饰，以 _ 开头 (虽然在苹果的 [Apple's Cocoa Coding Guidelines](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/CodingGuidelines/CodingGuidelines.html) 不建议这么做，但是其实 99% 以上的情况你都不会和现有的方法重名)
```Swift
private func _setUpApperance() {}
```

用参数列表中参数名表示为可读性的方法名，第一个参数作为方法名一部分：如
``` swift
func dateFromString(dateString: String) -> NSDate
func convertPointAt(column column: Int, row: Int) -> CGPoint
func timedAction(afterDelay delay: NSTimeInterval, perform action: SKAction) -> SKAction!

// would be called like this:
dateFromString("2014-03-14")
convertPointAt(column: 42, row: 13)
timedAction(afterDelay: 1.0, perform: someOtherAction)

class Counter {
func combineWith(otherCounter: Counter, options: Dictionary?) { ... }
func incrementBy(amount: Int) { ... }
}
```

## 使用国际英语而不是美式英语

使用国际化英语拼写与 Apple's API 匹配.
**DO**
```Swift
let color = "red"
```
**DON'T**
```Swift
let colour = "red"
```


