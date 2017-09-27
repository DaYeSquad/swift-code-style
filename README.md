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
 
## README 
 
我们同样定义了空格和换行的标准来统一代码的样式 
 
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
 
## README 
 
首先，虽然注释很重要，但是最好的注释就是自文档化。注释是用来解释说明某段特定的代码做了些什么，或者为什么这么做。同时，注释必须能否保持更新或删除。
注释是写给下一个看这些代码的人，下一个人可能就是你自己。 
 
# 类型 
 
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
 
# Class, Struct, Protocol 
 
## README 
 
Swift 2.0 开始强调值类型 (Objective-C 以及 Swift 中的 Class 是引用类型)，使用值类型可以给我们带来许多好处，如值拷贝、线程安全等 － 不要忽视他们，往往在复杂的工程中，往往就是一两处的问题 (却难以 Debug 到) 耽误了大量的时间。 
 
# 命名 
 
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
 
