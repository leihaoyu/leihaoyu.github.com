---
layout: post
title: "一些2016年官方视频观后小结"
description: ""
category: 
tags: []
---
{% include JB/setup %}
## 简单总结一下看了一些AppleDeveloper网站上wwdc2016的视频的领悟

持续更新中。。。


### 1.A Peek at 3D Touch

视频链接[点击此处](https://developer.apple.com/videos/play/wwdc2016/228/)

#### 我们为什么要使用3D-touch.
1. homescreen的3Dtouch可以快速进入app里面相应的功能，方便。
2. 里面的peek&pop，可以快速的对内容进行预览和操作，快速。
3. 新的UIPreviewInteraction让我们用，API支持。
4. 官方在推这一技术，如果你的app不支持，岂不是很low，潮流。

#### 使用3D-touch的要点.

#### Home screen quick action （主屏幕快捷框）

1. 分为静态的选项和动态的选项。
1. 静态的选项在Info.plist中添加一个 UIApplicationShortcutItems 的 Array 即可。
1. 动态的选项添加到 AppDelegate 中的 shortcutItems 属性即可。
2. 处理点击事件：

如果app在后台调用：

        func application(_ application: UIApplication, performActionFor shortcutItem: UIApplicationShortcutItem, completionHandler: (Bool) -> Void) {
        // 做你想做的事
        // 最后不要忘了调用completionHandler()
        }

如果app还没加载调用：

        func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [NSObject: AnyObject]?) -> Bool {
        // 通过 UIApplicationLaunchOptionsShortcutItemKey 在 launchOptions 中取得 UIApplicationShortcutItem
        // 然后做你想做的事
        }

补充一点就是作者说要注意版本问题，就是处理点击事件的时候可能正好是app升级过后数据可能是上个版本的数据，需要判断一下版本。

#### Peek and Pop （不知道怎么翻译好，概览？）
添加Peek and Pop要遵循以下几步：

1. 让需要预览的 ViewController 遵循 UIViewControllerPreviewingDelegate 协议
2. 调用 registerForPreviewing(with:sourceView:) 注册该ViewController
3. 在 preview 代理方法中提供一个预览的ViewController，并设置好 context 的 sourceRect.
4. 在 commit 代理方法中，直接调用 show(_:sender:) 即可。

Preview Action

在 Preview 的过程中，用户可以上滑来唤出类似 Action Sheet 的菜单。实现这一功能只需要重写 ViewController 中的 previewActionItems() -> [UIPreviewActionItem]方法即可。 系统提供了和 UIAlertAction 非常类似的 UIPreviewAction，来实现 UIPreviewActionItem。

### 2.What’s New in Swift (3.0的改动)

视频链接[点击此处](https://developer.apple.com/videos/play/wwdc2016/402/)

首先总结一下
1. swift3.0着重于基本层面的改动。
2. swift.org
3. 代码可以通过xcode迁徙到3.0

开始坐着介绍了swift开源半年来的事情，还有PackageManager，还有可以在Linux上用。
然后开始讲语言上的改动，说这些改动主要是通过大家一起的需求改动的。
#### 1.命名规范的改动。https://swift.org/documentation/api-design-guidelines/
#### 2.移除了一些特性：
* Currying func declaration 
* syntax var in function parameter 
* lists ++ and -- operators
* C-style for loop
* Implicit tuple splat in calls

#### 3.增加了一些特性：
* Scoped access level, new fileprivate access level 
* case labels with multiple variable bindings
* Generic Type Aliases
* Referencing Objective-C key-paths
* Referencing the selector for property getters and setters 
* Expanding Self to class members and value types 
* Adding a build configuration “is importable” test 
* Typealiases in protocols and protocol extensions

#### 4.修改了一些特性：
* Replace __FILE__ with #file
* inout moved to be part of the type
* Requiring leading dot prefixes for enum instance members 
* Attribute syntax: replace = with :
* Move @noescape and @autoclosure to be type attributes 
* Enforcing order of defaulted parameters
* Standardize function type argument syntax to require parentheses 
* Converting dynamicType from a property to an operator

#### 3.类型检查
＊ swift2中的判断！＝nil不再用了

    let ptr : UnsafeMutablePointer<Int> = nil
    if ptr != nil {
    ptr.memory = 42
    }

＊ swift3:

    let ptr : UnsafeMutablePointer<Int>? = nil 
    ptr?.memory = 42

关于解绑，swift2中：

    func f(value : Int!) {
    let x = value + 1 // x: Int - force unwrapped
    let y = value // y: Int!
    let array = [value, 42] // [Int], [Int!], [Int?], [Any]... Cannot convert value of type ‘[Int!]’ to argument type 
    use(array) }

swift3中：

    func f(value : Int!) {
    let x = value + 1 // x: Int - force unwrapped let y = value // y: Int?
    let array = [value, 42]
    let array2 = [value!, 42] // [Int]
    use(array) }

#### 4.StandardLib
swift2:

    i = collection.startIndex
    next = i.successor()

swift3:

    i = collection.startIndex
    next = collection.index(after: i)

swift2:

    let v = 2 * Float(M_PI)
    return x * CGFloat(M_PI) / 180

swift3:

    let v = 2 * Float.pi
    return x * .pi / 180

其他加入的特性：

＊ Add a Lazy flatMap for sequences of optionals
＊ Conversions Unsafe[Mutable]Pointer to Int and UInt
＊ Change Unmanaged to use UnsafePointer
＊ Add first(where:) method to Sequence
＊ Add generic result and error handling to autoreleasepool()
＊ Failable numeric conversion initializers
＊ Adding a public base property to slices
＊ Add sequence(first:next:) and sequence(state:next:) to the stdlib

#### 5.SwiftTools

在以下几方便都有提升

＊ Faster Type Checking
＊ Faster Startup Time
＊ Smaller Binaries
＊ Stack Promotion
＊ Incremental Compilation
＊ Faster Dictionary
＊ String Hashing
＊ Whole Module Optimization



